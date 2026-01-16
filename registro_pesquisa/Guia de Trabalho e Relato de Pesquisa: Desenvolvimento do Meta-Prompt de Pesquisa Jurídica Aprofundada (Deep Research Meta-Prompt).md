# Guia de Trabalho e Relato de Pesquisa: Desenvolvimento do Meta-Prompt de Pesquisa Jurídica Aprofundada (Deep Research Meta-Prompt)

## 1. Introdução e Contexto do Projeto

O presente documento serve como um guia de trabalho estruturado e um relato auditável do processo de desenvolvimento e refinamento de um **Meta-Prompt de Pesquisa Aprofundada** (doravante, *Deep Research Meta-Prompt*). O objetivo central foi criar uma ferramenta de orquestração cognitiva baseada em Inteligência Artificial (IA) capaz de transformar uma questão de pesquisa bruta em um *prompt* final altamente estruturado, adversarialmente robusto e pronto para análise aprofundada por modelos de linguagem (LLMs).

A arquitetura do projeto seguiu a filosofia do *framework* **Fabric** [1], que preconiza a criação de *patterns* (padrões) determinísticos e reutilizáveis, onde a lógica de comportamento é definida de forma clara e sequencial em um arquivo `system.md`. O *Deep Research Meta-Prompt* foi concebido como um **meta-pattern**, cuja função não é responder à questão do usuário, mas sim gerar um *prompt* de alta qualidade que será utilizado por um segundo modelo para a produção da resposta final.

O foco metodológico foi a **redução do viés confirmatório** e o aumento da **densidade cognitiva por *token***, especialmente em domínios de alta complexidade e litigância, como o Direito.

## 2. Metodologia de Desenvolvimento e Validação

O desenvolvimento do *Deep Research Meta-Prompt* foi conduzido através de um **Ciclo de Validação Empírica de Prompt**, um processo iterativo e baseado em evidências, em vez de ajustes intuitivos.

### 2.1. O Conceito de Pattern-Metaprompt (Fabric)

O *pattern* foi estruturado para atuar como uma camada intermediária em um *pipeline* de raciocínio em múltiplos passos. Sua função, conforme o modelo Fabric, é:
1. Receber uma entrada bruta (tema ou problema jurídico).
2. Executar uma etapa intermediária de pesquisa profunda (ativada externamente, por exemplo, com a *flag* `--search`).
3. Analisar e estruturar os resultados da pesquisa.
4. Construir automaticamente um *prompt* final otimizado.

O modelo final do *pattern* foi batizado de **"Architect"** (Arquiteto), em vez de "Generator" (Gerador), para reforçar o papel da LLM na projeção de uma estrutura de decisão, e não apenas na produção de texto.

### 2.2. Princípio da Anti-Consonância Obrigatória

O principal ajuste metodológico introduzido foi o **Princípio da Anti-Consonância Obrigatória**. Este princípio visa combater o viés confirmatório estrutural, que é a tendência dos modelos de linguagem de pressupor a tese do usuário e ignorar ou enfraquecer argumentos contrários.

A solução foi a inclusão de uma fase obrigatória de **Análise de Anti-Consonância** no *prompt* gerado, exigindo que o modelo:
*   **Mapeie Argumentos Contrários** na sua forma mais forte (*steelman approach*).
*   Realize uma **Mudança de Perspectiva Institucional**, redigindo uma subseção na voz do ator institucional oposto (e.g., Ministério Público, Instituição Financeira, Corte de Apelação).
*   Realize uma **Avaliação de Riscos** concretos à tese principal.
*   Proponha **Estratégias de Mitigação** para cada objeção.

### 2.3. Teste de Estresse Máximo (Cenários de Decisão Judicial)

Para elevar o nível de rigor, foi adicionada a seção **"Conclusão Crítica e Cenários de Decisão Judicial"**. Esta seção obriga o modelo a suspender qualquer presunção de sucesso e simular a mente de um **juiz cético**. O modelo deve apresentar, no mínimo, três desfechos distintos e plausíveis para a tese:
1.  Cenário de Acolhimento Total.
2.  Cenário de Acolhimento Parcial/Restritivo.
3.  Cenário de Rejeição (com fundamentos jurídicos explícitos).

### 2.4. O Problema da Carga Cognitiva e a Solução "Architect"

Durante os testes, foi identificado um problema de **compressão de carga cognitiva** (ou *lazy output*) por parte da LLM, especialmente quando o *input* de pesquisa era volumoso. O modelo tendia a economizar *tokens* nas seções mais "caras" (Anti-Consonância e Cenários), delegando o trabalho para o próximo modelo.

A solução foi um ajuste cirúrgico nas regras de saída, com a instrução: **"YOU must fill in the specific legal arguments based on your domain knowledge/research"** [2]. Isso transformou o meta-prompt em um **pré-curador jurídico**, forçando-o a preencher os argumentos adversariais e os cenários decisórios, em vez de apenas listar a necessidade de fazê-lo.

## 3. O Pattern Consolidado (Versão "Architect")

Abaixo está a estrutura final do *Deep Research Meta-Prompt* (conteúdo do `system.md`), que incorpora todos os ajustes metodológicos e de *stress test*.

```markdown
# IDENTITY
You are a Deep Research Meta-Prompt Architect.
Your function is NOT to answer the user’s question directly.
Your sole responsibility is to:
1. Conduct a preliminary deep research step (when search results are available),
2. Analyze and structure the findings,
3. Generate a final, high-quality, domain-appropriate prompt that the user can reuse for deeper analysis, drafting, or reasoning.
You operate as a meta-layer in a multi-step reasoning pipeline.

---

# INPUT
You will receive:
- A raw user input describing a topic, problem, or research question.
- Optionally, external search results provided by the execution environment (e.g., via `--search`).

---

# OBJECTIVES
Your objectives are, in order:
1. Identify the core research problem implicit in the user input.
2. Map the relevant domains involved (e.g., legal, technical, academic, factual, historical).
3. Extract and synthesize key findings from the preliminary research data.
4. Identify:
    - Consensus points
    - Disputed or controversial areas
    - Knowledge gaps
5. Transform this analysis into a single, well-structured final prompt.

---

# RESEARCH ANALYSIS PHASE
If external research data is available, incorporate it silently to:
- Refine scope
- Improve terminology
- Identify dominant legal frameworks
If external research data is unavailable or insufficient:
- Default to well-established domain knowledge
- Avoid speculative or novel claims

---

# PROMPT CONSTRUCTION RULES
The final output MUST be a reusable prompt that includes:

1. **Context**
    - Brief explanation of the topic and its relevance.
    - Scope limitations (jurisdiction, timeframe, discipline, etc.).
2. **Role Assignment**
    - Define the expected expertise of the model that will receive the prompt (e.g., legal scholar, appellate lawyer, researcher, policy analyst).
3. **Objectives**
    - Clear statement of what the model must accomplish.
4. **Methodology**
    - Step-by-step reasoning expectations.
    - Analytical frameworks to be applied (comparative analysis, doctrinal review, empirical reasoning, etc.).

5. **Mandatory Anti-Consonance Analysis**
    - The prompt MUST require explicit and structured engagement with dissenting positions.
    - This section is NOT optional. YOU must fill in the specific legal arguments based on your domain knowledge/research.
    It MUST include:
    ### 5.1 Mapping of Contrary Arguments
    - Identification of legal, doctrinal, or jurisprudential arguments that oppose the main or expected thesis.
    - Presentation of these arguments in their strongest possible form (steelman approach).
    ### 5.2 Institutional Perspective Shift
    - At least one subsection written entirely from the perspective of an opposing institutional actor (e.g., Public Prosecutor’s Office, appellate court, regulatory authority), explicitly arguing against the main thesis as if deciding the case.
    ### 5.3 Risk Assessment
    - Identification of concrete risks to the main thesis, including: unfavorable precedents, interpretative ambiguities, and factual distinctions commonly used to reject the argument.
    ### 5.4 Mitigation Strategies
    - Structured responses to EACH contrary argument identified.
    - Explicit classification of objections as: fully neutralized, partially mitigated, or unresolved and dependent on discretionary judgment.

6. **Maximum Stress Test – Judicial Decision Scenarios (MANDATORY)**
    - The prompt MUST include a section titled exactly: **“Critical Conclusion and Judicial Decision Scenarios”**.
    - It MUST present AT LEAST three internally coherent outcomes: Full Acceptance, Partial Acceptance/Restrictive, and Rejection Scenario.

7. **Constraints**
    - Explicit prohibitions (what must be avoided).
    - Required level of rigor and depth.
    - Methodological or evidentiary limitations.

8. **Citation and Sources (Domain-Sensitive)**
    - If the topic is legal or juridical in nature, the prompt MUST include this section, detailing the obrigatoriedade de citação ABNT/BR, citação imediata após o parágrafo e requisitos específicos para jurisprudência (Tribunal, Processo, Relator, Data, Link).

9. **Output Format**
    - Explicit structural formatting (headings, bullet points, tables, schemas).

---

# OUTPUT RULES
You MUST output ONLY the final constructed prompt.
You MUST NOT: Explain reasoning, Describe internal steps, Mention Fabric, patterns, agents, or execution flags.
The generated prompt must be neutral, reusable, and free of execution metadata.

---

# QUALITY BAR
The generated prompt MUST be: Precise, Non-ambiguous, Domain-aware, Adversarially robust, Explicitly stress-tested against unfavorable outcomes, Suitable for professional, academic, or judicial-grade analysis.
```

## 4. Casos de Teste (Hard Cases)

A validação do *Deep Research Meta-Prompt* foi realizada utilizando *hard cases* jurídicos, que são temas com jurisprudência fragmentada, conceitos indeterminados e alto risco de viés confirmatório.

| Domínio | Tema de Pesquisa (Input Sugerido) | Objetivo do Teste |
| :--- | :--- | :--- |
| **Direito Penal** | **Validade constitucional e processual penal do acesso a dados armazenados em telefone celular apreendido em flagrante, sem prévia autorização judicial, quando tais dados fundamentam a autoria e a materialidade delitiva.** [3] | Forçar o modelo a lidar com o conflito entre a urgência do crime permanente (e.g., tráfico de drogas) e a garantia constitucional da inviolabilidade de dados (art. 5º, XII, CF), distinguindo apreensão física de acesso informacional. |
| **Direito Civil/Digital** | **Responsabilidade civil por bloqueio unilateral e automatizado de conta digital por plataforma privada, sem contraditório prévio, e seus efeitos quanto à configuração de dano moral e abuso de direito.** [4] | Testar a capacidade de lidar com a jurisprudência fragmentada (STJ e TJs) sobre a eficácia horizontal dos direitos fundamentais, ponderando a autonomia privada (Termos de Uso) contra o devido processo privado e a boa-fé objetiva (CDC). |
| **Direito Civil/Digital (Boss Final)** | **Responsabilidade civil por erro de *scoring* ou perfilamento algorítmico (negativa de crédito baseada em modelo opaco).** [5] | Testar a capacidade de integrar o CDC, a LGPD e a Lei do Cadastro Positivo (Lei 12.414/2011), forçando o modelo a analisar se a opacidade algorítmica (*black box*) configura, *per se*, defeito na prestação do serviço ou prática abusiva. |

## 5. Problemas Surgidos e Lições Aprendidas

O principal problema surgido não foi de conceito, mas de **dinâmica de execução** da LLM, conforme detalhado na seção 2.4.

| Problema | Causa Raiz | Solução Implementada | Lição Aprendida |
| :--- | :--- | :--- | :--- |
| **Viés Confirmatório** | O prompt inicial pressupunha a tese do usuário (e.g., "impossibilidade de condenação"). | Inclusão da **Anti-Consonância Obrigatória** e do **Teste de Estresse Máximo**. | O modelo deve ser forçado a **deliberadamente construir e fortalecer os argumentos contrários** (*steelman approach*). |
| **Compressão Cognitiva** | Em *inputs* ricos, o modelo entra em modo de compressão, economizando *tokens* nas seções mais complexas (Anti-Consonância e Cenários). | Mudança de `IDENTITY` para **Architect** e inclusão da regra de **Preenchimento Obrigatório** (`YOU must fill in the specific legal arguments`). | A LLM deve ser transformada em um **pré-curador jurídico**, preenchendo os argumentos adversariais em vez de apenas listar a necessidade de fazê-lo. |
| **Alucinação/Rigor** | Risco de o modelo inventar precedentes ou teses. | Inclusão de **Regras de Citação ABNT/BR** e requisitos específicos para jurisprudência (Tribunal, Processo, Relator, Link). | A exigência de citação funciona como um **mecanismo disciplinar**, ancorando o texto em categorias jurídicas reais e melhorando a auditabilidade humana. |

## 6. Referências e Links Externos

Os seguintes links e referências foram citados e utilizados como base conceitual e estrutural durante o desenvolvimento do *Deep Research Meta-Prompt*.

| ID | Título | URL |
| :--- | :--- | :--- |
| [1] | Fabric - The open-source framework for augmenting humans with AI. | https://github.com/danielmiessler/Fabric |
| [2] | Regra de Preenchimento Obrigatório (YOU must fill in the specific legal arguments) | (Conteúdo da conversa) |
| [3] | Hard Case: Validade constitucional e processual penal do acesso a dados armazenados em telefone celular apreendido em flagrante. | (Conteúdo da conversa) |
| [4] | Hard Case: Responsabilidade civil por bloqueio unilateral e automatizado de conta digital por plataforma privada. | (Conteúdo da conversa) |
| [5] | Hard Case: Responsabilidade civil por erro de scoring ou perfilamento algorítmico. | (Conteúdo da conversa) |
| [6] | Conversa original no ChatGPT (fonte primária do material) | https://chatgpt.com/c/6968b8d5-0184-832d-8bd7-ba0407178640 |

---
*Documento elaborado por Manus AI em 16 de janeiro de 2026.*
