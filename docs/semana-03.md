# Semana 03 — Arquitetura de microcontroladores e ponteiros

Nesta semana, estudaremos como o hardware de um microcontrolador influencia o programa. O objetivo é deixar de tratar os pinos como números intercambiáveis e começar a escolhê-los a partir do datasheet, do pinout da placa e dos requisitos elétricos do circuito.

Também aprofundaremos ponteiros e organização da memória em C/C++, relacionando endereços, vetores, estruturas e periféricos à programação do ESP32.

## Atividades da semana

- [Lista de exercícios](../exercicios/semana-03.md)
- [Gabarito comentado](../gabaritos/semana-03.md)

## Referência de hardware adotada

As explicações de pinout usam como referência o **ESP32 clássico**, baseado nos processadores Xtensa LX6, e a placa **ESP32-DevKitC**. A família ESP32 também inclui variantes como ESP32-C3, C6, S2, S3 e outras, com arquiteturas e pinos diferentes.

> [!IMPORTANT]
> Antes de montar um circuito, identifique o código exato do chip, do módulo e da placa. Um pinout encontrado na internet só deve ser usado depois de comparado com a documentação oficial do modelo real.

## Sumário

1. [Objetivos de aprendizagem](#objetivos-de-aprendizagem)
2. [Arquitetura de um microcontrolador](#1-arquitetura-de-um-microcontrolador)
3. [Arquitetura do ESP32](#2-visão-geral-do-esp32-clássico)
4. [Memória](#3-organização-da-memória)
5. [Pinout](#4-como-interpretar-um-pinout)
6. [Escolha de pinos no ESP32](#5-escolha-de-pinos-no-esp32-clássico)
7. [LEDs e botões](#6-leds-e-botões-sem-armadilhas)
8. [Debounce](#7-debounce-de-botões)
9. [Ruído e filtragem](#8-ruído-e-filtragem)
10. [Ponteiros](#9-ponteiros-em-cc)
11. [Ponteiros e estruturas](#10-ponteiros-com-vetores-e-estruturas)
12. [Prática integrada](#11-prática-integrada-botão-robusto-com-ponteiro)
13. [Videoaulas](#videoaulas)
14. [Checklist](#checklist-de-projeto)

## Objetivos de aprendizagem

Ao concluir esta semana, você deverá ser capaz de:

- identificar CPU, memórias, barramentos, periféricos e GPIOs em uma arquitetura básica;
- explicar como o programa interage com periféricos;
- distinguir chip, módulo e placa de desenvolvimento;
- consultar datasheet, documentação da placa e pinout;
- reconhecer pinos de alimentação, reset, GPIO, entrada exclusiva, *strapping*, flash e comunicação;
- selecionar pinos apropriados para LEDs e botões no ESP32 clássico;
- evitar aplicar sinais de 5 V diretamente aos GPIOs do ESP32;
- explicar por que uma entrada flutuante produz leituras imprevisíveis;
- implementar debounce por software sem bloquear o programa;
- explicar um filtro RC e calcular sua constante de tempo;
- reconhecer fontes comuns de ruído e aplicar medidas básicas de mitigação;
- declarar, validar e desreferenciar ponteiros;
- usar ponteiros com vetores e estruturas;
- reconhecer ponteiro nulo, ponteiro pendente, acesso fora dos limites e vazamento de memória;
- relacionar `volatile` ao acesso de dados que podem mudar fora do fluxo comum do programa.

## Pré-requisitos e materiais

- conteúdo das Semanas 1 e 2;
- Arduino IDE 2 com suporte ao ESP32 instalado;
- uma placa ESP32 cuja identificação esteja legível;
- cabo USB com transmissão de dados;
- protoboard, botão, LED externo e resistor entre `220 Ω` e `330 Ω`;
- opcionalmente, resistor de `10 kΩ`, capacitor e multímetro;
- datasheet do chip ou módulo e documentação da placa utilizada.

### Links de preparação

- [Instalação oficial do Arduino Core para ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)
- [Documentação do Arduino Core para ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/)
- [Documentação oficial da ESP32-DevKitC](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/index.html)
- [Datasheet da série ESP32](https://documentation.espressif.com/esp32_datasheet_en.html)

---

## 1. Arquitetura de um microcontrolador

Um microcontrolador integra processamento, memória e periféricos em um único circuito. Esses blocos se comunicam por barramentos internos.

```text
                    ┌──────────────┐
                    │     CPU      │
                    └──────┬───────┘
                           │ barramentos
       ┌──────────────┬────┴────┬───────────────┐
       │              │         │               │
┌──────▼──────┐ ┌─────▼────┐ ┌──▼────────┐ ┌────▼─────┐
│   Memória   │ │   GPIO   │ │ Timers /  │ │ Interfaces│
│ Flash / RAM │ │          │ │   PWM     │ │ UART/I2C │
└─────────────┘ └──────────┘ └───────────┘ │ SPI etc. │
                                           └──────────┘
```

### CPU

A CPU busca instruções, decodifica-as e executa operações. Ela possui registradores internos, unidade de controle e unidades de cálculo.

### Barramentos

Barramentos transportam endereços, dados e sinais de controle. Uma arquitetura pode possuir caminhos separados ou compartilhados para diferentes tipos de acesso.

### Memórias

- **ROM:** conteúdo fixo ou gravado pelo fabricante;
- **flash:** armazena programa e dados persistentes;
- **RAM:** armazena dados utilizados durante a execução;
- **memória RTC:** pode permanecer disponível em determinados modos de baixo consumo;
- **memória externa:** flash ou PSRAM conectada ao chip, quando prevista pelo módulo.

### Periféricos

Periféricos realizam tarefas especializadas:

- GPIO digital;
- conversores ADC e DAC;
- PWM;
- temporizadores e watchdogs;
- UART, I2C, SPI e outras interfaces;
- controladores de interrupção;
- rádio, criptografia e recursos de baixo consumo.

### Clock

O clock sincroniza operações do processador e de periféricos. Frequência mais alta não significa automaticamente um sistema melhor: consumo, aquecimento, temporização e limites dos periféricos também importam.

### Interrupções

Uma interrupção permite que um evento solicite atenção da CPU. A execução normal é temporariamente desviada para uma rotina específica.

Rotinas de interrupção devem ser curtas. Dados compartilhados com o fluxo normal exigem cuidado com concorrência, atomicidade e `volatile`. Debounce completo e mensagens seriais extensas normalmente não pertencem à rotina de interrupção.

---

## 2. Visão geral do ESP32 clássico

O ESP32 clássico integra processamento de 32 bits, memórias, GPIOs, periféricos, Wi-Fi e Bluetooth. Algumas versões do chip possuem um núcleo e outras, dois núcleos. As capacidades exatas dependem do componente.

### Chip, módulo e placa

Esses termos não são equivalentes:

- **chip/SoC:** circuito integrado ESP32;
- **módulo:** reúne o chip, flash, cristal, antena e componentes de suporte, como ESP32-WROOM;
- **placa de desenvolvimento:** acrescenta regulador, USB–serial, botões, conectores e pinos, como ESP32-DevKitC.

Um mesmo chip pode aparecer em módulos e placas com conexões diferentes. Por isso, o datasheet do chip não substitui o esquema ou o guia da placa.

### GPIO Matrix e IO MUX

O ESP32 permite encaminhar muitos sinais de periféricos a diferentes GPIOs por uma matriz interna. Isso oferece flexibilidade, mas não transforma todos os pinos em equivalentes:

- sinais analógicos dependem de pinos específicos;
- alguns sinais rápidos usam caminhos dedicados;
- há pinos de inicialização;
- há pinos ocupados por flash ou PSRAM;
- alguns GPIOs são apenas entrada;
- nem todo pino do chip é exposto pela placa.

### Periféricos relevantes

O ESP32 clássico pode oferecer, conforme a variante:

- GPIOs programáveis;
- ADC, DAC e sensores capacitivos;
- PWM por hardware;
- UART, I2C, SPI e I2S;
- contadores de pulso e temporizadores;
- Wi-Fi de 2,4 GHz e Bluetooth;
- recursos de criptografia e geração de números aleatórios;
- modos de baixo consumo e coprocessador ULP.

Não presuma que a mesma lista vale para ESP32-C3, S3, C6 ou outras famílias. Consulte a matriz de recursos da variante utilizada.

---

## 3. Organização da memória

### Da compilação à execução

O código C/C++ é compilado e organizado em regiões. Um modelo conceitual simplificado é:

```text
flash:  código e dados constantes
RAM:    dados globais, dados estáticos, heap e stack
```

### Regiões comuns

| Região | Exemplos | Duração típica |
| --- | --- | --- |
| Texto/código | Instruções compiladas | Toda a execução |
| Somente leitura | Literais e constantes | Toda a execução |
| Dados inicializados | Globais com valor inicial | Toda a execução |
| BSS | Globais inicializadas com zero | Toda a execução |
| Heap | Objetos alocados dinamicamente | Até a liberação |
| Stack | Parâmetros e variáveis locais | Durante chamadas |

O ESP32 possui uma organização mais complexa que esse desenho, incluindo regiões internas, externas, caches e memórias com capacidades específicas. O modelo ainda é útil para raciocinar sobre duração e risco.

### Mapa de memória

Um mapa de memória associa faixas de endereços a memórias e periféricos. Em sistemas embarcados, alguns registradores de periféricos podem ser acessados como posições de memória.

```text
endereço → região de memória ou registrador de periférico
```

Bibliotecas como Arduino Core e ESP-IDF fornecem APIs que evitam manipular diretamente os endereços na maioria das tarefas. Acesso direto a registradores exige documentação exata do chip e não deve ser copiado entre famílias.

### `volatile`

`volatile` informa ao compilador que um valor pode mudar por um mecanismo que não aparece no fluxo normal daquela função, como hardware ou uma rotina de interrupção.

```cpp
volatile bool eventoPendente = false;
```

`volatile` não torna uma operação atômica, não cria exclusão mútua e não resolve sozinho problemas entre núcleos ou tarefas. Ele impede determinadas otimizações inadequadas para aquele tipo de acesso.

---

## 4. Como interpretar um pinout

Um pinout relaciona os conectores físicos da placa às funções elétricas e lógicas disponíveis.

### Informações que devem ser identificadas

- nome exato da placa;
- orientação do desenho;
- numeração dos GPIOs;
- pinos de alimentação e `GND`;
- tensão lógica;
- pino de habilitação/reset;
- pinos apenas de entrada;
- pinos de inicialização (*strapping*);
- pinos usados por flash ou PSRAM;
- interfaces seriais padrão;
- ADC, DAC, touch e PWM;
- LED integrado, quando existir;
- funções que entram em conflito.

### Número do GPIO não é posição física

`GPIO23` significa o sinal lógico de número 23. Ele não significa “vigésimo terceiro pino do conector”. Sempre use os rótulos impressos e o diagrama oficial.

### Pinout não é datasheet

Uma imagem de pinout é um resumo. Ela raramente informa todos os limites elétricos, estados de inicialização ou conflitos. O fluxo correto é:

```text
identificar placa → abrir guia/esquema → abrir datasheet → escolher pino → revisar circuito
```

### Planilha de escolha

Antes da montagem, registre:

| Sinal | Direção | Requisito | GPIO escolhido | Restrição consultada |
| --- | --- | --- | --- | --- |
| Botão | Entrada | Pull-up | A definir | Verificar pull interno |
| LED | Saída | GPIO comum | A definir | Verificar inicialização |
| Sensor analógico | Entrada | ADC | A definir | Verificar canal e faixa |
| Comunicação | Entrada/saída | UART/I2C/SPI | A definir | Verificar periférico |

---

## 5. Escolha de pinos no ESP32 clássico

As observações abaixo são pontos de partida para o ESP32 clássico. Elas não substituem a documentação da placa.

### Tensão lógica

O ESP32 opera com lógica de aproximadamente `3,3 V`. A documentação oficial informa tolerância máxima de `3,6 V` nos GPIOs.

> Não aplique `5 V` diretamente a um GPIO do ESP32. Use um circuito de adaptação de nível adequado quando necessário.

### GPIOs 34 a 39

No ESP32 clássico, GPIOs `34` a `39` são apenas entradas e não possuem pull-up ou pull-down interno controlável por software.

Consequências:

- não servem para acionar LED como saída;
- `INPUT_PULLUP` não fornece o resistor interno esperado;
- um botão nesses pinos precisa de resistor externo;
- continuam úteis para várias entradas, quando compatíveis com a placa e o periférico.

### GPIOs 6 a 11

Em módulos ESP32 comuns, GPIOs `6` a `11` são usados pela memória flash. Não devem ser escolhidos como GPIOs de aplicação.

### Pinos de *strapping*

GPIOs `0`, `2`, `5`, `12` e `15` participam da configuração de inicialização do ESP32 clássico. Circuitos externos que forçam níveis inadequados durante reset podem impedir a inicialização ou alterar o modo de boot.

Eles não são sempre proibidos, mas exigem análise do circuito e do datasheet. Para exercícios iniciais, é mais simples evitá-los.

### UART de programação

GPIOs `1` e `3` são normalmente usados por UART0 para programação e mensagens de inicialização. Usá-los em periféricos pode interferir com upload e diagnóstico.

### GPIOs 16 e 17

Em alguns módulos com PSRAM, GPIOs `16` e `17` são ocupados. Em outros módulos podem estar disponíveis. A decisão depende do módulo e da placa.

### ADC2 e Wi-Fi

No ESP32 clássico, canais ADC2 possuem restrições quando o Wi-Fi está ativo. Esse ponto será aprofundado na Semana 4. Registre desde já se um pino analógico pertence ao ADC1 ou ADC2.

### Pinos geralmente convenientes para práticas digitais

Em muitas placas com ESP32-WROOM, GPIOs como `18`, `19`, `21`, `22`, `23`, `25`, `26`, `27`, `32` e `33` costumam ser opções flexíveis para GPIO digital.

Isso não é autorização automática: alguns podem estar ligados a periféricos da placa ou necessários no projeto. Confirme sempre o esquema e o pinout do modelo utilizado.

### Checklist de seleção

Antes de conectar um componente, pergunte:

1. O pino está exposto na placa?
2. Ele aceita entrada, saída ou ambas?
3. Possui resistor interno adequado?
4. Participa do boot?
5. Está ligado à flash, PSRAM, USB–serial ou outro componente?
6. Possui a função analógica ou periférica necessária?
7. O sinal respeita a faixa de tensão?
8. O estado do componente durante reset é seguro?

---

## 6. LEDs e botões sem armadilhas

### LED externo

Uma ligação comum é:

```text
GPIO de saída → resistor de 220 Ω a 330 Ω → ânodo do LED
cátodo do LED → GND
```

O resistor limita a corrente. O valor adequado depende do LED, da tensão e dos limites elétricos documentados para o dispositivo.

Evite:

- pino apenas de entrada;
- pino usado pela flash;
- ligação sem resistor;
- carga que exija mais corrente que o GPIO pode fornecer;
- assumir que o LED integrado está no mesmo GPIO em todas as placas.

Para motores, relés, solenoides e outras cargas, use circuito de acionamento apropriado. Um GPIO não deve alimentar diretamente uma carga de potência.

### Botão com `INPUT_PULLUP`

```text
GPIO ─── botão ─── GND
```

```cpp
const int PINO_BOTAO = 27;

void setup() {
  pinMode(PINO_BOTAO, INPUT_PULLUP);
}
```

Nesse circuito:

- botão solto → `HIGH`;
- botão pressionado → `LOW`.

Essa lógica é chamada de ativa em nível baixo.

### Entrada flutuante

Uma entrada sem pull-up, pull-down ou outra fonte definida pode captar interferência e alternar aparentemente sozinha.

```cpp
pinMode(PINO_BOTAO, INPUT);  // Pode flutuar se o circuito não definir nível.
```

Use um resistor interno quando disponível ou um resistor externo compatível com o circuito.

---

## 7. Debounce de botões

Contatos mecânicos não mudam instantaneamente entre aberto e fechado. Durante alguns milissegundos, podem ocorrer várias transições.

```text
ideal:       HIGH ───────────── LOW

real:        HIGH ───── LOW HIGH LOW HIGH ─── LOW
                         └──── bounce ────┘
```

Sem tratamento, um pressionamento pode ser contado várias vezes.

### Debounce por software

Uma estratégia comum aceita a leitura somente depois que ela permanece estável por um intervalo.

```cpp
const unsigned long TEMPO_DEBOUNCE_MS = 40;

int leituraAnterior = HIGH;
int estadoEstavel = HIGH;
unsigned long instanteMudanca = 0;

void loop() {
  unsigned long agora = millis();
  int leitura = digitalRead(PINO_BOTAO);

  if (leitura != leituraAnterior) {
    leituraAnterior = leitura;
    instanteMudanca = agora;
  }

  if (agora - instanteMudanca >= TEMPO_DEBOUNCE_MS &&
      leitura != estadoEstavel) {
    estadoEstavel = leitura;

    if (estadoEstavel == LOW) {
      // Um novo pressionamento válido ocorreu.
    }
  }
}
```

O uso da diferença entre tempos é importante para continuar funcionando quando o contador de `millis()` transbordar.

### Debounce com resistor e capacitor

Um filtro RC pode suavizar transições rápidas. Em uma configuração simples com pull-up externo:

```text
3,3 V ── resistor ──┬── GPIO
                    │
                    ├── botão ── GND
                    │
                    └── capacitor ── GND
```

A constante de tempo é:

```text
τ = R × C
```

Exemplo:

```text
R = 10 kΩ
C = 1 µF
τ = 10 ms
```

O sinal não se torna digitalmente perfeito apenas por existir um capacitor. Limiares de entrada, tolerâncias, tempo de bounce e corrente transitória devem ser considerados. Para maior robustez, pode-se combinar RC com entrada Schmitt trigger ou debounce por software.

> Não adicione capacitores grandes diretamente a um GPIO sem analisar corrente, tempo de carga e descarga e limiares lógicos. No circuito mostrado, o resistor define a carga e limita a corrente contínua enquanto o botão está pressionado; a descarga do capacitor ocorre pelo botão e pode exigir resistência adicional em um projeto mais rigoroso.

### Hardware ou software?

| Estratégia | Vantagem | Limitação |
| --- | --- | --- |
| Software | Sem componentes extras e fácil de ajustar | Depende do programa continuar executando |
| RC | Atenua transições rápidas no sinal físico | Valor inadequado pode gerar bordas lentas ou atraso excessivo |
| Schmitt trigger | Reconstrói uma transição digital limpa | Exige componente ou entrada compatível |
| Combinação | Maior robustez | Maior complexidade e custo |

---

## 8. Ruído e filtragem

Ruído é uma perturbação indesejada que altera uma alimentação ou sinal. Fontes comuns incluem motores, relés, fontes chaveadas, cabos longos, descargas eletrostáticas e retorno de corrente mal planejado.

### Sintomas

- leituras digitais aleatórias;
- valores analógicos instáveis;
- resets do microcontrolador;
- falhas de comunicação;
- acionamentos falsos;
- comportamento diferente ao ligar motores ou rádio.

### Medidas básicas

- não deixar entradas flutuantes;
- usar pull-up ou pull-down apropriado;
- manter conexões curtas em protoboard;
- compartilhar referência de `GND` quando necessário;
- separar sinais sensíveis de cargas ruidosas;
- usar diodo de roda livre em cargas indutivas DC, com projeto adequado;
- usar desacoplamento próximo aos circuitos integrados;
- filtrar alimentação e dimensionar a fonte para picos de consumo;
- empregar cabos trançados, blindagem ou transmissão diferencial quando o ambiente exigir;
- aplicar média, mediana, histerese ou filtro digital conforme a natureza do sinal;
- verificar o circuito com osciloscópio quando a forma de onda importa.

### Capacitor de desacoplamento

Um capacitor de desacoplamento fornece energia local para transientes rápidos e oferece um caminho de baixa impedância para componentes de alta frequência. Deve ficar fisicamente próximo ao pino de alimentação correspondente.

Ele não substitui uma fonte adequada, trilhas corretas ou capacitores de reserva para variações mais lentas. Placas de desenvolvimento normalmente já incluem parte desse circuito; periféricos externos podem precisar do próprio desacoplamento.

### Filtro analógico e filtro digital

- **Filtro analógico:** atua no sinal elétrico antes da leitura, como um RC.
- **Filtro digital:** processa amostras já convertidas, como média móvel.

Um filtro não deve ser escolhido apenas porque “deixa o gráfico mais liso”. Ele introduz atraso e pode remover variações reais. Defina primeiro quais frequências e eventos devem ser preservados.

---

## 9. Ponteiros em C/C++

Um ponteiro armazena um endereço de memória associado a um tipo.

```cpp
int valor = 10;
int* ponteiro = &valor;
```

| Expressão | Significado |
| --- | --- |
| `valor` | Conteúdo da variável |
| `&valor` | Endereço da variável |
| `ponteiro` | Endereço armazenado |
| `*ponteiro` | Objeto localizado nesse endereço |

```cpp
*ponteiro = 25;
// valor passa a ser 25.
```

### A declaração deve ser lida por variável

```cpp
int* a;
int b;
```

`a` é ponteiro; `b` é inteiro comum. Em declarações múltiplas, o `*` pertence ao declarador.

### `nullptr`

```cpp
int* ponteiro = nullptr;
```

Antes de desreferenciar um ponteiro que pode ser nulo:

```cpp
if (ponteiro != nullptr) {
  Serial.println(*ponteiro);
}
```

### Passagem por ponteiro

```cpp
bool incrementar(int* valor) {
  if (valor == nullptr) {
    return false;
  }

  (*valor)++;
  return true;
}
```

Uso:

```cpp
int contador = 0;
incrementar(&contador);
```

Os parênteses em `(*valor)++` deixam claro que o conteúdo apontado é incrementado.

### Ponteiro constante e dado constante

```cpp
const int* p1;       // Não altera o int por meio de p1.
int* const p2 = ...; // p2 não pode apontar para outro endereço.
```

Para receber um vetor apenas para leitura:

```cpp
int somar(const int* valores, size_t quantidade) {
  int total = 0;

  for (size_t i = 0; i < quantidade; i++) {
    total += valores[i];
  }

  return total;
}
```

---

## 10. Ponteiros com vetores e estruturas

### Vetores

Em muitas expressões, o nome do vetor é convertido em um ponteiro para o primeiro elemento.

```cpp
int leituras[] = {12, 18, 25};
int* inicio = leituras;

Serial.println(*inicio);        // 12
Serial.println(*(inicio + 1));  // 18
Serial.println(inicio[2]);      // 25
```

O compilador não conhece automaticamente o tamanho do vetor a partir de um parâmetro `int*`. Passe a quantidade separadamente.

### Aritmética de ponteiros

Somar `1` a um `int*` avança para o próximo `int`, não necessariamente um byte.

```cpp
inicio + 1
```

O endereço é ajustado de acordo com o tamanho do tipo apontado.

### Estruturas

```cpp
struct Canal {
  int pino;
  bool ativo;
};

Canal led = {23, false};
Canal* canal = &led;

canal->ativo = true;
```

O operador `->` acessa um membro por meio de um ponteiro. Ele equivale a:

```cpp
(*canal).ativo = true;
```

### Duração e validade

Um endereço só pode ser usado enquanto o objeto correspondente existir.

Código incorreto:

```cpp
int* criarValorInvalido() {
  int local = 42;
  return &local;
}
```

Quando a função termina, `local` deixa de existir. O ponteiro devolvido fica pendente.

### Problemas comuns

- desreferenciar `nullptr`;
- usar ponteiro não inicializado;
- acessar depois de `delete`;
- devolver endereço de variável local automática;
- avançar além do fim de um vetor;
- escrever em memória somente leitura;
- usar tipo de ponteiro incompatível;
- perder a referência de uma alocação e causar vazamento;
- acessar dados compartilhados sem sincronização.

### Por que ponteiros aparecem em sistemas embarcados?

- passagem eficiente de buffers;
- drivers que recebem estruturas de configuração;
- manipulação de vetores e pacotes;
- acesso a periféricos mapeados em memória;
- callbacks e funções de interrupção;
- comunicação entre APIs de baixo nível.

Ponteiros oferecem controle, mas não validam automaticamente limites ou duração. A responsabilidade de manter o acesso válido é do programa.

---

## 11. Prática integrada — Botão robusto com ponteiro

Nesta prática, um botão alterna um LED externo. O estado de debounce é agrupado em uma estrutura, e uma função recebe um ponteiro para atualizá-la.

### Pinos de referência

- botão: `GPIO27`;
- LED externo: `GPIO23`.

Esses pinos são comuns em placas ESP32-WROOM, mas devem ser confirmados no pinout da placa utilizada.

### Circuito

```text
GPIO27 ─── botão ─── GND

GPIO23 ─── resistor ─── ânodo do LED
                          cátodo ─── GND
```

### Código

```cpp
const int PINO_BOTAO = 27;
const int PINO_LED = 23;
const unsigned long TEMPO_DEBOUNCE_MS = 40;

struct Debounce {
  int pino;
  int leituraAnterior;
  int estadoEstavel;
  unsigned long instanteMudanca;
};

Debounce botao = {
  PINO_BOTAO,
  HIGH,
  HIGH,
  0
};

bool detectarPressionamento(
    Debounce* estado,
    unsigned long agora) {
  if (estado == nullptr) {
    return false;
  }

  int leituraAtual = digitalRead(estado->pino);

  if (leituraAtual != estado->leituraAnterior) {
    estado->leituraAnterior = leituraAtual;
    estado->instanteMudanca = agora;
  }

  bool ficouEstavel =
      agora - estado->instanteMudanca >= TEMPO_DEBOUNCE_MS;

  if (ficouEstavel &&
      leituraAtual != estado->estadoEstavel) {
    estado->estadoEstavel = leituraAtual;
    return estado->estadoEstavel == LOW;
  }

  return false;
}

void setup() {
  pinMode(PINO_BOTAO, INPUT_PULLUP);
  pinMode(PINO_LED, OUTPUT);

  digitalWrite(PINO_LED, LOW);
  Serial.begin(115200);
}

void loop() {
  static bool ledLigado = false;

  if (detectarPressionamento(&botao, millis())) {
    ledLigado = !ledLigado;
    digitalWrite(PINO_LED, ledLigado ? HIGH : LOW);

    Serial.print("LED: ");
    Serial.println(ledLigado ? "ligado" : "desligado");
  }
}
```

### O que observar

1. `&botao` envia o endereço da estrutura.
2. O parâmetro `Debounce* estado` recebe esse endereço.
3. `estado->pino` acessa um membro por meio do ponteiro.
4. A função verifica `nullptr` antes de desreferenciar.
5. O evento ocorre somente na transição estável para `LOW`.
6. Manter o botão pressionado não alterna o LED repetidamente.
7. Não há `delay()`, portanto o programa permanece responsivo.
8. `ledLigado` é `static` e preserva o valor entre execuções de `loop()`.

### Extensões para estudo

- medir o tempo real de bounce no monitor serial;
- variar `TEMPO_DEBOUNCE_MS` e comparar resultados;
- usar um resistor de pull-up externo;
- adicionar um RC projetado e comparar hardware, software e combinação;
- duplicar a estrutura para controlar dois botões com a mesma função.

---

## Videoaulas

### Ponteiros em C

- [Ponteiros em C: se não aprender com esse vídeo, esquece!](https://www.youtube.com/watch?v=1Hgl4TU8CB0)

Durante o vídeo, relacione os exemplos a:

- operador de endereço `&`;
- operador de desreferenciação `*`;
- passagem de endereços a funções;
- vetores;
- validade e tipo do ponteiro.

### Curso ESP32 Básico — IoT

[Acessar a playlist completa](https://www.youtube.com/playlist?list=PLLHo4pVI8I_o_tIRC-twzYImDhwHgpYbw)

A playlist utiliza ESP32 com a Arduino IDE e está mais próxima do hardware e das ferramentas adotadas neste módulo. As aulas disponíveis atualmente são:

| Aula | O que explorar |
| --- | --- |
| [Aula 1 — Primeiros passos com ESP32](https://www.youtube.com/watch?v=5arcdEzRDA8) | Características da placa, comparação inicial com Arduino e configuração do ESP32 na Arduino IDE |
| [Introdução à Internet das Coisas](https://www.youtube.com/watch?v=OY-GmPdd59U) | Papel do dispositivo conectado e aplicações de IoT |
| [Aula 2 — LED com botão](https://www.youtube.com/watch?v=Psgn6KGQmMo) | GPIO de entrada e saída, protoboard, resistor, sensor, atuador e lógica de controle |
| [Aula 3 — Buzzer com ESP32](https://www.youtube.com/watch?v=bUDSkKMUMd0) | Saída digital, buzzer ativo e passivo, frequência e geração de sinais |
| [Aula 4 — Sensor de temperatura NTC 10k](https://www.youtube.com/watch?v=Jb_QwD0a0F0) | Divisor resistivo, entrada analógica, conversão e tomada de decisão |
| [Aula 5 — Sensor ultrassônico HC-SR04](https://www.youtube.com/watch?v=ztLUq0ckbl8) | Sinais de trigger e echo, temporização e necessidade de verificar níveis lógicos |
| [Aula 6 — Sensor de som KY-037](https://www.youtube.com/watch?v=r51-9N0TLzo) | Entradas digital e analógica, sensibilidade, ruído e limiares |
| [Aula 7 — MPU-6050 com ESP32](https://www.youtube.com/watch?v=VEU_fLbrySo) | Comunicação I2C, biblioteca, acelerômetro, giroscópio e múltiplos eixos |

Para a Semana 3, priorize:

1. **Aula 1**, para identificar a placa e configurar o ambiente;
2. **Aula 2**, para analisar a escolha dos GPIOs e a ligação de LED e botão;
3. o vídeo de ponteiros indicado anteriormente;
4. a documentação oficial de pinout e GPIO da Espressif.

As aulas 3 a 7 antecipam conteúdos das semanas de sensores, entradas analógicas, atuadores e comunicação.

### Como analisar os circuitos dos vídeos

Um vídeo prático ajuda a visualizar a montagem, mas não substitui o datasheet. Para cada aula, registre:

| Item | Pergunta de verificação |
| --- | --- |
| Placa | O modelo e o módulo são os mesmos usados por mim? |
| GPIO | O pino aceita a direção e o periférico necessários? |
| Boot | O pino participa da configuração de inicialização? |
| Tensão | Todos os sinais respeitam a tolerância do ESP32? |
| Corrente | A carga pode ser acionada diretamente pelo GPIO? |
| Proteção | O circuito precisa de resistor, divisor, transistor ou diodo? |
| Código | A API ainda corresponde à versão instalada do Arduino Core? |

No caso de módulos alimentados com `5 V`, como algumas versões do HC-SR04, verifique especialmente a tensão produzida pelo pino de saída antes de conectá-lo ao ESP32. Alimentação e nível lógico são requisitos diferentes.

---

## Checklist de projeto

### Antes de energizar

- [ ] Identifiquei chip, módulo e placa.
- [ ] Consultei documentação oficial do modelo correto.
- [ ] Confirmei tensão e direção de cada sinal.
- [ ] Não apliquei 5 V a um GPIO do ESP32.
- [ ] Evitei pinos da flash e verifiquei PSRAM.
- [ ] Analisei pinos de *strapping* e estados durante reset.
- [ ] Confirmei se entradas possuem pull-up ou pull-down.
- [ ] Usei resistor no LED.
- [ ] Mantive cargas de potência fora do GPIO.
- [ ] Revisei `GND`, alimentação e polaridade.

### Antes de executar o código

- [ ] Ponteiros foram inicializados.
- [ ] Ponteiros potencialmente nulos são verificados.
- [ ] Índices permanecem dentro dos vetores.
- [ ] Nenhum endereço de variável local é devolvido.
- [ ] A duração dos objetos é suficiente para o uso do ponteiro.
- [ ] Entradas mecânicas possuem debounce.
- [ ] O laço principal não é bloqueado desnecessariamente.
- [ ] Estados de falha conduzem as saídas a uma condição segura.

## Referências oficiais

- [ESP32 Series Datasheet](https://documentation.espressif.com/esp32_datasheet_en.html)
- [ESP32 Technical Reference Manual](https://www.espressif.com/sites/default/files/documentation/esp32_technical_reference_manual_en.pdf)
- [ESP32-DevKitC](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/index.html)
- [GPIO no ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/gpio.html)
- [GPIO no Arduino Core para ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/gpio.html)
- [Instalação do Arduino Core para ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)
- [ESP32 Hardware Design Guidelines](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32/)
- [FAQ técnica da Espressif](https://docs.espressif.com/projects/esp-faq/en/latest/)
- [Guia de memória do Arduino](https://docs.arduino.cc/learn/programming/memory-guide/)

[Voltar ao cronograma](../README.md#cronograma)
