# Semana 02 — Programação básica usada em microcontroladores

Nesta semana, avançaremos da execução de um programa simples para a construção de código organizado, reutilizável e consciente das limitações do hardware. Também compararemos microcontroladores e controladores lógicos programáveis (CLPs), duas tecnologias importantes em controle e automação.

O foco de programação será dividido em quatro eixos: **funções**, **caracteres e strings**, **memória** e **operações matemáticas**.

## Atividades da semana

- [Lista de exercícios](../exercicios/semana-02.md)
- [Gabarito comentado](../gabaritos/semana-02.md)

## Sumário

1. [Objetivos de aprendizagem](#objetivos-de-aprendizagem)
2. [Trilha sugerida](#trilha-sugerida)
3. [Controladores lógicos programáveis](#1-controladores-lógicos-programáveis-clps)
4. [CLP ou microcontrolador?](#2-clp-ou-microcontrolador)
5. [Arquitetura básica do microcontrolador](#3-arquitetura-básica-do-microcontrolador)
6. [Programação de microcontroladores](#4-programação-de-microcontroladores)
7. [Funções](#5-funções)
8. [Caracteres e strings](#6-caracteres-e-strings)
9. [Memória](#7-memória)
10. [Ponteiros](#8-introdução-a-ponteiros)
11. [Matemática](#9-operações-matemáticas)
12. [Prática integrada](#10-prática-integrada-ciclo-de-controle-modular)
13. [Videoaulas](#videoaulas)
14. [Boas práticas](#boas-práticas-para-código-embarcado)

## Objetivos de aprendizagem

Ao concluir esta semana, você deverá ser capaz de:

- explicar o propósito de um CLP e seu ciclo de varredura;
- comparar CLPs e microcontroladores sem tratá-los como tecnologias mutuamente exclusivas;
- reconhecer CPU, memória e periféricos como partes de um microcontrolador;
- decompor um programa em funções pequenas e bem definidas;
- diferenciar parâmetro, argumento, retorno, escopo e efeito colateral;
- trabalhar com `char`, vetores de caracteres e objetos `String`;
- explicar, em nível introdutório, memória de programa, memória de dados, pilha e heap;
- reconhecer riscos de alocação dinâmica em sistemas com memória limitada;
- interpretar os operadores `&` e `*` em exemplos simples com ponteiros;
- evitar erros comuns de divisão inteira, conversão e estouro numérico;
- aplicar funções matemáticas da linguagem Arduino;
- organizar a leitura de entradas, a tomada de decisão e a atualização de saídas em funções separadas.

## Pré-requisitos e materiais

- material e exercícios da Semana 1;
- Arduino IDE 2 ou Tinkercad Circuits;
- Arduino Uno físico ou simulado;
- um botão e fios para a prática integrada;
- acesso ao monitor serial;
- conhecimentos básicos de variáveis, condicionais, repetições e entradas/saídas digitais.

## Trilha sugerida

| Etapa | Atividade | Resultado esperado |
| --- | --- | --- |
| 1 | Estudar CLPs e microcontroladores | Escolher a plataforma com base no contexto |
| 2 | Revisar o ciclo de execução do Arduino | Entender quando cada trecho é executado |
| 3 | Praticar funções | Separar responsabilidades do programa |
| 4 | Estudar texto e memória | Manipular dados sem desperdiçar recursos |
| 5 | Revisar matemática | Evitar erros de tipo, escala e precisão |
| 6 | Executar a prática integrada | Implementar um ciclo de controle modular |
| 7 | Resolver a lista e o LeetCode | Consolidar lógica e C++ |

---

## 1. Controladores lógicos programáveis (CLPs)

Um **controlador lógico programável**, ou CLP, é um computador industrial criado para automatizar máquinas e processos. Ele recebe sinais de entrada, executa uma lógica de controle e atualiza as saídas de modo repetitivo e previsível.

Exemplos de aplicação:

- esteiras de produção;
- sistemas de envase;
- elevadores;
- estações de bombeamento;
- controle de motores industriais;
- intertravamentos de segurança;
- linhas de montagem.

### Elementos comuns

- **CPU:** executa o programa de controle;
- **memória:** armazena programa, configuração e variáveis;
- **módulos de entrada:** recebem sinais de botões, sensores e transmissores;
- **módulos de saída:** comandam contatores, válvulas, indicadores e outros equipamentos;
- **fonte de alimentação:** fornece energia adequada ao controlador;
- **interfaces de comunicação:** conectam outros CLPs, supervisórios, inversores e redes industriais;
- **ambiente de programação:** permite configurar, programar, monitorar e diagnosticar o sistema.

### Ciclo de varredura

O funcionamento básico de um CLP costuma ser explicado por um ciclo repetitivo:

```text
ler entradas → executar programa → atualizar saídas → diagnóstico/comunicação → repetir
```

Esse processo é chamado de **ciclo de varredura** ou *scan cycle*. O tempo necessário para concluí-lo é relevante: uma mudança muito rápida pode não ser percebida se ocorrer fora das capacidades de amostragem do sistema.

O conceito também é útil ao programar microcontroladores. Podemos organizar `loop()` como um ciclo de controle:

```cpp
void loop() {
  lerEntradas();
  executarControle();
  atualizarSaidas();
}
```

### Linguagens de CLP

A norma IEC 61131-3 organiza linguagens para controladores programáveis. Entre as formas atuais estão:

- **Ladder Diagram (LD):** representação gráfica inspirada em diagramas de relés;
- **Function Block Diagram (FBD):** blocos funcionais conectados graficamente;
- **Structured Text (ST):** linguagem textual estruturada;
- **Sequential Function Chart (SFC):** elementos para organizar comportamentos sequenciais.

Neste módulo, a programação principal continuará em C/C++, mas conhecer essas linguagens ajuda a relacionar programação, controle e automação industrial.

---

## 2. CLP ou microcontrolador?

Não existe uma resposta universal. A escolha depende do ambiente, dos requisitos e do ciclo de vida do produto.

| Critério | CLP | Microcontrolador |
| --- | --- | --- |
| Uso típico | Automação industrial e máquinas | Produtos embarcados e protótipos |
| Robustez pronta | Geralmente projetado para ambiente industrial | Depende do projeto da placa e do circuito |
| Entradas e saídas | Módulos industriais padronizados | Pinos e periféricos do chip ou da placa |
| Programação | Ferramentas e linguagens industriais | C/C++, frameworks, SDKs e outras linguagens |
| Manutenção | Diagnóstico e substituição em campo | Pode exigir conhecimento do hardware e firmware |
| Flexibilidade de produto | Modular e configurável | Alta liberdade de projeto e integração |
| Custo unitário | Frequentemente mais alto | Pode ser muito baixo em produção |
| Consumo e tamanho | Em geral maiores | Pode ser compacto e econômico |

### Quando um CLP tende a ser adequado

- ambiente industrial severo;
- manutenção por equipes de automação;
- necessidade de módulos industriais prontos;
- integração com redes e equipamentos industriais;
- diagnóstico e substituição rápida em campo.

### Quando um microcontrolador tende a ser adequado

- produto compacto ou alimentado por bateria;
- grande volume de produção;
- requisitos específicos de custo, tamanho ou consumo;
- controle próximo ao hardware;
- necessidade de personalização elevada.

Uma solução pode combinar ambos: um microcontrolador coleta dados localmente e um CLP coordena o processo industrial.

---

## 3. Arquitetura básica do microcontrolador

Um microcontrolador reúne vários recursos em um único circuito integrado.

### CPU

Busca e executa instruções do programa. Sua arquitetura influencia tipos de dados, desempenho, consumo e acesso à memória.

### Memória de programa

Armazena o código compilado e dados constantes. Em muitas placas Arduino, o programa permanece armazenado mesmo sem energia.

### Memória de dados

Armazena variáveis usadas durante a execução. Seu conteúdo volátil normalmente é perdido quando a energia é removida.

### Memória não volátil

Alguns microcontroladores ou placas oferecem EEPROM, flash ou outro mecanismo para preservar configurações. Cada tecnologia possui limites e formas próprias de acesso.

### Periféricos

São blocos especializados para interagir com o mundo externo:

- pinos digitais;
- conversores analógico-digitais;
- temporizadores;
- PWM;
- comunicação serial, I2C e SPI;
- interrupções;
- Wi-Fi e Bluetooth em placas como o ESP32.

### Recursos limitados

Um computador pessoal pode possuir gigabytes de memória; microcontroladores simples podem trabalhar com poucos quilobytes de RAM. Por isso, decisões que parecem pequenas — tamanho de uma string, tipo numérico ou criação repetida de objetos — podem comprometer o sistema.

---

## 4. Programação de microcontroladores

### Do código à execução

O fluxo básico é:

```text
código-fonte → compilação → programa de máquina → upload → execução na placa
```

O compilador verifica a sintaxe, traduz o programa e informa parte do uso de memória. O *upload* grava o programa na placa.

### `setup()` e `loop()`

```cpp
void setup() {
  // Configuração inicial: executada uma vez após iniciar ou reiniciar.
}

void loop() {
  // Lógica recorrente: executada continuamente.
}
```

O fato de `loop()` repetir não significa que todo o código deva ficar dentro dela. Funções bem nomeadas tornam o ciclo mais legível.

### Separação de responsabilidades

Uma organização útil é:

```text
entrada → processamento → saída
```

Em código:

```cpp
void loop() {
  bool botaoPressionado = lerBotao();
  bool deveAcender = calcularSaida(botaoPressionado);
  atualizarLed(deveAcender);
}
```

Essa separação facilita testes, manutenção e substituição de componentes.

### Monitor serial

O monitor serial ajuda a observar valores internos do programa.

```cpp
void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.println("Sistema em execucao");
  delay(1000);
}
```

A taxa configurada em `Serial.begin()` deve ser compatível com a selecionada no monitor serial.

> Mensagens de diagnóstico são úteis, mas consomem tempo e memória. Em um sistema final, avalie quais informações realmente precisam permanecer.

---

## 5. Funções

Uma função agrupa instruções que realizam uma tarefa. Ela pode receber dados por parâmetros e devolver um resultado.

### Estrutura geral

```cpp
tipoDeRetorno nomeDaFuncao(tipo parametro) {
  // instruções
  return resultado;
}
```

Exemplo:

```cpp
float celsiusParaFahrenheit(float celsius) {
  return celsius * 1.8 + 32.0;
}
```

Uso:

```cpp
float temperaturaF = celsiusParaFahrenheit(25.0);
```

Nesse exemplo:

- `float` antes do nome é o tipo de retorno;
- `celsiusParaFahrenheit` é o nome da função;
- `float celsius` é o parâmetro;
- `25.0` é o argumento enviado na chamada;
- `return` devolve o resultado.

### Funções sem retorno

Use `void` quando a função executa uma ação e não devolve um valor.

```cpp
void definirLed(bool ligado) {
  digitalWrite(LED_BUILTIN, ligado ? HIGH : LOW);
}
```

### Mais de um parâmetro

```cpp
int limitarValor(int valor, int minimo, int maximo) {
  if (valor < minimo) {
    return minimo;
  }

  if (valor > maximo) {
    return maximo;
  }

  return valor;
}
```

### Passagem por valor

Por padrão, tipos simples são passados por valor. A função recebe uma cópia.

```cpp
void dobrar(int valor) {
  valor = valor * 2;
}

int numero = 5;
dobrar(numero);
// numero continua valendo 5.
```

### Passagem por referência

Uma referência permite alterar a variável original.

```cpp
void dobrar(int& valor) {
  valor = valor * 2;
}

int numero = 5;
dobrar(numero);
// numero agora vale 10.
```

Quando a função deve apenas consultar um objeto sem copiá-lo, é comum usar referência constante:

```cpp
bool estaVazio(const String& texto) {
  return texto.length() == 0;
}
```

### Escopo

O escopo determina onde um nome pode ser usado.

```cpp
int global = 10;

void exemplo() {
  int local = 20;
  // global e local estão acessíveis aqui.
}

// Somente global está acessível aqui.
```

Variáveis globais existem durante toda a execução. Elas são úteis para estados compartilhados, mas o excesso de globais dificulta entender quais funções podem alterar cada valor.

### Funções previsíveis

Sempre que possível, prefira funções cujo resultado depende claramente dos parâmetros:

```cpp
float calcularMedia(float a, float b) {
  return (a + b) / 2.0;
}
```

Isso facilita testar a função sem conectar componentes físicos.

---

## 6. Caracteres e strings

### `char`

Um `char` representa um caractere. Literais de caractere usam aspas simples.

```cpp
char comando = 'L';
char quebraDeLinha = '\n';
```

`'A'` representa um caractere. `"A"` representa uma sequência de caracteres terminada por um marcador nulo.

### Vetor de caracteres

Uma string no estilo C é um vetor de `char` terminado por `\0`.

```cpp
char mensagem[] = "IoT";
```

Na memória, existem quatro posições:

```text
'I'  'o'  'T'  '\0'
```

Logo:

```cpp
sizeof(mensagem)  // 4 bytes neste exemplo
strlen(mensagem)  // 3 caracteres visíveis
```

Para usar `strlen()`, `strcmp()`, `strcpy()` e outras funções de C, inclua `<cstring>` quando necessário. Para `snprintf()`, use `<cstdio>`. Nunca copie mais caracteres do que o destino comporta.

### Buffer com tamanho fixo

```cpp
char mensagem[20];
snprintf(mensagem, sizeof(mensagem), "Valor: %d", 42);
```

`snprintf()` recebe o tamanho do destino e é mais seguro que operações que não limitam a escrita. Mesmo assim, o resultado pode ser truncado se o buffer for pequeno.

### Objeto `String` do Arduino

```cpp
String dispositivo = "sensor-01";
String mensagem = dispositivo + ": ativo";

Serial.println(mensagem);
```

`String` oferece métodos convenientes:

```cpp
mensagem.length();
mensagem.startsWith("sensor");
mensagem.indexOf(':');
mensagem.toUpperCase();
```

### `String` ou `char[]`?

| Alternativa | Vantagens | Cuidados |
| --- | --- | --- |
| `String` | API simples e legível | Pode usar alocação dinâmica e fragmentar o heap |
| `char[]` | Memória previsível | Exige controle explícito de tamanho e terminador |

Em placas com pouca RAM ou sistemas que devem funcionar continuamente, buffers fixos tornam o uso de memória mais previsível. Se `String` for adequado ao projeto, evite concatenações repetitivas desnecessárias e considere reservar capacidade quando o tamanho esperado for conhecido.

### Comparação

Para objetos `String`:

```cpp
if (comando == "LIGAR") {
  // ...
}
```

Para vetores de caracteres, não compare os endereços com `==`. Use `strcmp()`:

```cpp
if (strcmp(comando, "LIGAR") == 0) {
  // ...
}
```

---

## 7. Memória

### Regiões conceituais

| Região | Conteúdo típico | Duração |
| --- | --- | --- |
| Programa/flash | Instruções e constantes | Permanece sem energia |
| Dados globais/estáticos | Globais e variáveis `static` | Toda a execução |
| Stack/pilha | Parâmetros, retornos e variáveis locais | Durante as chamadas |
| Heap | Objetos criados dinamicamente | Até serem liberados |

A implementação exata varia conforme a arquitetura, mas esse modelo ajuda a entender problemas comuns.

### Variáveis locais, globais e estáticas

```cpp
int contadorGlobal = 0;

void registrarEvento() {
  int temporario = 10;
  static int totalDeEventos = 0;

  totalDeEventos++;
}
```

- `contadorGlobal` existe durante toda a execução;
- `temporario` é criado para a chamada da função;
- `totalDeEventos` mantém seu valor entre chamadas, mas só é acessível dentro da função.

### `sizeof`

O operador `sizeof` informa o tamanho, em bytes, de um tipo ou objeto.

```cpp
Serial.println(sizeof(int));
Serial.println(sizeof(float));
```

O resultado depende da arquitetura e do compilador. Não assuma que um tipo possui o mesmo tamanho em todas as placas.

### Stack e chamadas de função

Parâmetros e variáveis locais normalmente utilizam a pilha. Funções recursivas criam novas chamadas e consomem mais pilha. Em microcontroladores com pouca RAM, recursão profunda e grandes vetores locais podem causar falhas difíceis de diagnosticar.

### Heap e alocação dinâmica

Em C++:

```cpp
int* valor = new int(42);

// Uso de *valor...

delete valor;
valor = nullptr;
```

Problemas possíveis:

- **vazamento:** memória alocada deixa de ser liberada;
- **fragmentação:** espaços livres ficam divididos em blocos pequenos;
- **falha de alocação:** não existe um bloco adequado;
- **uso após liberação:** o programa acessa uma região que já não pertence ao objeto;
- **dupla liberação:** a mesma região é liberada mais de uma vez.

Para muitos sistemas embarcados pequenos, é preferível reservar buffers e objetos de forma estática, com limites conhecidos. Alocação dinâmica não é proibida, mas precisa de justificativa e controle.

---

## 8. Introdução a ponteiros

Um ponteiro armazena o endereço de outro objeto na memória. O aprofundamento ocorrerá na Semana 3; por enquanto, o objetivo é compreender a notação básica.

```cpp
int valor = 10;
int* ponteiro = &valor;
```

- `&valor` obtém o endereço de `valor`;
- `int*` declara um ponteiro para `int`;
- `ponteiro` armazena o endereço;
- `*ponteiro` acessa o objeto apontado.

```cpp
*ponteiro = 25;
// valor agora é 25.
```

### Ponteiro nulo

```cpp
int* ponteiro = nullptr;
```

`nullptr` indica que o ponteiro não aponta para um objeto válido. Desreferenciar um ponteiro nulo ou inválido produz comportamento indefinido.

```cpp
if (ponteiro != nullptr) {
  Serial.println(*ponteiro);
}
```

### Vetores e endereços

O nome de um vetor pode ser usado, em muitas expressões, como endereço do primeiro elemento.

```cpp
int valores[] = {10, 20, 30};
int* inicio = valores;

Serial.println(*inicio);        // 10
Serial.println(*(inicio + 1));  // 20
```

Isso não elimina a necessidade de respeitar os limites. Acessar além do vetor pode ler ou alterar outras regiões da memória.

---

## 9. Operações matemáticas

### Divisão inteira

```cpp
int media = (7 + 8) / 2;  // resultado: 7
```

Como todos os operandos são inteiros, a parte fracionária é descartada.

```cpp
float media = (7 + 8) / 2.0;  // resultado: 7.5
```

### Ordem das operações

Use parênteses quando eles tornarem a intenção explícita.

```cpp
float tensao = leitura * (5.0 / 1023.0);
```

Evite:

```cpp
float tensao = leitura * (5 / 1023);  // 5 / 1023 resulta em zero inteiro.
```

### Estouro

Todo tipo numérico possui limites. Quando um resultado ultrapassa o intervalo representável, ocorre estouro ou outro comportamento definido pelas regras do tipo e da linguagem.

```cpp
long produto = 1000L * 1000L;
```

O sufixo `L` força os literais a participarem da operação como `long`. Guardar o resultado em um tipo maior não corrige uma operação que já transbordou em um tipo menor.

### Sinal e tipos sem sinal

Tipos `unsigned` não representam números negativos e oferecem um intervalo positivo diferente. Misturar valores com e sem sinal pode produzir comparações inesperadas.

Para temporização com `millis()`, use `unsigned long` e compare diferenças:

```cpp
unsigned long agora = millis();

if (agora - instanteAnterior >= intervalo) {
  instanteAnterior = agora;
}
```

### Funções úteis do Arduino

```cpp
int limitado = constrain(valor, 0, 100);
long convertido = map(leitura, 0, 1023, 0, 100);
int distancia = abs(erro);
int menor = min(a, b);
int maior = max(a, b);
float raiz = sqrt(25.0);
float potencia = pow(2.0, 3.0);
```

Cuidados:

- `map()` opera com inteiros e não limita automaticamente valores fora do intervalo;
- `constrain()` limita um valor entre mínimo e máximo;
- números de ponto flutuante possuem precisão finita;
- `pow()` e outras operações de ponto flutuante podem ser mais custosas que operações inteiras;
- unidades devem estar explícitas em nomes ou comentários.

### Função de conversão com limites

```cpp
int converterPorcentagem(int leitura) {
  int leituraValida = constrain(leitura, 0, 1023);
  return map(leituraValida, 0, 1023, 0, 100);
}
```

Separar a conversão em uma função torna a regra reutilizável e testável.

---

## 10. Prática integrada — Ciclo de controle modular

Nesta prática, um botão funcionará como entrada e o LED interno como saída. A estrutura lembrará o ciclo de varredura de um CLP.

### Circuito

Conecte um botão entre o pino digital `2` e o `GND`. O programa usará o resistor interno de *pull-up*.

```text
pino 2 ─── botão ─── GND
```

### Código

```cpp
const int PINO_BOTAO = 2;

bool lerBotao() {
  return digitalRead(PINO_BOTAO) == LOW;
}

bool executarControle(bool botaoPressionado) {
  return botaoPressionado;
}

void atualizarLed(bool deveAcender) {
  digitalWrite(LED_BUILTIN, deveAcender ? HIGH : LOW);
}

void registrarEstado(bool botaoPressionado, bool ledAceso) {
  Serial.print("botao=");
  Serial.print(botaoPressionado ? "pressionado" : "solto");
  Serial.print("; led=");
  Serial.println(ledAceso ? "aceso" : "apagado");
}

void setup() {
  pinMode(PINO_BOTAO, INPUT_PULLUP);
  pinMode(LED_BUILTIN, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  bool botaoPressionado = lerBotao();
  bool ledAceso = executarControle(botaoPressionado);

  atualizarLed(ledAceso);
  registrarEstado(botaoPressionado, ledAceso);

  delay(100);
}
```

### Relação com o ciclo de varredura

| Etapa | Função |
| --- | --- |
| Ler entradas | `lerBotao()` |
| Executar a regra | `executarControle()` |
| Atualizar saídas | `atualizarLed()` |
| Diagnóstico | `registrarEstado()` |

### Exploração

1. Execute o circuito no Tinkercad.
2. Observe o monitor serial com o botão solto e pressionado.
3. Inverta a regra: LED aceso quando o botão estiver solto.
4. Altere `executarControle()` sem modificar as funções de entrada e saída.
5. Remova temporariamente o `delay(100)` e observe a quantidade de mensagens.
6. Explique por que separar as funções facilita mudar uma parte do sistema.

> O pequeno `delay()` reduz mensagens nesta prática, mas bloqueia o processador. O desafio da lista retomará temporização não bloqueante.

---

## Videoaulas

### Curso prático de Arduino com C++ no Tinkercad

[Acessar a playlist completa](https://www.youtube.com/playlist?list=PLClMa6CurIPM1dTgzrqVZU5YnJ_74rm5N)

A playlist possui as seguintes aulas. Os títulos abaixo reproduzem o conteúdo publicado; a coluna da direita indica o que pode ser explorado em cada projeto.

| Aula | O que explorar |
| --- | --- |
| [Introdução](https://www.youtube.com/watch?v=pFxSl75Vyhs) | Aplicações do Arduino e criação de conta no Tinkercad |
| [Acendendo um LED](https://www.youtube.com/watch?v=vpPYtPXC_f4) | Saída digital, circuito com resistor e primeiro código C++ |
| [Pressione o botão para acender o LED](https://www.youtube.com/watch?v=GPD3aTKkMgg) | Entrada digital, condicional e relação sensor–atuador |
| [Tocando as 7 notas musicais](https://www.youtube.com/watch?v=In_Ov8-8YT8) | Funções, frequências, sequências e buzzer |
| [Poste de luz automática](https://www.youtube.com/watch?v=k-6xy3Qvn4Q) | Leitura de luminosidade e regra automática |
| [Controlando um LED com potenciômetro](https://www.youtube.com/watch?v=pdcLTZ3racM) | Entrada analógica, escala e controle de saída |
| [Projeto dimmer com PWM](https://www.youtube.com/watch?v=vaFzRkQk0ec) | PWM, mapeamento de valores e intensidade luminosa |
| [3 LEDs + 3 botões](https://www.youtube.com/watch?v=RyXuaUHOPas) | Múltiplas entradas e saídas, organização e repetição de código |
| [Alarme com sensor de temperatura e buzzer](https://www.youtube.com/watch?v=_wUaFvZie5Q) | Funções matemáticas, limites e acionamento de alarme |
| [Semáforo de trânsito](https://www.youtube.com/watch?v=EYjnOUfEfFQ) | Sequência de estados e temporização |
| [Projeto RGB](https://www.youtube.com/watch?v=5atsF0BFuAU) | Combinação de canais e decomposição em funções |
| [Usando um LED RGB](https://www.youtube.com/watch?v=6SuzKI9801U) | Ligações do componente e controle de cores |
| [Usando um display LCD](https://www.youtube.com/watch?v=xR-SIV2R2VE) | Exibição de texto, strings e bibliotecas |
| [Criando desenhos no display LCD](https://www.youtube.com/watch?v=KOeeQDYBUGM) | Vetores de bytes, caracteres personalizados e memória |
| [Roleta de LEDs com sensor de pressão](https://www.youtube.com/watch?v=N0NEc06-V8I) | Vetores, sequências, sensor FSR e projeto interativo |

Para a Semana 2, priorize as aulas de introdução, LED, botão, notas musicais, três LEDs com três botões e display LCD. As demais podem ser usadas como antecipação de sensores, PWM e atuadores.

### Programação básica para Arduino

- [Arduino C++ 01 — primeiro programa](https://www.youtube.com/watch?v=1GyE3MXlkDE&list=PLhPlCBM3MH4EUp19uXz8AZhA9os_AbzzO): introduz a estrutura de um programa Arduino e inicia uma sequência com maior foco em programação.

Ao acompanhar, identifique `setup()`, `loop()`, variáveis, funções e chamadas da API Arduino. Compare o exemplo apresentado com a separação entre entrada, controle e saída usada neste material.

### Ponteiros e memória

- [Ponteiros em C](https://www.youtube.com/watch?v=1Hgl4TU8CB0)
- [Aprenda ponteiros de uma vez por todas](https://www.youtube.com/watch?v=ZhYiNrI2OLU&t=127s)
- [Stack vs. heap e alocação dinâmica em C++](https://www.youtube.com/watch?v=pVcuigMNFgA)

Durante os vídeos, procure responder:

1. Qual é a diferença entre um valor e seu endereço?
2. O que os operadores `&` e `*` fazem em cada contexto?
3. Qual é a diferença de duração entre uma variável local e uma alocação no heap?
4. O que é um vazamento de memória?
5. Por que fragmentação e falhas de alocação são especialmente importantes em microcontroladores?

Ponteiros serão aprofundados na Semana 3. Nesta semana, basta dominar endereço, desreferenciação, `nullptr` e a relação básica com vetores.

---

## Boas práticas para código embarcado

- Dê a cada função uma responsabilidade clara.
- Prefira nomes que incluam intenção e, quando útil, unidade: `tempoMs`, `tensaoVolts`.
- Use `const` para valores que não mudam.
- Evite globais quando um valor pode ser parâmetro ou retorno.
- Limite índices antes de acessar vetores.
- Dimensione buffers considerando o terminador `\0`.
- Não use alocação dinâmica sem compreender duração, liberação e fragmentação.
- Escolha tipos numéricos considerando intervalo, sinal e precisão.
- Evite conversões implícitas difíceis de perceber.
- Separe leitura, processamento e atuação.
- Registre pelo monitor serial apenas o necessário.
- Trate estados inválidos de sensores de forma segura.

## Checklist de aprendizagem

- [ ] Sei explicar o ciclo de varredura de um CLP.
- [ ] Consigo justificar a escolha entre CLP e microcontrolador em um cenário simples.
- [ ] Consigo escrever e chamar uma função com parâmetros e retorno.
- [ ] Entendo a diferença entre passagem por valor e por referência.
- [ ] Sei diferenciar `char`, `char[]` e `String`.
- [ ] Lembro que strings C terminam com `\0`.
- [ ] Sei explicar stack, heap e alocação estática em nível introdutório.
- [ ] Reconheço `&`, `*` e `nullptr` em um exemplo simples.
- [ ] Sei evitar divisão inteira quando preciso de resultado fracionário.
- [ ] Consigo organizar um ciclo como entrada, processamento e saída.

## Referências oficiais

- [Referência da linguagem Arduino](https://docs.arduino.cc/language-reference/)
- [Programação com Arduino](https://docs.arduino.cc/programming)
- [Uso de funções em sketches](https://docs.arduino.cc/learn/programming/functions/)
- [Guia de memória do Arduino](https://docs.arduino.cc/learn/programming/memory-guide/)
- [Objeto `String`](https://docs.arduino.cc/language-reference/en/variables/data-types/stringObject/)
- [Funções matemáticas do Arduino](https://docs.arduino.cc/language-reference/#functions-math)
- [Comunicação `Serial`](https://docs.arduino.cc/language-reference/en/functions/communication/serial/)
- [IEC 61131-3 — visão geral da PLCopen](https://www.plcopen.org/iec-61131-3)
- [Documentação de C++](https://en.cppreference.com/w/)

> A referência bibliográfica indicada como “Chapter 2, Sections 7 e 7.1” ainda precisa ser identificada pelo título e pela edição da obra para que a citação completa seja adicionada.

[Voltar ao cronograma](../README.md#cronograma)
