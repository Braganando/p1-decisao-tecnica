# P1 — Decisão Técnica e Diagnóstico de Viabilidade de IA no Atendimento Estudantil

Repositório de entregas do **Projeto 1 (P1)** da **Residência Tecnológica em Inteligência Artificial** (Instituto Hardware Br / TrendsIA).

## Resumo do Caso e Objetivo

Uma instituição de ensino superior avalia o uso de Modelos de Linguagem de Grande Porte (LLMs) para apoiar o atendimento ao estudante por e-mail — desde triagem e respostas a dúvidas gerais até demandas críticas (como benefícios socioeconômicos, revisão de notas e sanções disciplinares).

O objetivo deste projeto **não é construir um sistema completo em produção**, mas conduzir um diagnóstico técnico, empírico e auditável antes de qualquer investimento de código, respondendo com evidências:
1. Onde o uso de IA (com revisão humana) gera valor real;
2. Onde soluções convencionais determinísticas sem IA são superiores;
3. Onde a automação deve ser proibida por risco operacional, ético ou regulatório (LGPD).

> **Aviso de Privacidade e LGPD:** Todos os e-mails, identificadores (`[NOME_ALUNO_XX]`, `[RA_XX]`, `[CPF_XX]`), normas e protocolos presentes neste repositório são **100% fictícios e anonimizados**. Nenhum dado real de estudante foi utilizado ou submetido a APIs/ferramentas externas.

---

## Principais Artefatos

* **Memorando Técnico Completo:** [MEMORANDO_TECNICO.md](./MEMORANDO_TECNICO.md)
* **Vídeo Demonstrativo (YouTube — Não Listado):** *A ser publicado e vinculado na entrega final (`p1-avancado`).*
* **Releases / Tags de Evolução:**
  * `p1-basico` — Análise dos 7 pontos (F1, F2, F3), Base Normativa Fictícia, dataset anonimizado, Prompt V1 (Baseline) e 3 testes iniciais.
  * `p1-intermediario` — *(Em desenvolvimento)* Iteração V2/V3 com isolamento de variáveis, rubricas auditáveis e bateria de testes adversos nas 3 Zonas Operacionais.
  * `p1-avancado` — *(Planejado)* Matriz de riscos e LGPD (arts. 20 e 46), mitigações concretas de engenharia, classificação final, proposta de piloto e *Circuit Breaker*.

---

## Estrutura de Arquivos do Repositório

| Caminho | Descrição |
| :--- | :--- |
| `README.md` | Visão geral do projeto, ambiente de testes e guia de reprodução. |
| `MEMORANDO_TECNICO.md` | Documento executivo e técnico principal com diagnóstico, evidências e recomendações. |
| `docs/base_normativa_ficticia.md` | Fonte única de verdade fictícia usada para auditar fidelidade e alucinações nos testes. |
| `data/mensagens_ficticias.json` | Conjunto de 15 e-mails fictícios estruturados no formato pós-ingestão e sanitização de PII (separando payload de gabarito). |
| `prompts/prompt_v1_baseline.md` | Molde do 1º Prompt Protótipo (Baseline V1) estruturado nos 4 pilares obrigatórios. |
| `resultados/testes_basico_v1.md` | Registro literal das saídas do modelo e análise dos 3 casos do Nível Básico (`MSG-01`, `MSG-02`, `MSG-03`). |

---

## Ferramentas e Ambiente Utilizados

* **Execução dos Prompts Protótipos:** Interface Web Gemini (Google Workspace), modelo **Neon Alto**, parâmetros padrão de inferência.
* **Protocolo de Isolamento:** Cada caso de teste é executado em um **chat novo e completamente limpo**, garantindo ausência de contaminação de contexto entre execuções.
* **Versionamento e Edição:** Visual Studio Code, Git e GitHub.
* **Uso de IA no Desenvolvimento:** Documentado com transparência na Seção 10 do [MEMORANDO_TECNICO.md](./MEMORANDO_TECNICO.md).

---

## Como Reproduzir os Testes (Nível Básico — V1)

1. Abra o molde do prompt em `prompts/prompt_v1_baseline.md`.
2. Substitua o placeholder `{{BASE_NORMATIVA}}` pelo conteúdo integral de `docs/base_normativa_ficticia.md`.
3. Escolha um caso de teste em `data/mensagens_ficticias.json` (`MSG-01`, `MSG-02` ou `MSG-03`) e preencha `{{ASSUNTO}}`, `{{ANEXOS_RESUMO}}` e `{{CORPO_SANITIZADO}}` utilizando **apenas** os campos das chaves `etapa_1_ingestao` e `etapa_2_sanitizacao` (nunca envie o bloco `gabarito_p1`).
4. Abra um **novo chat limpo** na interface do modelo (**Neon Alto**), cole o prompt preenchido e execute.
5. Compare a saída obtida com a Base Normativa Oficial e com os registros salvos em `resultados/testes_basico_v1.md`.

---

## Autor

* **Fernando Braga Cassiano Silveira** — Residência Tecnológica em Inteligência Artificial (Instituto Hardware Br / TrendsIA)