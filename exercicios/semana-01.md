# Lista de exercícios — Semana 01

## LED interno, fundamentos de C/C++, Arduino e eletrônica básica

Esta lista contém questões autorais inspiradas no formato do ENADE. As oito primeiras questões são objetivas e apresentam dificuldade crescente. A questão 9 é discursiva e a questão 10 é um desafio de programação.

## Orientações

- Leia todo o enunciado antes de escolher uma alternativa.
- Nas questões objetivas, assinale apenas uma alternativa.
- Justifique sua resposta mesmo quando a questão for objetiva. A justificativa ajuda a identificar dúvidas conceituais.
- Execute e teste os códigos das questões práticas.
- Registre código, diagrama do circuito e evidências de execução no repositório.
- Não consulte o gabarito antes de concluir sua primeira tentativa.

## Distribuição sugerida da nota

| Questões | Valor |
| --- | ---: |
| 1 a 8 — objetivas | `0,5` ponto cada |
| 9 — discursiva | `2,0` pontos |
| 10 — desafio | `4,0` pontos |
| **Total** | **`10,0` pontos** |

---

## Questão 1 — Sensores e atuadores

**Nível: fácil**

Uma equipe está desenvolvendo um sistema automático para iluminar um corredor. Um componente mede a luminosidade do ambiente, a placa Arduino processa essa informação e outro componente produz luz quando o ambiente está escuro.

Nesse sistema, os papéis desempenhados pelos três elementos são, respectivamente:

A. atuador, sensor e microcontrolador.

B. sensor, microcontrolador e atuador.

C. microcontrolador, atuador e sensor.

D. sensor, atuador e microcontrolador.

E. atuador, microcontrolador e sensor.

---

## Questão 2 — Sistemas embarcados e IoT

**Nível: fácil**

Considere os dispositivos a seguir:

I. Uma calculadora simples, programada para realizar operações matemáticas e sem conexão de rede.

II. Um sensor de temperatura que envia medições por Wi-Fi para um dashboard remoto.

III. Um controlador de máquina de lavar que executa ciclos armazenados localmente e não troca dados com outros sistemas.

Com base nos conceitos de sistemas embarcados e Internet das Coisas, assinale a alternativa correta.

A. Somente o dispositivo II é um sistema embarcado.

B. Os três dispositivos são necessariamente IoT porque utilizam software.

C. I e III podem ser sistemas embarcados, enquanto II pode ser um sistema embarcado e também uma solução IoT.

D. Apenas dispositivos conectados à internet podem ser considerados sistemas embarcados.

E. O dispositivo II não é embarcado, pois utiliza comunicação Wi-Fi.

---

## Questão 3 — Execução do Blink

**Nível: fácil**

Analise o trecho de código.

```cpp
const int TEMPO_ESPERA = 500;

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

Desconsiderando o pequeno tempo gasto para executar as demais instruções, o comportamento esperado do LED e a duração aproximada de um ciclo completo são:

A. aceso por `500 ms`, apagado por `500 ms`; ciclo de `1 s`.

B. aceso por `1 s`, apagado por `1 s`; ciclo de `2 s`.

C. aceso por `500 s`, apagado por `500 s`; ciclo de `1000 s`.

D. permanentemente aceso, pois `loop()` é executada apenas uma vez.

E. permanentemente apagado, pois `TEMPO_ESPERA` foi declarado como constante.

---

## Questão 4 — Tipos, atribuição e comparação

**Nível: intermediário**

Durante a implementação de um sistema, devem ser armazenados:

- o estado de um LED, que pode estar ligado ou desligado;
- uma temperatura com casas decimais;
- o número de acionamentos de um botão;
- uma letra recebida como comando.

Além disso, o programa precisa verificar se o número de acionamentos é igual a dez.

Qual alternativa apresenta tipos adequados e uma comparação correta?

A. `int ligado; int temperatura; float acionamentos; bool comando; acionamentos = 10`

B. `bool ligado; float temperatura; int acionamentos; char comando; acionamentos == 10`

C. `char ligado; bool temperatura; int acionamentos; float comando; acionamentos = 10`

D. `float ligado; char temperatura; bool acionamentos; int comando; acionamentos == 10`

E. `bool ligado; int temperatura; char acionamentos; float comando; acionamentos = 10`

---

## Questão 5 — Protoboard

**Nível: intermediário**

Antes de energizar uma montagem, uma estudante analisa as conexões internas de uma protoboard convencional e formula as afirmações a seguir.

I. Em geral, grupos de cinco furos de um mesmo lado do canal central são conectados internamente.

II. Furos posicionados em lados opostos do canal central são sempre conectados entre si.

III. Os barramentos laterais de alimentação são sempre contínuos por todo o comprimento, independentemente do modelo.

É correto o que se afirma em:

A. I, apenas.

B. II, apenas.

C. I e II, apenas.

D. II e III, apenas.

E. I, II e III.

---

## Questão 6 — Uso seguro do multímetro

**Nível: intermediário**

Um grupo precisa realizar três verificações em um circuito de baixa tensão: medir a tensão de alimentação, conferir a continuidade de uma trilha da protoboard e medir a corrente consumida pelo circuito.

Avalie os procedimentos propostos.

I. A tensão deve ser medida em paralelo, com o circuito energizado e o multímetro configurado para tensão contínua.

II. A continuidade pode ser medida com o circuito energizado, pois o multímetro precisa da tensão da fonte para emitir o aviso sonoro.

III. A corrente deve ser medida conectando o multímetro diretamente, em paralelo, aos terminais da fonte.

IV. Após medir corrente, é importante devolver a ponta vermelha ao terminal usado para tensão e resistência.

É correto apenas o que se afirma em:

A. I e II.

B. I e IV.

C. II e III.

D. I, III e IV.

E. II, III e IV.

---

## Questão 7 — LED e limitação de corrente

**Nível: intermediário para difícil**

Considere as asserções a seguir e a relação proposta entre elas.

**I.** Ao utilizar um LED externo com uma placa Arduino, deve-se empregar um resistor adequado em série com o componente.

**PORQUE**

**II.** O resistor limita a corrente do circuito, ajudando a proteger tanto o LED quanto o pino do microcontrolador.

A respeito dessas asserções, assinale a alternativa correta.

A. As asserções I e II são verdadeiras, e a II justifica corretamente a I.

B. As asserções I e II são verdadeiras, mas a II não justifica a I.

C. A asserção I é verdadeira, e a II é falsa.

D. A asserção I é falsa, e a II é verdadeira.

E. As asserções I e II são falsas.

---

## Questão 8 — Análise de algoritmo

**Nível: difícil**

Uma estudante deseja produzir três piscadas rápidas seguidas de uma pausa de dois segundos. Para isso, escreveu o programa abaixo.

```cpp
const int TEMPO_LIGADO = 200;
const int TEMPO_DESLIGADO = 200;
const int TEMPO_PAUSA = 2000;

void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  for (int i = 0; i < 3; i++) {
    digitalWrite(LED_BUILTIN, HIGH);
    delay(TEMPO_LIGADO);
    digitalWrite(LED_BUILTIN, LOW);
    delay(TEMPO_DESLIGADO);
  }

  delay(TEMPO_PAUSA);
}
```

Desconsiderando o tempo de execução das outras instruções, avalie as afirmações.

I. O bloco controlado pelo `for` é executado três vezes a cada execução de `loop()`.

II. Cada piscada completa dura aproximadamente `400 ms`.

III. Uma sequência completa, incluindo a pausa, dura aproximadamente `3,2 s`.

IV. A variável `i` continuará aumentando indefinidamente porque foi declarada dentro do `for`.

É correto apenas o que se afirma em:

A. I e II.

B. I e IV.

C. II e III.

D. I, II e III.

E. II, III e IV.

---

## Questão 9 — Discursiva: programação e explicação

**Nível: difícil**

Uma sinalização deve manter o LED interno aceso por `250 ms` e apagado por `750 ms`, repetindo esse comportamento continuamente.

Elabore uma resposta que:

1. apresente um programa Arduino completo;
2. utilize `LED_BUILTIN`;
3. utilize constantes diferentes para os tempos aceso e apagado;
4. explique a função de `setup()` e `loop()`;
5. explique o papel de `pinMode()`, `digitalWrite()` e `delay()`;
6. determine a duração de um ciclo completo e a quantidade teórica de ciclos executados em um minuto;
7. explique uma limitação provocada pelo uso de `delay()`.

---

## Questão 10 — Desafio de programação: sinalizador de modos

**Nível: desafio**

Desenvolva, no Tinkercad ou em uma placa física, um sinalizador controlado por botão.

### Componentes

- Arduino Uno;
- LED interno da placa;
- um botão conectado entre o pino digital `2` e o `GND`.

### Comportamento

O sistema deve possuir três modos:

| Modo | Comportamento do LED |
| --- | --- |
| 0 | Sempre apagado |
| 1 | Alterna o estado a cada `1000 ms` |
| 2 | Alterna o estado a cada `200 ms` |

A cada novo pressionamento válido do botão, o programa deve avançar para o próximo modo:

```text
0 → 1 → 2 → 0 → ...
```

### Requisitos obrigatórios

- configurar o botão com `INPUT_PULLUP`;
- considerar que, nessa configuração, botão pressionado corresponde a `LOW`;
- detectar o pressionamento, e não contar repetidamente enquanto o botão permanecer segurado;
- implementar *debounce* por software;
- usar `millis()` para controlar o LED, sem usar `delay()` no laço principal;
- usar constantes com nomes claros;
- manter o sistema responsivo ao botão em qualquer modo;
- comentar as partes mais importantes do programa.

### Entrega do desafio

1. Código-fonte completo.
2. Diagrama ou captura do circuito.
3. Evidência dos três modos em funcionamento.
4. Explicação da ligação com `INPUT_PULLUP`.
5. Explicação de por que `millis()` é mais apropriado que `delay()` neste sistema.
6. Breve descrição da estratégia de *debounce* adotada.

### Extensão opcional

Adicione um LED externo para indicar que o sistema reconheceu um pressionamento válido. Esse LED deve produzir um pulso curto sem bloquear a leitura do botão.

---

[Consultar o material da Semana 01](../docs/semana-01.md) · [Voltar ao cronograma](../README.md#cronograma)
