
# Perícia Digital e Análise Forense de Rede — BioFrio / VaxGuard Connect

## Identificação do Projeto
* **Módulo:** Módulo B
* **Nome da Equipa:** CodElas
* **Membros:** Maria Fernanda Sloboda e Melissa Tonini
* **Avaliadores:** @Cardoso30S | @fiorezewesley | @Greggori

---

##  Resumo Executivo do Incidente
Durante a operação da câmara `CAM03` (`192.168.20.31`), foi identificada uma excursão térmica severa (> 8,0 °C). A análise forense do ficheiro de captura `captura_biofrio_2026.pcap` confirmou que a anomalia **não decorreu de falha mecânica**, mas sim de uma **ação maliciosa por injeção de comandos de rede**.

* **Origem do Ataque:** IP `192.168.20.66` (Host não autorizado na rede).
* **Vetor de Ataque:** Injeção do comando `DESLIGAR` no tópico MQTT `biofrio/cam03/cmd` às 14:14:30 (Pacote 24).
* **Vulnerabilidades Exploradas:** Ausência de encriptação no transporte (HTTP e MQTT em texto claro) e falta de Listas de Controlo de Acesso (ACLs) no broker (`192.168.20.10`).

---

## Estrutura do Repositório


├── README.md                 # Apresentação do projeto e resumo do incidente
└── pericia/
    └── relatorio_pericia.pdf  # Relatório completo com respostas às Questões 1 a 4 e evidências do Wireshark




## 📋 Síntese das Respostas 

### Questão 1 — Mapeamento de Rede

* **Cliente / Servidor:** `192.168.20.31` (IPv4) / `192.168.20.10` (IPv4)
* **Protocolo & Porta:** MQTT / Porta `1883`
* **Placa & Tópico:** ESP32 / `biofrio/cam03/telemetria`

### Questão 2 — Credenciais Expostas

* **HTTP (Painel Web):** `tecnico.cam03` / `Vac@BioFrio2026` (`%40` = `@`) | Cookie: `session=c6a1f09b7d2e4418`
* **MQTT (Broker):** `vaxguard` / `Fr10#Cam03`

### Questão 3 — Análise da Injeção de Comando

* **Injeção:** Host `192.168.20.66` publicou comando no tópico `biofrio/cam03/cmd`.
* **Causa do Repasse:** O broker opera em Pub/Sub sem validação de ACLs, retransmitindo o comando para o dispositivo subscrito.
* **Efeito Térmico:** O desligamento desativou o compressor, elevando a temperatura de ~4 °C para mais de 8 °C (estado CRÍTICO).

### Questão 4 — Conclusão e Recomendações

* **Classificação:** Ação Maliciosa (Ataque Cibernético / Injeção Não Autorizada).
* **Medidas Recomendadas:**
1. Criptografia de Transporte via **TLS/HTTPS** e **MQTTS** (Porta 8883).
2. Implementação de **MQTT ACLs** restritas ao servidor legítimo.
3. Autenticação mútua com certificados digitais **mTLS (X.509)**.



> *O detalhamento técnico e as capturas de ecrã comprovativas do Wireshark estão disponíveis no PDF localizado na pasta `pericia/`.*

