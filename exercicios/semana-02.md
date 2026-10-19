# Lista de exercícios — Semana 02

## Programação básica usada em microcontroladores

Esta lista contém oito questões objetivas autorais inspiradas no formato do ENADE, uma questão discursiva e um desafio de programação. Ao final, há uma trilha complementar com três problemas do LeetCode.

## Orientações

- Nas questões objetivas, assinale apenas uma alternativa e justifique sua escolha.
- Nas questões de código, analise tipos, escopo, fluxo e uso de memória antes de executar.
- Teste a questão discursiva e o desafio no Tinkercad ou em uma placa física.
- Registre código, circuito e evidências no repositório.
- Resolva os problemas do LeetCode em C++.
- Não consulte o gabarito antes de concluir sua primeira tentativa.

## Distribuição sugerida da nota

| Questões | Valor |
| --- | ---: |
| 1 a 8 — objetivas | `0,5` ponto cada |
| 9 — discursiva | `2,0` pontos |
| 10 — desafio | `4,0` pontos |
| **Total** | **`10,0` pontos** |

Os três exercícios do LeetCode são complementares e não entram nessa pontuação.

---

## Questão 1 — Ciclo de varredura do CLP

**Nível: fácil**

Um CLP controla uma esteira industrial. A cada ciclo, ele verifica sensores, aplica as regras programadas e comanda motores e sinalizadores.

Qual sequência representa melhor um ciclo de varredura simplificado?

A. Atualizar saídas → desligar a CPU → ler entradas → compilar o programa.

B. Compilar o programa → gravar a memória → trocar os sensores → atualizar saídas.

C. Ler entradas → executar o programa → atualizar saídas → repetir o ciclo.

D. Ler saídas → executar entradas → apagar a memória → reiniciar o CLP.

E. Atualizar a rede → ignorar entradas → executar apenas uma vez → desligar.

---

## Questão 2 — Escolha da plataforma

**Nível: fácil**

Uma indústria deseja controlar uma estação de bombeamento instalada em ambiente severo. A equipe de manutenção já trabalha com módulos industriais padronizados, redes de automação e diagnóstico em campo. Em outro projeto, a empresa precisa produzir milhares de sensores compactos, alimentados por bateria e com baixo custo unitário.

Considerando apenas essas informações, a escolha mais coerente é:

A. usar obrigatoriamente microcontroladores nos dois projetos, pois CLPs não executam programas.

B. considerar um CLP para a estação e um microcontrolador para o sensor compacto, validando os demais requisitos de cada projeto.

C. usar um CLP no sensor compacto porque CLPs sempre consomem menos energia.

D. rejeitar as duas tecnologias, pois nenhuma pode trabalhar com sensores.

E. usar a mesma plataforma nos dois projetos, pois ambiente, manutenção e escala de produção não influenciam a decisão.

---

## Questão 3 — Declaração de função

**Nível: fácil**

Um programa precisa converter uma temperatura recebida em graus Celsius para Fahrenheit e devolver um valor com casas decimais.

Qual declaração de função atende corretamente a esse objetivo?

A. `void celsiusParaFahrenheit() { return celsius * 1.8 + 32.0; }`

B. `int celsiusParaFahrenheit { float celsius; }`

C. `float celsiusParaFahrenheit(float celsius) { return celsius * 1.8 + 32.0; }`

D. `char celsiusParaFahrenheit(float) = celsius * 1.8 + 32.0;`

E. `return celsiusParaFahrenheit(float celsius);`

---

## Questão 4 — Passagem por valor e escopo

**Nível: intermediário**

Analise o código:

```cpp
int dobrar(int valor) {
  valor = valor * 2;
  return valor;
}

void setup() {
  Serial.begin(9600);

  int numero = 5;
  int resultado = dobrar(numero);

  Serial.print(numero);
  Serial.print(" ");
  Serial.println(resultado);
}

void loop() {
}
```

Qual saída será exibida e por quê?

A. `10 10`, pois o parâmetro sempre altera a variável original.

B. `5 5`, pois uma função não pode devolver valores.

C. `5 10`, pois `valor` recebe uma cópia de `numero`, e a função devolve a cópia modificada.

D. `10 5`, pois `return` restaura o valor anterior.

E. O programa não compila porque variáveis locais não podem ser passadas a funções.

---

## Questão 5 — Caracteres e strings

**Nível: intermediário**

Considere as afirmações:

I. O literal `'A'` representa um caractere, enquanto `"A"` representa uma sequência de caracteres.

II. A declaração `char texto[] = "IoT";` reserva espaço também para o terminador `\0`.

III. Para o vetor acima, `strlen(texto)` e `sizeof(texto)` produzem necessariamente o mesmo resultado.

IV. Dois vetores de caracteres devem ser comparados pelo conteúdo com uma função apropriada, como `strcmp()`, e não apenas com `==`.

É correto apenas o que se afirma em:

A. I e II.

B. I e III.

C. II e IV.

D. I, II e IV.

E. I, II, III e IV.

---

## Questão 6 — Organização e segurança da memória

**Nível: intermediário para difícil**

Ao revisar um programa embarcado, uma equipe faz as seguintes afirmações:

I. Variáveis locais automáticas e informações de chamadas de função normalmente utilizam a stack.

II. Memória obtida com `new` e nunca liberada pode causar vazamento.

III. Uma variável local declarada com `static` preserva seu valor entre chamadas da função.

IV. Criar e destruir objetos dinamicamente de forma contínua é sempre a estratégia mais segura em placas com pouca RAM.

É correto apenas o que se afirma em:

A. I e II.

B. I e IV.

C. II e III.

D. I, II e III.

E. II, III e IV.

---

## Questão 7 — Operações matemáticas e tipos

**Nível: difícil**

Um sensor produziu as leituras inteiras `7` e `8`. O programador escreveu:

```cpp
float mediaA = (7 + 8) / 2;
float mediaB = (7 + 8) / 2.0;
```

Assinale a alternativa correta.

A. `mediaA` e `mediaB` recebem `7.5`, pois o destino das duas expressões é `float`.

B. `mediaA` recebe `7.0` e `mediaB` recebe `7.5`, pois a primeira divisão ocorre entre inteiros.

C. `mediaA` recebe `7.5` e `mediaB` recebe `7.0`, pois `2.0` força uma divisão inteira.

D. Ambas recebem `7.0`, pois o Arduino não executa operações de ponto flutuante.

E. O código não compila porque literais inteiros não podem ser usados em uma expressão atribuída a `float`.

---

## Questão 8 — Função, memória e aritmética

**Nível: difícil**

Analise o código:

```cpp
int escala = 10;

int normalizar(int valor) {
  int limitado = constrain(valor, 0, 100);
  return limitado * escala / 100;
}

void setup() {
  Serial.begin(9600);

  int entrada = 75;
  int resultado = normalizar(entrada);

  Serial.println(resultado);
}

void loop() {
}
```

Avalie as afirmações.

I. O valor exibido será `7`, pois a expressão e o retorno utilizam inteiros.

II. A variável `entrada` continuará valendo `75`, pois o parâmetro é passado por valor.

III. A função consegue acessar `escala` porque ela foi declarada em escopo global.

IV. A função devolve `7.5`, pois `constrain()` converte automaticamente o resultado para `float`.

É correto apenas o que se afirma em:

A. I e II.

B. I e IV.

C. II e III.

D. I, II e III.

E. II, III e IV.

---

## Questão 9 — Discursiva: média, classificação e memória

**Nível: difícil**

Um dispositivo armazena cinco leituras inteiras de temperatura em um vetor. O programa deve calcular a média e classificá-la:

- abaixo de `20.0 °C`: `BAIXA`;
- de `20.0 °C` até menos de `30.0 °C`: `NORMAL`;
- a partir de `30.0 °C`: `ALTA`.

Elabore uma resposta que:

1. declare no `setup()` um vetor com cinco leituras de exemplo;
2. implemente `float calcularMedia(const int valores[], int quantidade)`;
3. trate `quantidade <= 0` sem realizar divisão por zero;
4. implemente uma função que receba a média e devolva a classificação;
5. exiba média e classificação no monitor serial;
6. explique parâmetro, argumento, retorno e escopo usando o próprio código;
7. explique por que a divisão deve produzir um valor de ponto flutuante;
8. explique o significado de `const` no parâmetro do vetor;
9. compare a memória usada pelo vetor com uma solução que alocasse as leituras dinamicamente.

---

## Questão 10 — Desafio de programação: controlador de reservatório

**Nível: desafio**

Implemente um controlador simplificado de enchimento de reservatório. O projeto deve ser executado no Tinkercad ou em uma placa física.

### Entradas simuladas

Use dois botões com `INPUT_PULLUP`:

- `SENSOR_BAIXO`, no pino `2`: ativo quando a água alcança o nível mínimo;
- `SENSOR_ALTO`, no pino `3`: ativo quando a água alcança o nível máximo.

Como os botões usam `INPUT_PULLUP`, pressionado corresponde a `LOW`.

### Saídas

- `LED_BUILTIN`: representa a bomba;
- LED amarelo no pino `8`: indica estado de enchimento;
- LED verde no pino `9`: indica reservatório cheio;
- LED vermelho no pino `10`: indica falha.

Use resistores adequados nos LEDs externos.

### Regras de controle

1. Quando a água estiver abaixo do sensor inferior, iniciar o enchimento.
2. Depois de iniciada, a bomba deve permanecer ligada até o sensor superior ser ativado.
3. Ao atingir o sensor superior, desligar a bomba e indicar reservatório cheio.
4. A bomba deve permanecer desligada enquanto a água ainda alcançar o sensor inferior.
5. Quando os dois sensores voltarem a ficar inativos, iniciar um novo ciclo.
6. Se o sensor superior estiver ativo enquanto o inferior estiver inativo, considerar uma combinação impossível: entrar em falha e desligar a bomba.
7. Quando a combinação de sensores voltar a ser válida, o controlador pode sair da falha de forma segura.

### Requisitos de software

- representar os estados com `enum`;
- criar funções distintas para leitura, validação, controle e atualização das saídas;
- não usar `String` para representar o estado interno;
- usar uma função que converta o estado para texto apenas durante o diagnóstico;
- publicar o estado no monitor serial no máximo duas vezes por segundo;
- realizar a temporização do diagnóstico com `millis()`, sem `delay()`;
- evitar alocação dinâmica (`new`, `delete`, `malloc` ou `free`);
- comentar as decisões de segurança;
- manter a bomba desligada durante inicialização e falha.

### Testes obrigatórios

Registre uma tabela com entrada, estado esperado e resultado observado para pelo menos estes casos:

| Sensor baixo | Sensor alto | Situação |
| :---: | :---: | --- |
| Inativo | Inativo | Reservatório abaixo do mínimo |
| Ativo | Inativo | Nível entre os sensores |
| Ativo | Ativo | Reservatório cheio |
| Inativo | Ativo | Combinação impossível/falha |

Além dos casos isolados, teste a sequência completa:

```text
vazio → nível intermediário → cheio → nível intermediário → vazio
```

### Entrega do desafio

1. Código-fonte completo.
2. Diagrama ou captura da montagem.
3. Tabela de testes preenchida.
4. Evidência do monitor serial.
5. Explicação do ciclo de controle.
6. Justificativa para o uso de estado interno.
7. Explicação de como o programa reage à condição impossível.

### Extensão opcional

Adicione um botão de reconhecimento de falha. O sistema só poderá sair de `FALHA` quando os sensores estiverem coerentes **e** o operador reconhecer o evento.

---

## Trilha complementar — LeetCode

Resolva os três problemas em C++. Eles não substituem as dez questões anteriores.

### LeetCode 412 — Fizz Buzz

[Abrir o problema](https://leetcode.com/problems/fizz-buzz/)

Treina:

- criação de função;
- repetição e condicionais;
- operador de resto `%`;
- conversão de números para texto;
- ordem das condições.

Antes de enviar, teste mentalmente os valores `3`, `5`, `15` e `16`.

### LeetCode 1480 — Running Sum of 1d Array

[Abrir o problema](https://leetcode.com/problems/running-sum-of-1d-array/)

Treina:

- vetores;
- índices e limites;
- acumulação;
- atualização *in-place*;
- complexidade de tempo e espaço.

Tente resolver modificando o próprio vetor e usando espaço extra constante.

### LeetCode 344 — Reverse String

[Abrir o problema](https://leetcode.com/problems/reverse-string/)

Treina:

- caracteres;
- vetores e posições de memória;
- troca de valores;
- dois índices ou ponteiros;
- modificação *in-place* com memória extra `O(1)`.

Implemente primeiro com índices. Depois, como estudo opcional, reescreva usando ponteiros.

### Registro no repositório

Para cada problema, registre:

- link do enunciado;
- estratégia usada;
- código aceito;
- complexidade de tempo;
- complexidade de espaço;
- pelo menos três casos de teste;
- principal erro ou aprendizado encontrado.

---

[Consultar o material da Semana 02](../docs/semana-02.md) · [Voltar ao cronograma](../README.md#cronograma)
