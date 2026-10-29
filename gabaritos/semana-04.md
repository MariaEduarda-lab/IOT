# Gabarito — Semana 04

## Sensores e processamento de entradas analógicas e digitais

> Consulte depois da primeira tentativa. O desafio final é autoestudo: a solução completa faz parte do conteúdo e não representa uma entrega a ser corrigida.

## Resumo das questões objetivas

| Questão | Resposta | Tema |
| :---: | :---: | --- |
| 1 | B | Formas de aquisição |
| 2 | C | Exatidão e precisão |
| 3 | D | Resolução do ADC |
| 4 | C | Divisor de tensão |
| 5 | D | ADC do ESP32 |
| 6 | C | Amostragem e aliasing |
| 7 | C | Filtragem e histerese |
| 8 | B | Normalização e limites |

---

## Questão 1

**Resposta: B.**

O LDR altera sua resistência e, em um divisor, produz tensão variável para o ADC. O fim de curso informa um estado lógico. O MPU-6050 mede grandezas contínuas internamente, mas entrega amostras numéricas por uma interface digital I2C. A natureza da grandeza não determina sozinha a interface elétrica.

## Questão 2

**Resposta: C.**

As leituras estão muito próximas entre si, portanto possuem boa precisão ou repetibilidade. Entretanto, ficam cerca de `2 °C` acima da referência, indicando baixa exatidão e provável erro sistemático ou offset.

## Questão 3

**Resposta: D.**

```text
2^12 = 4096
```

Como a contagem começa em zero, os códigos vão de `0` a `4095`. Quantidade de códigos e maior código não são o mesmo número.

## Questão 4

**Resposta: C.**

```text
Vout = 3,3 × R2 / (R1 + R2)
Vout = 3,3 × 10 / (10 + 10)
Vout = 1,65 V
```

O resultado ideal vale metade da alimentação porque os resistores são iguais.

## Questão 5

**Resposta: D.**

I, II e IV estão corretas. No ESP32 clássico, ADC2 conflita com o uso do Wi-Fi; GPIO34 pertence ao ADC1. Ele é somente entrada e não possui resistores internos de pull configuráveis. `analogRead()` fornece o código bruto, enquanto `analogReadMilliVolts()` usa calibração para fornecer milivolts.

A afirmação III é falsa: a faixa numérica do ADC não autoriza tensão de `5 V`. O limite elétrico deve ser obtido na documentação e a entrada deve permanecer dentro da faixa configurada.

## Questão 6

**Resposta: C.**

O limite teórico de Nyquist é maior que `2 × 400 = 800 Hz`. Na prática, deve haver margem e filtragem analógica antes do ADC. Uma média aplicada depois da amostragem não remove informação que já sofreu aliasing.

## Questão 7

**Resposta: C.**

I, II e III estão corretas. A mediana é resistente a valores extremos isolados; `alpha` menor atribui menos peso à nova amostra; a histerese separa os limiares de mudança de estado.

IV é falsa. Software não protege um GPIO contra sobretensão, curto-circuito, ausência de terra comum ou condicionamento incorreto.

## Questão 8

**Resposta: B.**

`map()` realiza extrapolação:

```text
(3500 - 1000) × 100 / (3000 - 1000) = 125
```

No segundo cálculo, `constrain()` transforma `3500` em `3000`, resultando em `100`. `map()` utiliza aritmética inteira e não limita automaticamente o resultado.

---

## Questão 9 — Resposta esperada

### Análise do circuito

O LDR está no ramo superior. Com mais luz, sua resistência diminui. Pela equação do divisor:

```text
Vout = 3,3 × Rfixo / (Rldr + Rfixo)
```

Quando `Rldr` diminui, o denominador diminui e `Vout` cresce. Por isso a leitura bruta tende a crescer com a iluminação.

GPIO34 é adequado como entrada ADC1 e não conflita com Wi-Fi no ESP32 clássico. Como é somente entrada e não possui pull interno, o divisor deve garantir o nível elétrico. GPIO23 é um GPIO de saída apropriado como ponto de partida para acionar o LED por resistor, após conferência da placa.

### Implementação possível

```cpp
const int PINO_LDR = 34;
const int PINO_LED = 23;

const int BRUTO_ESCURO = 700;
const int BRUTO_CLARO = 3300;
const float ALPHA = 0.20f;

const unsigned long INTERVALO_AMOSTRA_MS = 50;
const unsigned long INTERVALO_RELATORIO_MS = 500;

unsigned long ultimaAmostra = 0;
unsigned long ultimoRelatorio = 0;

int bruto = 0;
float filtrado = 0.0f;
int percentual = 0;
bool filtroInicializado = false;
bool ledLigado = false;

int converterPercentual(float valor) {
  float calculado =
      (valor - BRUTO_ESCURO) * 100.0f /
      (BRUTO_CLARO - BRUTO_ESCURO);

  calculado = constrain(calculado, 0.0f, 100.0f);
  return (int)(calculado + 0.5f);
}

void atualizarLed() {
  if (!ledLigado && percentual <= 30) {
    ledLigado = true;
  } else if (ledLigado && percentual >= 40) {
    ledLigado = false;
  }

  digitalWrite(PINO_LED, ledLigado ? HIGH : LOW);
}

void setup() {
  Serial.begin(115200);
  pinMode(PINO_LED, OUTPUT);
  digitalWrite(PINO_LED, LOW);

  analogReadResolution(12);
  analogSetPinAttenuation(PINO_LDR, ADC_11db);
}

void loop() {
  unsigned long agora = millis();

  if (agora - ultimaAmostra >= INTERVALO_AMOSTRA_MS) {
    ultimaAmostra = agora;
    bruto = analogRead(PINO_LDR);

    if (!filtroInicializado) {
      filtrado = bruto;
      filtroInicializado = true;
    } else {
      filtrado = ALPHA * bruto + (1.0f - ALPHA) * filtrado;
    }

    percentual = converterPercentual(filtrado);
    atualizarLed();
  }

  if (agora - ultimoRelatorio >= INTERVALO_RELATORIO_MS) {
    ultimoRelatorio = agora;
    Serial.print("bruto=");
    Serial.print(bruto);
    Serial.print("; filtrado=");
    Serial.print(filtrado, 1);
    Serial.print("; luz_pct=");
    Serial.print(percentual);
    Serial.print("; led=");
    Serial.println(ledLigado ? "ligado" : "desligado");
  }
}
```

### Papel de cada etapa

- **calibração:** associa os códigos observados em duas condições reais a `0%` e `100%`; esses pontos precisam ser medidos no sistema final;
- **filtro exponencial:** reduz variações rápidas, com custo de atraso;
- **limitação:** impede porcentagens menores que zero ou maiores que cem;
- **histerese:** evita o piscar do LED quando a leitura oscila perto do limiar;
- **temporização não bloqueante:** deixa o processador livre para futuras tarefas.

### Fontes de erro possíveis

- tolerância do LDR e do resistor;
- luz ambiente não uniforme;
- ruído da alimentação e da protoboard;
- não linearidade e variação do ADC;
- faixa ou atenuação inadequada;
- aquecimento e deriva dos componentes;
- posicionamento físico do sensor;
- calibração feita em condições diferentes das de uso.

### Verificações elétricas

Espera-se citar ao menos duas, por exemplo:

- conferir `GND` comum;
- medir o nó com multímetro e garantir que não excede a faixa permitida;
- confirmar valores e ligações do divisor;
- verificar resistor em série do LED;
- conferir GPIO no guia da placa;
- alimentar o divisor com `3,3 V`, não `5 V`.

### Critérios de correção — 4,0 pontos

| Critério | Valor |
| --- | ---: |
| Explicação do divisor e da escolha dos GPIOs | `0,6` |
| Aquisição não bloqueante a cada 50 ms | `0,5` |
| Filtro exponencial corretamente inicializado | `0,5` |
| Calibração, normalização e limitação | `0,7` |
| Histerese e acionamento do LED | `0,6` |
| Relatório serial a cada 500 ms | `0,3` |
| Explicação das etapas | `0,4` |
| Erros e verificações elétricas | `0,4` |

---

## Questão 10 — Solução guiada

Este desafio não possui nota. A implementação abaixo é uma referência para comparação e experimentação.

### Mediana do conjunto de teste

Ordenando as nove leituras:

```text
700, 700, 705, 705, 710, 710, 715, 720, 4095
```

A mediana é o quinto elemento: `710`. O pico `4095` quase não afeta o resultado, enquanto influenciaria a média.

### Implementação completa

```cpp
const int PINO_LDR = 34;
const int PINO_LED = 23;

const int BRUTO_ESCURO = 700;
const int BRUTO_CLARO = 3300;

const size_t TAMANHO_JANELA = 9;
const unsigned long INTERVALO_AMOSTRA_MS = 20;
const unsigned long INTERVALO_RELATORIO_MS = 500;

int janela[TAMANHO_JANELA] = {0};
size_t proximaPosicao = 0;
size_t quantidadeAmostras = 0;

unsigned long ultimaAmostra = 0;
unsigned long ultimoRelatorio = 0;

int valorMediano = 0;
int percentualLuz = 0;
bool ledLigado = false;
bool leituraValida = false;

void adicionarAmostra(int valor) {
  janela[proximaPosicao] = valor;
  proximaPosicao = (proximaPosicao + 1) % TAMANHO_JANELA;

  if (quantidadeAmostras < TAMANHO_JANELA) {
    quantidadeAmostras++;
  }
}

bool calcularMediana(
    const int* valores,
    size_t quantidade,
    int* resultado) {
  if (valores == nullptr ||
      resultado == nullptr ||
      quantidade == 0 ||
      quantidade > TAMANHO_JANELA) {
    return false;
  }

  int copia[TAMANHO_JANELA];

  for (size_t i = 0; i < quantidade; i++) {
    copia[i] = valores[i];
  }

  // Ordenação por inserção: suficiente para uma janela pequena.
  for (size_t i = 1; i < quantidade; i++) {
    int atual = copia[i];
    size_t j = i;

    while (j > 0 && copia[j - 1] > atual) {
      copia[j] = copia[j - 1];
      j--;
    }

    copia[j] = atual;
  }

  *resultado = copia[quantidade / 2];
  return true;
}

bool normalizarLuminosidade(int bruto, int* percentual) {
  if (percentual == nullptr || BRUTO_CLARO <= BRUTO_ESCURO) {
    return false;
  }

  long calculado =
      (long)(bruto - BRUTO_ESCURO) * 100L /
      (BRUTO_CLARO - BRUTO_ESCURO);

  *percentual = constrain((int)calculado, 0, 100);
  return true;
}

void atualizarLuminaria(int luzPercentual) {
  if (!ledLigado && luzPercentual <= 30) {
    ledLigado = true;
  } else if (ledLigado && luzPercentual >= 40) {
    ledLigado = false;
  }

  digitalWrite(PINO_LED, ledLigado ? HIGH : LOW);
}

void adquirir() {
  adicionarAmostra(analogRead(PINO_LDR));

  if (quantidadeAmostras < TAMANHO_JANELA) {
    leituraValida = false;
    return;
  }

  leituraValida =
      calcularMediana(
          janela,
          quantidadeAmostras,
          &valorMediano) &&
      normalizarLuminosidade(
          valorMediano,
          &percentualLuz);

  if (leituraValida) {
    atualizarLuminaria(percentualLuz);
  } else {
    ledLigado = false;
    digitalWrite(PINO_LED, LOW);
  }
}

void relatar() {
  if (!leituraValida) {
    Serial.println("leitura indisponivel ou calibracao invalida");
    return;
  }

  Serial.print("mediana=");
  Serial.print(valorMediano);
  Serial.print("; luz_pct=");
  Serial.print(percentualLuz);
  Serial.print("; luminaria=");
  Serial.println(ledLigado ? "ligada" : "desligada");
}

void setup() {
  Serial.begin(115200);
  pinMode(PINO_LED, OUTPUT);
  digitalWrite(PINO_LED, LOW);

  analogReadResolution(12);
  analogSetPinAttenuation(PINO_LDR, ADC_11db);
}

void loop() {
  unsigned long agora = millis();

  if (agora - ultimaAmostra >= INTERVALO_AMOSTRA_MS) {
    ultimaAmostra = agora;
    adquirir();
  }

  if (agora - ultimoRelatorio >= INTERVALO_RELATORIO_MS) {
    ultimoRelatorio = agora;
    relatar();
  }
}
```

### Como a solução atende aos requisitos

#### Vetor circular

`proximaPosicao` percorre de `0` a `8` e retorna a zero pelo operador `%`. Depois de cheia, a janela substitui sempre a amostra mais antiga, sem movimentar todo o vetor.

#### Mediana sem alteração dos dados

`calcularMediana()` recebe um ponteiro `const`, copia os valores para um vetor local e ordena apenas a cópia. A janela original permanece intacta.

#### Calibração segura

`normalizarLuminosidade()` valida o ponteiro de saída e exige `BRUTO_CLARO > BRUTO_ESCURO`. Assim, evita divisão por zero ou uma orientação de calibração incompatível com o circuito proposto. O uso de `long` reduz o risco de estouro na multiplicação.

#### Histerese como máquina de dois estados

```text
luminária desligada + luz <= 30% → liga
luminária ligada    + luz >= 40% → desliga
qualquer estado     + 30% < luz < 40% → mantém
```

O estado anterior faz parte da decisão. Isso impede alternância rápida perto de um único limiar.

#### Inicialização da janela

O cálculo só começa após nove amostras reais. Os zeros usados para inicializar o vetor não entram na primeira mediana válida.

### Experimentos sugeridos

1. troque temporariamente a mediana pela média e produza um pico sobre o LDR;
2. altere a janela para cinco amostras e compare o tempo de resposta;
3. registre também `analogReadMilliVolts()` e compare com multímetro;
4. refaça a calibração no ambiente real;
5. acrescente detecção de sensor desconectado baseada no circuito e em limites medidos;
6. preserve a aquisição e adicione, em outra tarefa futura, envio por MQTT.
