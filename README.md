# Monitoria de Programação — IoT

Repositório de apoio à monitoria de programação do módulo de **Internet das Coisas (IoT)**. Aqui serão reunidos os conteúdos semanais, tutoriais, exemplos de código, referências e listas de exercícios.

> [!IMPORTANT]
> Este material é **exclusivamente complementar**. Ele não substitui as aulas, os materiais oficiais da disciplina nem as explicações, orientações, critérios e atividades apresentados pelo professor. Em caso de divergência, sempre prevalece o que for comunicado pelo professor responsável.

## Como usar este repositório

Cada semana possui um documento próprio na pasta [`docs`](docs/). Os documentos seguem a mesma estrutura: objetivos, assuntos, materiais, lista de exercícios e espaço para dúvidas. O conteúdo será ampliado ao longo do módulo.

## Cronograma

| Semana | Tema |
| :---: | --- |
| 01 | [Comece por aqui: LED interno, ambiente e fundamentos](docs/semana-01.md) |
| 02 | [Programação básica usada em microcontroladores](docs/semana-02.md) |
| 03 | [Arquitetura de microcontroladores e ponteiros](docs/semana-03.md) |
| 04 | [Sensores e conversão de valores analógicos para digitais](docs/semana-04.md) |
| 05 | [Comunicação a distância e início da atividade ponderada](docs/semana-05.md) |
| 06 | [Atuadores de saída informacionais](docs/semana-06.md) |
| 07 | [Transmissão de sinal e dashboards](docs/semana-07.md) |
| 08 | [Sistema de disparo de atuadores](docs/semana-08.md) |
| 09 | [Segurança e biometria em IoT](docs/semana-09.md) |
| 10 | [Revisão final do projeto](docs/semana-10.md) |

## Exercícios e gabaritos

Cada semana terá uma lista com dez questões: oito questões objetivas em estilo ENADE, uma questão discursiva e um desafio de programação. Quando o desafio for destinado apenas a autoestudo, isso será indicado no próprio enunciado e ele não precisará ser submetido.

| Semana | Lista | Gabarito |
| :---: | --- | --- |
| 01 | [Exercícios](exercicios/semana-01.md) | [Gabarito](gabaritos/semana-01.md) |
| 02 | [Exercícios](exercicios/semana-02.md) | [Gabarito](gabaritos/semana-02.md) |
| 03 | [Exercícios](exercicios/semana-03.md) | [Gabarito](gabaritos/semana-03.md) |

## Links principais

- [Documentação do Arduino](https://docs.arduino.cc/)
- [Referência da linguagem Arduino](https://docs.arduino.cc/language-reference/)
- [Arduino IDE](https://docs.arduino.cc/software/ide/)
- [Documentação do ESP32 (Espressif)](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/)
- [Tinkercad](https://www.tinkercad.com/)
- [GitLab](https://gitlab.com/)
- [Documentação do HiveMQ](https://docs.hivemq.com/)
- [Eclipse Mosquitto](https://mosquitto.org/)
- [Adafruit IO](https://io.adafruit.com/)
- [Ubidots](https://ubidots.com/)

## Organização

```text
.
├── README.md
├── docs/
│   ├── semana-01.md
│   ├── semana-02.md
│   ├── ...
│   └── semana-10.md
├── exercicios/
│   ├── semana-01.md
│   ├── semana-02.md
│   └── semana-03.md
└── gabaritos/
    ├── semana-01.md
    ├── semana-02.md
    └── semana-03.md
```

Novas pastas poderão ser acrescentadas quando houver exemplos executáveis, imagens ou arquivos de apoio. A sugestão é manter códigos em `exemplos/`, recursos visuais em `assets/` e materiais auxiliares em `recursos/`.

## Convenções

- Os exemplos devem informar a placa e os componentes utilizados.
- Toda montagem deve apresentar o circuito ou uma descrição clara das conexões.
- Credenciais, senhas, tokens e chaves de serviços não devem ser versionados.
- Dúvidas recorrentes e soluções devem ser registradas no documento da semana correspondente.
- Os gabaritos devem explicar o raciocínio e os critérios de correção, não apenas indicar respostas.

## Progresso

Os documentos semanais já estão organizados com os tópicos iniciais. Explicações, exercícios, exemplos e entregáveis serão desenvolvidos durante as próximas sessões de monitoria.
