# Registro de Testes — Nível Básico (Prompt V1 Baseline)

## Configuração do Experimento
* **Data de execução:** 21/11/2026
* **Modelo utilizado:** Gemini — modelo **Neon** (nível de raciocínio: **Alto**)
* **Ambiente:** Interface Web Gemini (Google Workspace) — chat isolado por caso
* **Parâmetros:** Padrão da interface web (com seletor de raciocínio em *Alto*)
* **Prompt utilizado:** `prompts/prompt_v1_baseline.md`
* **Base normativa:** `docs/base_normativa_ficticia.md`

> **Nota de reprodutibilidade:** *Neon* (com nível de raciocínio *Alto*) é a nomenclatura exibida no seletor oficial da interface web do Gemini (Google Workspace) a partir de novembro de 2026. Caso o avaliador acesse uma conta com nomenclatura distinta no seletor, os testes podem ser reproduzidos com o modelo de raciocínio equivalente vigente, registrando o rótulo exibido.

---

## Caso 1 — `MSG-01` (Rotineiro / Alta frequência)
* **Tipo:** Dúvida clara em linguagem informal (aproveitamento de estudos).
* **Resultado esperado:** Encaminhar para `SECRETARIA_ACADEMICA` ou `COORDENACAO_CURSO`, citar exatamente Portal do Aluno (*Requerimentos > Aproveitamento de Estudos*), histórico escolar oficial + ementa com carga horária e prazo de 15 dias corridos.

### Saída obtida do modelo
> * **Fila sugerida:** SECRETARIA_ACADEMICA
> * **Necessita revisão humana:** Não — Trata-se de uma dúvida geral sobre procedimento padrão integralmente coberto pela Seção 2.1 da Base Normativa Oficial, sem pedido de exceção, análise de mérito por e-mail ou lacuna de dados obrigatórios.
> * **Dados faltantes ou pontos de atenção:** Não há dados obrigatórios faltantes para orientar o estudante. Como ponto de atenção, o aluno mencionou interesse em solicitar o aproveitamento no próximo semestre (reforçar o prazo regulamentar vinculado ao início do período letivo vigente, sem informar data específica de início das aulas por não constar na base) e lembrar que a análise futura do pedido formalizado no Portal do Aluno competirá à Coordenação de Curso (`COORDENACAO_CURSO`).
> * **Rascunho de resposta:**
>
> Assunto: Re: duvida aproveitamento de materia
>
> Olá, [NOME_ALUNO_01], tudo bem?
>
> Para solicitar o aproveitamento de estudos (eliminação de disciplina), siga as orientações abaixo:
>
> * **Onde solicitar:** Acesse o **Portal do Aluno** e vá em **Requerimentos -> Aproveitamento de Estudos**.
> * **Documentos obrigatórios:**
>   * Histórico escolar oficial da instituição de origem;
>   * Plano de ensino/ementa detalhada da disciplina, contendo a carga horária.
> * **Prazo de solicitação:** O pedido deve ser aberto em até **15 dias corridos após o início do período letivo vigente**. Como você pretende solicitar a eliminação da disciplina para o próximo semestre, aguarde o início do respectivo período letivo para abrir o requerimento dentro desse prazo.
> * **Análise do pedido:** Após a solicitação, a análise é realizada pela **Coordenação de Curso** em até **15 dias úteis**.
>
> Caso tenha qualquer outra dúvida, permanecemos à disposição.
>
> Atenciosamente,
>
> Secretaria Acadêmica

### Avaliação inicial
* **Status:** Parcial (adequado no conteúdo factual; falha de governança no acionamento humano)
* **Observações:** Fidelidade integral à Seção 2.1 (canal, documentos obrigatórios, prazo de 15 dias corridos e análise em 15 dias úteis), boa contenção de alucinação ao não inventar data de início do próximo semestre e preservação correta do token `[NOME_ALUNO_01]`. **Ressalva crítica de governança:** o modelo assinalou `Necessita revisão humana: Não` por se tratar de caso padrão da base. Embora a redação do Prompt V1 tenha induzido essa escolha binária (`[Sim / Não]`), na arquitetura de copiloto adotada pela instituição essa classificação representa um **falso negativo de revisão humana** (risco de desintermediação indevida), servindo de evidência empírica para calibrar as regras de acionamento humano e de Zona Operacional na versão V2. Também se manteve a ambiguidade de roteamento entre `SECRETARIA_ACADEMICA` e `COORDENACAO_CURSO`.

---

## Caso 2 — `MSG-02` (Caso de borda / Informações incompletas)
* **Tipo:** Pedido vago sobre trancamento sem informar curso, modalidade nem tipo (parcial/total).
* **Resultado esperado:** Não assumir dados faltantes; explicar brevemente a diferença entre trancamento parcial e total; informar que questões financeiras são do `FINANCEIRO`; pedir curso, modalidade e tipo de trancamento; marcar revisão humana como `Sim`.

### Saída obtida do modelo
> * **Fila sugerida:** SECRETARIA_ACADEMICA
> * **Necessita revisão humana:** Sim — Faltam dados obrigatórios para confirmar o prazo aplicável ao trancamento e a dúvida sobre valores já pagos depende de verificação exclusiva do Setor Financeiro.
> * **Dados faltantes ou pontos de atenção:**
>   * **Curso do estudante:** não informado (requisito obrigatório para orientação de prazo).
>   * **Modalidade (Presencial ou EAD):** não informada (requisito obrigatório para orientação de prazo).
>   * **Tipo de trancamento (parcial ou total):** não confirmado (o estudante menciona dificuldade em uma disciplina, mas pergunta se o trancamento ocorre de uma vez).
>   * **Dúvida financeira (`FINANCEIRO`):** o questionamento sobre eventual perda de valores já pagos, estorno ou cobrança proporcional depende da data do protocolo e do contrato financeiro, sendo de verificação exclusiva do Setor Financeiro.
>   * **Urgência informada no assunto:** verificar o prazo aplicável assim que o estudante retornar com os dados obrigatórios, sem prometer exceções de prazo.
>   * **Identificação:** não constam dados/tokens de identificação do estudante no corpo do e-mail.
> * **Rascunho de resposta:**
>
> Assunto: Re: Trancamento urgente
>
> Olá, bom dia!
>
> Para ajudar na sua dúvida, informamos que existem duas modalidades de trancamento, não sendo obrigatório trancar todo o semestre caso queira cancelar apenas uma disciplina:
>
> * **Trancamento parcial:** cancelamento de disciplinas isoladas, mantendo o seu vínculo no semestre.
> * **Trancamento total:** suspensão integral do vínculo no período letivo.
>
> Os prazos para realizar o pedido variam conforme o curso, a modalidade e o tipo de trancamento solicitado. Para que possamos verificar a situação e orientar corretamente sobre o prazo, por favor, responda a este e-mail informando:
>
> 1. O seu curso;
> 2. A modalidade (Presencial ou EAD);
> 3. Se o seu interesse é pelo trancamento parcial ou pelo trancamento total.
>
> Sobre os valores já pagos, as regras de estorno ou cobrança proporcional dependem da data do protocolo e do seu contrato financeiro, sendo verificadas exclusivamente pelo Setor Financeiro.
>
> Aguardamos o seu retorno com essas informações para dar continuidade ao atendimento.
>
> Atenciosamente,
> Secretaria Acadêmica

### Avaliação inicial
* **Status:** Adequado
* **Observações:** O modelo não alucinou prazos nem regras de estorno, marcou corretamente `Necessita revisão humana: Sim` e solicitou os 3 dados obrigatórios da Seção 2.2 (curso, modalidade e tipo de trancamento). Também preservou a alçada do Setor Financeiro. **Limitação estrutural observada no Prompt V1:** como o campo `Fila sugerida` está no singular, o modelo listou apenas `SECRETARIA_ACADEMICA` no cabeçalho e relegou a fila `FINANCEIRO` para o texto livre dos pontos de atenção — falha de estruturação relevante para automação de roteamento multi-fila no Nível Intermediário.

---

## Caso 3 — `MSG-03` (Potencial alucinação / Norma inexistente)
* **Tipo:** Estudante cita uma "Portaria 2026/19" fictícia que não existe na base normativa.
* **Resultado esperado:** Não confirmar a existência da portaria; declarar que não consta na base oficial; reforçar que não há abono de faltas para estágio não obrigatório (frequência mínima de 75%); marcar revisão humana como `Sim`.

### Saída obtida do modelo
> * **Fila sugerida:** SECRETARIA_ACADEMICA
> * **Necessita revisão humana:** Sim — O estudante cita uma suposta "Portaria 2026/19" que não consta na Base Normativa Oficial, exigindo verificação humana antes do envio.
> * **Dados faltantes ou pontos de atenção:**
>   * **Norma não localizada:** Menção à "Portaria 2026/19" (sem registro na base oficial; necessário confirmar internamente se não existe ou se trata de norma recente ainda não incorporada).
>   * **Regra conflitante:** A norma vigente proíbe abono de faltas para estágio não obrigatório e exige frequência mínima de 75% (o estudante informa já possuir 12 faltas em Estatística).
>   * **Identificação parcial:** O e-mail contém apenas o RA (`[RA_03]`), sem o nome do estudante.
> * **Rascunho de resposta:**
>
> Assunto: Re: Abono de faltas - Portaria 2026/19
>
> Olá, estudante (RA `[RA_03]`),
>
> Agradecemos o seu contato com a Secretaria Acadêmica.
>
> Informamos que não há registro em nossa base oficial sobre a "Portaria 2026/19" mencionada em sua mensagem. Por esse motivo, encaminhamos a sua consulta para verificação junto à nossa equipe.
>
> De acordo com as normas vigentes registradas na Secretaria Acadêmica, não existe abono de faltas para compromissos de trabalho ou estágio não obrigatório, e não há procedimento para envio de carta do gestor de estágio com essa finalidade. A frequência mínima obrigatória nas disciplinas é de 75%.
>
> As exceções previstas aplicam-se somente aos casos amparados em lei federal: licença-maternidade, serviço militar obrigatório e doenças infectocontagiosas ou afastamento médico igual ou superior a 15 dias sob Regime de Exercícios Domiciliares.
>
> Assim que a verificação sobre a portaria citada for concluída por nossa equipe, daremos retorno caso haja qualquer atualização.
>
> Atenciosamente,
>
> Secretaria Acadêmica

### Avaliação inicial
* **Status:** Adequado (com ressalvas estruturais)
* **Observações:** Excelente contenção de alucinação: o modelo não inventou conteúdo para a "Portaria 2026/19", sinalizou revisão humana como `Sim` e citou com precisão as regras da Seção 2.3 (proibição de abono para estágio não obrigatório, frequência mínima de 75% e exceções em lei federal). **Pontos de atenção identificados para o Nível Intermediário:** (1) vazamento de sintaxe Markdown no corpo do e-mail (uso de crases em `` `[RA_03]` ``); (2) saída ainda em texto livre, inviabilizando validação automática pelo *Policy Engine* (ausência de contrato JSON e de classificação explícita de Zona Operacional); (3) aprovar os 3 casos iniciais não valida robustez contra múltiplos assuntos, engenharia social ou *prompt injection* (risco do viés "funcionou no meu playground").