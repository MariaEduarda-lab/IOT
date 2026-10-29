# Semana 04 — Sensores e processamento de entradas analógicas e digitais

Nesta semana, estudaremos como uma grandeza física se transforma em informação para o microcontrolador. O foco será identificar sensores analógicos e digitais, usar o conversor analógico-digital (ADC) do ESP32 e construir uma cadeia de aquisição com validação, calibração, filtragem e tomada de decisão.

## Atividades da semana

- [Lista de exercícios](../exercicios/semana-04.md)
- [Gabarito comentado](../gabaritos/semana-04.md)

## Referência adotada

Os exemplos usam o **ESP32 clássico** em uma placa compatível com ESP32-WROOM. Outras variantes, como ESP32-C3, C6, S2 e S3, possuem pinout e características de ADC diferentes.

> [!IMPORTANT]
> Confirme a variante, a placa e a faixa elétrica do sensor antes da montagem. Nunca aplique `5 V` diretamente a um GPIO do ESP32 e não suponha que todo sensor “digital” produza apenas `HIGH` ou `LOW`.

## Objetivos de aprendizagem

Ao concluir esta semana, você deverá ser capaz de:

- explicar a cadeia de medição de uma grandeza física;
- distinguir sensor, transdutor e módulo;
- diferenciar saídas analógicas, digitais binárias e digitais por protocolo;
- interpretar faixa, sensibilidade, resolução, exatidão, precisão e tempo de resposta;
- explicar amostragem, quantização e resolução de um ADC;
- ler uma entrada analógica com o ESP32;
- selecionar pino, atenuação e faixa de tensão adequados;
- montar divisores resistivos com potenciômetro, LDR ou NTC;
- reconhecer ruído, saturação, offset e não linearidade;
- aplicar calibração, normalização, filtros e histerese;
- organizar a aquisição sem bloquear o programa com `delay()`.

## Pré-requisitos e materiais

- conteúdos das Semanas 1 a 3;
- Arduino IDE 2 com Arduino Core para ESP32;
- placa ESP32 e cabo USB de dados;
- protoboard e jumpers;
- potenciômetro de `10 kΩ` ou LDR com resistor de `10 kΩ`;
- LED externo e resistor de `220 Ω` a `330 Ω`;
- multímetro;
- opcionalmente, NTC de `10 kΩ` e capacitor cerâmico de `100 nF`.

### Documentação oficial

- [API de ADC do Arduino-ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/adc.html)
- [ADC no ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/adc.html)
- [Calibração do ADC do ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/adc_calibration.html)
- [GPIO do ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/gpio.html)
- [Datasheet da série ESP32](https://documentation.espressif.com/esp32_datasheet_en.html)

---

## 1. Da grandeza física ao programa

Um sistema de medição pode ser representado assim:

```text
grandeza física → sensor → condicionamento → ADC/interface → processamento → decisão
 temperatura       NTC       divisor RC       GPIO34         filtro       alarme
```

- **mensurando:** grandeza que se deseja medir, como temperatura ou luminosidade;
- **sensor:** elemento que responde ao mensurando;
- **transdutor:** converte uma forma de energia ou grandeza em outra;
- **módulo sensor:** placa que pode reunir sensor, resistores, comparador e circuito integrado;
- **condicionamento:** adapta, amplifica, filtra ou limita o sinal;
- **aquisição:** amostra e converte o sinal;
- **processamento:** valida, calibra, filtra e interpreta os dados.

O ADC mede **tensão**, não temperatura, luz ou pressão diretamente. Uma equação ou calibração relaciona a tensão à grandeza física.

## 2. Sensores analógicos e digitais

### Saída analógica

A tensão ou corrente varia dentro de uma faixa e representa a grandeza medida. Exemplos: potenciômetro, LDR ou NTC em divisor, sensor com saída de `0 a 3,3 V` e transmissor industrial de `4 a 20 mA` — este último exige condicionamento apropriado.

Uma saída analógica precisa ser amostrada por um ADC. “Analógico” não significa automaticamente mais preciso.

### Saída digital binária

Informa estados discretos, geralmente `HIGH` e `LOW`, como fim de curso ou saída `DO` de um módulo com comparador. É necessário verificar tensão, polaridade, pull-up e tipo de saída: *push-pull*, coletor aberto ou dreno aberto.

### Saída digital por protocolo

Um sensor digital também pode transmitir números codificados por I2C, SPI, UART ou outro protocolo. Um MPU-6050, por exemplo, transmite valores de aceleração e rotação por I2C; ele não se limita a ligado/desligado.

| Exemplo | Natureza da informação no ESP32 | Interface típica |
| --- | --- | --- |
| LDR em divisor | valor proporcional | ADC |
| botão/fim de curso | estado binário | GPIO |
| HC-SR04 | largura de pulso | GPIO + temporização |
| MPU-6050 | amostras numéricas | I2C |

## 3. Características de medição

| Característica | Pergunta respondida |
| --- | --- |
| Faixa | Quais valores mínimo e máximo podem ser medidos? |
| Sensibilidade | Quanto a saída muda quando a entrada muda? |
| Resolução | Qual a menor variação distinguível? |
| Exatidão | Quão próximo o resultado está do valor de referência? |
| Precisão/repetibilidade | Leituras repetidas ficam próximas entre si? |
| Linearidade | A relação entrada–saída se aproxima de uma reta? |
| Histerese | O resultado depende do sentido da variação? |
| Tempo de resposta | Quanto demora para acompanhar uma mudança? |
| Offset | Há um deslocamento aproximadamente constante? |
| Deriva | O resultado muda lentamente com tempo ou temperatura? |

Alta resolução não garante alta exatidão. Um ADC pode exibir muitos códigos e ainda possuir ruído, erro de referência e não linearidade.

## 4. Conversão analógico-digital

O ADC executa três ideias fundamentais:

1. **amostragem:** observa o sinal em instantes discretos;
2. **quantização:** aproxima a amostra para um dos códigos disponíveis;
3. **codificação:** representa o nível como um número binário.

Um ADC de `n` bits possui `2^n` códigos. Em 12 bits:

```text
2^12 = 4096 códigos: 0 a 4095
LSB ideal ≈ faixa de entrada / (2^n - 1)
```

Essa conta é um modelo. No hardware real há ruído, não linearidade, tolerâncias e variação da referência.

### Amostragem e aliasing

Para uma senoide de frequência máxima `fmax`, o teorema de Nyquist exige, idealmente, frequência de amostragem maior que `2 × fmax`. Na prática, usa-se margem e filtro analógico anti-aliasing. Se o sinal variar rápido demais, componentes de alta frequência podem aparecer como frequências falsas.

## 5. ADC no ESP32 clássico

No Arduino-ESP32:

```cpp
int bruto = analogRead(pino);                    // valor bruto, não calibrado
uint32_t milivolts = analogReadMilliVolts(pino); // resultado calibrado em mV
```

Por padrão, `analogRead()` usa 12 bits e retorna de `0` a `4095`. A faixa mensurável depende do chip e da atenuação; consulte a tabela oficial, pois ela não é idêntica em toda a família.

```cpp
analogReadResolution(12);
analogSetPinAttenuation(34, ADC_11db);
```

### ADC1, ADC2 e Wi-Fi

No ESP32 clássico, ADC2 não fica disponível ao aplicativo enquanto o Wi-Fi o utiliza. Para projetos IoT, prefira ADC1. O GPIO34 pertence ao ADC1 e é somente entrada, característica adequada a um sensor analógico.

GPIOs `34` a `39` são somente entradas, não possuem pull-up ou pull-down interno configurável e precisam de nível definido pelo circuito externo.

Na documentação atual, o ESP32 clássico com `ADC_11db` possui faixa mensurável aproximada de `150 mV` a `3100 mV`. Portanto, um potenciômetro alimentado em `3,3 V` pode produzir saturação nas extremidades. Isso não deve ser confundido com a tensão máxima absoluta do pino.

### Limites importantes

- não aplique `5 V` ao ADC;
- mantenha a entrada na faixa documentada para a atenuação;
- não trate `bruto × 3,3 / 4095` como tensão exata;
- use `analogReadMilliVolts()` ou calibração adequada quando a tensão importar;
- confirme o pino na documentação da placa, não apenas em imagem genérica.

## 6. Condicionamento e divisor de tensão

Sensores resistivos precisam ser convertidos em tensão:

```text
3,3 V ── R1 ──┬── Vout → ADC
              │
              R2
              │
             GND

Vout = 3,3 × R2 / (R1 + R2)
```

Com `R1 = R2 = 10 kΩ`, a saída ideal é `1,65 V`.

### NTC

Para `Rfixo` acima e o NTC abaixo:

```text
Rntc = Rfixo × Vout / (Vcc - Vout)
T(K) = 1 / [1/T0 + ln(Rntc/R0)/B]
T(°C) = T(K) - 273,15
```

`R0`, `T0` e `B` devem vir do datasheet. Nesta montagem, ao aquecer o NTC, sua resistência e `Vout` diminuem.

### Proteção e filtragem

O condicionamento pode exigir divisor ou conversor de nível, resistor de *shunt*, amplificador, buffer, diodos, isolamento ou filtro passa-baixas. Um capacitor de `100 nF` próximo à entrada pode reduzir ruído em algumas montagens, mas não corrige ligação, faixa ou aterramento inadequados.

## 7. Processamento das leituras

Uma sequência robusta é:

```text
ler → validar → filtrar → calibrar → converter unidade → decidir → registrar
```

### Calibração de dois pontos

Para valores conhecidos `(x1, y1)` e `(x2, y2)`:

```text
y = a × x + b
a = (y2 - y1) / (x2 - x1)
b = y1 - a × x1
```

Ela não remove automaticamente não linearidade, deriva ou erro fora da faixa calibrada.

### Normalização

```cpp
int percentual = map(bruto, brutoMin, brutoMax, 0, 100);
percentual = constrain(percentual, 0, 100);
```

`map()` usa aritmética inteira e não limita o resultado. Para maior precisão, use `float` e trate divisão por zero.

### Filtros simples

- **média móvel:** suaviza ruído, mas introduz atraso e é sensível a picos;
- **mediana:** rejeita bem impulsos isolados;
- **filtro exponencial:** usa pouca memória e ajusta a suavização por `alpha`.

```cpp
filtrado = alpha * amostra + (1.0f - alpha) * filtrado;
```

Quanto menor `alpha`, maior a suavização e mais lenta a resposta.

### Histerese

Um único limiar pode fazer a saída oscilar. Com dois limiares:

```text
desligado → liga acima de 2800
ligado    → desliga abaixo de 2400
entre os dois, mantém o estado anterior
```

## 8. Prática — aquisição com filtro e histerese

### Circuito

```text
Potenciômetro: externo → 3,3 V; central → GPIO34; externo → GND
LED: GPIO23 → resistor de 220 Ω a 330 Ω → ânodo; cátodo → GND
```

Use somente `3,3 V` no potenciômetro. Verifique o cursor com o multímetro antes de conectá-lo ao GPIO34.

> A leitura pode saturar perto das extremidades do potenciômetro. O objetivo desta prática é observar o processamento do código bruto, não usar o ESP32 como voltímetro de precisão.

### Código

```cpp
const int PINO_SENSOR = 34;
const int PINO_LED = 23;
const unsigned long INTERVALO_LEITURA_MS = 50;
const unsigned long INTERVALO_RELATORIO_MS = 250;
const float ALPHA = 0.15f;
const float LIMIAR_LIGAR = 2800.0f;
const float LIMIAR_DESLIGAR = 2400.0f;

unsigned long ultimaLeitura = 0;
unsigned long ultimoRelatorio = 0;
float valorFiltrado = 0.0f;
int valorBruto = 0;
bool filtroInicializado = false;
bool ledLigado = false;

void setup() {
  Serial.begin(115200);
  pinMode(PINO_LED, OUTPUT);
  digitalWrite(PINO_LED, LOW);
  analogReadResolution(12);
  analogSetPinAttenuation(PINO_SENSOR, ADC_11db);
}

void loop() {
  unsigned long agora = millis();

  if (agora - ultimaLeitura >= INTERVALO_LEITURA_MS) {
    ultimaLeitura = agora;
    valorBruto = analogRead(PINO_SENSOR);

    if (!filtroInicializado) {
      valorFiltrado = valorBruto;
      filtroInicializado = true;
    } else {
      valorFiltrado =
          ALPHA * valorBruto + (1.0f - ALPHA) * valorFiltrado;
    }

    if (!ledLigado && valorFiltrado >= LIMIAR_LIGAR) {
      ledLigado = true;
    } else if (ledLigado && valorFiltrado <= LIMIAR_DESLIGAR) {
      ledLigado = false;
    }

    digitalWrite(PINO_LED, ledLigado ? HIGH : LOW);
  }

  if (agora - ultimoRelatorio >= INTERVALO_RELATORIO_MS) {
    ultimoRelatorio = agora;
    Serial.print("bruto=");
    Serial.print(valorBruto);
    Serial.print("; filtrado=");
    Serial.print(valorFiltrado, 1);
    Serial.print("; led=");
    Serial.println(ledLigado ? "ligado" : "desligado");
  }
}
```

### O que observar

1. compare leitura bruta e filtrada no Plotter Serial;
2. observe o atraso introduzido pelo filtro;
3. mantenha a entrada entre os limiares e confirme que o LED preserva o estado;
4. altere `ALPHA` e compare ruído e tempo de resposta;
5. teste `analogReadMilliVolts()` separadamente e compare com o multímetro.

## 9. Videoaulas e trilhas sugeridas

### Visão geral

- [Tudo sobre sensores: analógicos e digitais](https://www.youtube.com/watch?v=dd-EdNesVyQ) — classificações, sinais e aplicações.

### Curso ESP32 básico — IoT

A [playlist Curso ESP32 Básico — IoT](https://www.youtube.com/playlist?list=PLLHo4pVI8I_o_tIRC-twzYImDhwHgpYbw) contém:

1. [Primeiros passos com ESP32](https://www.youtube.com/watch?v=5arcdEzRDA8);
2. [O que é IoT?](https://www.youtube.com/watch?v=OY-GmPdd59U);
3. [LED com botão](https://www.youtube.com/watch?v=Psgn6KGQmMo) — entrada digital;
4. [Buzzer com ESP32](https://www.youtube.com/watch?v=bUDSkKMUMd0) — saída;
5. [NTC 10 kΩ com ESP32](https://www.youtube.com/watch?v=Jb_QwD0a0F0) — sensor resistivo e ADC;
6. [HC-SR04 com ESP32](https://www.youtube.com/watch?v=ztLUq0ckbl8) — tempo de pulso;
7. [Sensor de som KY-037](https://www.youtube.com/watch?v=r51-9N0TLzo) — saídas analógica e digital;
8. [MPU-6050 com ESP32](https://www.youtube.com/watch?v=VEU_fLbrySo) — sensor digital por I2C.

Nesta semana, priorize as aulas 5 a 8 e compare as quatro formas de aquisição.

### Tipos de sensores industriais

A [playlist Tipos de Sensores](https://www.youtube.com/playlist?list=PLE46_r_BGfFiU-9DNcMSoN_ewiuNISiEw) amplia o repertório com sensores ultrassônicos, indutivos, ópticos e capacitivos. Explore princípio físico, alvo detectável, alcance, ambiente, tipo de saída, faceamento, supressão de fundo, modos *light-on/dark-on*, conectorização e grau de proteção IP.

Esses vídeos apresentam contexto industrial. Não conecte um sensor industrial diretamente ao ESP32 sem verificar alimentação, tipo de saída e condicionamento de nível.

## 10. Erros frequentes

- alimentar módulo em `5 V` e ligar sua saída ao ESP32 sem conferir o nível;
- confundir protocolo digital com saída binária;
- usar ADC2 e Wi-Fi simultaneamente no ESP32 clássico;
- considerar todos os ESP32 iguais;
- deixar entrada analógica flutuante;
- usar `map()` sem validar e limitar a faixa;
- usar média para esconder uma ligação ruim;
- escolher limiar único e provocar oscilações;
- usar `delay()` e interromper outras tarefas;
- converter código bruto em grandeza física sem calibração.

## Checklist de aprendizagem

- [ ] Classifico a saída de um sensor pela documentação.
- [ ] Explico amostragem, quantização e resolução.
- [ ] Sei por que 12 bits não garantem exatidão de 12 bits.
- [ ] Confiro pino, ADC, atenuação e tensão.
- [ ] Calculo um divisor de tensão.
- [ ] Sei quando usar média, mediana ou filtro exponencial.
- [ ] Implemento histerese.
- [ ] Calibro dois pontos e reconheço seus limites.
- [ ] Organizo a aquisição com `millis()`.

## Dúvidas para levar à monitoria

- Qual sensor e qual placa você está usando exatamente?
- A saída é tensão, corrente, pulso, estado lógico ou protocolo?
- Qual faixa apareceu no multímetro e no ADC?
- O ruído é elétrico, mecânico, ambiental ou de software?
- A aplicação precisa de exatidão, repetibilidade ou apenas detectar tendência?
