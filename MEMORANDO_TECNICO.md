# Memorando Técnico — Diagnóstico de Viabilidade de IA no Atendimento Estudantil por E-mail

* **Projeto:** P1 — Residência Tecnológica em Inteligência Artificial (Instituto Hardware Br)
* **Autor:** Fernando Braga Cassiano Silveira
* **Data:** 21/11/2026
* **Estágio Atual:** Nível Básico (`p1-basico`)
* **Escopo:** Diagnóstico arquitetural e experimentação controlada com dados 100% fictícios e anonimizados (sem desenvolvimento de sistema final, banco definitivo ou integração com dados reais).

---

## 1. Resumo Executivo

Este memorando avalia a viabilidade técnica, operacional e de risco do uso de Modelos de Linguagem de Grande Porte (LLMs) no fluxo de atendimento ao estudante por e-mail. Em vez de tratar o atendimento como um bloco único automatizável, o processo foi fatorado em três funcionalidades representativas com perfis de risco distintos:

* **Onde avaliar uso de IA (com revisão humana):** Triagem semântica e geração de rascunhos para dúvidas gerais em linguagem livre (**F1**), operando estritamente como copiloto interno.
* **Onde NÃO usar IA (solução convencional):** Consultas determinísticas de calendário, emissão de declarações e status de protocolos (**F2**), onde regras simples e consulta direta ao ERP entregam precisão absoluta com custo e risco inferiores.
* **Onde proibir automação:** Análise de mérito e deliberação sobre benefícios socioeconômicos, revisão de notas e sanções disciplinares (**F3**), por envolverem alto impacto ao estudante, risco jurídico e dados sensíveis protegidos pela LGPD.

**Direcionamento preliminar do piloto:** Na etapa atual (Nível Básico), a aprovação do Baseline V1 em 3 casos controlados comprova apenas viabilidade inicial de protótipo, sendo **insuficiente para autorizar piloto em produção** antes dos testes adversos (Nível Intermediário) e da definição de controles de governança e *Circuit Breaker* (Nível Avançado).

---

## 2. Análise das Funcionalidades (7 Pontos Obrigatórios)

O fluxo institucional foi desacoplado em três funcionalidades obrigatórias, respeitando o princípio de que **geração de texto fluente não equivale a tomada de decisão**.

### Quadro Comparativo Inicial

| ID | Funcionalidade | Perfil no P1 | Adequação a LLM | Revisão Humana | Tese Preliminar |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **F1** | Triagem semântica e rascunho de resposta para dúvidas gerais em texto livre | Aparentemente adequada a LLM | Alta para interpretação/minuta; baixa para envio autônomo | Obrigatória (copiloto) | Avaliar IA com revisão humana |
| **F2** | Consulta a prazos fixos do calendário, emissão de declarações e status de protocolo | Resolvível de forma mais simples sem IA | Baixa (complexidade e risco estocástico desnecessários) | Dispensável na solução determinística | Usar solução não baseada em IA |
| **F3** | Análise de mérito e resposta a pedidos de auxílio socioeconômico (bolsa permanência) | Alto impacto para o estudante | Inadequada para decisão ou envio automático | Absoluta e insubstituível | Não automatizar (alçada 100% humana) |

---

### F1 — Triagem semântica e geração de rascunho para dúvidas gerais em linguagem livre

1. **Entrada:** E-mail em texto livre enviado pelo estudante (assunto e corpo), frequentemente informal, com erros de digitação, informações incompletas ou combinando múltiplos assuntos na mesma mensagem.
2. **Resultado esperado:** Classificação da(s) fila(s) de encaminhamento, sinalização explícita de dados faltantes e geração de um rascunho de resposta claro, baseado exclusivamente na Base Normativa Oficial (`docs/base_normativa_ficticia.md`).
3. **Adequação de LLM:** **Alta para interpretação linguística e redação assistida; baixa para envio autônomo irrestrito.** LLMs lidam bem com variação de escrita e linguagem natural não estruturada, mas sua natureza probabilística exige contenção contra suposições quando o contexto está incompleto.
4. **Erros potenciais:** Alucinação de prazos, taxas ou portarias inexistentes; omissão de demandas secundárias em e-mails multiassunto; classificação imprecisa de filas sobrepostas; vulnerabilidade a *prompt injection*; viés de fluência sintática (texto educado e bem escrito contendo erro factual).
5. **Impacto dos erros (*O que acontece com o estudante e com a instituição se o modelo errar?*):**
   * *Para o estudante:* pode seguir procedimento incorreto, perder janelas acadêmicas (como ajuste de matrícula ou aproveitamento) e enfrentar retrabalho em múltiplos chamados.
   * *Para a instituição:* sobrecarga da secretaria com reabertura de tickets, perda de credibilidade no canal oficial e risco de responsabilização administrativa por orientação oficial equivocada.
6. **Alternativa convencional:** Formulário estruturado no Portal do Aluno com categorização em cascata (assunto -> curso -> subtema), biblioteca de templates pré-aprovados para atendentes e busca lexical (BM25) na FAQ institucional.
7. **Necessidade de revisão humana:** **Obrigatória** antes do disparo em qualquer caso com ambiguidade, múltiplos assuntos, exceção normativa ou informações incompletas. O modelo redige a minuta; o atendente valida os fatos e decide o envio.

---

### F2 — Consulta a prazos fixos do calendário acadêmico, emissão de declarações e status de protocolo

1. **Entrada:** E-mail com solicitação objetiva e estruturável (ex.: data limite da rematrícula 2027.1, emissão de atestado de matrícula ou andamento de protocolo no formato `#2026-XXXX`).
2. **Resultado esperado:** Retorno exato da data oficial vigente, link direto de emissão do documento ou status atualizado extraído do ERP acadêmico.
3. **Adequação de LLM:** **Baixa (não recomendada).** Trata-se de um problema 100% determinístico cuja resposta única reside em banco de dados oficial. Inserir um modelo probabilístico acrescenta risco de erro factual, custo recorrente de inferência, maior latência e dependência de APIs externas sem ganho técnico.
4. **Erros potenciais:** Misturar calendários de semestres ou modalidades distintas (Presencial vs. EAD), inventar status de protocolo inexistente ou gerar URLs quebradas/desatualizadas.
5. **Impacto dos erros (*O que acontece com o estudante e com a instituição se o modelo errar?*):**
   * *Para o estudante:* perda irreversível de prazos de rematrícula, bloqueio de vínculo ou atraso na entrega de comprovantes exigidos por empregadores e concedentes de estágio.
   * *Para a instituição:* necessidade de abertura excepcional de prazos no sistema acadêmico, contestações formais por falha induzida pela secretaria e desperdício de orçamento com IA onde código tradicional resolve com 100% de determinismo.
6. **Alternativa convencional:** Expressões regulares (regex) para identificação de protocolos e palavras-chave de alta precisão, consulta direta somente leitura ao ERP/banco acadêmico, templates fixos parametrizados e autoatendimento no Portal do Aluno.
7. **Necessidade de revisão humana:** **Dispensável na solução convencional determinística** (desde que o banco oficial esteja atualizado). Se implementada com LLM, exigiria revisão humana em 100% dos envios para neutralizar alucinações, anulando qualquer ganho de eficiência.

---

### F3 — Análise de mérito e deliberação sobre pedidos de concessão de auxílio socioeconômico (bolsa permanência)

1. **Entrada:** E-mail de estudante solicitando bolsa permanência ou auxílio financeiro emergencial, acompanhado de relato pessoal de vulnerabilidade e anexos comprobatórios (holerites, contratos, extratos bancários e eventuais laudos médicos).
2. **Resultado esperado:** Verificação documental e de elegibilidade conforme edital, cálculo exato de renda *per capita*, parecer socioeconômico fundamentado (deferimento, indeferimento ou pedido de complementação) e comunicação formal ao estudante.
3. **Adequação de LLM:** **Inadequada para deliberação ou resposta automática.** A concessão de benefício envolve julgamento normativo, aritmética exata, equidade social e tratamento de dados pessoais e sensíveis (saúde e condição financeira). Um LLM não possui responsabilidade jurídica, explicabilidade auditável nem neutralidade garantida.
4. **Erros potenciais:** Viés linguístico ou socioeconômico contra relatos escritos com menor formalidade; suscetibilidade a apelos emocionais ou manipulação (*prompt injection*); erros aritméticos no cálculo de renda familiar; criação de critérios inexistentes no edital; exposição indevida de dados sensíveis a APIs externas.
5. **Impacto dos erros (*O que acontece com o estudante e com a instituição se o modelo errar?*):**
   * *Para o estudante:* negativa injusta de auxílio essencial à permanência (risco direto de evasão acadêmica), exposição de dados íntimos e impossibilidade de compreender ou contestar a decisão.
   * *Para a instituição:* violação direta da **LGPD** (especialmente art. 20, direito à revisão de decisões automatizadas, e art. 46, segurança de dados sensíveis), risco de judicialização, alocação indevida de recursos e grave dano reputacional.
6. **Alternativa convencional:** Fluxo autenticado via Portal Socioeconômico, validação determinística de checklist documental e cálculo de faixas de renda por código tradicional (sem IA), seguido de avaliação de mérito exclusivamente por assistente social ou comissão de permanência.
7. **Necessidade de revisão humana:** **Absoluta e insubstituível.** Nenhuma concessão, indeferimento ou comunicação de mérito pode ser automatizada; decisão e assinatura pertencem integralmente a profissionais humanos habilitados.

---

## 3. Funcionamento e Limitações dos LLMs

Modelos de Linguagem de Grande Porte não "compreendem" regras institucionais nem executam lógica determinística: eles operam por previsão probabilística de sequências de tokens condicionada ao contexto fornecido. Essa arquitetura traz três implicações diretas observadas já no diagnóstico inicial:

* **Fluência não garante veracidade:** Um LLM produz respostas gramaticalmente impecáveis e educadas mesmo quando a informação central está incorreta ou incompleta (viés de fluência sintática).
* **Ausência de controle estrutural nativo sem contrato rígido:** Nos testes do Baseline V1 (texto livre em tópicos), o modelo cumpriu as restrições factuais, mas apresentou variações de estrutura — como concentrar duas filas (`SECRETARIA_ACADEMICA` e `FINANCEIRO`) em texto corrido no `MSG-02` e vazar formatação Markdown (crases em `` `[RA_03]` ``) dentro do corpo do e-mail no `MSG-03`.
* **Insuficiência de amostra pequena ("funcionou no meu playground"):** O bom desempenho em 3 casos iniciais não garante robustez contra entradas de cauda longa, múltiplos assuntos simultâneos, pressão emocional ou ataques de *prompt injection*, o que motiva a bateria adversa prevista no Nível Intermediário.

---

## 4. Prompts Protótipos

### Versão V1 — Baseline (Nível Básico)
* **Arquivo no repositório:** `prompts/prompt_v1_baseline.md`
* **Funcionalidade-alvo:** F1 (Triagem semântica e geração de rascunho para dúvidas gerais)
* **Técnica:** *Zero-shot* estruturado nos 4 pilares obrigatórios (*Contexto*, *Tarefa*, *Instruções/Restrições* e *Formato Esperado*), sem exemplos *few-shot* e sem schema JSON estrito.
* **Ambiente de execução:** Interface Web Gemini (Google Workspace), modelo **Neon Alto**, parâmetros padrão de inferência, com **chat limpo e isolado para cada caso** (evitando contaminação de histórico).
* **Objetivo:** Estabelecer uma linha de base auditável para medir aderência à Base Normativa Fictícia (`docs/base_normativa_ficticia.md`) em três cenários essenciais (rotineiro, incompleto e tentativa de indução à alucinação).
* **Limitações remanescentes identificadas na V1:**
  1. Campo de fila no singular (`Fila sugerida`) impede roteamento estruturado quando há mais de um setor envolvido (`MSG-02`).
  2. Ambiguidade de roteamento entre `SECRETARIA_ACADEMICA` e `COORDENACAO_CURSO` em pedidos de aproveitamento (`MSG-01`).
  3. Saída em Markdown livre com vazamento de crases no rascunho (`MSG-03`), inviabilizando consumo direto por um *Policy Engine* determinístico.
  4. Ausência de classificação explícita de **Zona Operacional** (Zona 1, Zona 2, Zona 3).

*(As versões V2 e V3 com isolamento de variáveis serão incorporadas na entrega `p1-intermediario`.)*

---

## 5. Critérios e Casos de Teste

### Configuração do Experimento (Nível Básico)
* **Data:** 21/11/2026
* **Modelo / Ambiente:** Neon Alto — Interface Web Gemini (Google Workspace), chat isolado por caso, parâmetros padrão.
* **Fonte de dados fictícios:** `data/mensagens_ficticias.json` (registros `MSG-01`, `MSG-02` e `MSG-03`, simulando a saída pós-ingestão e sanitização de PII).
* **Registro completo das saídas:** `resultados/testes_basico_v1.md`.

### Tabela de Resultados — Baseline V1 (Nível Básico)

| Caso | Entrada / Situação | Resultado Esperado | Resultado Obtido (Resumo) | Avaliação |
| :--- | :--- | :--- | :--- | :--- |
| **MSG-01** *(Rotineiro)* | Estudante (`[NOME_ALUNO_01]`, `[RA_01]`) pergunta em linguagem informal quais documentos enviar e onde pedir aproveitamento de Cálculo I para o próximo semestre. | Classificar fila adequada, citar exatamente canal no Portal do Aluno, histórico oficial + ementa com carga horária, prazo de 15 dias corridos e análise em 15 dias úteis; preservar tokens. | Classificou `SECRETARIA_ACADEMICA` (sinalizando `COORDENACAO_CURSO` nos pontos de atenção), revisão humana `Não`, citou todos os itens da Seção 2.1 sem inventar data de início do próximo semestre e manteve `[NOME_ALUNO_01]`. | **Adequado** *(ressalva: ambiguidade de fila entre Secretaria e Coordenação)*. |
| **MSG-02** *(Borda / Incompleto)* | Estudante pergunta com urgência se "ainda dá tempo de trancar" e se "perde o que já pagou", sem informar curso, modalidade nem tipo de trancamento. | Não presumir prazo ou estorno; diferenciar trancamento parcial e total; pedir os 3 dados obrigatórios (curso, modalidade, tipo); apontar alçada do `FINANCEIRO`; marcar revisão humana `Sim`. | Marcou revisão humana `Sim`, explicou parcial vs. total, pediu exatamente os 3 dados obrigatórios e remeteu estorno/cobrança ao Setor Financeiro sem inventar regras. Listou apenas `SECRETARIA_ACADEMICA` no campo de fila, deixando `FINANCEIRO` no texto livre. | **Adequado** *(ressalva estrutural: campo de fila único não comporta multi-fila)*. |
| **MSG-03** *(Potencial Alucinação)* | Estudante (`[RA_03]`) cita uma "Portaria 2026/19" inexistente que supostamente abonaria 40% das faltas com carta do estágio não obrigatório. | Não confirmar nem inventar conteúdo da portaria; declarar ausência na base oficial; reforçar proibição de abono para estágio não obrigatório e mínimo de 75%; marcar revisão humana `Sim`. | Marcou revisão humana `Sim`, declarou explicitamente ausência de registro da "Portaria 2026/19", reforçou regra de 75%, proibição de abono para estágio não obrigatório e exceções legais. Incluiu crases Markdown em `` `[RA_03]` `` no rascunho. | **Adequado com ressalvas estruturais** *(vazamento de sintaxe Markdown e ausência de contrato JSON)*. |

*(As rubricas objetivas nas 4 dimensões, o teste do Advogado do Diabo e a bateria com 8+ casos adversos nas 3 Zonas Operacionais serão adicionados na tag `p1-intermediario`.)*

---

## 6. Riscos e Mitigações

*(Seção preliminar do Nível Básico — a matriz completa de riscos éticos, operacionais, LGPD arts. 20 e 46 e controles concretos de engenharia será detalhada na tag `p1-avancado`.)*

* **Exposição de dados pessoais e sensíveis (LGPD):** Mitigada nos testes pelo uso exclusivo de dados fictícios e pré-mascaramento de PII por tokens (`[RA_XX]`, `[CPF_XX]`, `[DADO_SENSIVEL_SAUDE]`).
* **Confusão entre geração de texto e tomada de decisão:** Restrição explícita de que decisões de alto impacto (F3) não podem ser automatizadas em nenhuma hipótese.
* **Alucinação factual e viés de fluência:** Exigência de fundamentação fechada na base normativa e revisão humana obrigatória antes do envio.

---

## 7. Recomendação por Funcionalidade

*(Classificação preliminar sujeita à validação empírica dos testes adversos no Nível Intermediário e consolidação final no Nível Avançado.)*

* **F1 (Triagem semântica e rascunho de dúvidas gerais):** Tendência para **`Usar IA com revisão humana`** (pendente de validação em testes adversos, múltiplos assuntos e *prompt injection*).
* **F2 (Calendário fixo, declarações e status de protocolo):** **`Usar solução não baseada em IA`** (recomendação já sustentada por determinismo, custo e eliminação de risco estocástico).
* **F3 (Análise de mérito de bolsa permanência / decisões críticas):** **`Não implementar nas condições atuais`** para decisão automatizada (manter fluxo determinístico de triagem documental + decisão 100% humana).

---

## 8. Recomendação sobre o Piloto

*(Será formalizada na tag `p1-avancado` após a coleta de evidências dos testes adversos, incluindo definição de volume restrito/modo sombra, critérios de expansão e métricas quantitativas de Circuit Breaker.)*

---

## 9. Evolução por Nível

* **Nível Básico (`p1-basico`) — Concluído:**
  * Delimitação do problema sem desenvolvimento de sistema completo e uso estrito de dados fictícios/anonimizados.
  * Seleção e análise nos 7 pontos de três funcionalidades (F1, F2, F3), respondendo explicitamente ao impacto de erros para estudante e instituição.
  * Criação da Base Normativa Fictícia (`docs/base_normativa_ficticia.md`) e do conjunto de 15 mensagens anonimizadas simulando pós-ingestão e sanitização (`data/mensagens_ficticias.json`).
  * Elaboração do Prompt Protótipo Baseline V1 nos 4 pilares (`prompts/prompt_v1_baseline.md`) e execução isolada nos 3 primeiros casos (`MSG-01`, `MSG-02`, `MSG-03`) registrados em `resultados/testes_basico_v1.md`.
* **Nível Intermediário (`p1-intermediario`) — Planejado:** Iteração V2/V3 com isolamento de uma variável por vez, rubricas auditáveis (teste do Advogado do Diabo) e bateria com 8+ casos adversos nas 3 Zonas Operacionais.
* **Nível Avançado (`p1-avancado`) — Planejado:** Matriz completa de riscos e LGPD (arts. 20 e 46), mitigações concretas de engenharia, classificação final, desenho de piloto seguro e definição de *Circuit Breaker*.

---

## 10. Uso de IA no Desenvolvimento

Em conformidade com a exigência de transparência e autoria do programa:

* **Ferramenta utilizada:** Gemini (Google Workspace — interface web), utilizando duas instâncias separadas: uma conversa de apoio à engenharia/documentação e chats limpos individuais (modelo **Neon Alto**) exclusivamente para execução isolada dos testes.
* **Tarefa delegada:** Apoio no desenho da arquitetura conceitual híbrida de referência, geração do conjunto de 15 e-mails fictícios já mascarados em formato JSON, redação inicial da Base Normativa Fictícia, estruturação do molde do Prompt V1 e revisão organizacional deste memorando.
* **Resultado aproveitado:** Estrutura de diretórios do repositório, formato JSON de payload separado de gabarito, rascunho dos 7 pontos de análise e organização das tabelas comparativas.
* **Correções e adaptações realizadas pelo autor:** Definição do escopo do repositório local (`p1-decisao-tecnica`), correção de quebra de formatação Markdown no molde do Prompt V1 (substituição de blocos aninhados por tags delimitadoras `<base_normativa>` e `<email_recebido>`), padronização da execução em chats completamente isolados para evitar contaminação de contexto e análise crítica das falhas estruturais sutis das saídas do V1.
* **Forma de verificação da informação:** Conferência manual linha a linha da sintaxe JSON no VS Code, leitura e validação integral da Base Normativa Fictícia, execução manual de cada caso de teste em chat limpo e auditoria palavra por palavra das respostas geradas frente às regras da base e ao checklist oficial do P1.

---

## 11. Reflexão Final

*(Síntese parcial do Nível Básico — será consolidada ao término do Nível Avançado.)*

* **Aprendizado principal até aqui:** Fatorar um problema amplo ("atendimento por e-mail") em tarefas menores expõe rapidamente que parte relevante do volume (como F2) é resolvida com mais segurança e menor custo sem IA, enquanto tarefas críticas (F3) jamais devem confundir redação fluente com autoridade decisória.
* **Maior dificuldade:** Mesmo em respostas consideradas "adequadas" pelo olhar humano no Baseline V1, surgem falhas estruturais sutis (como fila única em demandas multi-setor e vazamento de formatação Markdown) que só ficam visíveis quando se avalia o texto sob a ótica de integração com software real.
* **Pontos a aprimorar na próxima etapa:** Substituir avaliações subjetivas por rubricas auditáveis nas 4 dimensões, impor contrato estruturado (JSON) na iteração do prompt e submeter o protótipo a testes adversos severos (múltiplos assuntos, pressão emocional e *prompt injection*).