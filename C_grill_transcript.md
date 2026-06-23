# Transcript — /grill-me: Service Blueprint AS-IS · URA Seguro-Desemprego

> **Artefato de referência:** `B_relatorio_assistente_v3.md`
> **Metodologia:** Service Blueprint de Shostack
> **Data:** 2026-06-22
> **Status:** G8 em aberto

---

## BLOCO 1 — Escopo e identidade do blueprint

### G1 — Por que E1 não configura escopo inflado?

**Avaliador:**
Você definiu o escopo como "URA da Caixa", mas o blueprint começa em E1 (Pré-contato & Habilitação) — que ocorre inteiramente no MTE, no empregador e no DATAPREV, semanas antes de o cidadão discar o 0800-726-0207. Por que isso não configura escopo inflado? Se você retirou o 158 da linha de frente por ser canal do MTE, como justifica manter E1 — que é 100% MTE e empregador — dentro das etapas do blueprint?

**Candidato (resposta 1):**
Canal E1 É A ETAPA ANTERIOR ao 158 da linha de frente e o 0800 apresenta muitas tentativas sem respostas >70.

**Avaliador — pressão:**

*Pressão 1 — O argumento cronológico não é argumento de escopo.*
Shostack não delimita o blueprint por ordem temporal de vida do cidadão. Ele delimita pelo service encounter: o momento em que o prestador do serviço mapeado e o cliente entram em contato e há troca de valor. Pergunta direta: quem é o prestador em E1? É o empregador, o MTE, ou a Caixa? Se não é a Caixa, E1 não é etapa do blueprint da URA — é pré-condição externa documentada como dependência.

*Pressão 2 — Você misturou dois canais diferentes na defesa.*
O canal com congestionamento documentado é o 158 (MTE), não o 0800-726-0207 (Caixa). São canais de órgãos distintos. Confirme: o dado de ">70 tentativas" se refere ao 158 ou ao 0800-726-0207?

*Pressão 3 — Se E1 entra, onde você traça o limite?*
Se a lógica é "incluo tudo que antecede e condiciona o acesso à URA", você deveria incluir também a negociação trabalhista que levou à rescisão, a obrigação do FGTS, o cadastro no eSocial na admissão. Qual é o critério objetivo que inclui E1 mas exclui esses?

**Candidato (resposta 2):**
A esmagadora maioria dos erros e das "linhas mortas" que o cidadão tenta resolver na URA da Caixa (ex: vínculo não encontrado, divergência de PIS) é gerada exatamente em E1 (no envio do eSocial pelo empregador ou no processamento da DATAPREV). Se omitirmos E1 do blueprint, a URA vira uma caixa-preta. Não conseguiríamos mapear por que o cidadão está ligando para a Caixa frustrado, e as soluções na URA seriam apenas paliativas (sintoma), em vez de informativas sobre a origem do problema (causa raiz).

**Avaliador — pressão residual:**
O raciocínio de causa-raiz é correto. Mas E1 não pertence à linha temporal do cliente — o cidadão não age em E1, ele é objeto de ações de terceiros. Solução: posicionar E1 como **processos de suporte pré-encontro** (dentro do blueprint, camada inferior), não como etapa da jornada do cidadão.

**Candidato — esclarecimento de canal:**
Canal 158 está com maior de 70 tentativas sem resposta.

**Avaliador — status G1:**
Confirmado: dado pertence ao 158 (MTE) — FP-03 interorganizacional, fora do escopo da Caixa. G1 parcialmente resolvido; posicionamento de E1 na camada correta retomado em G4.

---

### G2 — A transação central do blueprint

**Avaliador:**
Complete em no máximo duas linhas: *"O cidadão liga para a URA Caixa para _____________, e o serviço entrega _____________ em _____________."*

**Candidato:**
"O cidadão liga para a URA Caixa para **consultar o status do Seguro-Desemprego**, e o serviço entrega **informação objetiva e direcionamento imediato** em **tempo real**."

**Avaliador — consequências da definição:**

*Consequência 1 — E5 e E6 estão no lugar errado.*
Se a transação é "consultar status + direcionamento imediato", E5 (Processamento & Pagamento) e E6 (Recurso & Presencial) não são executados pela URA — ocorrem após o encerramento da chamada, em outros canais, com outros prestadores. Pertencem ao blueprint da URA ou são o que acontece quando a URA falha em entregar direcionamento suficiente?

*Consequência 2 — "Direcionamento" expõe limitação estrutural.*
A URA não resolve, ela informa e encaminha. Fail points que ocorrem após a chamada são do blueprint da URA ou do serviço ampliado de Seguro-Desemprego?

*Consequência 3 — E1 se reposiciona sozinho.*
Com essa definição, E1 é pré-condição de sucesso da consulta, não etapa da consulta.

**Candidato — Critério da Agência Operacional:**
Para garantir o rigor metodológico, adoto o **Critério da Agência Operacional**. E5 e E6 devem figurar estritamente como fronteiras externas documentadas. Diferente de E1 — que injeta dados que a URA é obrigada a processar em tempo real —, o que ocorre em E5 (canais digitais) e E6 (saque/agência) acontece após o encerramento da sessão da URA. Tratá-los como etapas internas inflaria o escopo, fundindo o blueprint da URA com o do App e das Agências. Eles ficam na borda do mapa para garantir a qualidade do handoff, mas o foco do desenho se fecha na transação do canal telefônico.

**Avaliador — status G2:** Argumento aceito. Estrutura que emerge: blueprint com quatro etapas ativas (E2→E3→E4 como núcleo + E1 como suporte pré-encontro) e E5/E6 como fronteiras de handoff na borda.

---

## BLOCO 2 — Camadas e linhas divisórias (Shostack)

### G3 — Linha de visibilidade e latência da API

**Avaliador:**
O cidadão digita o CPF, a URA para de falar, há silêncio de 2 a 4 segundos enquanto a API responde — então a locução retorna com o status. Esse silêncio é percebido pelo cidadão. Na lógica de Shostack, a linha de visibilidade separa o que o cliente percebe do que não percebe. A latência da API fica acima ou abaixo da linha de visibilidade — e o que isso implica para onde você posiciona o risco de abandono de chamada por timeout?

**Candidato:**
A latência da API fica **ABAIXO** da linha de visibilidade, mas o seu impacto imediato (o risco de abandono) se projeta **ACIMA** dela.

**Avaliador — pressão:**
Se o impacto projeta acima da linha, o silêncio de 2–4s não é detalhe operacional — é **artefato de evidência física** que pertence à camada mais alta do blueprint. "Pausa de processamento / ausência de feedback auditivo" está ausente da Dimensão C. Você aceita desdobrar FP-04 em dois fail points distintos e adicionar "ausência de feedback auditivo durante processamento" na camada de evidências físicas de E3?

| | FP-04a | FP-04b |
|---|---|---|
| **Nome** | Abandono por silêncio de API | CPF/NIS não reconhecido |
| **Posição** | Na linha de visibilidade | Abaixo, com projeção acima |
| **Causa-raiz** | Design da URA (sem áudio de espera) | Inconsistência DATAPREV ↔ Caixa |
| **Resolve** | Caixa (URA) | DATAPREV + Caixa |

**Candidato:** Sim.

**Avaliador — status G3:** Fechado. FP-04 desdobrado em FP-04a e FP-04b. "Pausa de processamento / ausência de feedback auditivo" adicionada às evidências físicas de E3.

---

### G4 — Colapso de camadas: Backstage vs. Processos de Suporte

**Avaliador:**
Shostack distingue Backstage (pessoas que poderiam interagir com o cliente mas não interagem naquele momento) de Processos de Suporte (infraestrutura que nenhuma pessoa opera diretamente para aquele cliente). DATAPREV, API MTE↔Caixa, Atendente humano Caixa, COMPE/STR/PIX e CTI/ACD estão todos no mesmo "bastidor". Quais pertencem ao backstage e quais aos processos de suporte?

**Candidato:**
Atendente humano Caixa pertence ao **backstage**. Os outros sistemas (DATAPREV, API MTE↔Caixa, COMPE/STR/PIX, CTI/ACD) pertencem aos **processos de suporte** de Shostack.

**Avaliador — implicações:**

*Implicação 1 — FP-05 muda de camada e de responsável.*
A falha de transferência é problema de comunicação backstage→frontstage. Solução é de processo e design de URA, não de TI de infraestrutura. Responsável: gestor do canal Caixa.

*Implicação 2 — Falhas no DATAPREV são irresoluveis em tempo real.*
DATAPREV está nos processos de suporte — abaixo da linha de interação interna. Nenhuma pessoa da Caixa pode intervir durante a sessão. A falha visível de FP-04b é de comunicação, não de dados.

*Implicação 3 — E1 encontra sua camada definitiva.*
eSocial, DATAPREV, Empregador Web → processos de suporte pré-encontro.

**Candidato:** Confirmo as três implicações.

**Avaliador — status G4:** Fechado. Estrutura de três camadas consolidada:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  EVIDÊNCIAS FÍSICAS
  AÇÕES DO CIDADÃO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ linha de interação
  FRONTSTAGE  — URA (locução, menus, locução de status)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ linha de visibilidade
  BACKSTAGE   — Atendente humano Caixa
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ linha de interação interna
  PROCESSOS DE SUPORTE — DATAPREV · API MTE↔Caixa
                         COMPE/STR · CTI/ACD · eSocial
                         (inclui E1 como pré-encontro)
```

---

## BLOCO 3 — Evidências físicas

### G5 — Evidências físicas de E2

**Avaliador:**
"Tempo de espera percebido" está listado como evidência física de E2. É evidência física ou indicador de qualidade de serviço — e o que seria a evidência física real de E2?

**Candidato (resposta 1 — conceitual):**
"Tempo de espera percebido" é um indicador de qualidade de serviço (dimensão da Empatia e Receptividade), não uma evidência física.

**Avaliador — pressão:**
Você respondeu o conceito, não os artefatos. Nomeie três artefatos auditivos concretos de E2 e para cada um responda: o que acontece quando está ausente? O cidadão consegue distinguir falha técnica de normalidade?

**Candidato (resposta completa):**

*1. Elementos Sonoros e Acústicos*
- **Fraseologia e Persona da Voz:** tom, ritmo, sotaque e clareza da gravação. Representação "física" da Caixa na linha.
- **Sinais de Feedback Sistêmico:** "bip" de confirmação após digitar dado, som de clique de transição de menu, música de espera que prova que a ligação não caiu.
- **Silêncio Estruturado:** pausas milimetricamente calculadas entre frases para que o usuário processe a informação.

*2. Elementos de Interface e Entrada*
- **Tons DTMF:** som gerado quando o cidadão aperta as teclas. Retorno sonoro do próprio aparelho como interface física de digitação.
- **Menu Falado (Arquitetura de Escolha):** sequência de opções narradas. Ordem e tamanho das frases funcionam como "paredes" e "corredores" de navegação.

*3. Elementos Periféricos Vinculados*
- **SMS de Confirmação:** extensão física da chamada no canal multimodal.

*Teste de Shostack proposto pelo candidato:*
"Se você puder gravar, medir a frequência em Hertz, cronometrar a duração exata em segundos ou receber na tela do celular, é evidência física. O 'tempo de espera percebido', o 'nervosismo do cidadão' ou a 'sensação de burocracia' são reações psicológicas geradas por essas evidências."

**Avaliador — status G5:** Fechado. Inventário consolidado de evidências físicas E2–E3:

| Artefato | Camada Shostack | Fail point quando ausente |
|---|---|---|
| Fraseologia / Persona de voz | Frontstage | Locução ininteligível → abandono |
| Sinal de feedback sistêmico (bip, clique) | Frontstage | Silêncio ambíguo → FP-04a |
| Silêncio estruturado (pausas intencionais) | Frontstage | Ausente → sobrecarga cognitiva |
| Silêncio de API (não intencional) | Linha de visibilidade | Indistinguível do estruturado → FP-04a |
| Tons DTMF | Evidência física tátil/auditiva | Ausente → entrada não confirmada |
| Menu falado (arquitetura de escolha) | Frontstage | Menu longo/confuso → FP-05 |
| SMS pós-chamada | Extensão multicanal | Ausente → FP-07 (sem protocolo) |

---

## BLOCO 4 — Fail points

### G6 — Critério de inclusão de fail points

**Avaliador:**
FP-01 (empregador não envia eSocial) e FP-02 (empregador não emite Requerimento) ocorrem inteiramente nos processos de suporte pré-encontro. O cidadão não experimenta FP-01 na URA — experimenta a consequência quando a URA retorna "vínculo não encontrado". Pelo critério de Shostack, são fail points do blueprint da URA ou causas-raiz de fail points pertencentes à documentação de dependências?

**Candidato:**
Pelo critério estrito de Shostack, FP-01 e FP-02 não devem figurar na camada de fail points do mapa da URA; eles são causas-raiz externas que pertencem à documentação de dependências.

*1. O que é um Fail Point segundo Shostack:*
Um fail point é um nó específico dentro do fluxo do serviço onde o processo corre risco de colapsar ou gerar insatisfação direta no cliente. Se o eSocial está fora do ar ou o empregador errou o envio, a URA não falhou em nada. Ela executou sua programação perfeitamente: consultou a base e não achou o registro. O colapso do dado não é fail point da URA — é dado de entrada corrompido da camada de suporte.

*2. O mapeamento correto em cascata:*
- **Processos de Suporte (Dependências):** FP-01 e FP-02 como riscos de indisponibilidade ou inconsistência de dados (causa-raiz).
- **Fail Points da URA (Frontstage/Backstage):** a incapacidade da URA de tratar esse erro técnico. Se a DATAPREV cai e a URA derruba a ligação ou produz silêncio eterno, esse silêncio ou queda é o legítimo fail point da URA — falha de omissão de design.

*Síntese do candidato:*
"FP-01 e FP-02 pertencem à documentação de dependências. A URA não pode controlar o eSocial ou a DATAPREV. O fail point legítimo do blueprint deve ser posicionado na reação da URA a essas falhas externas. Se o suporte falha, o papel do design da URA é garantir que o canal não colapse junto, criando fraseologia de contingência que isole o problema e oriente o cidadão."

**Avaliador — status G6:** Fechado. FP-01 e FP-02 migram para documentação de dependências. Substituídos por:

- **FP-01b:** Ausência de fraseologia de contingência quando DATAPREV está indisponível.
- **FP-02b:** Ausência de orientação ao cidadão quando Requerimento não foi emitido pelo empregador — URA retorna "vínculo não encontrado" sem distinguir causa.

**Categoria nova derivada pelo candidato:**

| Tipo de fail point | Exemplo | Responsável |
|---|---|---|
| Falha de infraestrutura própria | CTI cai, URA desconecta | Caixa — TI |
| Falha de integração | API MTE↔Caixa retorna erro | Caixa + MTE |
| **Omissão de design** | DATAPREV indisponível → URA silencia ou derruba | Caixa — design do canal |
| Causa-raiz externa | eSocial não enviado | Empregador / MTE — nas dependências |

---

### G7 — O fail point sem causa-raiz

**Avaliador:**
FP-09 está marcado como `[PENDENTE — validar em campo]`. Em um blueprint AS-IS, um fail point sem causa-raiz confirmada serve para alguma coisa analiticamente — ou é um buraco no mapa? O que você coloca no lugar enquanto o dado real não existe?

**Candidato:**
Um item marcado como [PENDENTE] em um blueprint AS-IS não é um buraco inútil — é um dos pontos mais valiosos do mapa. Um blueprint serve tanto para consolidar certezas quanto para **mapear a nossa ignorância**.

*Utilidade analítica do [PENDENTE]:*
1. **Direcionamento de Esforço de Pesquisa:** funciona como farol de priorização para UX Research / Análise de Negócios.
2. **Gestão de Risco Pragmática:** para tomadores de decisão, sinaliza onde o projeto está "pisando em gelo fino". Melhor um mapa que avisa "aqui há neblina" do que um mapa que finge que a estrada é reta.
3. **Hipótese de Impacto:** mesmo sem causa-raiz, o efeito de frontstage é conhecido e quantificável (volume de chamadas geradas por status "Emitido").

*O que colocar no lugar enquanto o dado real não existe:*

**1. Funil de Hipóteses (vetores de probabilidade):**
- Hipótese A (Infra Bancária): Rejeição COMPE/STR (TED/DOC devolvido por dígito inválido).
- Hipótese B (Regra de Negócio MTE): Estorno por não validação de conta elegível.
- Hipótese C (Compliance Caixa): Bloqueio antifraude ou inconsistência de CPF.

**2. Fail Point de Comunicação (o que a URA faz HOJE):**
Seja qual for a causa-raiz (A, B ou C), a falha de design da URA no AS-IS é idêntica: recebe código genérico (ex: "Erro 99") e informa ao cidadão que o dinheiro está disponível — gerando ida inútil à agência.

**3. Indicador de Proxy:**
Se não há dado de causa-raiz, coloca-se dado de sintoma. Exemplo: "FP-09 [Causa em Investigação] — Responde por 14% do transbordo humano na árvore de pagamento."

*Síntese do candidato:*
"No blueprint AS-IS, um ponto marcado como [PENDENTE] não é um buraco; é um achado de pesquisa. Ele indica que o processo é tão opaco que nem a própria organização domina sua causa-raiz em tempo real. Enquanto o dado de campo não isola se o erro é da COMPE, do MTE ou da Caixa, o FP-09 permanece ali como uma **Falha por Omissão de Diagnóstico**, nos forçando a desenhar uma URA futura que, no mínimo, saiba dizer ao cidadão: 'Seu pagamento retificou, estamos verificando com o seu banco. Não vá à agência ainda'."

**Avaliador — status G7:** Fechado. Framework de tratamento de incerteza consolidado:

```
FP-09 — Parcela emitida, não creditada
│
├── Causa-raiz: [EM INVESTIGAÇÃO]
│   ├── Hipótese A: Rejeição COMPE/STR
│   ├── Hipótese B: Estorno por regra MTE
│   └── Hipótese C: Bloqueio antifraude Caixa
│
├── Fail point de design CONFIRMADO (independe da hipótese):
│   URA recebe código genérico → informa "disponível" →
│   cidadão vai à agência desnecessariamente
│
└── Indicador de proxy:
    % do transbordo humano gerado por status "emitido/não creditado"
```

---

## BLOCO 5 — Normativo e operação

### G8 — O normativo que sustenta a URA [EM ABERTO]

**Avaliador:**
Você substituiu o Decreto 6.523/2008 pelo Decreto 11.034/2022. O Decreto 11.034/2022 regula o SAC de empresas que atuam em setores regulados pelo poder público federal (telecomunicações, energia, planos de saúde, serviços financeiros). A Caixa, operando a URA especificamente para atender ao benefício público Seguro-Desemprego, está atuando como **agente pagador de política pública federal** — não como prestadora de serviço financeiro ao consumidor.

**Pergunta em duas partes:**
1. O Decreto 11.034/2022 se aplica à Caixa neste contexto específico — ou a obrigação de atendimento telefônico adequado vem de outro instrumento normativo?
2. Se o Decreto 11.034/2022 não se aplica diretamente, qual norma ocupa esse lugar — e o que muda nas obrigações concretas da Caixa (tempo de transferência, protocolo, acessibilidade)?

**Candidato:** `[RESPOSTA PENDENTE]`

---

## Consolidado de decisões estruturais da sessão

| Elemento | Posição anterior (v3) | Posição correta (pós-grill) | Motivação |
|---|---|---|---|
| E1 (eSocial, DATAPREV, Empregador Web) | Etapa da jornada | Processos de suporte pré-encontro | G1 + G2 + G4 |
| E5 / E6 | Etapas internas | Fronteiras externas de handoff | G2 — Critério da Agência Operacional |
| Atendente humano Caixa | Backstage genérico | Backstage — acima da linha de interação interna | G4 |
| DATAPREV / API / COMPE / CTI | Backstage genérico | Processos de suporte — abaixo da linha de interação interna | G4 |
| FP-04 | Um fail point (CPF não reconhecido) | Dois: FP-04a (silêncio de API) + FP-04b (inconsistência cadastral) | G3 |
| FP-05 | Falha de infraestrutura | Falha de processo/design backstage→frontstage | G4 implicação 1 |
| FP-04b (CPF não reconhecido) | Falha de dados | Falha de comunicação — URA não informa limitação | G4 implicação 2 |
| FP-01 / FP-02 | Fail points da URA | Causas-raiz externas → documentação de dependências | G6 |
| FP-01b / FP-02b | Ausentes | Novos: Falhas por Omissão de Design diante de erro externo | G6 |
| FP-09 causa-raiz | [PENDENTE] genérico | Funil de hipóteses A/B/C + fail point de comunicação confirmado + indicador de proxy | G7 |
| "Tempo de espera percebido" | Evidência física | KPI de qualidade — removido da camada de evidências | G5 |
| Silêncio de processamento de API | Ausente | Adicionado como evidência física na linha de visibilidade de E3 | G3 + G5 |
| Decreto 6.523/2008 | Normativo aplicado | Revogado — substituição pelo 11.034/2022 pendente de fundamentação | G8 em aberto |
