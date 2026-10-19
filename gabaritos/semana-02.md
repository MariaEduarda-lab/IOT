# Gabarito — Semana 02

## Programação básica usada em microcontroladores

> Consulte este documento depois de concluir uma primeira tentativa. Códigos diferentes das soluções de referência são válidos quando atendem aos requisitos e apresentam comportamento correto e seguro.

## Resumo das questões objetivas

| Questão | Resposta | Tema |
| :---: | :---: | --- |
| 1 | C | Ciclo de varredura |
| 2 | B | Escolha entre CLP e microcontrolador |
| 3 | C | Declaração de função |
| 4 | C | Passagem por valor |
| 5 | D | Caracteres e strings |
| 6 | D | Organização da memória |
| 7 | B | Divisão inteira |
| 8 | D | Função, escopo e aritmética |

---

## Questão 1

**Resposta: C.**

Em um modelo simplificado, o CLP lê as entradas, executa a lógica programada, atualiza as saídas e repete o processo. Diagnóstico e comunicação também podem fazer parte do ciclo, mas não alteram a sequência fundamental apresentada.

## Questão 2

**Resposta: B.**

O CLP tende a ser adequado ao contexto industrial descrito por oferecer integração com módulos, redes, diagnóstico e manutenção industrial. O microcontrolador tende a ser adequado ao dispositivo compacto, produzido em escala e alimentado por bateria. Essa é uma decisão inicial: requisitos de segurança, desempenho, certificação e custo total ainda precisam ser avaliados.

## Questão 3

**Resposta: C.**

A alternativa declara corretamente:

- retorno do tipo `float`;
- nome da função;
- parâmetro `float celsius`;
- corpo entre chaves;
- expressão devolvida por `return`.

As demais alternativas possuem retorno incompatível, parâmetros ausentes ou sintaxe inválida.

## Questão 4

**Resposta: C.**

O parâmetro `valor` recebe uma cópia de `numero`. A cópia é multiplicada por dois e devolvida, produzindo `resultado == 10`. A variável original não é alterada e continua valendo `5`. A saída é:

```text
5 10
```

Para alterar a variável original diretamente, a função poderia receber `int& valor`.

## Questão 5

**Resposta: D.**

As afirmações I, II e IV estão corretas.

- `'A'` é um `char`; `"A"` representa uma sequência terminada por `\0`.
- `"IoT"` contém três caracteres visíveis e o terminador, ocupando quatro posições no vetor declarado.
- Nesse caso, `strlen(texto)` resulta em `3`, enquanto `sizeof(texto)` resulta em `4`.
- `strcmp()` compara o conteúdo de strings C. Usar `==` com vetores ou ponteiros não compara todo o texto.

## Questão 6

**Resposta: D.**

As afirmações I, II e III estão corretas. A pilha normalmente sustenta chamadas e variáveis locais automáticas. Uma alocação perdida sem `delete` gera vazamento. Uma variável `static` mantém sua duração por toda a execução.

A afirmação IV é falsa. Alocações e liberações repetidas podem fragmentar o heap, além de introduzir falhas de alocação e erros de gerenciamento. Em sistemas pequenos, estruturas de tamanho fixo costumam oferecer comportamento mais previsível.

## Questão 7

**Resposta: B.**

Em `mediaA`, todos os operandos da divisão são inteiros. O resultado intermediário é `7`, só depois convertido para `float`, produzindo `7.0`.

Em `mediaB`, `2.0` é um literal de ponto flutuante. A operação preserva a parte fracionária e resulta em `7.5`.

## Questão 8

**Resposta: D.**

As afirmações I, II e III estão corretas.

Para `entrada == 75`, `constrain()` mantém o valor. A expressão inteira calcula `75 * 10 / 100`, isto é, `750 / 100`, cujo resultado inteiro é `7`. O parâmetro é uma cópia, portanto `entrada` não é modificada. `escala` possui escopo global e pode ser lida pela função.

`constrain()` não transforma automaticamente a expressão em ponto flutuante. Para preservar a fração, seria possível alterar o retorno e a expressão:

```cpp
float normalizar(int valor) {
  int limitado = constrain(valor, 0, 100);
  return limitado * 10.0 / 100.0;
}
```

---

## Questão 9 — Resposta esperada

### Exemplo de código

```cpp
float calcularMedia(const int valores[], int quantidade) {
  if (quantidade <= 0) {
    return 0.0;
  }

  long soma = 0;

  for (int i = 0; i < quantidade; i++) {
    soma += valores[i];
  }

  return static_cast<float>(soma) / quantidade;
}

const char* classificarTemperatura(float media) {
  if (media < 20.0) {
    return "BAIXA";
  }

  if (media < 30.0) {
    return "NORMAL";
  }

  return "ALTA";
}

void setup() {
  Serial.begin(9600);

  int leituras[] = {18, 21, 25, 27, 29};
  int quantidade = sizeof(leituras) / sizeof(leituras[0]);

  float media = calcularMedia(leituras, quantidade);
  const char* classificacao = classificarTemperatura(media);

  Serial.print("Media: ");
  Serial.print(media, 1);
  Serial.println(" C");

  Serial.print("Classificacao: ");
  Serial.println(classificacao);
}

void loop() {
}
```

### Resultado para os dados do exemplo

```text
Media: 24.0 C
Classificacao: NORMAL
```

### Elementos esperados na explicação

- `valores` e `quantidade` são parâmetros; `leituras` e `quantidade` usados na chamada fornecem os argumentos.
- `calcularMedia()` devolve um `float`; `classificarTemperatura()` devolve um ponteiro para texto constante.
- `leituras` é uma variável local de `setup()` e não pode ser acessada diretamente em outras funções.
- A conversão para `float` ocorre antes da divisão, preservando a parte fracionária.
- `const` no parâmetro impede que `calcularMedia()` altere os elementos por meio daquele acesso.
- O vetor local tem tamanho definido e uso de memória previsível. Uma solução dinâmica exigiria reservar e liberar memória, além de tratar falha de alocação e fragmentação.
- A soma usa `long` para ampliar o intervalo em relação a um `int` em arquiteturas nas quais `int` é menor. Ainda assim, os limites dos dados devem ser considerados.

### Critérios de correção — 2,0 pontos

| Critério | Valor |
| --- | ---: |
| Cálculo correto e proteção contra divisão por zero | `0,5` |
| Classificação correta nos três intervalos | `0,4` |
| Uso e exibição no `setup()` | `0,3` |
| Explicação de função, parâmetros, retorno e escopo | `0,3` |
| Explicação de ponto flutuante e `const` | `0,3` |
| Comparação de memória estática/local e dinâmica | `0,2` |

---

## Questão 10 — Solução de referência

### Estratégia

O controlador precisa memorizar se está enchendo ou aguardando o nível baixar. Apenas as leituras instantâneas `baixo ativo / alto inativo` não revelam qual dessas fases está ocorrendo. Por isso, o estado interno faz parte da regra.

### Exemplo de código

```cpp
const int PINO_SENSOR_BAIXO = 2;
const int PINO_SENSOR_ALTO = 3;

const int PINO_LED_ENCHENDO = 8;
const int PINO_LED_CHEIO = 9;
const int PINO_LED_FALHA = 10;

const unsigned long INTERVALO_DIAGNOSTICO_MS = 500;

enum Estado {
  ENCHENDO,
  CHEIO,
  FALHA
};

struct Entradas {
  bool nivelBaixoAtivo;
  bool nivelAltoAtivo;
};

Estado estado = FALHA;
unsigned long instanteUltimoDiagnostico = 0;

Entradas lerEntradas() {
  Entradas entradas;
  entradas.nivelBaixoAtivo =
      digitalRead(PINO_SENSOR_BAIXO) == LOW;
  entradas.nivelAltoAtivo =
      digitalRead(PINO_SENSOR_ALTO) == LOW;
  return entradas;
}

bool entradasSaoValidas(const Entradas& entradas) {
  // O nível superior não pode estar coberto se o inferior estiver seco.
  return !(entradas.nivelAltoAtivo &&
           !entradas.nivelBaixoAtivo);
}

Estado recuperarComSeguranca(const Entradas& entradas) {
  if (entradas.nivelAltoAtivo) {
    return CHEIO;
  }

  if (!entradas.nivelBaixoAtivo) {
    return ENCHENDO;
  }

  // Com apenas o sensor inferior ativo, o histórico foi perdido.
  // A opção conservadora é manter a bomba desligada.
  return CHEIO;
}

Estado calcularProximoEstado(
    Estado estadoAtual,
    const Entradas& entradas) {
  if (!entradasSaoValidas(entradas)) {
    return FALHA;
  }

  if (estadoAtual == FALHA) {
    return recuperarComSeguranca(entradas);
  }

  if (estadoAtual == ENCHENDO) {
    if (entradas.nivelAltoAtivo) {
      return CHEIO;
    }

    return ENCHENDO;
  }

  // Estado CHEIO: aguarda a água baixar do sensor inferior.
  if (!entradas.nivelBaixoAtivo) {
    return ENCHENDO;
  }

  return CHEIO;
}

void atualizarSaidas(Estado estadoAtual) {
  bool bombaLigada = estadoAtual == ENCHENDO;

  digitalWrite(
      LED_BUILTIN,
      bombaLigada ? HIGH : LOW
  );
  digitalWrite(
      PINO_LED_ENCHENDO,
      estadoAtual == ENCHENDO ? HIGH : LOW
  );
  digitalWrite(
      PINO_LED_CHEIO,
      estadoAtual == CHEIO ? HIGH : LOW
  );
  digitalWrite(
      PINO_LED_FALHA,
      estadoAtual == FALHA ? HIGH : LOW
  );
}

const char* estadoParaTexto(Estado estadoAtual) {
  switch (estadoAtual) {
    case ENCHENDO:
      return "ENCHENDO";
    case CHEIO:
      return "CHEIO";
    case FALHA:
      return "FALHA";
    default:
      return "DESCONHECIDO";
  }
}

void publicarDiagnostico(
    const Entradas& entradas,
    Estado estadoAtual,
    unsigned long agora) {
  if (agora - instanteUltimoDiagnostico <
      INTERVALO_DIAGNOSTICO_MS) {
    return;
  }

  instanteUltimoDiagnostico = agora;

  Serial.print("baixo=");
  Serial.print(entradas.nivelBaixoAtivo ? "1" : "0");
  Serial.print("; alto=");
  Serial.print(entradas.nivelAltoAtivo ? "1" : "0");
  Serial.print("; estado=");
  Serial.println(estadoParaTexto(estadoAtual));
}

void setup() {
  pinMode(PINO_SENSOR_BAIXO, INPUT_PULLUP);
  pinMode(PINO_SENSOR_ALTO, INPUT_PULLUP);

  pinMode(LED_BUILTIN, OUTPUT);
  pinMode(PINO_LED_ENCHENDO, OUTPUT);
  pinMode(PINO_LED_CHEIO, OUTPUT);
  pinMode(PINO_LED_FALHA, OUTPUT);

  // Garante condição segura antes de começar o controle.
  atualizarSaidas(FALHA);
  Serial.begin(9600);
}

void loop() {
  Entradas entradas = lerEntradas();
  estado = calcularProximoEstado(estado, entradas);

  atualizarSaidas(estado);
  publicarDiagnostico(entradas, estado, millis());
}
```

### Comportamento esperado

| Sensor baixo | Sensor alto | Estado resultante ou mantido | Bomba |
| :---: | :---: | --- | :---: |
| Inativo | Inativo | `ENCHENDO` | Ligada |
| Ativo | Inativo | `ENCHENDO` se já enchia; `CHEIO` se aguardava baixar | Depende do estado |
| Ativo | Ativo | `CHEIO` | Desligada |
| Inativo | Ativo | `FALHA` | Desligada |

O caso intermediário demonstra por que o estado anterior é necessário. A mesma combinação de sensores pode ocorrer enquanto o nível sobe ou desce.

### Decisões importantes

- O sistema inicia em `FALHA`, garantindo bomba desligada até a primeira avaliação.
- Uma combinação fisicamente incoerente conduz imediatamente a `FALHA`.
- Na recuperação sem histórico confiável e com nível intermediário, a referência escolhe manter a bomba desligada.
- `const char*` aponta para literais fixos, evitando criar objetos `String` apenas para diagnóstico.
- As estruturas possuem tamanho fixo e não utilizam heap explicitamente.
- A diferença entre valores de `millis()` permite diagnóstico periódico sem bloquear o controle.

### Critérios de correção — 4,0 pontos

| Critério | Valor |
| --- | ---: |
| Montagem, `INPUT_PULLUP` e saídas em condição segura | `0,5` |
| Modelagem coerente com `enum` e estado persistente | `0,6` |
| Regras de enchimento e parada | `0,8` |
| Detecção e tratamento seguro da condição impossível | `0,6` |
| Separação em funções | `0,5` |
| Diagnóstico não bloqueante com `millis()` | `0,4` |
| Ausência de alocação dinâmica e organização do código | `0,3` |
| Testes, evidências e explicação | `0,3` |

---

## Soluções da trilha LeetCode

As soluções abaixo são referências concisas. Compare-as com sua estratégia somente depois de obter uma solução própria.

### LeetCode 412 — Fizz Buzz

```cpp
class Solution {
 public:
  vector<string> fizzBuzz(int n) {
    vector<string> resposta;
    resposta.reserve(n);

    for (int i = 1; i <= n; i++) {
      if (i % 15 == 0) {
        resposta.push_back("FizzBuzz");
      } else if (i % 3 == 0) {
        resposta.push_back("Fizz");
      } else if (i % 5 == 0) {
        resposta.push_back("Buzz");
      } else {
        resposta.push_back(to_string(i));
      }
    }

    return resposta;
  }
};
```

- Tempo: `O(n)`.
- Espaço adicional, desconsiderando a saída: `O(1)`; a saída ocupa `O(n)`.
- O teste por `15` deve ocorrer antes dos testes individuais por `3` e `5`.

### LeetCode 1480 — Running Sum of 1d Array

```cpp
class Solution {
 public:
  vector<int> runningSum(vector<int>& nums) {
    for (int i = 1; i < static_cast<int>(nums.size()); i++) {
      nums[i] += nums[i - 1];
    }

    return nums;
  }
};
```

- Tempo: `O(n)`.
- Espaço adicional: `O(1)`.
- A solução reutiliza o próprio vetor, acumulando em cada posição o resultado anterior.

### LeetCode 344 — Reverse String

```cpp
class Solution {
 public:
  void reverseString(vector<char>& s) {
    if (s.empty()) {
      return;
    }

    size_t esquerda = 0;
    size_t direita = s.size() - 1;

    while (esquerda < direita) {
      swap(s[esquerda], s[direita]);
      esquerda++;
      direita--;
    }
  }
};
```

- Tempo: `O(n)`.
- Espaço adicional: `O(1)`.
- Cada troca coloca dois caracteres em suas posições finais.
- O teste `s.empty()` evita subtrair um de zero em um índice sem sinal.

---

[Voltar à lista de exercícios](../exercicios/semana-02.md) · [Consultar o material da Semana 02](../docs/semana-02.md)
