# Meta-Prompt v2 — Service Blueprint AS-IS: Atendimento ao Seguro-Desemprego pela URA da Caixa

> **Versão:** 2.0 | **Baseada em:** auditoria de `B_relatorio_auditoria_v1.md`
> **Changelog completo:** ver seção final deste arquivo.

---

## Contexto e objetivo

Você é um especialista em design de serviços públicos e experiência do cidadão. Sua tarefa é **documentar o estado atual (AS-IS) do serviço de atendimento ao Seguro-Desemprego realizado pela URA (Unidade de Resposta Audível) da Caixa Econômica Federal**, produzindo um Service Blueprint completo com cinco dimensões analíticas obrigatórias.

O produto final deve ser utilizável por equipes de CX, gestores públicos e times de transformação digital para identificar oportunidades de melhoria, redesenho de processos e priorização de investimentos.

> **Escopo do canal:** a URA da Caixa (0800-726-0207) é um **canal formal** de acompanhamento de parcelas — não um canal primário de requerimento (que ocorre via gov.br, SINE/SRTE ou Empregador Web) e não um canal exclusivo de saque. Ela é **um dos vários pontos de contato do ecossistema**, com papel específico de consulta de situação e orientação de encaminhamento.

---

## Instrução principal

Estruture sua resposta nas cinco dimensões abaixo. Para cada dimensão, percorra **todas as seis etapas da jornada** na sequência:

> **E1 — Pré-contato & Habilitação** · **E2 — Acesso ao canal (URA/158/digital)** · **E3 — Navegação e autenticação** · **E4 — Consulta / Orientação** · **E5 — Processamento & Pagamento** · **E6 — Exceção: revisão, recurso e presencial**

---

## Dimensão A — Jornada do Cidadão (linha de frente)

Para cada etapa, descreva:
- **O que o cidadão está tentando fazer** (job-to-be-done)
- **Como ele age** (canais, dispositivo, comportamento)
- **O que ele sente** (frustrações, ansiedades, expectativas)
- **Restrições de tempo** que impactam sua ação (ex.: prazo de 7 a 120 dias pós-demissão conforme Lei 7.998/1990, art. 7º)
- **Pré-condição operacional:** o cidadão precisa ter recebido o **Documento de Requerimento do Seguro-Desemprego** emitido pelo empregador via **Empregador Web** (sistema MTE) para dar prosseguimento ao pedido — sem ele, o requerimento não pode ser efetivado

Considere perfis distintos:
- Trabalhador com baixa literacia digital
- Trabalhador com deficiência auditiva (ausência de canal alternativo à URA é um fail point crítico)
- Trabalhador em zona rural com acesso limitado à internet e à agência física
- Trabalhador de órgão público ou organismo internacional (fluxo pode divergir do eSocial padrão)

---

## Dimensão B — Processos de Bastidor (backstage)

Para cada etapa, identifique em formato de tabela:

| Etapa | Ator | Ação concreta | Sistema/plataforma | Dependência crítica |
|---|---|---|---|---|
| E1 | Empregador | Envia dados de desligamento | **eSocial** (regra geral para obrigados) ou **CAGED** (hipóteses específicas: órgãos públicos, organismos internacionais, optantes) | Sem envio, DATAPREV não executa pré-habilitação |
| E1 | Empregador | Emite Requerimento do SD | **Empregador Web** (sd.maisemprego.trabalho.gov.br) | Documento gerado aqui habilita o trabalhador a requerer o benefício |
| E1 | DATAPREV | Pré-habilitação (cruzamento RAIS/CAGED/CNIS/Empregador Web) | **DATAPREV — CNIS, CADÚnico** | Dados do empregador devem estar consistentes |
| E1 | MTE | Habilitação formal do benefício | **SD-Web / SINE / SRTE** | Pré-habilitação DATAPREV deve estar OK |
| E2 | Caixa | Roteamento da chamada para URA | **CTI / ACD (Genesys/Avaya)** | Disponibilidade da plataforma telefônica |
| E3 | URA Caixa | Autentica CPF/NIS, consulta API | **API MTE ↔ Caixa (SOAP/REST)** | Consistência cadastral entre DATAPREV e Caixa |
| E4 | URA / Atendente | Informa status, orienta canal de resolução | **Sistema de benefícios Caixa** | Status deve estar sincronizado entre MTE e Caixa |
| E5 | Caixa | Gera Ordem de Pagamento (OP) | **Infraestrutura de pagamentos Caixa** | MTE deve ter validado elegibilidade da parcela |
| E5 | Sistema financeiro | Executa crédito | **COMPE / STR / PIX** | Dados bancários do beneficiário devem ser válidos |
| E6 | MTE | Recebe e processa recurso | **Portal Facilita Brasil (DATAPREV)** | Documentação completa pelo cidadão |

> **Importante:** o eSocial **não é o único gatilho** de habilitação. Órgãos públicos, organismos internacionais e optantes específicos ainda usam **CAGED** para comunicação de desligamento. O ecossistema operacional combina **eSocial + CAGED + Empregador Web** — tratar qualquer um deles como exclusivo é imprecisão factual.

---

## Dimensão C — Evidências Físicas e Digitais

Para cada etapa, liste **todos** os artefatos tangíveis ou digitais que o cidadão toca, vê, ouve ou recebe:

**E1 — Pré-contato & Habilitação**
- Termo de Rescisão do Contrato de Trabalho (TRCT)
- **Documento de Requerimento do Seguro-Desemprego** (impresso ou digital, gerado pelo Empregador Web)
- Carteira de Trabalho Digital (app CTPS / gov.br)
- Cartão do NIS / Cartão Cidadão
- Comprovante de CPF
- Portaria/tabela MTE com prazos e faixas de valor

**E2 — Acesso ao canal**
- Tom de discagem e saudação da URA (0800-726-0207)
- Tempo de espera percebido (ausência de estimativa é fail point)
- Interface do app **CAIXA Tem** ou **app Benefícios Sociais CAIXA** (canal alternativo digital)
- **Portal Cidadão** (caixa.gov.br/beneficios-trabalhador)

**E3 — Navegação e autenticação**
- Árvore de menus narrada (DTMF)
- Confirmação de CPF/NIS lida pela URA
- Mensagem de erro (quando CPF não reconhecido)
- Interface do **app CAIXA Tem** (tela de consulta de benefício)

**E4 — Consulta / Orientação**
- Locução com status do benefício e data de crédito prevista
- Protocolo de atendimento (quando gerado — ausência é fail point)
- SMS de confirmação (eventual)
- Orientação de encaminhamento: gov.br, agência, SRTE

**E5 — Processamento & Pagamento**
- Notificação push no **app CAIXA Tem** ou **app Benefícios Sociais CAIXA**
- Extrato no **app Caixa** / **Internet Banking**
- Comprovante de saque em **caixa eletrônico (ATM)**
- Comprovante de saque em **Casa Lotérica**
- Comprovante de saque em **Correspondente CAIXA Aqui**
- Autenticação por **biometria digital** (em agências e ATMs habilitados)
- Movimentação na **Conta Poupança Social Digital** (quando aplicável)
- Comprovante presencial em **agência Caixa** (com CPF e documento de identidade)

**E6 — Exceção: revisão, recurso e presencial**
- E-mail / SMS de parcela bloqueada ou negada
- Histórico no **portal gov.br** / **Portal Facilita Brasil**
- Formulário de recurso (F-SD-200 ou equivalente digital)
- Protocolo de recurso gerado pelo MTE
- Documentação física exigida para comparecimento presencial (quando inevitável)

---

## Dimensão D — Normativos Aplicáveis

Para cada etapa, referencie os instrumentos legais que **governam, restringem ou garantem direitos** naquele momento, indicando o que exigem do prestador e o que garantem ao cidadão:

| Etapa | Normativo | Dispositivo | Obriga o prestador a… | Garante ao cidadão… |
|---|---|---|---|---|
| E1 | **Lei 7.998/1990** (arts. 2º–7º) | Base do Programa SD e FAT | Gerir o benefício conforme critérios legais | Receber SD se elegível |
| E1 | **Lei 13.134/2015** | Atualização de critérios de elegibilidade | Aplicar critérios revisados de concessão | Acesso conforme novas regras |
| E1 | **Portaria MTE 290/2021** | Prazos e procedimentos de requerimento | Receber requerimento dentro do prazo (7º–120º dia) | Prazo garantido para requerer |
| E2–E3 | **Lei 8.078/1990 — CDC** (art. 6º) | Qualidade e acesso ao serviço | Garantir atendimento adequado (aplicável subsidiariamente quando há relação de consumo com a Caixa, conforme Lei 13.460, art. 1º, §2º) | Atendimento de qualidade |
| E2–E3 | **Decreto 6.523/2008** (SAC) | Normas de atendimento telefônico | Transferir para atendente em até 60s; não desligar antes de resolver | Acesso efetivo ao atendimento |
| E2–E6 | **Lei 13.460/2017** (arts. 5º–9º) | Direitos do usuário de serviço público | Prestar serviço com regularidade, eficiência e cortesia | Protocolo, prazo de resposta, canal de reclamação |
| E2–E6 | **Decreto 9.094/2017** (arts. 1º–5º) | Simplificação do atendimento / Carta de Serviços | Não exigir do cidadão dados já disponíveis em bases oficiais; usar CPF como identificador suficiente; publicar Carta de Serviços | Atendimento simplificado; CPF como documento único |
| E3–E4 | **Lei 13.709/2018 — LGPD** | Tratamento de dados pessoais (CPF/NIS) | Garantir segurança e finalidade no uso dos dados | Proteção dos dados pessoais |
| E3–E4 | **Res. CODEFAT 957/2022** e **Res. CODEFAT 1.031/2025** | Regras operacionais do benefício | Processar o benefício conforme resoluções vigentes | Benefício calculado conforme regras atualizadas |
| E5 | **Res. CMN 3.876** | Conta poupança social simplificada | Oferecer conta acessível para recebimento | Recebimento mesmo sem conta bancária convencional |
| E6 | **Lei 9.784/1999** | Processo administrativo federal | Decidir recurso com motivação e prazo | Direito ao recurso com resposta fundamentada |
| E6 | **Lei 13.460/2017** (art. 9º) | Mecanismo de participação e reclamação | Manter ouvidoria acessível e dar retorno | Canal de reclamação com prazo de resposta |

> **Nota sobre CDC × Lei 13.460:** a relação do cidadão com a URA para tratar de benefício público é regida **primariamente** pela Lei 13.460/2017 (serviço público). Contudo, o art. 1º, §2º da Lei 13.460 **não afasta** o CDC quando configurada relação de consumo com a Caixa enquanto instituição financeira. A exclusão categórica do CDC **não tem base normativa suficiente** — o regime adequado é o de **subsidiariedade**, não exclusão.

---

## Dimensão E — Fail Points Conhecidos

Para cada fail point, use a estrutura:

```
FAIL POINT: [nome curto]
Etapa: Ex
Sintoma observado: [o que o cidadão percebe]
Causa-raiz provável: [processo, sistema ou política]
Evidência / fonte: [fonte rastreável]
Impacto: [consequência concreta]
Atores: [quem falhou / quem absorve o impacto]
```

---

**FP-01 — Empregador não envia CAGED/eSocial**
Etapa: E1
Sintoma observado: Cidadão solicita benefício e sistema informa que não há requerimento disponível.
Causa-raiz: Empregador não cumpriu obrigação de envio via eSocial (regra geral) ou CAGED (hipóteses específicas). Sem esse dado, DATAPREV não executa pré-habilitação.
Evidência: FAQ oficial gov.br — "Solicitar o Seguro-Desemprego"; fluxo Empregador Web (MTE).
Impacto: Bloqueio total na jornada; cidadão precisa acionar o empregador ou SRTE.
Atores: Empregador (falha); cidadão e MTE (absorvem impacto).

---

**FP-02 — Empregador não emite o Requerimento via Empregador Web**
Etapa: E1
Sintoma observado: Trabalhador chega ao posto ou URA sem o documento de requerimento; serviço não pode ser efetivado.
Causa-raiz: Empregador não acessou o sistema Empregador Web (sd.maisemprego.trabalho.gov.br) para emitir o requerimento antes ou no ato da rescisão.
Evidência: TRT-5 — "Aplicativo do MTE permite informar seguro-desemprego pela internet"; FAQ Empregador Web (MTE).
Impacto: Cidadão retorna ao empregador; atraso no processo; risco de perder prazo.
Atores: Empregador (falha); trabalhador (absorve).

---

**FP-03 — Canal 158 congestionado (handoff interorganizacional)**
Etapa: E2
Sintoma observado: Cidadão não consegue completar chamada após dezenas de tentativas.
Causa-raiz: Subdimensionamento de capacidade do canal MTE (158) em períodos de alta demanda.
Evidência: Reclame Aqui — relatos de >70 tentativas sem resposta (Ministério do Trabalho e Emprego).
Impacto: Cidadão bloqueado no início da jornada; migra para fila presencial.
Atores: MTE (falha no canal próprio); cidadão (absorve). **Nota de atribuição:** o 158 é canal do MTE, não da Caixa — é um **touchpoint interorganizacional** da jornada, não uma falha da URA Caixa. Classificar como fail point da Caixa seria erro de atribuição; deve ser mapeado como **dependência interorganizacional crítica**.

---

**FP-04 — URA Caixa: CPF/NIS não reconhecido**
Etapa: E3
Sintoma observado: URA informa "cadastro não encontrado" mesmo com dados corretos.
Causa-raiz: Inconsistência cadastral entre base DATAPREV e base Caixa; divergência de NIS/CPF não sincronizada.
Evidência: FAQ Caixa — perguntas frequentes sobre seguro-desemprego; relatos Reclame Aqui (Caixa).
Impacto: Cidadão preso em loop; não consegue consulta; precisa ir à agência.
Atores: Caixa / DATAPREV (falha na sincronização); cidadão (absorve).

---

**FP-05 — Loop de menu sem saída e sem transferência para humano**
Etapa: E3
Sintoma observado: Cidadão navega em menus que não levam à solução e não consegue ser transferido.
Causa-raiz: Desenho deficiente da árvore de URA; ausência de opção de transferência imediata para atendente conforme Decreto 6.523/2008.
Evidência: Decreto 6.523/2008 (obriga transferência em até 60s); relatos de uso da central 0800.
Impacto: Abandono da chamada; migração para canal presencial.
Atores: Caixa (design da URA); cidadão (absorve).

---

**FP-06 — Status divergente entre MTE e Caixa**
Etapa: E4
Sintoma observado: URA informa "benefício não encontrado" enquanto portal MTE mostra benefício habilitado.
Causa-raiz: Latência ou falha na sincronização entre API MTE e base operacional da Caixa; eventual defasagem de batch noturno.
Evidência: Reclame Aqui — "Problemas com pagamento do seguro desemprego" (Caixa Econômica Federal); FAQ Caixa.
Impacto: Cidadão recebe informação falsa negativa; desiste ou vai à agência desnecessariamente.
Atores: Caixa / MTE (integração sistêmica); cidadão (absorve).

---

**FP-07 — Nenhum protocolo gerado ao final da chamada**
Etapa: E4
Sintoma observado: Cidadão encerra a ligação sem número de protocolo; não tem comprovante de que acionou o serviço.
Causa-raiz: URA não emite protocolo sistematicamente; ausência de envio de SMS/e-mail pós-chamada.
Evidência: Lei 13.460/2017, art. 9º (direito a protocolo); Decreto 6.523/2008 (registro de atendimento).
Impacto: Cidadão sem comprovação de contato; dificuldade em abrir recurso posterior.
Atores: Caixa (falha no processo de encerramento da chamada); cidadão (absorve).

---

**FP-08 — Crédito em conta errada (poupança vs. conta corrente)**
Etapa: E5
Sintoma observado: Parcela creditada em conta poupança social que o cidadão desconhece ou não acessa.
Causa-raiz: Caixa abre conta poupança social digital automaticamente quando não há conta informada; cidadão não é comunicado adequadamente.
Evidência: Reclame Aqui — "Crédito bloqueado parcela do seguro desemprego" (Caixa); FAQ Caixa (canais de saque alternativos).
Impacto: Cidadão não localiza o dinheiro; precisa comparecer à agência ou lotérica com Cartão Cidadão.
Atores: Caixa (comunicação inadequada); cidadão (absorve).

---

**FP-09 — Falha de crédito / TED para outros bancos**
Etapa: E5
Sintoma observado: Parcela marcada como "emitida" no MTE mas não creditada na conta do cidadão em outro banco.
Causa-raiz: Falha no processamento de transferência interbancária (COMPE/STR); pode ser pontual (evento sistêmico) ou recorrente (dados bancários inválidos).
Evidência: Reclame Aqui — "Atraso no pagamento do seguro desemprego e falta de suporte da Caixa" (relato de junho/2025); FAQ Caixa sobre meios alternativos de saque.
Impacto: Cidadão fica sem renda; precisa investigar em dois órgãos (MTE e Caixa) simultaneamente.
Atores: Caixa / sistema financeiro (falha de processamento); cidadão (absorve).

---

**FP-10 — Parcela bloqueada sem comunicação proativa**
Etapa: E5
Sintoma observado: Parcela não cai e cidadão só descobre ao ligar ou verificar app dias depois.
Causa-raiz: Ausência de notificação automática (push/SMS/e-mail) quando parcela é bloqueada por suspeita de irregularidade ou divergência cadastral.
Evidência: Reclame Aqui — "Seguro desemprego bloqueado" (Caixa); Lei 13.460/2017, art. 5º (direito à informação clara e tempestiva).
Impacto: Cidadão perde dias de subsistência sem saber a causa.
Atores: Caixa / MTE (ausência de notificação); cidadão (absorve).

---

**FP-11 — Benefício não liberado após 30 dias sem feedback**
Etapa: E5–E6
Sintoma observado: Cidadão aguarda além do prazo previsto sem receber parcela nem comunicação.
Causa-raiz: Dados divergentes no requerimento; inconsistência CNIS/CAGED não resolvida automaticamente; processo em fila de análise manual no MTE.
Evidência: FAQ oficial gov.br — "Solicitar o Seguro-Desemprego" (seção de situações de não liberação).
Impacto: Cidadão sem renda; sem orientação sobre próximo passo.
Atores: MTE / DATAPREV (falha no processamento e na comunicação); cidadão (absorve).

---

**FP-12 — Dificuldade de acesso ao gov.br / app para acompanhamento**
Etapa: E6
Sintoma observado: Cidadão não consegue logar no gov.br ou app para consultar ou interpor recurso.
Causa-raiz: Nível de conta gov.br insuficiente (bronze vs. prata/ouro); ausência de biometria ou validação facial; falta de internet em zona rural.
Evidência: FAQ oficial gov.br — "Solicitar o Seguro-Desemprego" (item sobre dificuldades de cadastro/acesso).
Impacto: Cidadão excluído dos canais digitais; única saída é o presencial.
Atores: Gov.br / Serpro (barreira de acesso digital); cidadão (absorve).

---

**FP-13 — Parcela indisponível via Cartão Cidadão**
Etapa: E5–E6
Sintoma observado: Cidadão tenta sacar na lotérica com o Cartão Cidadão e o terminal informa saldo indisponível.
Causa-raiz: Cartão bloqueado (senha errada, validade vencida, inconsistência de NIS); ou parcela ainda não liberada para esse canal.
Evidência: FAQ Caixa — perguntas frequentes seguro-desemprego (item sobre Cartão Cidadão); FAQ oficial gov.br.
Impacto: Cidadão sem acesso ao dinheiro mesmo com parcela creditada.
Atores: Caixa (gestão do Cartão Cidadão); cidadão (absorve).

---

**FP-14 — Recurso administrativo sem resposta (E6)**
Etapa: E6
Sintoma observado: Recurso interposto no portal MTE / Facilita Brasil sem resposta após semanas ou meses.
Causa-raiz: Subdimensionamento da equipe de análise de recursos no MTE; ausência de SLA publicado e monitorado.
Evidência: Reclame Aqui — "Recurso Seguro-Desemprego sem Resposta" (Ministério do Trabalho e Emprego); Lei 9.784/1999, art. 49 (prazo de 30 dias para decisão de recurso).
Impacto: Cidadão fica meses sem a parcela e sem perspectiva de resolução.
Atores: MTE (falha de capacidade e gestão); cidadão (absorve).

---

**FP-15 — Redirecionamento à agência física para resolver falha originada na URA**
Etapa: E6
Sintoma observado: Após falha na URA, cidadão é orientado a "comparecer à agência" sem solução no próprio canal.
Causa-raiz: URA não possui capacidade de resolução — apenas de consulta e orientação; atendente humano também não tem acesso aos sistemas necessários.
Evidência: Reclame Aqui — relatos de "3 horas de fila" em agência; FAQ Caixa (comparecimento presencial como última instância).
Impacto: Custo de tempo e deslocamento para cidadão; contratransferência de responsabilidade.
Atores: Caixa (design do serviço sem resolução no canal); cidadão (absorve).

---

## Síntese de Criticidade

| # | Fail Point | Impacto no cidadão | Complexidade de resolução |
|---|---|---|---|
| FP-02 | Empregador não emite Requerimento (Empregador Web) | **Alto** | **Médio prazo** (obrigatoriedade + fiscalização) |
| FP-01 | Empregador não envia eSocial/CAGED | **Alto** | **Longo prazo** (compliance eSocial) |
| FP-06 | Status divergente MTE ↔ Caixa | **Alto** | **Médio prazo** (integração API) |
| FP-11 | Benefício não liberado após 30 dias sem feedback | **Alto** | **Médio prazo** (SLA + notificação) |
| FP-14 | Recurso sem resposta no MTE | **Alto** | **Médio prazo** (capacidade + SLA) |
| FP-10 | Parcela bloqueada sem comunicação | **Alto** | **Curto prazo** (notificação push/SMS) |
| FP-09 | Falha TED para outros bancos | **Alto** | **Curto prazo** (monitoramento + retry) |
| FP-08 | Crédito em conta errada | **Médio** | **Curto prazo** (comunicação ao cidadão) |
| FP-05 | Loop de menu sem transferência para humano | **Médio** | **Curto prazo** (redesign URA + Decreto 6.523) |
| FP-04 | CPF/NIS não reconhecido pela URA | **Médio** | **Médio prazo** (sincronização cadastral) |
| FP-12 | Barreira de acesso ao gov.br / app | **Médio** | **Longo prazo** (inclusão digital) |
| FP-15 | Redirecionamento à agência para resolver falha da URA | **Médio** | **Médio prazo** (design de resolução no canal) |
| FP-07 | Nenhum protocolo gerado | **Médio** | **Curto prazo** (obrigação Decreto 6.523) |
| FP-13 | Parcela indisponível no Cartão Cidadão | **Médio** | **Curto prazo** (gestão do cartão) |
| FP-03 | Canal 158 congestionado (handoff MTE) | **Médio** | **Médio prazo** (capacidade MTE — fora do escopo Caixa) |

---

## Restrições de formato

- Use tabelas para as dimensões B e D; narrativa densa para A; lista estruturada para C e E.
- Cite o normativo com número e artigo sempre que mencionar uma obrigação legal.
- Este é um blueprint **AS-IS, descritivo e factual** — não faça recomendações de melhoria.
- Quando houver lacuna de informação, sinalize com `[DADO NÃO DISPONÍVEL — validar em campo]`.
- Atribua falhas ao ator correto: não classifique falhas do MTE (canal 158) como falhas da URA Caixa — mapeie como **dependências interorganizacionais**.
- Não trate o eSocial como gatilho exclusivo — use "eSocial (regra geral para obrigados) ou CAGED (hipóteses específicas)".

---

## Fontes primárias a consultar

- [Caixa — Seguro-Desemprego](https://www.caixa.gov.br/beneficios-trabalhador/seguro-desemprego/Paginas/default.aspx)
- [gov.br — Solicitar Seguro-Desemprego](https://www.gov.br/pt-br/servicos/solicitar-o-seguro-desemprego)
- [Lei 7.998/1990 — Planalto](http://www.planalto.gov.br/ccivil_03/leis/l7998compilado.htm)
- [Lei 13.134/2015 — Planalto](http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2015/lei/l13134.htm)
- [Decreto 9.094/2017 — Planalto](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2017/decreto/d9094.htm)
- [Res. CODEFAT 957/2022 — LegisWeb](https://www.legisweb.com.br/legislacao/?id=436595)
- [Empregador Web — MTE](https://sd.maisemprego.trabalho.gov.br/sdweb/empregadorweb/login.jsf)
- [DATAPREV — Seguro-Desemprego](https://dataprev.gov.br/seguro-desemprego)
- [Portal Facilita Brasil — DATAPREV](https://facilitabrasil.dataprev.gov.br)
- Reclame Aqui — Caixa Econômica Federal e Ministério do Trabalho e Emprego (filtro: "seguro desemprego")

---

---

# Changelog v1 → v2: decisão sobre cada falha da auditoria

| # | Falha auditada | Decisão | Ação tomada |
|---|---|---|---|
| 1 | Canal "111" atribuído à URA Caixa | **(a) Corrigir** — a v1 do meta-prompt não cometeu esse erro, mas o contexto foi clarificado | Seção de escopo do canal reforça que o número correto é 0800-726-0207; "111" removido de qualquer menção |
| 2 | Gatilho "estritamente via eSocial" | **(a) Corrigir** | Dimensão B agora especifica "eSocial (regra geral para obrigados) **ou** CAGED (hipóteses específicas)"; nota explicativa adicionada |
| 3 | 158 como fail point da Caixa | **(b) Defender parcialmente + (a) corrigir** | Mantido como touchpoint real da jornada; reclassificado explicitamente como **dependência interorganizacional** (FP-03); não atribuído à Caixa |
| 4 | Exclusão categórica do CDC | **(a) Corrigir** | Nota normativa na Dimensão D explica regime de **subsidiariedade** (Lei 13.460, art. 1º, §2º); exclusão categórica removida |
| 5 | URA como "canal secundário/contingencial" | **(a) Corrigir** | Seção de escopo usa linguagem neutra: "canal formal de acompanhamento de parcelas — um dos vários pontos de contato" |
| 6 | Omissão do Empregador Web / Requerimento | **(a) Corrigir** | Empregador Web adicionado na Dimensão B (E1) e na Dimensão C (E1); FP-02 criado |
| 7 | Omissão do fluxo de revisão/recurso/presencial | **(a) Corrigir** | E6 renomeado para "Exceção: revisão, recurso e presencial"; FP-11, FP-12, FP-14, FP-15 adicionados |
| 8 | Omissão de canais e evidências físicas Caixa | **(a) Corrigir** | Dimensão C agora inclui app CAIXA Tem, app Benefícios Sociais CAIXA, Portal Cidadão, Conta Poupança Social Digital, ATMs, Casas Lotéricas, CAIXA Aqui, biometria, agências |
| 9 | Omissão de Lei 7.998/1990 e Lei 13.134/2015 | **(b) Defender** — já estavam na v1; nenhum erro na v1 | Mantidos e expandidos na tabela da Dimensão D |
| 10 | Omissão do Decreto 9.094/2017 | **(a) Corrigir** | Decreto 9.094/2017 adicionado na Dimensão D (E2–E6) com dispositivos e implicações |
| 11 | Fail points oficiais não mapeados | **(a) Corrigir** | FP-11 (não liberado após 30 dias), FP-12 (barreira gov.br/app), FP-13 (Cartão Cidadão), FP-14 (recurso sem resposta), FP-08 (conta errada) detalhados com fontes oficiais |
| 12 | Inferências fortes sem evidência rastreável | **(a) Corrigir** | Cada fail point agora tem campo **Evidência / fonte** obrigatório; linguagem de grau de certeza ajustada |
