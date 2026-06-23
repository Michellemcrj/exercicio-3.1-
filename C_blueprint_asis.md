# Service Blueprint AS-IS
## Atendimento ao Seguro-Desemprego pela URA da Caixa Econômica Federal

> **Versão:** 1.1
> **Metodologia:** Service Blueprint de Shostack (1984/1992)
> **Base:** Meta-Prompt v3 + decisões estruturais do grill `C_grill_transcript.md`
> **Status normativo:** G8 em aberto — aplicabilidade do Decreto 11.034/2022 à Caixa como agente pagador de política pública pendente de fundamentação

---

## 0. Tabela Blueprint — Camada × Etapa

> **Transação central:** *"O cidadão liga para a URA Caixa (0800-726-0207) para consultar o status do Seguro-Desemprego, e o serviço entrega informação objetiva e direcionamento imediato em tempo real."*
>
> **Leitura:** colunas = etapas da jornada · linhas = camadas de Shostack · ⚠ = fail point · 🔗 = dependência externa

---

| Camada \ Etapa | **E1 · Pré-Encontro** *(suporte)* | **E2 · Liga para a URA** | **E3a · Navega no Menu** | **E3b · Autentica CPF/NIS** | **E4 · Recebe Consulta e Direcionamento** |
|---|---|---|---|---|---|
| **Evidências Físicas** | TRCT · Documento de Requerimento SD (Empregador Web) · CTPS Digital · NIS / Cartão Cidadão | Tom de discagem · **Persona de voz / saudação institucional** · Sinal de feedback de conexão *(ausência = ⚠)* | **Menu falado** (arquitetura de escolha) · Tons DTMF ao pressionar teclas · Silêncio Estruturado (pausas intencionais entre frases) | Locução de confirmação "CPF recebido" · **⚠ Pausa de processamento 2–4s** *(mesma assinatura acústica do silêncio estruturado — cidadão não distingue)* · Mensagem de erro (quando CPF não reconhecido) | Locução com status do benefício e data de crédito · **Protocolo de atendimento** *(ausência = ⚠ FP-07)* · SMS pós-chamada *(eventual)* · Orientação auditiva de encaminhamento |
| **Ações do Cidadão** | Recebe TRCT e Documento de Requerimento do empregador · Reúne documentos (NIS, CPF) · Verifica prazo (7º–120º dia) | Disca 0800-726-0207 · Aguarda conexão · Ouve saudação | Ouve menu · Pressiona tecla DTMF para opção Seguro-Desemprego | Digita CPF e/ou NIS via teclado · Aguarda confirmação · *(risco: desliga durante pausa de API)* | Ouve status · Anota/memoriza data de crédito · Decide: aguardar / acessar app / ir à agência / solicitar atendente humano |
| — *Linha de Interação* — | | | | | |
| **Frontstage (URA)** | — *(cidadão ainda não está no canal)* | Reproduz saudação institucional · Apresenta menu principal | Valida entrada DTMF · Navega na árvore de menus | Envia CPF/NIS à API MTE↔Caixa · Aguarda resposta · Reproduz confirmação ou mensagem de erro | Reproduz status do benefício via locução · Oferece encaminhamentos · Transfere para atendente (quando acionado) · Encerra sessão e **deveria** gerar protocolo |
| — *Linha de Visibilidade* — | | | | **↑ Latência da API projeta-se aqui como silêncio percebido** | |
| **Backstage (Atendente Caixa)** | — | — | — *(disponível, não acionado)* | — *(disponível, não acionado)* | Recebe transferência · Acessa sistema de benefícios · Tenta resolver ou orienta presencial |
| — *Linha de Interação Interna* — | | | | | |
| **Processos de Suporte** | **Empregador** → eSocial/CAGED · **Empregador** → Empregador Web (Requerimento SD) · **DATAPREV** → pré-habilitação (CNIS/CADÚnico) · **MTE** → habilitação formal (SD-Web/SINE) | CTI/ACD (Genesys/Avaya) roteia chamada para a URA | — | **API MTE↔Caixa (SOAP/REST)** consulta base de beneficiários · DATAPREV — CNIS (origem dos dados) | Sistema de benefícios Caixa (status sincronizado com MTE) |
| **Normativos-chave** | Lei 7.998/1990 · Lei 13.134/2015 · Portaria MTE 290/2021 | Lei 13.460/2017 · Decreto 9.094/2017 · *[G8: Decreto 11.034/2022?]* | Lei 13.460/2017 · *[G8: SAC?]* | LGPD (Lei 13.709/2018) · Decreto 9.094/2017, art. 2º (CPF como ID suficiente) | Lei 13.460/2017, art. 9º (protocolo) · Lei 9.784/1999 · Res. CODEFAT 957/2022 |
| **⚠ Fail Points** | 🔗 **FP-01** Empregador não envia eSocial/CAGED *(causa-raiz externa)* · 🔗 **FP-02** Empregador não emite Requerimento no Empregador Web *(causa-raiz externa)* · 🔗 **FP-03** Canal 158 congestionado >70 tentativas *(interorganizacional MTE)* | **FP-E2-01** URA indisponível sem feedback ao cidadão | **FP-05** Loop de menu sem saída para atendente humano *(omissão de design — falha backstage→frontstage; resolve: gestão do canal, não TI)* | **FP-04a** ⚡ Abandono por silêncio de API *(omissão de design: ausência de áudio de espera; posicionado na linha de visibilidade)* · **FP-04b** CPF/NIS não reconhecido *(falha de comunicação: URA não informa sua limitação)* | **FP-06** Status divergente MTE↔Caixa · **FP-07** Nenhum protocolo gerado · **FP-01b** Sem fraseologia de contingência (DATAPREV indisponível) · **FP-02b** "Vínculo não encontrado" sem distinção de causa |

---

### → Fronteiras Externas de Handoff (após encerramento da sessão)

| Handoff | Canal | Artefatos | ⚠ Fail Points |
|---|---|---|---|
| **E5 · Pagamento** | App CAIXA Tem · ATM · Lotérica · CAIXA Aqui · Agência | Notificação push · Extrato · Comprovante de saque · Conta Poupança Social Digital | FP-08 Conta errada · FP-09 Parcela emitida não creditada *(hipóteses A/B/C — causa pendente)* · FP-10 Bloqueio sem comunicação · FP-13 Cartão Cidadão indisponível |
| **E6 · Recurso** | CTPS Digital / gov.br (sistema SD/MTE) · SRTE presencial | Formulário de recurso · Protocolo MTE · Documentação física | FP-11 Sem crédito após 30 dias · FP-12 Barreira gov.br (nível de conta) · FP-14 Recurso sem resposta (Lei 9.784/1999, art. 49) · FP-15 Redirecionamento à agência por falha da URA |

---

### Legenda de tipos de fail point

| Símbolo / Rótulo | Significado |
|---|---|
| ⚠ | Fail point da transação da URA (dentro do escopo da Caixa) |
| 🔗 | Causa-raiz externa — documentada nas dependências, não na camada de fail points da URA |
| *(omissão de design)* | A URA não falhou tecnicamente — falhou em isolar o erro externo e comunicar ao cidadão |
| *(falha de integração)* | Dado ou status divergente entre sistemas de organismos distintos |
| *[G8 em aberto]* | Normativo aplicável pendente de fundamentação |

---

## 1. Definição da Transação Central

> *"O cidadão liga para a URA Caixa (0800-726-0207) para **consultar o status do Seguro-Desemprego**, e o serviço entrega **informação objetiva e direcionamento imediato** em **tempo real**."*

**Critério da Agência Operacional** (derivado do grill G2):

| Elemento | Posição no Blueprint | Justificativa |
|---|---|---|
| E1 — Pré-contato & Habilitação | Processos de Suporte pré-encontro | Prestador em E1 é MTE/Empregador/DATAPREV — fora da agência operacional da Caixa |
| E2 → E3 → E4 | Etapas ativas da transação | A Caixa é prestadora; o cidadão age; há troca de valor em tempo real |
| E5 — Processamento & Pagamento | Fronteira externa de handoff | Ocorre após encerramento da sessão; prestador é infraestrutura Caixa/COMPE |
| E6 — Recurso & Presencial | Fronteira externa de handoff | Ocorre em outro canal, outro tempo, outro prestador (MTE/SRTE) |

---

## 2. Arquitetura de Camadas (Shostack)

```
╔══════════════════════════════════════════════════════════════════════╗
║  CAMADA 1 — EVIDÊNCIAS FÍSICAS                                       ║
║  (artefatos auditivos, táteis e digitais percebidos pelo cidadão)    ║
╠══════════════════════════════════════════════════════════════════════╣
║  CAMADA 2 — AÇÕES DO CIDADÃO                                         ║
║  (o que o cidadão faz ativamente em cada etapa)                      ║
╠══════════════════════════════════════════════════════════════════════╣
  ━━━━━━━━━━━━━━━━━ LINHA DE INTERAÇÃO ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
╠══════════════════════════════════════════════════════════════════════╣
║  CAMADA 3 — FRONTSTAGE                                               ║
║  (URA: locução, menus, autenticação, resposta de status)             ║
╠══════════════════════════════════════════════════════════════════════╣
  ━━━━━━━━━━━━━━━━━ LINHA DE VISIBILIDADE ━━━━━━━━━━━━━━━━━━━━━━━━━━
╠══════════════════════════════════════════════════════════════════════╣
║  CAMADA 4 — BACKSTAGE                                                ║
║  (Atendente humano Caixa: poderia interagir, mas ainda não acionado) ║
╠══════════════════════════════════════════════════════════════════════╣
  ━━━━━━━━━━━━━━━━━ LINHA DE INTERAÇÃO INTERNA ━━━━━━━━━━━━━━━━━━━━━
╠══════════════════════════════════════════════════════════════════════╣
║  CAMADA 5 — PROCESSOS DE SUPORTE                                     ║
║  (DATAPREV · API MTE↔Caixa · CTI/ACD · eSocial/CAGED · Empregador  ║
║   Web — nenhuma pessoa opera diretamente para aquele cidadão)        ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 3. Blueprint — Camadas × Etapas

> **Leitura:** percorra as colunas (etapas da transação) e leia cada linha (camada) de cima para baixo. As linhas divisórias separam o que o cidadão percebe do que não percebe.

---

### PRÉ-ENCONTRO — E1: Pré-contato & Habilitação
*(Processos de Suporte — não é etapa da jornada do cidadão; injeta dados que a URA processará em E3)*

| Camada | Conteúdo |
|---|---|
| **Processos de Suporte** | **Empregador** envia dados de desligamento via **eSocial** (regra geral) ou **CAGED** (órgãos públicos, organismos internacionais, optantes Grupo 4) |
| | **Empregador** emite Requerimento do SD via **Empregador Web** (sd.maisemprego.trabalho.gov.br) — gera Documento de Requerimento entregue ao trabalhador |
| | **DATAPREV** executa pré-habilitação cruzando CNIS, CADÚnico, CAGED/eSocial |
| | **MTE** valida habilitação formal via SD-Web / SINE / SRTE |
| **Dependência crítica** | Sem envio do eSocial/CAGED → DATAPREV não executa pré-habilitação → URA retorna "vínculo não encontrado" em E3 *(causa-raiz externa — ver Dependências)* |
| **Artefatos gerados** | Documento de Requerimento do SD · TRCT · Carteira de Trabalho Digital · NIS / Cartão Cidadão |

---

### ETAPA E2 — Acesso à URA Caixa

| Camada | Conteúdo |
|---|---|
| **Evidências Físicas** | Tom de discagem ao 0800-726-0207 |
| | **Persona de voz / fraseologia da saudação** (tom, ritmo, sotaque, clareza — representação "física" da Caixa na linha) |
| | **Sinal de feedback sistêmico** de abertura de sessão (transição sonora que indica chamada conectada) |
| | *Ausência de estimativa de tempo de espera* ← fail point de evidência |
| **Ações do Cidadão** | Disca 0800-726-0207 |
| | Aguarda conexão |
| | Ouve saudação institucional |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO* |
| **Frontstage (URA)** | Reproduz saudação institucional |
| | Apresenta menu principal com opções numeradas |
| ━━━━━━━━━━━━━━ | *LINHA DE VISIBILIDADE* |
| **Backstage** | Atendente humano Caixa disponível na fila — ainda não acionado |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO INTERNA* |
| **Processos de Suporte** | CTI/ACD (Genesys/Avaya) roteia chamada para a URA |
| **Normativos** | Lei 13.460/2017, art. 5º (acesso ao serviço) · Decreto 9.094/2017 (Carta de Serviços) · *[G8 em aberto: SAC — Decreto 11.034/2022 ou regime de serviço público?]* |
| **⚠ Fail Points** | **FP-E2-01** — URA indisponível / chamada não conecta: cidadão sem feedback sobre indisponibilidade |

---

### ETAPA E3 — Navegação e Autenticação

| Camada | Conteúdo |
|---|---|
| **Evidências Físicas** | **Menu falado (arquitetura de escolha):** sequência narrada "Digite 1 para X, digite 2 para Y" — a ordem e extensão das frases definem os "corredores" de navegação |
| | **Tons DTMF:** retorno sonoro do aparelho ao pressionar teclas — interface física de digitação |
| | **Silêncio Estruturado:** pausas intencionais entre frases para processamento cognitivo do cidadão |
| | **⚠ Pausa de processamento / ausência de feedback auditivo (2–4s):** silêncio não intencional durante consulta à API — mesma assinatura acústica do Silêncio Estruturado; cidadão não distingue processamento normal de chamada caída |
| | Confirmação de CPF/NIS lida pela URA (locução de eco) |
| | Mensagem de erro (quando CPF não reconhecido) |
| **Ações do Cidadão** | Navega no menu via teclas DTMF |
| | Digita CPF e/ou NIS |
| | Aguarda confirmação |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO* |
| **Frontstage (URA)** | Valida entrada DTMF |
| | Envia CPF/NIS para API MTE↔Caixa |
| | Aguarda resposta da API |
| | Reproduz confirmação de CPF ou mensagem de erro |
| ━━━━━━━━━━━━━━ | *LINHA DE VISIBILIDADE* |
| | **↑ A latência da API projeta-se acima desta linha como silêncio percebido pelo cidadão** |
| **Backstage** | Atendente disponível — acionado apenas se cidadão solicitar ou URA transferir |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO INTERNA* |
| **Processos de Suporte** | **API MTE↔Caixa (SOAP/REST):** consulta base de beneficiários em tempo real |
| | **DATAPREV — CNIS:** origem dos dados de elegibilidade consultados |
| **Normativos** | Lei 13.709/2018 — LGPD (autenticação por CPF/NIS) · Decreto 9.094/2017, art. 2º (CPF como identificador suficiente) |
| **⚠ Fail Points** | **FP-04a — Abandono por silêncio de API** *(Omissão de Design)*: URA não emite áudio de espera durante processamento → silêncio idêntico ao estruturado → cidadão interpreta como chamada caída → desliga. **Causa-raiz:** design da URA (ausência de hold music ou locução "aguarde"). **Resolve:** Caixa — design do canal |
| | **FP-04b — CPF/NIS não reconhecido** *(Falha de Integração)*: API retorna "não encontrado" por inconsistência cadastral DATAPREV↔Caixa. URA não informa se é erro temporário ou estrutural. **Causa-raiz:** dessincronização de base. **Resolve:** DATAPREV + Caixa. *A falha visível é de comunicação: a URA não informa sua limitação ao cidadão* |
| | **FP-05 — Loop de menu sem transferência** *(Omissão de Design)*: árvore de URA sem saída para atendente humano. Falha de comunicação backstage→frontstage. **Causa-raiz:** design da URA / ausência de regra de negócio de escalonamento. **Resolve:** Caixa — gestão do canal *(não é falha de TI de infraestrutura)* |

---

### ETAPA E4 — Consulta / Orientação

| Camada | Conteúdo |
|---|---|
| **Evidências Físicas** | **Locução com status do benefício** (parcelas disponíveis, data de crédito prevista, valor) |
| | **Protocolo de atendimento** — *quando gerado* (ausência é fail point) |
| | **SMS pós-chamada** com protocolo ou link — extensão multicanal da sessão *(eventual)* |
| | Orientação auditiva de encaminhamento (gov.br / agência / SRTE) |
| **Ações do Cidadão** | Ouve status do benefício |
| | Anota ou memoriza informação |
| | Decide próximo passo (aguardar crédito / ir ao canal indicado / solicitar atendente) |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO* |
| **Frontstage (URA)** | Reproduz status do benefício via locução |
| | Oferece opções de encaminhamento |
| | Transfere para atendente humano (quando acionado) |
| | Encerra sessão e (deveria) gerar protocolo |
| ━━━━━━━━━━━━━━ | *LINHA DE VISIBILIDADE* |
| **Backstage** | **Atendente humano Caixa:** recebe transferência; acessa sistema de benefícios; tenta resolver ou orienta presencial |
| ━━━━━━━━━━━━━━ | *LINHA DE INTERAÇÃO INTERNA* |
| **Processos de Suporte** | Sistema de benefícios Caixa (consulta status sincronizado com MTE) |
| **Normativos** | Lei 13.460/2017, art. 9º (direito a protocolo) · *[G8 em aberto: Decreto 11.034/2022 ou regime público?]* (registro de atendimento obrigatório) · Lei 9.784/1999 (processo administrativo) |
| **⚠ Fail Points** | **FP-06 — Status divergente MTE↔Caixa** *(Falha de Integração)*: URA informa "benefício não encontrado" enquanto portal MTE mostra benefício habilitado. Cidadão recebe informação falsa negativa. **Causa-raiz:** latência ou falha na sincronização API. **Resolve:** Caixa + MTE |
| | **FP-07 — Nenhum protocolo gerado** *(Omissão de Design)*: sessão encerra sem protocolo; sem SMS. Cidadão sem comprovação de contato para futuro recurso. **Causa-raiz:** URA não emite protocolo sistematicamente. **Resolve:** Caixa — design do canal |
| | **FP-01b — Ausência de fraseologia de contingência (DATAPREV indisponível)** *(Omissão de Design)*: quando DATAPREV não responde, URA silencia ou derruba a chamada em vez de comunicar erro externo e orientar o cidadão. **Causa-raiz:** ausência de script de contingência. **Resolve:** Caixa — design do canal |
| | **FP-02b — "Vínculo não encontrado" sem distinção de causa** *(Omissão de Design)*: URA não diferencia se o problema é ausência de dados (empregador não enviou eSocial) ou dado incorreto (inconsistência DATAPREV). Cidadão não sabe se volta ao empregador, ao MTE ou à Caixa. **Causa-raiz:** ausência de lógica de diagnóstico na URA. **Resolve:** Caixa — design do canal |

---

## 4. Fronteiras Externas de Handoff

*(Ocorrem após o encerramento da sessão da URA — não são etapas internas do blueprint)*

### → E5: Processamento & Pagamento

| Elemento | Conteúdo |
|---|---|
| **O que a URA entregou** | Direcionamento: "sua parcela está disponível" ou "acesse o app / vá à agência" |
| **O que acontece depois** | Caixa gera Ordem de Pagamento (OP) → COMPE/STR/PIX executa crédito → cidadão acessa via app CAIXA Tem, ATM, lotérica, CAIXA Aqui, agência |
| **Artefatos do handoff** | Notificação push (app CAIXA Tem) · extrato bancário · comprovante de saque · Conta Poupança Social Digital |
| **⚠ Fail Points do handoff** | **FP-08** — Crédito em conta errada (poupança vs. corrente) |
| | **FP-09** — Parcela emitida, não creditada *(ver estrutura abaixo)* |
| | **FP-10** — Parcela bloqueada sem comunicação proativa |
| | **FP-13** — Parcela indisponível via Cartão Cidadão |

**Estrutura de FP-09 — Parcela emitida, não creditada:**
```
FP-09 — Parcela emitida, não creditada
│
├── Causa-raiz: [EM INVESTIGAÇÃO — validar com auditoria TCU/CGU/BCB]
│   ├── Hipótese A: Rejeição COMPE/STR (TED devolvido por dígito inválido)
│   ├── Hipótese B: Estorno por regra MTE (conta inelegível)
│   └── Hipótese C: Bloqueio antifraude Caixa (CPF/conta inconsistente)
│
├── Fail point de design CONFIRMADO (independe da hipótese):
│   URA recebe código genérico → informa "disponível" →
│   cidadão vai à agência desnecessariamente
│   [Falha por Omissão de Diagnóstico]
│
└── Indicador de proxy (enquanto causa-raiz não é isolada):
    % do transbordo humano gerado por status "emitido/não creditado"
```

### → E6: Recurso & Comparecimento Presencial

| Elemento | Conteúdo |
|---|---|
| **O que a URA entregou** | Direcionamento: "procure uma agência Caixa ou o portal gov.br" |
| **O que acontece depois** | Cidadão interpõe recurso via CTPS Digital / Portal gov.br → sistema SD/MTE, ou comparece presencialmente à SRTE |
| **Artefatos do handoff** | Formulário de recurso digital (sistema SD/MTE) · protocolo de recurso MTE · documentação física para SRTE |
| **⚠ Fail Points do handoff** | **FP-11** — Benefício não liberado após 30 dias sem feedback |
| | **FP-12** — Barreira de acesso ao gov.br / app (nível de conta insuficiente) |
| | **FP-14** — Recurso administrativo sem resposta (Lei 9.784/1999, art. 49: prazo de 30 dias) |
| | **FP-15** — Redirecionamento à agência para resolver falha originada na URA |

---

## 5. Inventário Consolidado de Fail Points

### 5.1 Fail Points da Transação da URA (E2–E4)

| # | Nome | Tipo | Etapa | Causa-raiz | Responsável |
|---|---|---|---|---|---|
| FP-E2-01 | URA indisponível sem feedback | Falha de infraestrutura | E2 | CTI/ACD fora do ar | Caixa — TI |
| FP-04a | Abandono por silêncio de API | **Omissão de Design** | E3 — linha de visibilidade | Ausência de áudio de espera | Caixa — design do canal |
| FP-04b | CPF/NIS não reconhecido | Falha de integração + omissão de comunicação | E3 | Dessincronização DATAPREV↔Caixa | DATAPREV + Caixa |
| FP-05 | Loop de menu sem transferência | **Omissão de Design** | E3 | Regra de escalonamento ausente na URA | Caixa — gestão do canal |
| FP-06 | Status divergente MTE↔Caixa | Falha de integração | E4 | Latência/falha na sync da API | Caixa + MTE |
| FP-07 | Nenhum protocolo gerado | **Omissão de Design** | E4 | URA não emite protocolo | Caixa — design do canal |
| FP-01b | Sem fraseologia de contingência | **Omissão de Design** | E4 | Ausência de script para DATAPREV indisponível | Caixa — design do canal |
| FP-02b | "Não encontrado" sem diagnóstico | **Omissão de Design** | E4 | Ausência de lógica de distinção de causa | Caixa — design do canal |

### 5.2 Dependências Externas (causas-raiz — documentadas fora da camada de fail points da URA)

| # | Nome | Onde ocorre | Causa | Consequência na URA |
|---|---|---|---|---|
| FP-01 | Empregador não envia eSocial/CAGED | E1 — Processos de Suporte | Inadimplência do empregador | URA retorna "vínculo não encontrado" → dispara FP-02b |
| FP-02 | Empregador não emite Requerimento (Empregador Web) | E1 — Processos de Suporte | Desconhecimento ou omissão do empregador | Cidadão chega à URA sem habilitação → dispara FP-02b |
| FP-03 | Canal 158 congestionado | Dependência interorganizacional MTE | Subdimensionamento MTE | Cidadão bloqueia antes de acessar a URA Caixa |

### 5.3 Fail Points dos Handoffs (E5 / E6)

| # | Nome | Handoff | Causa | Ator |
|---|---|---|---|---|
| FP-08 | Crédito em conta errada | E5 | Conta poupança social aberta sem comunicação | Caixa |
| FP-09 | Parcela emitida, não creditada | E5 | [Em investigação — Hipóteses A/B/C] | [Pendente] |
| FP-10 | Parcela bloqueada sem comunicação | E5 | Ausência de notificação push/SMS | Caixa / MTE |
| FP-11 | Benefício não liberado após 30 dias | E5→E6 | Análise manual MTE sem SLA | MTE / DATAPREV |
| FP-12 | Barreira de acesso ao gov.br | E6 | Nível de conta gov.br insuficiente | Gov.br / Serpro |
| FP-13 | Parcela indisponível no Cartão Cidadão | E5→E6 | Cartão bloqueado / NIS inconsistente | Caixa |
| FP-14 | Recurso sem resposta no MTE | E6 | Subdimensionamento MTE; ausência de SLA | MTE |
| FP-15 | Redirecionamento à agência por falha da URA | E6 | Design sem resolução no canal | Caixa |

---

## 6. Normativos por Camada

| Camada / Etapa | Normativo | Dispositivo | O que exige do prestador | O que garante ao cidadão |
|---|---|---|---|---|
| E1 — Suporte | Lei 7.998/1990 (arts. 2º–7º) | Base do Programa SD e FAT | Gerir o benefício conforme critérios legais | Receber SD se elegível |
| E1 — Suporte | Lei 13.134/2015 | Atualização de elegibilidade | Aplicar critérios revisados | Acesso conforme novas regras |
| E1 — Suporte | Portaria MTE 290/2021 | Prazos de requerimento | Receber requerimento no prazo (7º–120º dia) | Prazo garantido |
| E2–E4 | Lei 13.460/2017 (arts. 5º–9º) | Direitos do usuário de serviço público | Regularidade, eficiência, cortesia, protocolo | Protocolo, prazo de resposta, canal de reclamação |
| E2–E4 | Decreto 9.094/2017 (arts. 1º–5º) | Simplificação / Carta de Serviços | Não exigir dados já disponíveis em bases oficiais; CPF como identificador suficiente | Atendimento simplificado |
| E2–E4 | *[G8 em aberto]* Decreto 11.034/2022 **ou** regime de serviço público | SAC — transferência, protocolo, acessibilidade | Transferência para atendente; protocolo; acessibilidade | Atendimento efetivo e registrado |
| E3–E4 | Lei 13.709/2018 — LGPD | Tratamento de dados pessoais | Segurança e finalidade no uso dos dados | Proteção dos dados |
| E3–E4 | Res. CODEFAT 957/2022 e 1.031/2025 | Regras operacionais do benefício | Processar conforme resoluções vigentes | Benefício calculado conforme regras |
| E2–E4 | Lei 8.078/1990 — CDC | Subsidiário quando há relação de consumo com a Caixa (Lei 13.460, art. 1º, §2º) | Atendimento de qualidade na dimensão consumerista | Direito à informação clara e adequada |
| E5 — Handoff | Res. CMN 3.876 | Conta poupança social simplificada | Oferecer conta acessível | Recebimento mesmo sem conta convencional |
| E6 — Handoff | Lei 9.784/1999 (art. 49) | Processo administrativo federal | Decidir recurso com motivação em até 30 dias | Direito ao recurso fundamentado |

---

## 7. Decisões Estruturais Incorporadas

*(Rastreabilidade: cada decisão cita o bloco do grill que a gerou)*

| Decisão | Origem |
|---|---|
| E1 posicionado como processos de suporte pré-encontro, não etapa da jornada | G1 + G2 + G4 |
| E5 e E6 como fronteiras externas de handoff (Critério da Agência Operacional) | G2 |
| Atendente humano Caixa → Backstage (acima da linha de interação interna) | G4 |
| DATAPREV / API / COMPE / CTI → Processos de Suporte (abaixo da linha de interação interna) | G4 |
| FP-04 desdobrado em FP-04a (silêncio de API) e FP-04b (inconsistência cadastral) | G3 |
| "Pausa de processamento / ausência de feedback auditivo" adicionada às evidências físicas de E3 | G3 + G5 |
| FP-05 classificado como falha de processo/design backstage→frontstage (não de TI) | G4 — implicação 1 |
| FP-04b reformulado como falha de comunicação, não de dados | G4 — implicação 2 |
| FP-01 e FP-02 migrados para documentação de dependências externas | G6 |
| FP-01b e FP-02b criados como Falhas por Omissão de Design | G6 |
| FP-09 estruturado com funil de hipóteses A/B/C + fail point de comunicação confirmado + indicador de proxy | G7 |
| "Tempo de espera percebido" removido da camada de evidências físicas (é KPI) | G5 |
| Decreto 6.523/2008 removido (revogado) — substituição pelo Decreto 11.034/2022 marcada como [G8 em aberto] | audit_v2 A1 + G8 |
