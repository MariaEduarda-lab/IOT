# Lista de exercícios — Semana 04

## Sensores e processamento de entradas analógicas e digitais

Esta lista contém oito questões objetivas autorais inspiradas no formato do ENADE, uma questão discursiva e um desafio guiado de programação.

## Orientações

- Assinale uma alternativa nas questões objetivas e justifique sua escolha.
- Considere o ESP32 clássico em placa compatível com ESP32-WROOM quando não houver outra indicação.
- Não aplique `5 V` diretamente aos GPIOs do ESP32.
- Use a documentação do sensor, do chip e da placa antes de montar.
- Não consulte o gabarito antes da primeira tentativa.

## Avaliação sugerida

| Questões | Valor |
| --- | ---: |
| 1 a 8 — objetivas | `0,75` ponto cada |
| 9 — discursiva | `4,0` pontos |
| **Total avaliado** | **`10,0` pontos** |
| 10 — desafio guiado | Autoestudo, sem nota e sem submissão |

> O desafio não precisa ser entregue e não será corrigido. Seu funcionamento está explicado no enunciado para permitir uma tentativa autônoma; depois, compare seu programa com a solução completa do gabarito.

---

## Questão 1 — Formas de aquisição

**Nível: fácil**

Uma equipe utilizará três dispositivos: um LDR em divisor resistivo, um fim de curso e um acelerômetro MPU-6050.

Qual classificação está correta?

A. Os três devem ser lidos exclusivamente por `analogRead()`.

B. O LDR gera informação analógica para o ADC; o fim de curso produz estado binário; o MPU-6050 transmite dados digitais por I2C.

C. O LDR é digital porque possui apenas dois terminais.

D. O MPU-6050 é analógico porque mede uma grandeza contínua.

E. O fim de curso exige ADC de 12 bits para indicar aberto ou fechado.

---

## Questão 2 — Exatidão e precisão

**Nível: fácil**

Um sensor mede repetidamente uma referência de `25,0 °C` e apresenta `27,1`, `27,0`, `27,1` e `27,0 °C`.

Esses resultados são:

A. exatos e imprecisos;

B. exatos e precisos;

C. precisos, mas pouco exatos, pois estão agrupados e afastados da referência;

D. nem precisos nem comparáveis, pois temperatura é analógica;

E. prova de que o ADC não possui resolução.

---

## Questão 3 — Resolução do ADC

**Nível: fácil**

Um ADC ideal configurado com 12 bits disponibiliza:

A. 12 códigos, numerados de 0 a 11.

B. 1024 códigos, numerados de 0 a 1023.

C. 2048 códigos, numerados de 1 a 2048.

D. 4096 códigos, numerados de 0 a 4095.

E. 4095 códigos, numerados de 1 a 4095.

---

## Questão 4 — Divisor de tensão

**Nível: intermediário**

Considere um divisor ligado a `3,3 V`, com `R1 = 10 kΩ` entre a alimentação e o nó de leitura e `R2 = 10 kΩ` entre o nó e o `GND`.

Desprezando tolerâncias e carga do ADC, a tensão no nó será:

A. `0 V`.

B. `1,10 V`.

C. `1,65 V`.

D. `3,30 V`.

E. `6,60 V`.

---

## Questão 5 — ADC do ESP32 e Wi-Fi

**Nível: intermediário**

Um projeto com ESP32 clássico lerá um potenciômetro e transmitirá dados por Wi-Fi. A equipe avalia as afirmações:

I. Um canal de ADC1, como o GPIO34, evita o conflito conhecido entre ADC2 e Wi-Fi.

II. O GPIO34 é somente entrada e não possui pull-up ou pull-down interno configurável.

III. Como `analogRead()` retorna até 4095, é seguro aplicar `5 V` ao GPIO34.

IV. `analogReadMilliVolts()` fornece uma conversão calibrada, enquanto `analogRead()` fornece o valor bruto.

É correto apenas o que se afirma em:

A. I e II.

B. I e III.

C. II e IV.

D. I, II e IV.

E. I, II, III e IV.

---

## Questão 6 — Amostragem e aliasing

**Nível: intermediário**

Uma equipe deseja digitalizar uma senoide cuja maior frequência relevante é `400 Hz`.

Qual proposta é tecnicamente mais adequada?

A. Amostrar a `200 Hz`, pois uma amostra representa dois ciclos.

B. Amostrar exatamente a `400 Hz`, sem filtro, garante reconstrução perfeita.

C. Usar frequência superior a `800 Hz`, com margem e filtro anti-aliasing adequado.

D. Usar média móvel elimina qualquer aliasing depois da conversão.

E. A frequência de amostragem não interfere em sensores analógicos.

---

## Questão 7 — Filtragem e histerese

**Nível: difícil**

Considere as afirmações:

I. A mediana de uma janela pode rejeitar um pico isolado melhor que a média.

II. Reduzir `alpha` em um filtro exponencial tende a suavizar mais e responder mais lentamente.

III. Histerese usa limiares diferentes para ligar e desligar, reduzindo oscilações próximas ao ponto de decisão.

IV. Um filtro digital corrige automaticamente sobretensão e ligação elétrica incorreta.

É correto apenas o que se afirma em:

A. I e II.

B. II e III.

C. I, II e III.

D. I, III e IV.

E. I, II, III e IV.

---

## Questão 8 — `map()` e limitação

**Nível: difícil**

Analise:

```cpp
int bruto = 3500;
int percentualSemLimite = map(bruto, 1000, 3000, 0, 100);
int percentualLimitado = map(
    constrain(bruto, 1000, 3000),
    1000,
    3000,
    0,
    100
);
```

Quais valores serão obtidos?

A. `100` e `100`, porque `map()` sempre limita a saída.

B. `125` e `100`, porque `map()` extrapola e `constrain()` limita a entrada.

C. `125` e `125`, porque `constrain()` não aceita inteiros.

D. `75` e `100`, porque o ADC é invertido.

E. `3500` e `3000`, porque `map()` não altera valores.

---

## Questão 9 — Discursiva: estação de luminosidade

**Nível: difícil**

Projete uma estação com ESP32 clássico, LDR, resistor e LED. Considere a montagem:

```text
3,3 V → LDR → nó de leitura → resistor de 10 kΩ → GND
nó de leitura → GPIO34
GPIO23 → resistor de 220 Ω a 330 Ω → LED → GND
```

Durante a calibração, foram observados `700` no escuro e `3300` sob a iluminação de referência.

Sua resposta deve:

1. explicar por que a leitura cresce com a iluminação nessa montagem;
2. justificar GPIO34 e GPIO23;
3. apresentar código não bloqueante que amostre a cada `50 ms`;
4. aplicar filtro exponencial com `alpha = 0,20`;
5. converter a leitura filtrada em `0 a 100%`, limitando valores externos à faixa;
6. ligar o LED quando a iluminação for menor ou igual a `30%` e desligá-lo quando for maior ou igual a `40%`;
7. enviar leitura bruta, filtrada, percentual e estado a cada `500 ms`;
8. explicar a função da calibração, do filtro e da histerese;
9. citar ao menos três fontes de erro e duas verificações elétricas.

---

## Questão 10 — Desafio guiado: luminária autônoma robusta

**Modalidade: autoestudo, sem nota e sem submissão**

### Como o sistema funciona

Uma luminária autônoma mede luminosidade pelo mesmo divisor da Questão 9. O programa deve ser resistente a picos isolados e continuar disponível para futuras tarefas de comunicação.

O sistema funciona assim:

1. o ADC é lido a cada `20 ms`, sem `delay()`;
2. as nove leituras mais recentes formam uma janela;
3. a mediana da janela rejeita picos isolados;
4. os pontos `BRUTO_ESCURO = 700` e `BRUTO_CLARO = 3300` convertem a mediana em porcentagem;
5. valores abaixo ou acima da faixa são limitados a `0%` e `100%`;
6. a luminária liga em `30%` ou menos e desliga em `40%` ou mais;
7. entre `30%` e `40%`, mantém o estado anterior;
8. um relatório é enviado a cada `500 ms` com mediana, porcentagem e estado;
9. calibração inválida deve ser detectada sem divisão por zero.

### Requisitos

- ESP32 clássico: LDR no GPIO34 e LED no GPIO23;
- resolução de 12 bits e atenuação configurada;
- vetor circular de nove amostras;
- função que calcule a mediana sem alterar o vetor original;
- função de normalização que devolva erro se os pontos forem inválidos;
- temporização com `millis()`;
- nenhuma alocação dinâmica e nenhum `delay()`.

Antes de abrir o gabarito, desenhe os estados da histerese, teste a mediana com `{700, 710, 705, 4095, 715, 720, 700, 710, 705}` e escreva sua primeira versão. A solução completa está disponível no gabarito para estudo.
