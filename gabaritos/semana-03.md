# Gabarito — Semana 03

## Arquitetura de microcontroladores, pinout e ponteiros

> Consulte este documento depois de concluir uma primeira tentativa. O desafio final é uma atividade de autoestudo: a solução completa faz parte do conteúdo e não representa uma entrega a ser corrigida.

## Resumo das questões objetivas

| Questão | Resposta | Tema |
| :---: | :---: | --- |
| 1 | A | Arquitetura de microcontroladores |
| 2 | B | Chip, módulo e placa |
| 3 | B | Escolha de GPIOs |
| 4 | B | Restrições do ESP32 |
| 5 | A | Debounce |
| 6 | B | Ponteiros |
| 7 | D | Vetores e aritmética de ponteiros |
| 8 | D | Validade e segurança da memória |

---

## Questão 1

**Resposta: A.**

Um microcontrolador reúne CPU, memórias e periféricos no mesmo circuito integrado. GPIO, temporizadores, conversores e interfaces podem estar dentro do chip. Nem todo microcontrolador possui rádio, e cada periférico precisa ser configurado e conectado ao restante do sistema.

## Questão 2

**Resposta: B.**

O chip define a arquitetura e os periféricos internos. O módulo combina o chip com itens como flash, cristal e antena. A placa de desenvolvimento acrescenta elementos de suporte, conectores e um roteamento específico. Para selecionar pinos corretamente, os três níveis podem ser relevantes.

## Questão 3

**Resposta: B.**

GPIO23 e GPIO27 costumam ser flexíveis em placas com ESP32-WROOM. O LED precisa de resistor em série, e o botão ligado ao `GND` pode usar `INPUT_PULLUP` no GPIO27.

As demais alternativas usam pinos somente de entrada como saída, pinos da flash, ligações perigosas ou pinos de *strapping* sem análise do estado durante o boot.

## Questão 4

**Resposta: B.**

As afirmações I e II estão corretas. GPIOs `34` a `39` são somente entrada no ESP32 clássico e não possuem pull interno configurável por software. GPIOs `6` a `11` normalmente atendem à flash.

Pinos de *strapping* são amostrados durante a inicialização, antes de `setup()`. Além disso, a alimentação por USB não torna os GPIOs tolerantes a `5 V`; a documentação oficial informa tolerância de até `3,6 V`.

## Questão 5

**Resposta: A.**

As duas asserções são verdadeiras, e a segunda explica a primeira. O contato mecânico pode alternar várias vezes antes de estabilizar. Um programa que conta cada borda sem debounce interpreta essas transições como múltiplos eventos.

## Questão 6

**Resposta: B.**

`ponteiro` recebe `&valor`, ou seja, o endereço de `valor`. A expressão `*ponteiro` acessa o conteúdo naquele endereço. Somar três ao conteúdo altera a própria variável `valor`, que passa de `7` para `10`. O endereço armazenado não muda.

## Questão 7

**Resposta: D.**

`p` aponta para o primeiro elemento. `p + 1` aponta para o segundo elemento, considerando o tamanho de `int`. A atribuição modifica o vetor para `{2, 6, 8}`. Portanto:

```text
2 + 6 + 8 = 16
```

## Questão 8

**Resposta: D.**

As afirmações I, II e IV estão corretas.

- Uma variável local automática deixa de existir ao final da chamada; seu endereço não pode ser usado depois.
- Um ponteiro não nulo ainda pode estar pendente, fora de limites ou apontar para um objeto incompatível.
- Acesso além do vetor viola os limites do objeto.
- `volatile` não fornece atomicidade, exclusão mútua ou sincronização. Ele apenas afeta a maneira como o compilador trata acessos ao objeto.

---

## Questão 9 — Resposta esperada

### Problemas encontrados

#### LED no GPIO35

No ESP32 clássico, GPIO35 é somente entrada. Ele não pode comandar o LED como saída. Além disso, todo LED externo precisa de limitação de corrente.

Correção possível:

```text
GPIO23 → resistor de 220 Ω a 330 Ω → ânodo do LED
cátodo do LED → GND
```

#### Botão no GPIO34 com `INPUT_PULLUP`

GPIO34 é somente entrada e não oferece pull-up interno configurável. O botão ficaria sem um nível definido quando solto se não houvesse resistor externo.

Duas correções possíveis:

1. manter GPIO34 e adicionar pull-up externo apropriado; ou
2. mover o botão para um pino como GPIO27 e usar `INPUT_PULLUP`.

Para a segunda opção:

```text
GPIO27 → botão → GND
```

#### Sinal de 5 V no GPIO27

O sinal excede a tolerância documentada do GPIO. A ligação direta pode danificar o ESP32.

A adaptação pode usar:

- conversor de nível lógico apropriado;
- buffer compatível;
- divisor resistivo projetado, quando adequado à saída e à frequência do sensor.

Exemplo conceitual de divisor para uma saída digital push-pull:

```text
saída 5 V ── 10 kΩ ──┬── GPIO32
                     │
                    20 kΩ
                     │
                    GND
```

O valor nominal no nó é:

```text
5 V × 20 kΩ / (10 kΩ + 20 kΩ) ≈ 3,33 V
```

Antes de adotar o divisor, devem ser analisados tolerância dos resistores, tensão máxima real, frequência, corrente e tipo de saída do sensor. Um módulo conversor pode ser mais apropriado.

#### GPIO6 reservado para saída

GPIO6 pertence ao grupo normalmente usado pela flash em módulos ESP32 comuns. Reconfigurá-lo pode impedir a execução do programa. A saída deve ser movida para um GPIO documentado como disponível.

#### Endereço de variável local

`leitura` possui duração automática. Ela deixa de existir quando `lerBotao()` termina. O ponteiro devolvido fica pendente e não pode ser desreferenciado com segurança.

### Função corrigida

```cpp
const int PINO_BOTAO = 27;

bool lerBotao(int* destino) {
  if (destino == nullptr) {
    return false;
  }

  *destino = digitalRead(PINO_BOTAO);
  return true;
}
```

Uso:

```cpp
int leitura = HIGH;

if (lerBotao(&leitura)) {
  bool pressionado = leitura == LOW;
  // Processar a leitura...
}
```

Agora o objeto `leitura` pertence ao chamador e continua existindo depois que a função retorna. A função valida `nullptr` e informa sucesso ou falha.

### Incorporação do debounce

O programa pode armazenar:

- leitura anterior;
- estado estável;
- instante da última mudança.

A cada passagem por `loop()`, ele lê o pino, registra mudanças com `millis()` e aceita o novo estado somente depois que permanecer estável pelo intervalo de debounce. Não é necessário usar `delay()`.

### Documentos esperados

- identificação impressa na placa e no módulo;
- guia e esquema da placa;
- datasheet do módulo;
- datasheet da série ESP32 correspondente;
- documentação oficial de GPIO;
- documentação do sensor;
- documentação do conversor de nível ou cálculo completo do divisor;
- limites elétricos dos LEDs e demais componentes.

### Critérios de correção — 4,0 pontos

| Critério | Valor |
| --- | ---: |
| Identificação dos problemas de GPIO35, GPIO34 e GPIO6 | `0,8` |
| Identificação do risco do sinal de 5 V | `0,6` |
| Correção elétrica de LED e botão | `0,6` |
| Adaptação de nível coerente e justificada | `0,5` |
| Explicação do ponteiro pendente | `0,4` |
| Função segura com validação de `nullptr` | `0,5` |
| Estratégia de debounce não bloqueante | `0,3` |
| Documentos e processo de verificação | `0,3` |

---

## Questão 10 — Solução guiada

Este desafio não possui nota. A solução abaixo deve ser lida como continuação do material.

### Circuito de cada canal

Botão:

```text
GPIO do botão ─── botão ─── GND
```

LED:

```text
GPIO do LED ─── resistor de 220 Ω a 330 Ω ─── ânodo
cátodo do LED ─── GND
```

### Implementação completa

```cpp
const unsigned long TEMPO_DEBOUNCE_MS = 40;

struct Canal {
  const char* nome;
  int pinoBotao;
  int pinoLed;
  int leituraAnterior;
  int estadoEstavel;
  unsigned long instanteMudanca;
  bool ledLigado;
  unsigned long acionamentos;
};

Canal canais[] = {
  {"canal-1", 25, 18, HIGH, HIGH, 0, false, 0},
  {"canal-2", 26, 19, HIGH, HIGH, 0, false, 0},
  {"canal-3", 27, 23, HIGH, HIGH, 0, false, 0}
};

const size_t QUANTIDADE_CANAIS =
    sizeof(canais) / sizeof(canais[0]);

bool configurarCanal(Canal* canal) {
  if (canal == nullptr) {
    return false;
  }

  pinMode(canal->pinoBotao, INPUT_PULLUP);
  pinMode(canal->pinoLed, OUTPUT);
  digitalWrite(canal->pinoLed, LOW);

  int leituraInicial = digitalRead(canal->pinoBotao);
  canal->leituraAnterior = leituraInicial;
  canal->estadoEstavel = leituraInicial;
  canal->instanteMudanca = millis();
  canal->ledLigado = false;
  canal->acionamentos = 0;

  return true;
}

bool detectarPressionamento(
    Canal* canal,
    unsigned long agora) {
  if (canal == nullptr) {
    return false;
  }

  int leituraAtual = digitalRead(canal->pinoBotao);

  if (leituraAtual != canal->leituraAnterior) {
    canal->leituraAnterior = leituraAtual;
    canal->instanteMudanca = agora;
  }

  bool ficouEstavel =
      agora - canal->instanteMudanca >= TEMPO_DEBOUNCE_MS;

  if (ficouEstavel &&
      leituraAtual != canal->estadoEstavel) {
    canal->estadoEstavel = leituraAtual;

    if (canal->estadoEstavel == LOW) {
      return true;
    }
  }

  return false;
}

void registrarEvento(const Canal* canal) {
  if (canal == nullptr) {
    return;
  }

  Serial.print(canal->nome);
  Serial.print("; led=");
  Serial.print(canal->ledLigado ? "ligado" : "desligado");
  Serial.print("; acionamentos=");
  Serial.println(canal->acionamentos);
}

void processarCanal(
    Canal* canal,
    unsigned long agora) {
  if (canal == nullptr) {
    return;
  }

  if (!detectarPressionamento(canal, agora)) {
    return;
  }

  canal->ledLigado = !canal->ledLigado;
  canal->acionamentos++;

  digitalWrite(
      canal->pinoLed,
      canal->ledLigado ? HIGH : LOW
  );

  registrarEvento(canal);
}

void setup() {
  Serial.begin(115200);

  for (size_t i = 0; i < QUANTIDADE_CANAIS; i++) {
    configurarCanal(&canais[i]);
  }
}

void loop() {
  unsigned long agora = millis();

  for (size_t i = 0; i < QUANTIDADE_CANAIS; i++) {
    processarCanal(&canais[i], agora);
  }
}
```

### Como a implementação funciona

#### Um vetor de estados

`canais` contém três objetos consecutivos do tipo `Canal`. Cada objeto guarda configuração e estado de execução. Não é necessário criar variáveis separadas como `botao1`, `botao2` e `botao3`.

#### Endereço de um elemento

```cpp
&canais[i]
```

Essa expressão obtém o endereço do elemento atual. O endereço é passado a `configurarCanal()` e `processarCanal()`.

#### Operador `->`

Dentro das funções, `canal` é um ponteiro. Portanto:

```cpp
canal->pinoLed
```

equivale a:

```cpp
(*canal).pinoLed
```

#### Estado independente

Cada elemento possui sua própria leitura anterior, estado estável, temporização, estado do LED e contador. A mesma função opera sobre canais diferentes porque recebe qual objeto deve ser alterado.

#### Debounce

Uma mudança reinicia `instanteMudanca`. O novo nível só é aceito depois de permanecer estável por `40 ms`. O retorno `true` ocorre apenas na transição estável para `LOW`.

#### Ausência de bloqueio

`loop()` percorre rapidamente os três canais. Como não existe `delay()`, o programa pode observar outro botão enquanto um canal está em processo de estabilização.

#### Memória previsível

O vetor possui três elementos definidos estaticamente. Não há `new` ou `delete`, e a quantidade de memória necessária é conhecida durante a compilação.

### Experimentos recomendados

1. Comente temporariamente o debounce e compare o contador.
2. Altere o intervalo para `5 ms`, `40 ms` e `200 ms`.
3. Imprima os endereços de `canais[0]`, `canais[1]` e `canais[2]`.
4. Compare a diferença entre esses endereços com `sizeof(Canal)`.
5. Troque um dos botões para um pino somente de entrada e acrescente pull-up externo.
6. Adicione um quarto canal apenas incluindo outro elemento no vetor.
7. Implemente uma função que desligue todos os LEDs sem duplicar código.

### Cuidados ao comparar com sua solução

- Uma solução diferente pode estar correta.
- Não copie números de pino sem conferir sua placa.
- `nullptr` não é o único ponteiro inválido possível.
- O simulador pode não reproduzir o bounce elétrico real.
- Em hardware real, avalie alimentação, ruído e comprimento dos fios.

---

[Voltar à lista de exercícios](../exercicios/semana-03.md) · [Consultar o material da Semana 03](../docs/semana-03.md)
