# paw-topics

Aiven kafka topics for Team PAW

## Resette kafka offset for en consumer group

Det enkleste er å lage en ny consumer group.

Legg til en -v1 suffix på group.id'en, og bump den for hver gang du trenger å resette.

## Lese og/eller skrive til topics

### Krav

- [nais-cli](https://doc.nais.io/cli/)
- [kafkakat (kcat)](https://github.com/edenhill/kcat) (kan også kjøres v/docker)
- Vær koblet til naisdevice

#### Legg til bruker navn i acl for topic

Legg til ditt github-brukernavn i acl i topic.yml, f.eks.

```yml
acl:
  - team: paw
    application: maccyber
    access: readwrite
```

#### Lag secret

Lag secret i 10 dager

```sh
$ nais aiven create kafka maccyber paw -p nav-dev -s a-totally-random-secretname -e 10
2022/11/10 18:25:37 AivenApplication: 'maccyber' updated.
2022/11/10 18:25:37 use: 'nais aiven get kafka a-totally-random-secretname paw' to generate configuration secrets.
```

#### Hent kafka konfigurasjon

Henter kafka-konfigurasjon for `kcat`

```sh
$ nais aiven get kafka a-totally-random-secretname paw
2022/11/10 12:05:38 generating kafka config from secret a-totally-random-secretname
2022/11/10 12:05:38 configurations from secret 'a-totally-random-secretname' found here: '/tmp/aiven-secret-2613059212'.
```

#### Produser melding

Produser en melding med `kcat`, du blir da sendt inn i et interaktivt shell. Avslutt med `<CTRL+D>`

Eksempelet bruker topicen `paw.testplayground`

```sh
$ kafkacat -F /tmp/aiven-secret-2613059212/kcat.conf -t paw.testplayground -P
% Reading configuration from file kcat.conf
{"message":"hello paw"}
```

#### Consumer melding

```sh
$ kafkacat -F /tmp/aiven-secret-2613059212/kcat.conf -t paw.testplayground -C
% Reading configuration from file kcat.conf
{"message":"hello paw"}
% Reached end of topic paw.testplayground [0] at offset 1
```

#### Rydd opp filer

```sh
$ nais aiven tidy
2022/11/10 19:28:17 tidy: /tmp/aiven-secret-2613059212
```

# Kilder

- https://docs.aiven.io/docs/products/kafka/howto/kcat.html
- https://docs.nais.io/cli/commands/aiven/
