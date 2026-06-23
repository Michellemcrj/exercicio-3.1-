# Meta-Prompt — Service Blueprint AS-IS: Atendimento ao Seguro-Desemprego pela URA da Caixa

---

## Contexto e objetivo

Você é um especialista em design de serviços públicos e experiência do cidadão. Sua tarefa é **documentar o estado atual (AS-IS) do serviço de atendimento ao Seguro-Desemprego realizado pela URA (Unidade de Resposta Audível) da Caixa Econômica Federal**, produzindo um Service Blueprint completo com cinco dimensões analíticas obrigatórias.

O produto final deve ser utilizável por equipes de CX, gestores públicos e times de transformação digital para identificar oportunidades de melhoria, redesenho de processos e priorização de investimentos.

---

## Instrução principal

Estruture sua resposta nas cinco dimensões abaixo. Para cada dimensão, percorra **todas as seis etapas da jornada** na sequência:

> **E1 — Pré-contato** · **E2 — Acesso à URA** · **E3 — Navegação no menu** · **E4 — Consulta / Solicitação** · **E5 — Processamento & Pagamento** · **E6 — Pós-atendimento / Recurso**

---

## Dimensão A — Jornada do Cidadão (linha de frente)

Para cada etapa, descreva:
- **O que o cidadão está tentando fazer** (job-to-be-done)
- **Como ele age** (canais, dispositivo, comportamento)
- **O que ele sente** (frustrações, ansiedades, expectativas)
- **Restrições de tempo** que impactam sua ação (ex.: prazo de 7 a 120 dias pós-demissão conforme Lei 7.998/1990, art. 7º)

Considere perfis distintos: trabalhador com baixa literacia digital, trabalhador com deficiência auditiva, trabalhador em zona rural com acesso limitado.

---

## Dimensão B — Processos de Bastidor (backstage)

Para cada etapa, identifique:
- **Ator responsável** (MTE, DATAPREV, Caixa, empregador)
- **Ação concreta** realizada fora da vista do cidadão
- **Sistema/plataforma** envolvido (eSocial, CAGED, CNIS, SD-Web, API MTE↔Caixa, COMPE/STR, DATAPREV)
- **Dependências críticas** entre atores (ex.: empregador deve enviar CAGED antes de a pré-habilitação ocorrer no DATAPREV)

Mapeie a cadeia: **Empregador → eSocial/CAGED → DATAPREV (pré-habilitação) → MTE (habilitação) → Caixa (geração de OP) → Crédito em conta**.

---

## Dimensão C — Evidências Físicas e Digitais

Para cada etapa, liste todos os **artefatos tangíveis ou digitais** que o cidadão toca, vê, ouve ou recebe:
- Documentos em papel (TRCT, NIS, Cartão Cidadão)
- Interfaces digitais (app Caixa, portal gov.br, portal Facilita Brasil)
- Sinais auditivos (locução da URA, tom de espera, mensagem de erro)
- Registros gerados (protocolo de atendimento, SMS, extrato)
- Ausência de evidência como fail point (ex.: nenhum protocolo gerado ao final da chamada)

---

## Dimensão D — Normativos Aplicáveis

Para cada etapa, referencie os instrumentos legais e regulatórios que **governam, restringem ou garantem direitos** naquele momento:

| Etapa | Normativo | Dispositivo relevante |
|---|---|---|
| E1 | Lei 7.998/1990 | Arts. 2º–7º (elegibilidade e prazo) |
| E1 | Lei 13.134/2015 | Atualização de critérios |
| E2–E3 | Lei 8.078/1990 (CDC) | Atendimento ao consumidor |
| E2–E3 | Decreto 6.523/2008 | Normas SAC (tempo máximo de espera, transferência) |
| E3–E4 | Lei 13.709/2018 (LGPD) | Autenticação por CPF/NIS |
| E3–E4 | Res. CODEFAT 957/2022 e 1.031/2025 | Regras operacionais do benefício |
| E5 | Res. CMN 3.876 | Conta poupança social simplificada |
| E6 | Lei 9.784/1999 | Processo administrativo federal (recurso) |
| E6 | Lei 13.460/2017 | Defesa dos direitos do usuário de serviços públicos |

Aprofunde cada normativo indicando **o que ele exige do prestador** e **o que ele garante ao cidadão**.

---

## Dimensão E — Fail Points Conhecidos

Para cada etapa, mapeie as falhas recorrentes com a seguinte estrutura:

```
FAIL POINT: [nome curto]
Etapa: Ex
Sintoma observado: [o que o cidadão percebe]
Causa-raiz provável: [processo, sistema ou política]
Frequência/evidência: [fonte: Reclame Aqui, auditoria TCU, relatos MTE]
Impacto no cidadão: [consequência concreta]
Atores envolvidos na falha: [quem falhou e quem absorve o impacto]
```

Fail points obrigatórios a cobrir:

- **FP-01 (E1):** Empregador não envia CAGED/eSocial → pré-habilitação bloqueada
- **FP-02 (E2):** Canal 158 congestionado (relatos de >70 tentativas sem resposta)
- **FP-03 (E3):** CPF/NIS não reconhecido pela URA por inconsistência cadastral entre DATAPREV e Caixa
- **FP-04 (E3):** Loop de menu sem saída e sem transferência para atendente humano
- **FP-05 (E4):** Status divergente entre sistema MTE e sistema Caixa — benefício habilitado mas URA informa "não encontrado"
- **FP-06 (E5):** Crédito em conta errada (poupança ao invés de conta corrente informada)
- **FP-07 (E5):** Falha em TED para outros bancos (episódio de 06/06/2025 — interrupção sistêmica)
- **FP-08 (E5):** Parcela bloqueada sem comunicação proativa ao cidadão
- **FP-09 (E6):** Recurso administrativo sem resposta por meses no portal MTE
- **FP-10 (E6):** Cidadão redirecionado à agência física (fila de 3h) para resolver falha originada na URA

---

## Restrições de formato

- Use tabelas para as dimensões B e D; narrativa densa para A; lista estruturada para C e E.
- Cite o normativo com número e artigo sempre que mencionar uma obrigação legal.
- Não faça recomendações de melhoria — este é um blueprint AS-IS, descritivo e factual.
- Quando houver lacuna de informação, sinalize explicitamente com `[DADO NÃO DISPONÍVEL — validar em campo]` em vez de inventar.
- Ao final, inclua uma **Síntese de Criticidade** ranqueando os 10 fail points por impacto no cidadão (alto / médio / baixo) e por complexidade de resolução (curto / médio / longo prazo).

---

## Fontes primárias a consultar

- [Caixa — Seguro-Desemprego](https://www.caixa.gov.br/beneficios-trabalhador/seguro-desemprego/Paginas/default.aspx)
- [gov.br — Solicitar Seguro-Desemprego](https://www.gov.br/pt-br/servicos/solicitar-o-seguro-desemprego)
- [Lei 7.998/1990 — Planalto](http://www.planalto.gov.br/ccivil_03/leis/l7998compilado.htm)
- [Res. CODEFAT 957/2022 — LegisWeb](https://www.legisweb.com.br/legislacao/?id=436595)
- [MTE — Reajuste Seguro-Desemprego 2026](https://www.gov.br/trabalho-e-emprego/pt-br/noticias-e-conteudo/2026/janeiro/mte-reajusta-valores-do-beneficio-seguro-desemprego)
- [DATAPREV — Seguro-Desemprego](https://dataprev.gov.br/seguro-desemprego)
- Reclame Aqui — Caixa Econômica Federal (filtro: "seguro desemprego")
