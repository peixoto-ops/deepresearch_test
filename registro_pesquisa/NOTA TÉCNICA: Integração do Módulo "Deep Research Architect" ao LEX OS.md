# NOTA TÉCNICA: Integração do Módulo "Deep Research Architect" ao LEX OS
**Para:** Qwen Coder / Equipe de Desenvolvimento LEX OS
**Assunto:** Implementação do Meta-Prompt de Pesquisa Jurídica Adversarial (Pattern Architect)

## 1. Visão Geral
Esta nota detalha a integração do novo sistema de **Meta-Prompt Architect** ao ecossistema **LEX OS**. O objetivo é elevar a capacidade de orquestração do sistema, permitindo que o LEX OS não apenas processe dados, mas projete estruturas de decisão jurídica adversarialmente robustas antes da produção final de peças ou pareceres.

## 2. O Novo Componente: `deep_research_architect`
Diferente dos patterns tradicionais do Fabric (como `make_firac+`), o `deep_research_architect` atua como uma **camada meta**. Ele recebe o input bruto e os resultados de pesquisa (Zotero/Web) e gera um **prompt customizado de alta densidade** que deve ser usado no passo seguinte do pipeline.

### Pilares do Pattern:
1.  **Anti-Consonância Obrigatória:** Força a construção de argumentos contrários (*steelman approach*).
2.  **Mudança de Perspectiva Institucional:** Simula a voz do MP ou Magistratura.
3.  **Stress Test de Cenários:** Gera obrigatoriamente três desfechos (Acolhimento, Parcial, Rejeição).
4.  **Preenchimento Ativo:** O pattern não apenas lista seções, ele **pré-popula** os argumentos jurídicos com base na pesquisa.

## 3. Roteiro de Integração no LEX OS

### 3.1. Atualização do Módulo de Execução (`src/lex_os_server.py`)
Deve-se adicionar um novo tipo de pipeline à função `run_fabric_pipeline`:

```python
# Novo pipeline sugerido: "pesquisa_adversarial"
if pipeline_type == "pesquisa_adversarial":
    # Passo 1: Gera o prompt estruturado usando o novo pattern
    structured_prompt = execute_fabric(pattern="deep_research_architect", input=user_input, flags="--search")
    
    # Passo 2: O prompt gerado é então alimentado em um pattern de redação final
    # (ex: legal_analysis ou thesis_builder)
    final_result = execute_fabric_with_custom_prompt(prompt=structured_prompt)
```

### 3.2. Configuração de Patterns (`paths.yaml`)
O novo pattern deve ser adicionado ao diretório configurado em `custom_patterns_path`.
- **Nome da Pasta:** `deep_research_architect`
- **Arquivo Principal:** `system.md` (Conteúdo conforme especificação do projeto).

### 3.3. Fluxo de Memória (Obsidian First)
A integração deve respeitar a lógica `check_local_memory`. Se uma análise adversarial para o tema já existir no vault, o sistema deve sugerir o refinamento em vez de uma nova geração do zero.

## 4. Requisitos de Saída para o Qwen Coder
Ao implementar a integração, o Qwen Coder deve garantir que:
1.  **Citações ABNT:** O output final do pipeline deve manter a obrigatoriedade de citações imediatas após cada parágrafo.
2.  **Persistência:** O prompt gerado pelo Architect deve ser salvo como uma nota de "Metodologia de Trabalho" no Obsidian, vinculada ao caso (`case_id`).
3.  **Auditabilidade:** O log do sistema deve registrar quais argumentos contrários foram identificados pelo Architect para fins de auditoria posterior.

## 5. Exemplo de Input para Teste de Integração
Para validar a integração, utilize o seguinte *Hard Case*:
> *"Responsabilidade civil por bloqueio unilateral e automatizado de conta digital por plataforma privada, sem contraditório prévio, à luz da boa-fé objetiva e do CDC."*

---
**Status da Implementação:** Aguardando codificação do wrapper no `lex_os_server.py`.
**Referência do Pattern:** `manus-slides://UCbdU7ysqdiUtyCfuVmw5u` (Visualização da Metodologia).
