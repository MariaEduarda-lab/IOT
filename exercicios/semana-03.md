# Lista de exercícios — Semana 03

## Arquitetura de microcontroladores, pinout e ponteiros

Esta lista contém oito questões objetivas autorais inspiradas no formato do ENADE, uma questão discursiva e um desafio guiado de programação.

## Orientações

- Assinale apenas uma alternativa nas questões objetivas e justifique sua escolha.
- Considere o ESP32 clássico em uma placa ESP32-DevKitC com módulo ESP32-WROOM quando o enunciado não indicar outra variante.
- Consulte a documentação oficial da sua placa antes de realizar uma montagem.
- Não aplique 5 V diretamente aos GPIOs do ESP32.
- Execute os códigos somente depois de revisar pinos, alimentação, `GND` e resistores.
- Não consulte o gabarito antes de concluir sua primeira tentativa.

## Avaliação sugerida

| Questões | Valor |
| --- | ---: |
| 1 a 8 — objetivas | `0,75` ponto cada |
| 9 — discursiva | `4,0` pontos |
| **Total avaliado** | **`10,0` pontos** |
| 10 — desafio guiado | Autoestudo, sem nota e sem submissão |

> O desafio não precisa ser entregue e não será corrigido. Ele foi incluído para experimentação autônoma. Leia primeiro como o sistema funciona, tente implementá-lo e depois compare sua solução com o gabarito completo.

---

## Questão 1 — Blocos de um microcontrolador

**Nível: fácil**

Um estudante afirma que um microcontrolador é apenas uma CPU e que todo acesso a sensores depende de circuitos computacionais externos.

Qual alternativa corrige adequadamente essa afirmação?

A. Um microcontrolador reúne CPU, memórias e periféricos, podendo integrar GPIO, temporizadores e interfaces de comunicação no mesmo chip.

B. Um microcontrolador contém apenas memória; a CPU sempre fica na placa de desenvolvimento.

C. GPIO e temporizadores são recursos exclusivos de computadores pessoais.

D. Todo microcontrolador possui obrigatoriamente Wi-Fi e Bluetooth.

E. Periféricos não se comunicam com a CPU e funcionam sem configuração.

---

## Questão 2 — Chip, módulo e placa

**Nível: fácil**

Uma equipe encontra três documentos: o datasheet do chip ESP32, o datasheet do módulo ESP32-WROOM e o guia de uma ESP32-DevKitC.

Sobre esses níveis de documentação, assinale a alternativa correta.

A. Os três documentos são equivalentes porque todo módulo e toda placa expõem os mesmos pinos.

B. O chip define recursos internos; o módulo acrescenta elementos como flash e antena; a placa acrescenta alimentação, interface USB e conexões próprias.

C. A placa de desenvolvimento está contida fisicamente dentro do chip.

D. O datasheet do chip informa necessariamente a posição física de cada conector da placa.

E. A identificação do módulo é irrelevante para saber se GPIOs estão ocupados por PSRAM.

---

## Questão 3 — Escolha de pinos

**Nível: intermediário**

Um estudante precisa conectar um LED externo e um botão a uma ESP32-DevKitC com ESP32-WROOM. Ele deseja usar resistor interno no botão e evitar pinos conhecidos por participarem do boot ou da flash.

Qual proposta é a mais adequada como ponto de partida, ainda sujeita à conferência do esquema da placa?

A. LED no GPIO35 e botão no GPIO6 com `INPUT_PULLUP`.

B. LED no GPIO23 com resistor em série e botão no GPIO27 com `INPUT_PULLUP` ligado ao `GND`.

C. LED diretamente no GPIO23 sem resistor e botão ligado diretamente a `3,3 V` e `GND` ao mesmo tempo.

D. LED no GPIO34 e botão no GPIO39 com `INPUT_PULLUP`.

E. LED no GPIO12 e botão no GPIO0, ignorando os níveis durante o reset.

---

## Questão 4 — Restrições de GPIO

**Nível: intermediário**

Considere as afirmações sobre o ESP32 clássico:

I. GPIOs `34` a `39` são somente entrada e não possuem pull-up ou pull-down interno configurável por software.

II. GPIOs `6` a `11` são normalmente usados pela flash em módulos comuns e devem ser evitados na aplicação.

III. Pinos de *strapping* nunca influenciam a inicialização, pois só passam a funcionar depois de `setup()`.

IV. Como a placa pode ser alimentada por USB, todos os GPIOs aceitam sinais de `5 V`.

É correto apenas o que se afirma em:

A. I.

B. I e II.

C. II e III.

D. I, II e IV.

E. I, II, III e IV.

---

## Questão 5 — Debounce e filtro RC

**Nível: intermediário**

Considere as asserções e a relação proposta entre elas.

**I.** Um único pressionamento de botão pode ser interpretado como vários eventos por uma entrada digital.

**PORQUE**

**II.** Contatos mecânicos podem produzir transições rápidas durante a abertura e o fechamento; debounce por software ou condicionamento elétrico pode impedir que essas transições sejam tratadas como novos eventos.

Assinale a alternativa correta.

A. As asserções I e II são verdadeiras, e a II justifica corretamente a I.

B. As asserções I e II são verdadeiras, mas a II não justifica a I.

C. A asserção I é verdadeira, e a II é falsa.

D. A asserção I é falsa, e a II é verdadeira.

E. As asserções I e II são falsas.

---

## Questão 6 — Endereço e desreferenciação

**Nível: intermediário**

Analise o código:

```cpp
int valor = 7;
int* ponteiro = &valor;

*ponteiro = *ponteiro + 3;
```

Após a execução, qual alternativa descreve corretamente o estado das variáveis?

A. `valor` continua `7`, pois ponteiros armazenam uma cópia independente.

B. `valor` passa a `10`, e `ponteiro` continua armazenando o endereço de `valor`.

C. `ponteiro` passa a armazenar o número `10` como endereço.

D. `valor` passa a `3`, pois o operador `*` realiza multiplicação.

E. O código é inválido porque `&` só pode ser usado com funções.

---

## Questão 7 — Vetores e aritmética de ponteiros

**Nível: difícil**

Analise o trecho:

```cpp
int valores[] = {2, 4, 8};
int* p = valores;

*(p + 1) = 6;
int resultado = *p + *(p + 1) + p[2];
```

O valor de `resultado` será:

A. `10`, pois `p + 1` avança um byte.

B. `12`, pois o vetor original nunca pode ser alterado por ponteiro.

C. `14`, pois `p[2]` acessa o segundo elemento.

D. `16`, pois o segundo elemento passa a valer `6`.

E. Indefinido, pois o nome de um vetor não pode ser usado como ponteiro.

---

## Questão 8 — Validade de ponteiros e `volatile`

**Nível: difícil**

Uma revisão de firmware apresenta as afirmações:

I. Devolver o endereço de uma variável local automática produz um ponteiro inválido após o término da função.

II. Verificar `nullptr` antes da desreferenciação reduz o risco de acesso inválido, mas não prova que qualquer ponteiro não nulo seja válido.

III. `volatile` torna todas as operações atômicas e elimina a necessidade de sincronização entre tarefas ou núcleos.

IV. Acessar uma posição além do fim de um vetor pode corromper dados ou produzir comportamento indefinido.

É correto apenas o que se afirma em:

A. I e II.

B. I e III.

C. II e IV.

D. I, II e IV.

E. I, II, III e IV.

---

## Questão 9 — Discursiva: auditoria de circuito e código

**Nível: difícil**

Uma equipe propôs o seguinte circuito para uma ESP32-DevKitC com módulo ESP32-WROOM:

- LED externo no GPIO35, sem resistor;
- botão entre GPIO34 e `GND`, configurado com `INPUT_PULLUP`;
- saída digital de um sensor de `5 V` conectada diretamente ao GPIO27;
- GPIO6 reservado para uma futura saída;
- código que devolve o endereço de uma variável local:

```cpp
int* lerBotao() {
  int leitura = digitalRead(34);
  return &leitura;
}
```

Elabore uma auditoria que:

1. identifique e explique cada problema elétrico, de pinout ou de memória;
2. proponha pinos mais adequados para LED e botão;
3. apresente uma ligação correta para o LED e para o botão;
4. indique como adaptar com segurança o sinal de `5 V` do sensor;
5. substitua `lerBotao()` por uma função segura que receba `int* destino`, valide `nullptr`, grave a leitura e devolva sucesso ou falha;
6. explique por que o endereço da variável local não pode ser devolvido;
7. indique como o debounce seria incorporado sem usar `delay()`;
8. liste os documentos que devem ser consultados antes da montagem.

---

## Questão 10 — Desafio guiado: painel robusto de três canais

**Modalidade: autoestudo, sem nota e sem submissão**

Este desafio demonstra como usar um vetor de estruturas e ponteiros para reaproveitar a mesma lógica em vários botões.

### Como o sistema funciona

O painel possui três canais independentes. Cada canal contém:

- um botão configurado com `INPUT_PULLUP`;
- um LED externo com resistor;
- o estado necessário para debounce;
- o estado atual do LED;
- um contador de acionamentos válidos.

Quando um botão é pressionado e permanece estável pelo tempo de debounce:

1. o evento é aceito uma única vez;
2. o LED daquele canal alterna entre ligado e desligado;
3. o contador do canal é incrementado;
4. uma mensagem é exibida no monitor serial.

Manter o botão pressionado não gera novos eventos. Os outros canais continuam responsivos porque o programa não usa `delay()`.

### Pinos de referência

| Canal | Botão | LED |
| :---: | :---: | :---: |
| 1 | GPIO25 | GPIO18 |
| 2 | GPIO26 | GPIO19 |
| 3 | GPIO27 | GPIO23 |

Confirme esses pinos no modelo real da placa antes da montagem.

### O que você deve tentar implementar

1. Crie uma `struct Canal` com pinos, estados de debounce, estado do LED e contador.
2. Crie um vetor estático com três canais.
3. Escreva uma função `configurarCanal(Canal* canal)`.
4. Escreva uma função `detectarPressionamento(Canal* canal, unsigned long agora)`.
5. Percorra o vetor em `loop()` e passe o endereço de cada elemento às funções.
6. Faça o debounce com `millis()`.
7. Não use alocação dinâmica.
8. Verifique `nullptr` nas funções que recebem ponteiros.

### Roteiro de experimentação

1. Comece com apenas um canal.
2. Confirme que um pressionamento alterna o LED uma vez.
3. Mantenha o botão pressionado e confirme que o contador não aumenta.
4. Pressione rapidamente e observe o debounce.
5. Acrescente os outros dois canais sem duplicar a lógica.
6. Teste dois botões em sequência.
7. Compare o uso de memória estática com uma solução que criasse objetos com `new`.
8. Consulte o gabarito para estudar a solução completa e comparar decisões.

### O que aprender com o desafio

- reutilização de funções;
- vetor de estruturas;
- ponteiro para estrutura e operador `->`;
- aritmética de índices sem sair dos limites;
- estado persistente por canal;
- debounce não bloqueante;
- configuração segura de GPIOs;
- memória de tamanho previsível.

> Não envie este desafio. O objetivo é experimentar e usar o gabarito como material de estudo, não como modelo para avaliação.

---

[Consultar o material da Semana 03](../docs/semana-03.md) · [Voltar ao cronograma](../README.md#cronograma)
