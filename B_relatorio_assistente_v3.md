# Meta-Prompt v3 — Service Blueprint AS-IS: Atendimento ao Seguro-Desemprego pela URA da Caixa

> **Versão:** 3.0 (versão final)
> **Histórico:** v1 → audit_v1 (B_relatorio_auditoria_v1.md) → v2 → audit_v2 (B_relatorio_auditoria_v2.md) → v3
> **Changelog v2 → v3:** ver seção final deste arquivo — cada delta cita o ponto da audit_v2 que o motivou.

---

## Contexto e objetivo

Você é um especialista em design de serviços públicos e experiência do cidadão. Sua tarefa é **documentar o estado atual (AS-IS) do serviço de atendimento ao Seguro-Desemprego realizado pela URA (Unidade de Resposta Audível) da Caixa Econômica Federal**, produzindo um Service Blueprint completo com cinco dimensões analíticas obrigatórias.

O produto final deve ser utilizável por equipes de CX, gestores públicos e times de transformação digital para identificar oportunidades de melhoria, redesenho de processos e priorização de investimentos.

> **Escopo do canal (revisado — audit_v2 C2):** a URA da Caixa (0800-726-0207) é **o canal central deste Blueprint**. Canais de outros órgãos — como o telefone 158 do MTE — **não compõem as etapas de linha de frente** deste mapa; constam estritamente como **dependências externas e fronteiras de handoff** mapeadas no backstage. Isso evita "crise de identidade" do Blueprint entre o que é Caixa e o que é MTE.

---

## Instrução principal

Estruture sua resposta nas cinco dimensões abaixo. Para cada dimensão, percorra **todas as seis etapas da jornada** na sequência:

> **E1 — Pré-contato & Habilitação** · **E2 — Acesso à URA Caixa (0800-726-0207)** · **E3 — Navegação e autenticação** · **E4 — Consulta / Orientação** · **E5 — Processamento & Pagamento** · **E6 — Exceção: revisão, recurso e comparecimento presencial**

> **Nota de escopo (audit_v2 A2 e C2):** o título de E2 foi corrigido de "Acesso ao canal (URA/158/digital)" para "Acesso à URA Caixa". O canal 158 (MTE) aparece **somente** na Dimensão B como dependência interorganizacional — nunca como etapa da linha de frente.

---

## Dimensão A — Jornada do Cidadão (linha de frente)

Para cada etapa, descreva:
- **O que o cidadão está tentando fazer** (job-to-be-done)
- **Como ele age** (canais, dispositivo, comportamento)
- **O que ele sente** (frustrações, ansiedades, expectativas)
- **Restrições de tempo** que impactam sua ação (ex.: prazo de 7 a 120 dias pós-demissão conforme Lei 7.998/1990, art. 7º)
- **Pré-condição operacional:** o cidadão precisa ter recebido o **Documento de Requerimento do Seguro-Desemprego** emitido pelo empregador via **Empregador Web** (sistema MTE) — sem ele, o requerimento não pode ser efetivado

Considere perfis distintos:
- Trabalhador com baixa literacia digital
- Trabalhador com deficiência auditiva (ausência de canal alternativo à URA é um fail point crítico)
- Trabalhador em zona rural com acesso limitado à internet e à agência física
- Trabalhador de órgão público ou organismo internacional (fluxo pode divergir do eSocial padrão)

---

## Dimensão B — Processos de Bastidor (backstage)

> **Nota de atribuição (audit_v2 A2 e C2):** O canal 158 (MTE) figura nesta dimensão como **dependência interorganizacional externa**, não como canal gerido pela Caixa. Canais do MTE nunca compõem o título de etapas do Blueprint.

Para cada etapa, identifique em formato de tabela:

| Etapa | Ator | Ação concreta | Sistema / plataforma | Dependência crítica |
|---|---|---|---|---|
| E1 | Empregador | Envia dados de desligamento | **eSocial** (regra geral para obrigados) ou **CAGED** (hipóteses específicas: órgãos públicos, organismos internacionais, optantes do Grupo 4) | Sem envio, DATAPREV não executa pré-habilitação |
| E1 | Empregador | Emite Requerimento do SD | **Empregador Web** (sd.maisemprego.trabalho.gov.br) | Documento gerado aqui habilita o trabalhador a requerer o benefício |
| E1 | DATAPREV | Pré-habilitação | **DATAPREV — CNIS, CADÚnico** | Dados do empregador devem estar consistentes |
| E1 | MTE | Habilitação formal do benefício | **SD-Web / SINE / SRTE** | Pré-habilitação DATAPREV deve estar OK |
| E1 | MTE (canal externo) | Canal 158: orientação inicial ao trabalhador | **Central 158 — MTE** | **Dependência externa**: congestionamento no 158 é fail point interorganizacional, fora do escopo de gestão da Caixa |
| E2 | Caixa | Roteamento da chamada para URA | **CTI / ACD (Genesys/Avaya)** | Disponibilidade da plataforma telefônica |
| E3 | URA Caixa | Autentica CPF/NIS; consulta API | **API MTE ↔ Caixa (SOAP/REST)** | Consistência cadastral entre DATAPREV e Caixa |
| E4 | URA / Atendente | Informa status; orienta canal de resolução | **Sistema de benefícios Caixa** | Status deve estar sincronizado entre MTE e Caixa |
| E5 | Caixa | Gera Ordem de Pagamento (OP) | **Infraestrutura de pagamentos Caixa** | MTE deve ter validado elegibilidade da parcela |
| E5 | Sistema financeiro | Executa crédito | **COMPE / STR / PIX** | Dados bancários do beneficiário devem ser válidos |
| E6 | Cidadão / MTE | Interposição de recurso | **CTPS Digital / Portal gov.br → sistema SD/MTE** ou **presencialmente via SRTE** | Documentação completa pelo cidadão |
| E6 | SRTE | Processamento presencial de recurso | **Sistema de Recursos das Superintendências Regionais do Trabalho (SRTE)** | Comparecimento do cidadão com documentação |

> **Correção de arquitetura (audit_v2 B1):** a v2 incorretamente apontava o **Portal Facilita Brasil** como plataforma operacional de processamento de recursos de Seguro-Desemprego. Isso foi corrigido: recursos são interpostos via **CTPS Digital / Portal gov.br** (sistema SD/MTE) ou presencialmente via **SRTE**. O Portal Facilita Brasil é canal de acesso unificado a serviços, não a plataforma transacional de processamento de recursos de amparo ao trabalhador.

> **Nota sobre eSocial vs. CAGED (audit_v1 falha 2 / audit_v2 A3):** o eSocial **não é o único gatilho** de habilitação. Órgãos públicos, organismos internacionais e optantes específicos ainda usam **CAGED**. Tratar qualquer um deles como exclusivo é imprecisão factual.

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

**E2 — Acesso à URA Caixa**
- Tom de discagem e saudação da URA (0800-726-0207)
- Tempo de espera percebido (ausência de estimativa é fail point)
- Mensagem de indisponibilidade do sistema (quando ocorre)

**E3 — Navegação e autenticação**
- Árvore de menus narrada (DTMF)
- Confirmação de CPF/NIS lida pela URA
- Mensagem de erro (quando CPF não reconhecido)

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
- Histórico no **CTPS Digital / Portal gov.br**
- Formulário de recurso (digital via sistema SD/MTE ou físico via SRTE)
- Protocolo de recurso gerado pelo MTE
- Documentação física exigida para comparecimento presencial na SRTE

---

## Dimensão D — Normativos Aplicáveis

> **Correção crítica (audit_v2 A1 e C1):** o **Decreto nº 6.523/2008** foi **integralmente revogado pelo Decreto nº 11.034/2022**. Todas as obrigações de atendimento telefônico (tempo de transferência, registro de atendimento, acessibilidade) que a v2 atribuía ao Decreto 6.523/2008 foram migradas para o Decreto 11.034/2022, que é a norma vigente do SAC.

Para cada etapa, referencie os instrumentos legais que **governam, restringem ou garantem direitos** naquele momento:

| Etapa | Normativo | Dispositivo | Obriga o prestador a… | Garante ao cidadão… |
|---|---|---|---|---|
| E1 | **Lei 7.998/1990** (arts. 2º–7º) | Base do Programa SD e FAT | Gerir o benefício conforme critérios legais | Receber SD se elegível |
| E1 | **Lei 13.134/2015** | Atualização de critérios de elegibilidade | Aplicar critérios revisados de concessão | Acesso conforme novas regras |
| E1 | **Portaria MTE 290/2021** | Prazos e procedimentos de requerimento | Receber requerimento dentro do prazo (7º–120º dia) | Prazo garantido para requerer |
| E2–E4 | **Decreto nº 11.034/2022** *(substitui o revogado Decreto 6.523/2008)* | Normas do SAC: acessibilidade, transferência, registro, resolutividade | Oferecer transferência para atendente; registrar protocolo; garantir acessibilidade ao canal; medir resolutividade no 1º contato | Atendimento acessível, protocolo de registro, transferência efetiva |
| E2–E6 | **Lei 13.460/2017** (arts. 5º–9º) | Direitos do usuário de serviço público | Prestar serviço com regularidade, eficiência e cortesia | Protocolo, prazo de resposta, canal de reclamação |
| E2–E6 | **Decreto 9.094/2017** (arts. 1º–5º) | Simplificação do atendimento / Carta de Serviços | Não exigir do cidadão dados já disponíveis em bases oficiais; usar CPF como identificador suficiente; publicar Carta de Serviços | Atendimento simplificado; CPF como documento único |
| E3–E4 | **Lei 13.709/2018 — LGPD** | Tratamento de dados pessoais (CPF/NIS) | Garantir segurança e finalidade no uso dos dados | Proteção dos dados pessoais |
| E3–E4 | **Res. CODEFAT 957/2022** e **Res. CODEFAT 1.031/2025** | Regras operacionais do benefício | Processar o benefício conforme resoluções vigentes | Benefício calculado conforme regras atualizadas |
| E2–E4 | **Lei 8.078/1990 — CDC** | Qualidade e acesso ao serviço | Garantir atendimento adequado (aplicável **subsidiariamente** quando há relação de consumo com a Caixa enquanto instituição financeira, conforme Lei 13.460, art. 1º, §2º) | Atendimento de qualidade na dimensão consumerista |
| E5 | **Res. CMN 3.876** | Conta poupança social simplificada | Oferecer conta acessível para recebimento | Recebimento mesmo sem conta bancária convencional |
| E6 | **Lei 9.784/1999** (art. 49) | Processo administrativo federal | Decidir recurso com motivação em até 30 dias | Direito ao recurso com resposta fundamentada e tempestiva |
| E6 | **Lei 13.460/2017** (art. 9º) | Mecanismo de participação e reclamação | Manter ouvidoria acessível e dar retorno | Canal de reclamação com prazo de resposta |

> **Nota sobre CDC × Lei 13.460:** a relação do cidadão com a URA para tratar de benefício público é regida **primariamente** pela Lei 13.460/2017 (serviço público). O CDC aplica-se **subsidiariamente** quando configurada relação de consumo com a Caixa na condição de instituição financeira operadora do pagamento — conforme art. 1º, §2º da Lei 13.460. A exclusão categórica do CDC **não tem base normativa suficiente**; o regime correto é a **subsidiariedade**.

---

## Dimensão E — Fail Points Conhecidos

> **Nota metodológica (audit_v2 B2):** a causa-raiz de falhas de pagamento (FP-09) foi ajustada. A versão anterior atribuía a falha ao COMPE/STR com base exclusivamente em relatos do Reclame Aqui — inferência técnica desproporcional, pois usuários relatam sintomas, não diagnosticam infraestrutura macrofinanceira. A causa-raiz foi reformulada para descrever o sintoma confirmado e marcar como `[PENDENTE — validar em campo]` a camada de infraestrutura responsável.

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

**FP-01 — Empregador não envia eSocial/CAGED**
Etapa: E1
Sintoma observado: Cidadão solicita benefício e sistema informa que não há requerimento disponível.
Causa-raiz: Empregador não cumpriu obrigação de envio via eSocial (regra geral) ou CAGED (hipóteses específicas). Sem esse dado, DATAPREV não executa pré-habilitação.
Evidência: FAQ oficial gov.br — "Solicitar o Seguro-Desemprego"; fluxo Empregador Web (MTE).
Impacto: Bloqueio total na jornada; cidadão precisa acionar empregador ou SRTE.
Atores: Empregador (falha); cidadão e MTE (absorvem impacto).

---

**FP-02 — Empregador não emite o Requerimento via Empregador Web**
Etapa: E1
Sintoma observado: Trabalhador chega ao posto ou URA sem o documento de requerimento; serviço não pode ser efetivado.
Causa-raiz: Empregador não acessou Empregador Web para emitir o requerimento antes ou no ato da rescisão.
Evidência: TRT-5 — "Aplicativo do MTE permite informar seguro-desemprego pela internet"; FAQ Empregador Web (MTE).
Impacto: Cidadão retorna ao empregador; risco de perder prazo.
Atores: Empregador (falha); trabalhador (absorve).

---

**FP-03 — Canal 158 congestionado (dependência interorganizacional)**
Etapa: E1 (backstage) — dependência externa ao escopo da URA Caixa
Sintoma observado: Cidadão não consegue completar chamada ao MTE após dezenas de tentativas.
Causa-raiz: Subdimensionamento de capacidade do canal MTE (158) em períodos de alta demanda.
Evidência: Reclame Aqui — relatos de >70 tentativas sem resposta (Ministério do Trabalho e Emprego).
Impacto: Cidadão bloqueado antes de chegar à URA Caixa; migra para fila presencial.
Atores: MTE (falha no canal próprio); cidadão (absorve).
Nota de atribuição: Fail point **fora do escopo de gestão da Caixa**. Mapeado como fronteira interorganizacional, não como falha da URA.

---

**FP-04 — URA Caixa: CPF/NIS não reconhecido**
Etapa: E3
Sintoma observado: URA informa "cadastro não encontrado" mesmo com dados corretos.
Causa-raiz: Inconsistência cadastral entre base DATAPREV e base Caixa; NIS/CPF não sincronizados.
Evidência: FAQ Caixa — perguntas frequentes seguro-desemprego; Reclame Aqui (Caixa).
Impacto: Cidadão preso em loop; precisa ir à agência.
Atores: Caixa / DATAPREV (falha na sincronização); cidadão (absorve).

---

**FP-05 — Loop de menu sem transferência para atendente humano**
Etapa: E3
Sintoma observado: Cidadão navega em menus que não levam à solução e não consegue ser transferido.
Causa-raiz: Desenho deficiente da árvore de URA; ausência de opção de transferência para atendente conforme **Decreto nº 11.034/2022** *(norma SAC vigente — o revogado Decreto 6.523/2008 foi substituído)*.
Evidência: Decreto 11.034/2022 (obriga acessibilidade e transferência efetiva); relatos de uso da central 0800.
Impacto: Abandono da chamada; migração para canal presencial.
Atores: Caixa (design da URA); cidadão (absorve).

---

**FP-06 — Status divergente entre MTE e Caixa**
Etapa: E4
Sintoma observado: URA informa "benefício não encontrado" enquanto portal MTE mostra benefício habilitado.
Causa-raiz: Latência ou falha na sincronização entre API MTE e base operacional da Caixa.
Evidência: Reclame Aqui — "Problemas com pagamento do seguro desemprego" (Caixa); FAQ Caixa.
Impacto: Cidadão recebe informação falsa negativa; vai à agência desnecessariamente.
Atores: Caixa / MTE (integração sistêmica); cidadão (absorve).

---

**FP-07 — Nenhum protocolo gerado ao final da chamada**
Etapa: E4
Sintoma observado: Cidadão encerra a ligação sem número de protocolo.
Causa-raiz: URA não emite protocolo sistematicamente; ausência de SMS/e-mail pós-chamada.
Evidência: Lei 13.460/2017, art. 9º (direito a protocolo); **Decreto nº 11.034/2022** *(norma SAC vigente)* (registro de atendimento obrigatório).
Impacto: Cidadão sem comprovação de contato; dificuldade em abrir recurso posterior.
Atores: Caixa (falha no encerramento da chamada); cidadão (absorve).

---

**FP-08 — Crédito em conta errada (poupança vs. conta corrente)**
Etapa: E5
Sintoma observado: Parcela creditada em conta poupança social que o cidadão desconhece ou não acessa.
Causa-raiz: Caixa abre conta poupança social digital automaticamente quando não há conta informada; cidadão não é comunicado.
Evidência: Reclame Aqui — "Crédito bloqueado parcela do seguro desemprego" (Caixa); FAQ Caixa.
Impacto: Cidadão não localiza o dinheiro; precisa comparecer à agência ou lotérica.
Atores: Caixa (comunicação inadequada); cidadão (absorve).

---

**FP-09 — Parcela marcada como emitida mas não creditada**
Etapa: E5
Sintoma observado: Sistema MTE indica parcela "emitida"; conta do cidadão não recebe o crédito.
Causa-raiz provável: `[PENDENTE — validar em campo]` A falha pode estar em qualquer ponto da cadeia: validação de agência/conta no MTE, bloqueio cadastral na Caixa, ou falha no processamento de transferência interbancária. Atribuir a causa ao COMPE/STR com base em relatos de usuário é inferência desproporcional — usuários relatam sintomas, não diagnosticam infraestrutura macrofinanceira (audit_v2 B2).
Evidência: Reclame Aqui — "Atraso no pagamento do seguro desemprego e falta de suporte da Caixa"; FAQ Caixa sobre meios alternativos de saque.
Impacto: Cidadão fica sem renda; precisa investigar em MTE e Caixa simultaneamente.
Atores: `[PENDENTE — validar com auditoria TCU/CGU ou BCB]`.

---

**FP-10 — Parcela bloqueada sem comunicação proativa**
Etapa: E5
Sintoma observado: Parcela não cai; cidadão só descobre ao ligar ou verificar app dias depois.
Causa-raiz: Ausência de notificação automática (push/SMS/e-mail) quando parcela é bloqueada.
Evidência: Reclame Aqui — "Seguro desemprego bloqueado" (Caixa); Lei 13.460/2017, art. 5º.
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
Causa-raiz: Nível de conta gov.br insuficiente (bronze vs. prata/ouro); ausência de biometria; falta de internet em zona rural.
Evidência: FAQ oficial gov.br — "Solicitar o Seguro-Desemprego" (item sobre dificuldades de cadastro/acesso).
Impacto: Cidadão excluído dos canais digitais; única saída é o presencial.
Atores: Gov.br / Serpro (barreira de acesso digital); cidadão (absorve).

---

**FP-13 — Parcela indisponível via Cartão Cidadão**
Etapa: E5–E6
Sintoma observado: Cidadão tenta sacar na lotérica com Cartão Cidadão e o terminal informa saldo indisponível.
Causa-raiz: Cartão bloqueado (senha errada, validade vencida, inconsistência de NIS); ou parcela não liberada para esse canal.
Evidência: FAQ Caixa — perguntas frequentes seguro-desemprego; FAQ oficial gov.br.
Impacto: Cidadão sem acesso ao dinheiro mesmo com parcela creditada.
Atores: Caixa (gestão do Cartão Cidadão); cidadão (absorve).

---

**FP-14 — Recurso administrativo sem resposta**
Etapa: E6
Sintoma observado: Recurso interposto no portal gov.br / sistema SD/MTE sem resposta após semanas ou meses.
Causa-raiz: Subdimensionamento da equipe de análise de recursos no MTE; ausência de SLA publicado e monitorado.
Evidência: Reclame Aqui — "Recurso Seguro-Desemprego sem Resposta" (Ministério do Trabalho e Emprego); Lei 9.784/1999, art. 49 (prazo de 30 dias para decisão).
Impacto: Cidadão fica meses sem a parcela e sem perspectiva de resolução.
Atores: MTE (falha de capacidade e gestão); cidadão (absorve).

---

**FP-15 — Redirecionamento à agência física para resolver falha originada na URA**
Etapa: E6
Sintoma observado: Após falha na URA, cidadão é orientado a "comparecer à agência" sem solução no canal.
Causa-raiz: URA sem capacidade de resolução — apenas de consulta e orientação; atendente humano também sem acesso aos sistemas necessários.
Evidência: Reclame Aqui — relatos de "3 horas de fila" em agência; FAQ Caixa (comparecimento presencial como última instância).
Impacto: Custo de tempo e deslocamento; transferência de responsabilidade ao cidadão.
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
| FP-09 | Parcela emitida não creditada | **Alto** | `[PENDENTE — validar em campo]` |
| FP-08 | Crédito em conta errada | **Médio** | **Curto prazo** (comunicação ao cidadão) |
| FP-05 | Loop de menu sem transferência (Decreto 11.034/2022) | **Médio** | **Curto prazo** (redesign URA) |
| FP-04 | CPF/NIS não reconhecido | **Médio** | **Médio prazo** (sincronização cadastral) |
| FP-12 | Barreira de acesso ao gov.br / app | **Médio** | **Longo prazo** (inclusão digital) |
| FP-15 | Redirecionamento à agência para resolver falha da URA | **Médio** | **Médio prazo** (design de resolução no canal) |
| FP-07 | Nenhum protocolo gerado (Decreto 11.034/2022) | **Médio** | **Curto prazo** (obrigação normativa) |
| FP-13 | Parcela indisponível no Cartão Cidadão | **Médio** | **Curto prazo** (gestão do cartão) |
| FP-03 | Canal 158 congestionado (handoff MTE — fora do escopo Caixa) | **Médio** | **Médio prazo** (capacidade MTE) |

---

## Restrições de formato

- Use tabelas para as dimensões B e D; narrativa densa para A; lista estruturada para C e E.
- Cite o normativo com número e artigo sempre que mencionar uma obrigação legal — **use apenas normas vigentes**.
- Este é um blueprint **AS-IS, descritivo e factual** — não faça recomendações de melhoria.
- Quando houver lacuna de informação, sinalize com `[DADO NÃO DISPONÍVEL — validar em campo]`.
- Atribua falhas ao ator correto: canais do MTE são **dependências externas**, não canais da Caixa.
- Não trate o eSocial como gatilho exclusivo — use "eSocial (regra geral) ou CAGED (hipóteses específicas)".
- Nunca citar o **Decreto 6.523/2008** — foi revogado pelo **Decreto 11.034/2022**.
- Não atribuir causa-raiz a infraestrutura sistêmica (COMPE/STR) sem fonte de auditoria (TCU/CGU/BCB) — marcar como `[PENDENTE]`.

---

## Fontes primárias a consultar

- [Caixa — Seguro-Desemprego](https://www.caixa.gov.br/beneficios-trabalhador/seguro-desemprego/Paginas/default.aspx)
- [gov.br — Solicitar Seguro-Desemprego](https://www.gov.br/pt-br/servicos/solicitar-o-seguro-desemprego)
- [Lei 7.998/1990 — Planalto](http://www.planalto.gov.br/ccivil_03/leis/l7998compilado.htm)
- [Lei 13.134/2015 — Planalto](http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2015/lei/l13134.htm)
- [Decreto 9.094/2017 — Planalto](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2017/decreto/d9094.htm)
- [**Decreto 11.034/2022 — Planalto**](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2022/decreto/d11034.htm) *(substitui o revogado Decreto 6.523/2008)*
- [Res. CODEFAT 957/2022 — LegisWeb](https://www.legisweb.com.br/legislacao/?id=436595)
- [Empregador Web — MTE](https://sd.maisemprego.trabalho.gov.br/sdweb/empregadorweb/login.jsf)
- [DATAPREV — Seguro-Desemprego](https://dataprev.gov.br/seguro-desemprego)
- Reclame Aqui — Caixa Econômica Federal e Ministério do Trabalho (filtro: "seguro desemprego")

---

---

# Changelog v2 → v3

Cada delta abaixo cita o ponto da **audit_v2** que o motivou, conforme critério B12.

| Delta | Ponto da audit_v2 | Decisão | Ação tomada na v3 |
|---|---|---|---|
| **D1** Decreto 6.523/2008 substituído pelo Decreto 11.034/2022 em todas as menções (Dimensão D, FP-05, FP-07) | **audit_v2 A1 e C1** — "norma extinta há anos"; "mandatório substituir" | **(a) Corrigir** | Todas as referências ao Decreto 6.523/2008 foram removidas e substituídas pelo **Decreto 11.034/2022** com nota explícita de revogação |
| **D2** Canal 158 retirado do título da Etapa E2 e restrito ao backstage como dependência externa | **audit_v2 A2 e C2** — "polui o escopo"; canais MTE não devem compor título de etapas | **(a) Corrigir** | E2 renomeada para "Acesso à URA Caixa (0800-726-0207)"; 158 aparece apenas na Dimensão B como dependência interorganizacional com nota explícita |
| **D3** Portal Facilita Brasil removido como plataforma de recursos; substituído por CTPS Digital/gov.br e SRTE | **audit_v2 B1** — "erro factual de arquitetura"; Facilita Brasil não é a plataforma de processamento de recursos | **(a) Corrigir** | Dimensão B (E6) e FP-14 agora referenciam **CTPS Digital / Portal gov.br → sistema SD/MTE** e **SRTE** como canais corretos |
| **D4** Causa-raiz do FP-09 reformulada; atribuição ao COMPE/STR marcada como `[PENDENTE]` | **audit_v2 B2** — "inferência técnica completamente desproporcional" baseada em Reclame Aqui | **(c) Marcar como pendente** | FP-09 agora descreve o sintoma confirmado e marca a camada de infraestrutura responsável como `[PENDENTE — validar com auditoria TCU/CGU ou BCB]` |
| **D5** Restrições de formato expandidas com proibições explícitas | **audit_v2 C3** — "aprovação cega do changelog"; inconsistência entre o que o changelog dizia e o que o corpo do texto fazia | **(a) Corrigir** | Seção de restrições agora inclui: "nunca citar Decreto 6.523/2008", "não atribuir causa-raiz a COMPE/STR sem fonte de auditoria", "canais MTE são dependências externas" |
| **D6** eSocial vs. CAGED mantido com redação da v2 | **audit_v2 A3** — "endereçado com sucesso" | **(b) Manter** | Sem alteração: eSocial (regra geral) / CAGED (hipóteses específicas) — já correto na v2 |
| **D7** Regime CDC × Lei 13.460 mantido com subsidiariedade | **audit_v2 A4** — "endereçado com sucesso" | **(b) Manter** | Sem alteração: nota de subsidiariedade já correta na v2 |
| **D8** Empregador Web e CTPS Digital mantidos | **audit_v2 A5** — "endereçado com sucesso" | **(b) Manter** | Sem alteração: ambos já presentes e bem integrados na v2 |
