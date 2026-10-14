# Semana 01 — Comece por aqui

## Tutorial: LED interno com Tinkercad, Arduino IDE e GitLab

Nesta semana você conhecerá o fluxo básico de um projeto de sistemas embarcados: entender o problema, montar ou simular um circuito, programar o microcontrolador, testar o comportamento e versionar o código.

Ao final, você terá um programa que pisca o LED interno da placa, executado primeiro em uma simulação e depois, se houver uma placa disponível, no hardware real.

## Sumário

1. [Objetivos de aprendizagem](#objetivos-de-aprendizagem)
2. [Trilha sugerida](#trilha-sugerida)
3. [Sistemas embarcados, IoT e automação](#1-sistemas-embarcados-iot-e-automação)
4. [Arduino e microcontroladores](#2-arduino-e-microcontroladores)
5. [Eletrônica básica](#3-eletrônica-básica)
6. [Protoboard](#4-protoboard)
7. [Multímetro](#5-multímetro)
8. [Sensores e atuadores](#6-sensores-e-atuadores)
9. [C/C++ para Arduino](#7-fundamentos-de-cc-para-arduino)
10. [Simulação no Tinkercad](#8-prática-1-simulação-no-tinkercad)
11. [Arduino IDE e placa física](#9-prática-2-arduino-ide-e-placa-física)
12. [Git e GitLab](#10-registrando-o-projeto-com-git-e-gitlab)
13. [Videoaulas](#videoaulas)
14. [Exercícios](#lista-de-exercícios)
15. [Entrega](#entrega-da-semana)

## Objetivos de aprendizagem

Ao concluir esta semana, você deverá ser capaz de:

- explicar, em linhas gerais, o que são sistemas embarcados, automação e IoT;
- identificar o papel de um microcontrolador em um sistema;
- diferenciar sensores de atuadores;
- reconhecer tensão, corrente, resistência e polaridade em um circuito simples;
- entender as conexões básicas de uma protoboard;
- realizar medições simples com um multímetro de forma segura;
- declarar variáveis e usar condicionais e repetições básicas em C/C++;
- explicar o papel das funções `setup()` e `loop()`;
- simular o exemplo Blink no Tinkercad;
- compilar e enviar um programa pela Arduino IDE;
- registrar o projeto em um repositório Git/GitLab.

## Pré-requisitos e materiais

### Para a simulação

- computador com acesso à internet;
- conta gratuita na [Autodesk/Tinkercad](https://www.tinkercad.com/);
- navegador atualizado.

### Para a prática com hardware

- uma placa compatível com Arduino, preferencialmente Arduino Uno;
- cabo USB compatível com a placa;
- computador com a Arduino IDE 2;
- protoboard, LED e resistor entre `220 Ω` e `330 Ω` para a atividade opcional com LED externo;
- multímetro, se disponível.

> O exemplo principal usa o LED já presente na placa. Portanto, a primeira execução em hardware não exige protoboard nem componentes externos.

## Trilha sugerida

| Etapa | Atividade | Resultado esperado |
| --- | --- | --- |
| 1 | Ler as seções conceituais | Compreender os elementos de um sistema embarcado |
| 2 | Assistir às videoaulas introdutórias | Familiarizar-se com Arduino, sensores e Tinkercad |
| 3 | Revisar C/C++ | Entender o código do exemplo Blink |
| 4 | Executar a Prática 1 | LED interno piscando no Tinkercad |
| 5 | Instalar a Arduino IDE | Ambiente local preparado |
| 6 | Executar a Prática 2 | LED interno piscando na placa física |
| 7 | Fazer os exercícios | Modificar e explicar o comportamento do programa |
| 8 | Versionar no GitLab | Evidências e código registrados |

---

## 1. Sistemas embarcados, IoT e automação

### Sistema embarcado

Um sistema embarcado é um sistema computacional criado para cumprir uma função específica dentro de um equipamento. Diferentemente de um computador de uso geral, ele normalmente possui recursos limitados e interage diretamente com o ambiente.

Exemplos:

- controlador de temperatura de uma geladeira;
- central eletrônica de um automóvel;
- fechadura eletrônica;
- relógio inteligente;
- controlador de irrigação.

Um sistema embarcado costuma reunir:

1. **entradas**, como botões e sensores;
2. **processamento**, normalmente realizado por um microcontrolador;
3. **saídas**, como LEDs, displays, motores e relés;
4. **software**, que define as regras de funcionamento;
5. **comunicação**, quando há troca de dados com outros dispositivos.

### Internet das Coisas

IoT, ou Internet das Coisas, conecta objetos físicos a redes para que eles coletem dados, troquem informações e executem ações. Nem todo sistema embarcado é IoT: para ser considerado IoT, o dispositivo normalmente precisa participar de uma rede ou serviço conectado.

Exemplo de fluxo de um sistema IoT:

```text
temperatura → sensor → microcontrolador → Wi-Fi → plataforma → dashboard
                                      ↓
                                  ventilador
```

### Controle e automação

Automação é o uso de sistemas para executar tarefas com pouca ou nenhuma intervenção humana. Um controle simples pode seguir esta lógica:

```text
medir → comparar com uma regra → decidir → atuar → medir novamente
```

Por exemplo: se a temperatura ultrapassar `30 °C`, ligar um ventilador. O sensor fornece a entrada, o microcontrolador toma a decisão e o ventilador é o atuador.

---

## 2. Arduino e microcontroladores

### O que é um microcontrolador?

Um microcontrolador é um pequeno computador integrado em um único circuito. Ele reúne processador, memória e periféricos de entrada e saída. Seu programa fica armazenado na própria placa e começa a executar quando o dispositivo é ligado ou reiniciado.

### O que é Arduino?

Arduino é um ecossistema de hardware e software para prototipagem eletrônica. Ele inclui placas, uma linguagem baseada em C/C++, bibliotecas, exemplos e ferramentas como a Arduino IDE.

Neste material, o Arduino Uno é a principal referência porque está disponível no simulador Tinkercad e possui um LED integrado fácil de controlar.

### Elementos importantes de uma placa

- **Microcontrolador:** executa o programa.
- **Pinos digitais:** trabalham principalmente com dois estados, `HIGH` e `LOW`.
- **Pinos analógicos:** medem níveis de tensão dentro da faixa aceita pela placa.
- **Pinos de alimentação:** fornecem tensão e referência de terra (`GND`).
- **Conector USB:** permite alimentação, programação e comunicação com o computador.
- **Botão de reset:** reinicia a execução do programa.
- **LED interno:** LED já conectado pela própria placa a um pino digital.

> As características elétricas e o número do pino do LED interno variam entre placas. Use a constante `LED_BUILTIN` em vez de escrever diretamente o número `13`.

---

## 3. Eletrônica básica

### Tensão, corrente e resistência

| Grandeza | Símbolo | Unidade | Ideia simplificada |
| --- | --- | --- | --- |
| Tensão | `V` | volt (`V`) | Diferença de potencial que impulsiona as cargas |
| Corrente | `I` | ampere (`A`) | Fluxo de carga elétrica |
| Resistência | `R` | ohm (`Ω`) | Oposição à passagem de corrente |

A relação entre essas grandezas em muitos circuitos resistivos é descrita pela Lei de Ohm:

```text
V = R × I
```

Também podemos reorganizá-la:

```text
I = V / R
R = V / I
```

### Por que um LED precisa de resistor?

O LED permite a passagem de corrente principalmente em um sentido e possui polaridade:

- **ânodo:** terminal positivo, geralmente a perna mais longa;
- **cátodo:** terminal negativo, geralmente a perna mais curta e o lado achatado do encapsulamento.

Sem limitação de corrente, um LED externo pode ser danificado e também pode exigir corrente excessiva do pino da placa. Por isso, usamos um resistor em série. Para as práticas iniciais com Arduino Uno, valores entre `220 Ω` e `330 Ω` são comuns.

> Nunca conecte um LED externo diretamente entre um pino de saída e o `GND` sem um resistor adequado.

### Circuito aberto, fechado e curto-circuito

- **Circuito aberto:** o caminho está interrompido; não há corrente no caminho.
- **Circuito fechado:** existe um caminho completo para a corrente.
- **Curto-circuito:** dois pontos com diferença de potencial são ligados por uma resistência muito baixa. Isso pode causar corrente elevada e danificar componentes.

Antes de energizar uma montagem, confira se alimentação e `GND` não estão conectados diretamente.

---

## 4. Protoboard

A protoboard permite montar e alterar circuitos sem solda. Os furos são conectados internamente em grupos.

Em um modelo comum:

- as cinco posições de uma mesma linha, de cada lado do canal central, são conectadas;
- os dois lados do canal central não são conectados entre si;
- as linhas laterais de alimentação costumam ser conectadas ao longo do comprimento;
- algumas protoboards possuem os barramentos laterais interrompidos no meio.

### Antes de montar

1. Observe as marcações `+` e `−` dos barramentos.
2. Não presuma que o barramento percorre toda a placa: confirme visualmente ou com o modo continuidade do multímetro.
3. Mantenha fios curtos e use cores consistentes, como vermelho para alimentação e preto para `GND`.
4. Desligue a alimentação antes de alterar conexões.

### Ligação opcional de um LED externo

Uma ligação possível é:

```text
pino digital → resistor de 220 Ω a 330 Ω → ânodo do LED
cátodo do LED → GND
```

O resistor pode aparecer antes ou depois do LED; como os elementos estão em série, sua função de limitar a corrente é preservada.

---

## 5. Multímetro

O multímetro é usado para medir grandezas elétricas e verificar conexões. As funções mais úteis nesta etapa são tensão contínua, resistência e continuidade.

### Preparação

- conecte a ponta preta ao terminal `COM`;
- conecte a ponta vermelha ao terminal indicado para tensão e resistência;
- confira a posição do seletor antes de tocar no circuito;
- se o instrumento não tiver seleção automática de escala, comece por uma escala maior que o valor esperado.

### Medição de tensão contínua

A tensão é medida **em paralelo** e pode ser verificada com o circuito ligado:

1. selecione tensão contínua, normalmente indicada por `V` com uma linha contínua;
2. encoste a ponta preta na referência `GND`;
3. encoste a ponta vermelha no ponto que deseja medir;
4. leia o valor e observe o sinal exibido.

### Continuidade e resistência

Essas medições devem ser feitas com o circuito **desligado**:

1. remova a alimentação;
2. selecione continuidade ou resistência;
3. encoste as pontas nos dois pontos a verificar;
4. no modo continuidade, um aviso sonoro geralmente indica resistência muito baixa.

### Medição de corrente

A corrente é medida com o multímetro **em série**, o que exige abrir o circuito. Além disso, pode ser necessário mover a ponta vermelha para outro terminal do instrumento.

> Uma conexão errada no modo corrente pode criar um curto-circuito. Faça esse tipo de medição somente com orientação. Ao terminar, devolva a ponta vermelha ao terminal usado para tensão e resistência.

---

## 6. Sensores e atuadores

### Sensores

Sensores transformam uma grandeza do ambiente em uma informação que o sistema consegue ler.

Exemplos:

- botão: estado pressionado ou solto;
- LDR: variação relacionada à luminosidade;
- sensor de temperatura: medida de temperatura;
- sensor ultrassônico: estimativa de distância.

### Atuadores

Atuadores transformam comandos do sistema em uma ação perceptível ou física.

Exemplos:

- LED: sinal luminoso;
- buzzer: sinal sonoro;
- motor: movimento;
- relé: chaveamento de outro circuito;
- display: informação visual.

Um mesmo projeto pode combinar vários sensores e atuadores. No primeiro exercício, o LED interno é o atuador e o tempo decorrido é a condição usada pelo programa para alternar seu estado.

---

## 7. Fundamentos de C/C++ para Arduino

Os programas do Arduino, chamados de *sketches*, são escritos usando C/C++ com funções e bibliotecas fornecidas pelo ecossistema Arduino.

### Estrutura mínima

```cpp
void setup() {
  // Executado uma vez quando a placa inicia ou reinicia.
}

void loop() {
  // Executado repetidamente enquanto a placa estiver ligada.
}
```

### Variáveis e constantes

Uma variável associa um nome a um valor que pode ser usado ou alterado.

```cpp
int contador = 0;
float temperatura = 24.5;
char letra = 'A';
bool ligado = false;
```

Quando um valor não deve mudar, declare uma constante:

```cpp
const int TEMPO_ESPERA = 1000;
```

Nomes claros tornam o programa mais fácil de entender. Prefira `tempoEspera` ou `TEMPO_ESPERA` a nomes genéricos como `x`.

### Tipos usados neste início

| Tipo | Exemplo | Uso comum |
| --- | --- | --- |
| `int` | `int tentativas = 3;` | Números inteiros |
| `float` | `float tensao = 3.3;` | Números com casas decimais |
| `char` | `char comando = 'L';` | Um caractere |
| `bool` | `bool ativo = true;` | Verdadeiro ou falso |

O tamanho e o intervalo de alguns tipos dependem da arquitetura da placa. Esse assunto será retomado ao estudar memória e arquitetura de microcontroladores.

### Operadores

```cpp
contador = contador + 1;  // atribuição e soma
contador++;               // incremento equivalente para este caso

temperatura > 30.0        // maior que
contador == 10            // igual a; não confundir com atribuição
ativo && conectado        // E lógico
erro || emergencia        // OU lógico
!ativo                    // negação
```

### Controle de fluxo

#### Condicional `if`

```cpp
if (temperatura > 30.0) {
  ligado = true;
} else {
  ligado = false;
}
```

#### Repetição `for`

```cpp
for (int i = 0; i < 5; i++) {
  // Este bloco será executado cinco vezes.
}
```

#### Repetição `while`

```cpp
while (contador < 5) {
  contador++;
}
```

Em sistemas embarcados, todo laço deve possuir uma condição de saída adequada ou ser intencionalmente contínuo. Um laço infinito no lugar errado pode impedir que o restante do sistema responda.

### Funções usadas no exemplo

- `pinMode(pino, modo)`: configura um pino como entrada ou saída;
- `digitalWrite(pino, valor)`: escreve `HIGH` ou `LOW` em um pino digital configurado como saída;
- `delay(tempo)`: interrompe a execução pelo número informado de milissegundos.

### Exemplo Blink comentado

```cpp
const int TEMPO_ESPERA = 1000;

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  delay(TEMPO_ESPERA);

  digitalWrite(LED_BUILTIN, LOW);
  delay(TEMPO_ESPERA);
}
```

O fluxo é o seguinte:

1. `TEMPO_ESPERA` recebe `1000`, que representa `1000 ms` ou `1 s`.
2. `setup()` configura o pino do LED interno como saída.
3. `loop()` coloca o pino em nível alto e aguarda um segundo.
4. O pino passa para nível baixo e o programa aguarda outro segundo.
5. Ao chegar ao fim de `loop()`, a execução recomeça automaticamente.

> `delay()` é adequado para o primeiro exemplo, mas bloqueia a execução. Em projetos que precisam realizar várias tarefas, outras estratégias de temporização serão mais apropriadas.

---

## 8. Prática 1 — Simulação no Tinkercad

O Tinkercad Circuits permite montar circuitos virtuais e executar programas sem uma placa física.

### Criar o circuito

1. Acesse [Tinkercad Circuits](https://www.tinkercad.com/circuits).
2. Entre em sua conta.
3. Crie um novo circuito.
4. Localize `Arduino Uno R3` na lista de componentes.
5. Arraste a placa para a área de trabalho.
6. Renomeie o projeto para `semana-01-led-interno`.

### Inserir o programa

1. Selecione **Código**.
2. Troque o modo de blocos para **Texto**. Se a plataforma exibir um aviso, confirme a alteração.
3. Apague o código inicial.
4. Insira o exemplo Blink comentado da seção anterior.
5. Selecione **Iniciar simulação**.

O LED identificado com `L` na placa virtual deve permanecer um segundo aceso e um segundo apagado.

### Testar uma alteração

Troque o valor de `TEMPO_ESPERA` para `200` e reinicie a simulação:

```cpp
const int TEMPO_ESPERA = 200;
```

Perguntas para observar:

- O LED pisca mais rápido ou mais devagar?
- Quanto tempo dura um ciclo completo de acender e apagar?
- Qual linha determina o tempo de cada estado?

### Exportar o código

Copie o código textual para um arquivo chamado `semana-01-led-interno.ino`. Se usar a opção de download do Tinkercad, confira o arquivo gerado antes de adicioná-lo ao repositório.

---

## 9. Prática 2 — Arduino IDE e placa física

### Links oficiais

- [Download oficial da Arduino IDE](https://www.arduino.cc/en/software)
- [Guia oficial de download e instalação](https://support.arduino.cc/hc/en-us/articles/360019833020-Download-and-install-Arduino-IDE)
- [Tutorial oficial da Arduino IDE 2](https://docs.arduino.cc/software/ide-v2/tutorials/getting-started/ide-v2-downloading-and-installing/)
- [Como selecionar placa e porta](https://support.arduino.cc/hc/en-us/articles/4406856349970-Select-board-and-port-in-Arduino-IDE)
- [Como enviar um sketch para a placa](https://support.arduino.cc/hc/en-us/articles/4733418441116-Upload-a-sketch-in-Arduino-IDE)
- [Exemplo oficial Blink](https://docs.arduino.cc/built-in-examples/basics/Blink)
- [Referência oficial da linguagem](https://docs.arduino.cc/language-reference/)

Use a versão atual da **Arduino IDE 2** disponível na página oficial. Evite sites de terceiros para baixar o instalador.

### Instalação resumida

#### Windows

1. Baixe o instalador para Windows na página oficial.
2. Execute o arquivo `.exe`.
3. Siga o assistente de instalação.
4. Abra a Arduino IDE.

#### macOS

1. Escolha o download correspondente ao processador do computador.
2. Abra o arquivo `.dmg`.
3. Mova a Arduino IDE para a pasta de aplicativos.
4. Inicie o aplicativo.

#### Linux

1. Baixe o AppImage ou o arquivo ZIP de 64 bits disponível na página oficial.
2. No caso do AppImage, permita a execução do arquivo.
3. Abra a Arduino IDE.
4. Se houver problemas com acesso USB ou dependências, consulte o guia oficial de instalação.

### Abrir e verificar o Blink

1. Conecte a placa ao computador com um cabo USB que transmita dados.
2. Abra a Arduino IDE.
3. Acesse **File > Examples > 01.Basics > Blink**.
4. Clique em **Verify** para compilar o código.
5. Leia a área de saída e confirme que a compilação terminou sem erros.

Verificar compila o programa, mas não o envia à placa. Essa etapa detecta problemas de sintaxe e de configuração.

### Selecionar placa e porta

1. Use o seletor de placa da IDE ou abra o menu **Tools**.
2. Selecione o modelo correto, por exemplo, `Arduino Uno`.
3. Selecione a porta associada à placa.
4. Se a placa não aparecer, teste outro cabo ou outra porta USB e consulte a documentação oficial.

> Cabos destinados apenas a carregamento fornecem energia, mas não transportam dados. Esse é um motivo comum para a placa não aparecer.

### Enviar o programa

1. Clique em **Upload**.
2. Aguarde a compilação e o envio.
3. Confirme a mensagem de conclusão na área de saída.
4. Observe o LED interno da placa.

Depois, substitua o código pelo exemplo comentado deste material, envie novamente e compare os resultados.

### Diagnóstico rápido

| Sintoma | Possível causa | Verificação |
| --- | --- | --- |
| A placa não aparece | Cabo sem dados ou porta USB | Trocar cabo e porta |
| Erro ao enviar | Placa ou porta incorreta | Conferir seletores da IDE |
| Código não compila | Erro de sintaxe | Ler a primeira mensagem de erro |
| LED não pisca | LED interno diferente ou ausente | Consultar a documentação da placa |
| Pisca rápido demais | Tempo em milissegundos muito pequeno | Conferir o argumento de `delay()` |
| Programa antigo continua executando | O envio falhou | Conferir a mensagem final da IDE |

Ao pedir ajuda, registre:

- modelo da placa;
- sistema operacional;
- versão da IDE;
- código completo;
- texto completo da primeira mensagem de erro;
- foto da montagem, se houver circuito externo.

---

## 10. Registrando o projeto com Git e GitLab

Git registra o histórico dos arquivos localmente. GitLab hospeda o repositório e facilita o compartilhamento do projeto.

### Estrutura sugerida

```text
semana-01-led-interno/
├── README.md
└── semana-01-led-interno.ino
```

O `README.md` do exercício deve informar:

- objetivo;
- placa utilizada ou simulada;
- explicação resumida do código;
- instruções para executar;
- evidência do resultado;
- respostas pedidas na lista de exercícios.

### Fluxo básico em um repositório já clonado

```bash
git status
git add semana-01-led-interno.ino README.md
git commit -m "Adiciona exercício do LED interno"
git push
```

### Fluxo básico para um diretório novo

```bash
git init
git add .
git commit -m "Adiciona exercício do LED interno"
git branch -M main
git remote add origin URL_DO_REPOSITORIO
git push -u origin main
```

Substitua `URL_DO_REPOSITORIO` pela URL fornecida pelo GitLab. Antes de cada commit, use `git status` para conferir exatamente quais arquivos serão registrados.

Links úteis:

- [Documentação oficial do Git](https://git-scm.com/doc)
- [Documentação do GitLab sobre Git](https://docs.gitlab.com/topics/git/)
- [Criar um projeto no GitLab](https://docs.gitlab.com/user/project/)

---

## Videoaulas

Os vídeos abaixo complementam o material escrito. Alguns usam interfaces ou exemplos diferentes dos apresentados aqui; concentre-se nos conceitos e utilize os links oficiais deste documento para obter as versões atuais das ferramentas.

### Introdução e ferramentas

- [Tinkercad — tutorial para iniciantes](https://www.youtube.com/watch?v=gOs6Mdj7y_4)
- [O que é Arduino, afinal de contas?](https://www.youtube.com/watch?v=sv9dDtYnE1g)
- [Sensores e atuadores](https://www.youtube.com/watch?v=Sp-EDi3Dh0E)

### Programação

- [Curso de linguagem C/C++](https://www.youtube.com/playlist?list=PLkgVKRZdQfL-qDepFdoLHLPkei3GAEE9o)
- [Curso de Arduino com simulação](https://www.youtube.com/playlist?list=PLhPlCBM3MH4EUp19uXz8AZhA9os_AbzzO)

### Ordem recomendada

1. Assista ao vídeo introdutório sobre Arduino.
2. Assista ao material sobre sensores e atuadores.
3. Veja o tutorial do Tinkercad antes da primeira simulação.
4. Use o curso de C/C++ para reforçar variáveis, tipos e controle de fluxo.
5. Acompanhe o curso de Arduino com simulação durante as atividades práticas.

---

## Lista de exercícios

### Exercício 1 — Reconhecendo o sistema

Escolha dois objetos eletrônicos do cotidiano. Para cada um, indique:

1. qual é sua função principal;
2. quais podem ser suas entradas;
3. qual processamento ou regra ele realiza;
4. quais são suas saídas;
5. se ele é apenas um sistema embarcado ou também pode ser considerado IoT.

### Exercício 2 — Variáveis e tipos

Indique um tipo C/C++ adequado para cada informação e justifique:

1. estado de um LED, ligado ou desligado;
2. quantidade de vezes que um botão foi pressionado;
3. temperatura com casas decimais;
4. uma letra recebida como comando;
5. tempo de espera do Blink.

### Exercício 3 — Leitura do Blink

Sem executar o programa, responda:

1. Quantas vezes `setup()` é executada após o reset?
2. Quantas vezes `loop()` é executada?
3. O que aconteceria sem `pinMode(LED_BUILTIN, OUTPUT)`?
4. Qual é a unidade usada por `delay()`?
5. Quanto dura um ciclo completo quando cada `delay()` recebe `1000`?

### Exercício 4 — Frequências diferentes

Simule três versões do programa usando os valores abaixo para `TEMPO_ESPERA`:

- `1000`;
- `500`;
- `100`.

Registre para cada versão:

- tempo aceso;
- tempo apagado;
- duração total de um ciclo;
- quantidade teórica de ciclos em dez segundos.

### Exercício 5 — Tempos assimétricos

Altere o código para manter o LED:

- aceso por `200 ms`;
- apagado por `800 ms`.

Use duas constantes com nomes claros. Explique por que o ciclo completo continua durando um segundo.

### Exercício 6 — Sinal de identificação

Crie uma sequência que repita continuamente:

1. três piscadas rápidas;
2. uma pausa de dois segundos.

Evite copiar o mesmo bloco de comandos três vezes: use uma estrutura `for`.

### Exercício 7 — Protoboard e multímetro

Com o circuito desligado:

1. use a função de continuidade para mapear uma linha da protoboard;
2. verifique se os barramentos laterais são interrompidos no meio;
3. desenhe as conexões internas identificadas;
4. peça a validação do monitor antes de energizar qualquer montagem.

### Exercício 8 — LED externo opcional

Monte ou simule um LED externo com resistor entre `220 Ω` e `330 Ω`. Controle-o com um pino digital diferente do LED interno.

O registro deve conter:

- pino escolhido;
- valor do resistor;
- polaridade do LED;
- diagrama ou captura da montagem;
- código utilizado.

### Exercício 9 — Dois LEDs

Use o LED interno e um LED externo. Faça-os alternar: quando um estiver aceso, o outro deverá estar apagado.

Desafio: altere o programa para que os dois pisquem juntos cinco vezes e depois alternem cinco vezes.

### Exercício 10 — Investigação de erro

O código abaixo não funciona como esperado. Encontre os problemas, corrija-os e explique cada correção.

```cpp
const int tempo = 1000

void setup() {
  pinMode(LED_BUILTIN, INPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH)
  delay(tempo);
  digitalWrite(LED_BUILTIN, LOW);
}
```

---

## Entrega da semana

Entregue um link para o repositório no GitLab contendo:

- código `.ino` do LED interno;
- `README.md` com objetivo, instruções de execução e explicação do código;
- captura da simulação ou foto da placa executando o programa;
- respostas dos exercícios 1 a 6 e 10;
- exercícios 7 a 9 quando houver os componentes necessários;
- histórico de commits que mostre a evolução do trabalho.

### Critérios de conferência

- [ ] O código compila.
- [ ] O LED apresenta o comportamento solicitado.
- [ ] `LED_BUILTIN` foi usado no exemplo principal.
- [ ] Variáveis e constantes possuem nomes compreensíveis.
- [ ] O código está indentado.
- [ ] O README permite que outra pessoa reproduza o exercício.
- [ ] A evidência de execução foi incluída.
- [ ] O repositório não contém senhas, tokens ou arquivos desnecessários.

---

## Glossário

- **ADC:** conversor analógico-digital, responsável por transformar uma tensão medida em um valor numérico.
- **Atuador:** componente que executa uma ação a partir de um comando.
- **Compilar:** transformar o código-fonte em um programa que o microcontrolador consegue executar.
- **GND:** referência elétrica de terra do circuito.
- **IDE:** ambiente integrado usado para escrever, compilar e enviar programas.
- **IoT:** Internet das Coisas; objetos conectados que coletam, trocam ou usam dados.
- **Microcontrolador:** circuito integrado programável com processador, memória e periféricos.
- **Pino:** conexão elétrica do microcontrolador ou da placa.
- **Sensor:** componente que transforma uma condição do ambiente em informação.
- **Sketch:** nome dado a um programa no ecossistema Arduino.
- **Upload:** envio do programa compilado para a placa.

## Referências oficiais

- [Arduino — documentação](https://docs.arduino.cc/)
- [Arduino — download da IDE](https://www.arduino.cc/en/software)
- [Arduino — instalação da IDE](https://support.arduino.cc/hc/en-us/articles/360019833020-Download-and-install-Arduino-IDE)
- [Arduino — referência da linguagem](https://docs.arduino.cc/language-reference/)
- [Arduino — exemplos integrados](https://docs.arduino.cc/built-in-examples)
- [Arduino — exemplo Blink](https://docs.arduino.cc/built-in-examples/basics/Blink)
- [Autodesk Tinkercad](https://www.tinkercad.com/)
- [Git — documentação](https://git-scm.com/doc)
- [GitLab — documentação](https://docs.gitlab.com/)

[Voltar ao cronograma](../README.md#cronograma)
