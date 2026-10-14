# Gabarito — Semana 01

## LED interno, fundamentos de C/C++, Arduino e eletrônica básica

> Use este documento somente depois de concluir uma primeira tentativa. Nas questões de código, são aceitas soluções diferentes que atendam integralmente aos requisitos.

## Resumo das questões objetivas

| Questão | Resposta | Tema |
| :---: | :---: | --- |
| 1 | B | Sensores e atuadores |
| 2 | C | Sistemas embarcados e IoT |
| 3 | A | Execução do Blink |
| 4 | B | Tipos e operadores |
| 5 | A | Protoboard |
| 6 | B | Multímetro |
| 7 | A | LED e resistor |
| 8 | D | Controle de fluxo e temporização |

---

## Questão 1

**Resposta: B.**

O componente que mede a luminosidade é o **sensor**. A placa Arduino contém o **microcontrolador**, que processa o valor e aplica a regra de controle. O componente que produz luz é o **atuador**.

## Questão 2

**Resposta: C.**

Uma calculadora e um controlador de máquina de lavar podem ser sistemas embarcados mesmo sem conexão à internet. O sensor conectado possui processamento dedicado e comunicação em rede, podendo ser classificado como sistema embarcado e como parte de uma solução IoT. Conectividade não é um requisito para todo sistema embarcado, mas é uma característica essencial do cenário IoT descrito.

## Questão 3

**Resposta: A.**

`delay()` recebe um valor em milissegundos. Assim, `500 ms` correspondem a meio segundo. O LED fica meio segundo aceso e meio segundo apagado, totalizando aproximadamente `1000 ms`, ou `1 s`, por ciclo.

## Questão 4

**Resposta: B.**

- `bool` representa adequadamente dois estados lógicos;
- `float` permite representar temperatura com casas decimais;
- `int` armazena uma contagem inteira;
- `char` armazena um caractere;
- `==` compara dois valores, enquanto `=` realiza uma atribuição.

## Questão 5

**Resposta: A.**

A afirmação I descreve a organização comum das linhas de uma protoboard. O canal central separa eletricamente os dois lados, portanto II é falsa. Alguns modelos possuem interrupções nos barramentos de alimentação, logo não se pode assumir que eles sempre sejam contínuos e III também é falsa.

## Questão 6

**Resposta: B.**

As afirmações I e IV estão corretas.

- A tensão é medida em paralelo e, para verificar uma alimentação, o circuito deve estar energizado.
- Continuidade e resistência devem ser verificadas com o circuito desligado; o multímetro utiliza sua própria fonte interna.
- A corrente deve ser medida em série. Ligar um multímetro configurado para corrente diretamente em paralelo com a fonte pode provocar um curto-circuito.
- Deixar a ponta no terminal de corrente aumenta o risco de um erro perigoso na medição seguinte.

## Questão 7

**Resposta: A.**

As duas asserções são verdadeiras, e a segunda explica a primeira. O resistor em série limita a corrente. Sem essa limitação, a corrente pode ultrapassar os valores seguros do LED e do pino do microcontrolador.

## Questão 8

**Resposta: D.**

O `for` executa três iterações, correspondentes às três piscadas. Cada iteração mantém o LED `200 ms` aceso e `200 ms` apagado, totalizando `400 ms`. As três piscadas consomem aproximadamente `1200 ms`; somadas à pausa de `2000 ms`, produzem uma sequência de `3200 ms`, ou `3,2 s`.

A variável `i` existe apenas no escopo do `for`. A cada nova execução de `loop()`, ela é criada novamente e começa em zero. Portanto, a afirmação IV é falsa.

---

## Questão 9 — Resposta esperada

### Exemplo de código

```cpp
const int TEMPO_ACESO = 250;
const int TEMPO_APAGADO = 750;

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(TEMPO_ACESO);

  digitalWrite(LED_BUILTIN, LOW);
  delay(TEMPO_APAGADO);
}
```

### Elementos esperados na explicação

- `setup()` é executada uma vez após a placa iniciar ou reiniciar. Ela configura o pino do LED interno como saída.
- `loop()` é repetida continuamente enquanto a placa estiver em funcionamento.
- `pinMode()` define como o pino será utilizado.
- `digitalWrite()` coloca a saída em nível `HIGH` ou `LOW`, acendendo ou apagando o LED da placa de referência.
- `delay()` suspende a execução pelo número informado de milissegundos.
- Um ciclo dura `250 ms + 750 ms = 1000 ms`, isto é, um segundo.
- Em um minuto, o programa completa teoricamente `60` ciclos.
- Durante `delay()`, o microcontrolador não avança para as próximas instruções do programa. Isso prejudica sistemas que precisam monitorar sensores, botões ou comunicação enquanto temporizam outra ação.

### Critérios de correção — 2,0 pontos

| Critério | Valor |
| --- | ---: |
| Programa completo e sintaticamente coerente | `0,5` |
| Uso de `LED_BUILTIN` e das duas constantes | `0,3` |
| Explicação de `setup()` e `loop()` | `0,3` |
| Explicação das três funções utilizadas | `0,3` |
| Cálculos de ciclo e ciclos por minuto | `0,3` |
| Limitação de `delay()` | `0,3` |

---

## Questão 10 — Solução de referência

### Ligação

O botão deve ser conectado entre o pino digital `2` e o `GND`. Como o pino é configurado com `INPUT_PULLUP`, o resistor interno mantém a leitura em `HIGH` quando o botão está solto. Ao pressioná-lo, o pino é conectado ao `GND` e passa a ser lido como `LOW`.

### Exemplo de código

```cpp
const int PINO_BOTAO = 2;
const int TEMPO_DEBOUNCE = 50;
const int INTERVALO_LENTO = 1000;
const int INTERVALO_RAPIDO = 200;

int modo = 0;
int ultimaLeitura = HIGH;
int estadoEstavelBotao = HIGH;
bool estadoLed = false;

unsigned long instanteUltimaMudanca = 0;
unsigned long instanteAnteriorLed = 0;

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
  pinMode(PINO_BOTAO, INPUT_PULLUP);
  digitalWrite(LED_BUILTIN, LOW);
}

void loop() {
  unsigned long agora = millis();
  int leituraAtual = digitalRead(PINO_BOTAO);

  // Uma mudança reinicia a janela de estabilização do botão.
  if (leituraAtual != ultimaLeitura) {
    instanteUltimaMudanca = agora;
  }

  // A leitura só é aceita depois de permanecer estável.
  if (agora - instanteUltimaMudanca >= TEMPO_DEBOUNCE) {
    if (leituraAtual != estadoEstavelBotao) {
      estadoEstavelBotao = leituraAtual;

      // INPUT_PULLUP: o pressionamento produz nível LOW.
      if (estadoEstavelBotao == LOW) {
        modo = (modo + 1) % 3;
        estadoLed = false;
        digitalWrite(LED_BUILTIN, LOW);
        instanteAnteriorLed = agora;
      }
    }
  }

  ultimaLeitura = leituraAtual;

  if (modo == 0) {
    estadoLed = false;
    digitalWrite(LED_BUILTIN, LOW);
    return;
  }

  unsigned long intervalo =
      (modo == 1) ? INTERVALO_LENTO : INTERVALO_RAPIDO;

  if (agora - instanteAnteriorLed >= intervalo) {
    instanteAnteriorLed = agora;
    estadoLed = !estadoLed;
    digitalWrite(LED_BUILTIN, estadoLed ? HIGH : LOW);
  }
}
```

### Como a solução funciona

1. O operador `% 3` mantém `modo` no intervalo de zero a dois.
2. O programa somente aceita uma alteração do botão depois que a leitura permanece estável por `50 ms`.
3. A mudança de modo ocorre na transição para `LOW`. Manter o botão pressionado não gera novas mudanças.
4. `millis()` informa o tempo desde a inicialização da placa. Comparar esse valor com o instante anterior permite temporizar sem interromper o restante do programa.
5. Como não há `delay()` no laço principal, o botão continua sendo lido enquanto o LED pisca.
6. O tipo `unsigned long` é adequado para armazenar os valores retornados por `millis()`.

### Observações para correção

Soluções diferentes são válidas quando:

- implementam os três modos corretamente;
- registram apenas um evento por pressionamento;
- tratam o ruído mecânico do botão;
- não usam `delay()` no laço principal;
- continuam responsivas durante a temporização;
- usam `INPUT_PULLUP` de forma coerente com a montagem.

### Critérios de correção — 4,0 pontos

| Critério | Valor |
| --- | ---: |
| Circuito e uso correto de `INPUT_PULLUP` | `0,5` |
| Transição cíclica entre os três modos | `0,8` |
| Detecção de um evento por pressionamento | `0,6` |
| Debounce funcional | `0,6` |
| Temporização não bloqueante com `millis()` | `0,8` |
| Clareza, constantes, organização e comentários | `0,4` |
| Evidências e explicação da solução | `0,3` |

---

[Voltar à lista de exercícios](../exercicios/semana-01.md) · [Consultar o material da Semana 01](../docs/semana-01.md)
