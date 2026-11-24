# P1 — Decisão Técnica e Diagnóstico de Viabilidade de IA no Atendimento Estudantil

Repositório de entregas do **Projeto 1 (P1)** da **Residência Tecnológica em Inteligência Artificial** (Instituto Hardware Br / TrendsIA).

## Resumo do Caso e Objetivo

Uma instituição de ensino superior avalia o uso de Modelos de Linguagem de Grande Porte (LLMs) para apoiar o atendimento ao estudante por e-mail — desde triagem e respostas a dúvidas gerais até demandas críticas (como benefícios socioeconômicos, revisão de notas e sanções disciplinares).

O objetivo deste projeto **não é construir um sistema completo em produção**, mas conduzir um diagnóstico técnico, empírico e auditável antes de qualquer investimento de código, respondendo com evidências:
1. Onde o uso de IA (com revisão humana) gera valor real;
2. Onde soluções convencionais determinísticas sem IA são superiores;
3. Onde a automação deve ser proibida por risco operacional, ético ou regulatório (LGPD).

> **Aviso de Privacidade e LGPD:** Todos os e-mails, identificadores (`[NOME_ALUNO_XX]`, `[RA_XX]`, `[CPF_XX]`, `[DADO_SENSIVEL_SAUDE]`, `[DADO_BANCARIO_XX]`), normas e protocolos presentes neste repositório são **100% fictícios e anonimizados**. Nenhum dado real de estudante foi utilizado ou submetido a APIs/ferramentas externas.

---

## Principais Artefatos

* **Memorando Técnico Completo:** [MEMORANDO_TECNICO.md](./MEMORANDO_TECNICO.md)
* **Vídeo Demonstrativo (YouTube — Não Listado):** *A ser publicado e vinculado na entrega final (`p1-avancado`).*
* **Releases / Tags de Evolução:**
  * `p1-basico` *(Concluído)* — Análise dos 7 pontos (F1, F2, F3), Base Normativa Fictícia, dataset anonimizado, Prompt V1 (Baseline) e 3 testes iniciais.
  * `p1-intermediario` *(Concluído)* — Rubricas auditáveis em 4 dimensões com critérios de veto, iterações controladas **V2 (Contrato JSON — 10/10 `PASS`)** e **V3 (Few-Shot Contrastivo — 5/5 `PASS`)**, protocolo oficial de isolamento com **Memória desativada** e decisão de engenharia sobre trade-off custo vs. calibração.
  * `p1-avancado` *(Planejado)* — Matriz de riscos e LGPD (arts. 20 e 46), mitigações concretas de engenharia, classificação final, proposta de piloto e *Circuit Breaker*.

---

## Estrutura de Arquivos do Repositório

| Caminho | Descrição |
| :--- | :--- |
| `README.md` | Visão geral do projeto, ambiente de testes, síntese experimental e guia de reprodução. |
| `MEMORANDO_TECNICO.md` | Documento executivo e técnico principal com diagnóstico, evidências V1–V3 e recomendações. |
| `docs/base_normativa_ficticia.md` | Fonte única de verdade fictícia (v1.0) usada para auditar fidelidade e alucinações nos testes. |
| `docs/rubricas_avaliacao.md` | Régua formal de auditoria nas 4 dimensões (D1–D4), critérios de veto e teste do *Advogado do Diabo*. |
| `data/mensagens_ficticias.json` | Conjunto de 15 e-mails fictícios estruturados no formato pós-ingestão e sanitização de PII (separando payload de gabarito). |
| `prompts/prompt_v1_baseline.md` | Molde do 1º Prompt Protótipo (Baseline V1, *zero-shot* em Markdown livre) nos 4 pilares obrigatórios. |
| `prompts/prompt_v2_json.md` | Iteração V2 — alteração isolada do formato de saída para contrato JSON estrito parseável por `json.loads()`. |
| `prompts/prompt_v3_fewshot.md` | Iteração V3 — inclusão isolada de 3 exemplos sintéticos contrastivos (`ZONA_1`, `ZONA_2`, `ZONA_3`) sobre a V2. |
| `resultados/testes_basico_v1.md` | Registro literal das saídas e auditoria dos 3 casos do Nível Básico (`MSG-01`, `MSG-02`, `MSG-03`). |
| `resultados/testes_intermediario_v2.md` | Bateria completa de 10 casos da V2 nas 3 Zonas Operacionais + validação por controle cego sem memória. |
| `resultados/testes_intermediario_v3.md` | Bateria comparativa focalizada de 5 casos estratégicos da V3 (100% com Memória desativada) e síntese V1 → V2 → V3. |

---

## Síntese Experimental (V1 → V2 → V3)

| Versão | Variável Isolada Alterada | Escopo Testado | Taxa `PASS` | Vetos D1/D4 | Decisão / Destaque |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **V1 (Baseline)** | Linha de base *zero-shot* em Markdown livre | 3 casos (`MSG-01` a `MSG-03`) | 2/3 (1 Parcial) | 0 | Falso negativo de revisão humana (`MSG-01`), fila única (`MSG-02`) e vazamento de crases (`MSG-03`). |
| **V2 (Contrato JSON)** | Apenas *Formato Esperado* → JSON estrito tipado | 10 casos nas 3 Zonas (+ controle cego sem memória) | **10/10 (100%)** | **0** | **Recomendada para Neon Alto** (com micro-ajuste textual em `dados_faltantes`); eliminou todas as falhas da V1. |
| **V3 (Few-Shot Contrastivo)** | Apenas +3 exemplos sintéticos contrastivos sobre a V2 | 5 casos estratégicos (100% com Memória desativada) | **5/5 (100%)** | **0** | Calibrou `dados_faltantes: []` no `MSG-09` sem sobreajuste; mantida como **referência/fallback** para modelos menores. |

---

## Ferramentas e Ambiente Utilizados

* **Execução dos Prompts Protótipos:** Interface Web Gemini (Google Workspace), modelo **Neon (nível de raciocínio: Alto)**, parâmetros padrão de inferência.
  * *Nota de reprodutibilidade:* "Neon" (com nível de raciocínio "Alto") é o identificador exibido no seletor oficial da interface web do Gemini em novembro de 2026. Em contas que exibam outra nomenclatura de seletor, os testes podem ser reproduzidos com o modelo de raciocínio equivalente vigente.
* **Protocolo Oficial de Isolamento:** Todos os testes seguem execução em **chat novo e completamente limpo por caso**, exclusão manual da conversa de teste anterior antes da rodada seguinte e **chave de Memória da conta desativada** (*Configurações → Inteligência Personalizada → Memória*) durante toda a bateria, garantindo inferência sem contaminação de conversas ou gabaritos anteriores. Na V2, a consistência dos resultados foi confirmada por reexecução de controle cego sem memória em `MSG-13`, `MSG-04` e `MSG-09`; na V3, 100% dos testes ocorreram com a memória desligada.
* **Versionamento e Edição:** Visual Studio Code, Git e GitHub.
* **Uso de IA no Desenvolvimento:** Documentado com transparência na Seção 10 do [MEMORANDO_TECNICO.md](./MEMORANDO_TECNICO.md).

---

## Como Reproduzir os Testes (V1, V2 e V3)

1. **Preparar o isolamento:** Nas configurações do Gemini (*Configurações → Inteligência Personalizada*), **desative a chave Memória** antes de iniciar a bateria.
2. **Escolher o molde do prompt:**
   * Nível Básico (V1): `prompts/prompt_v1_baseline.md`
   * Nível Intermediário (V2 — JSON estrito): `prompts/prompt_v2_json.md`
   * Nível Intermediário (V3 — Few-Shot contrastivo): `prompts/prompt_v3_fewshot.md`
3. **Injetar a Base Normativa:** Substitua `{{BASE_NORMATIVA}}` pelo conteúdo integral de `docs/base_normativa_ficticia.md` (obrigatório em sua totalidade, sem resumos ou omissões).
4. **Injetar o caso de teste:** Escolha uma mensagem em `data/mensagens_ficticias.json` e preencha `{{ASSUNTO}}`, `{{ANEXOS_RESUMO}}` e `{{CORPO_SANITIZADO}}` utilizando **exclusivamente** os campos das chaves `etapa_1_ingestao` e `etapa_2_sanitizacao` — **nunca inclua** o bloco `gabarito_p1` no prompt enviado ao modelo.
5. **Executar em sessão limpa:** Abra um **novo chat limpo** selecionando **Neon (nível de raciocínio: Alto)** (ou equivalente), cole o prompt preenchido, execute e exclua o chat antes de rodar o próximo caso.
6. **Auditar a saída:**
   * Para **V2 e V3**, valide o contrato sintático executando `import json; json.loads(resposta)` diretamente no terminal Python sobre o texto bruto retornado pelo modelo, confirmando que a saída é **100% parseável sem nenhuma cerca de código** (como `` ```json `` ou `` ``` ``) nem texto externo.
   * Audite o conteúdo nas 4 dimensões (D1–D4) definidas em `docs/rubricas_avaliacao.md` e compare com os registros salvos em `resultados/testes_basico_v1.md`, `resultados/testes_intermediario_v2.md` e `resultados/testes_intermediario_v3.md`.

---

## Autor

* **Fernando Braga Cassiano Silveira** — Residência Tecnológica em Inteligência Artificial (Instituto Hardware Br / TrendsIA)