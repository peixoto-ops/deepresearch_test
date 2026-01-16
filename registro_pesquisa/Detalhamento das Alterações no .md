# Detalhamento das Alterações no `lex_os_server.py`

Este documento detalha as modificações necessárias no Módulo de Execução (`src/lex_os_server.py`) do LEX OS para integrar o novo pipeline de pesquisa adversarial, utilizando o pattern **Deep Research Architect**.

## 1. Funções Auxiliares Necessárias

Assumindo que o `lex_os_server.py` já possui uma função para interagir com o Fabric (que chamaremos de `execute_fabric`), será necessário garantir que ela suporte a execução com flags e a passagem de prompts customizados.

### 1.1. Função `execute_fabric(pattern, input_data, flags="")`

Esta função deve encapsular a chamada ao Fabric CLI.

```python
# Exemplo de assinatura (a ser adaptada ao código existente)
def execute_fabric(pattern: str, input_data: str, flags: str = "") -> str:
    """
    Executa um pattern do Fabric com a entrada e flags especificadas.
    Retorna o output do LLM.
    """
    # Lógica para chamar o Fabric CLI (ex: subprocess.run)
    # Exemplo de comando: fabric --pattern <pattern> --text <input_data> <flags>
    pass
```

## 2. Modificação na Função `run_fabric_pipeline`

A principal alteração ocorre na função `run_fabric_pipeline`, que deve ser estendida para reconhecer o novo tipo de pipeline: `pesquisa_adversarial`.

### 2.1. Inserção do Novo Bloco de Lógica

O novo bloco de lógica deve ser inserido no Módulo de Execução (Fabric Wrapper), dentro da função `run_fabric_pipeline`:

```python
# Dentro de src/lex_os_server.py, na função run_fabric_pipeline(pipeline_type, user_input, case_id):

# ... (código existente para outros pipelines)

elif pipeline_type == "pesquisa_adversarial":
    # ----------------------------------------------------------------
    # PASSO 1: Geração do Meta-Prompt Estruturado (Deep Research Architect)
    # ----------------------------------------------------------------
    
    # O pattern 'deep_research_architect' é executado com a flag --search
    # para realizar a pesquisa preliminar e gerar o prompt final.
    
    print(f"Iniciando Meta-Prompt Architect para o caso {case_id}...")
    
    # Assumindo que 'deep_research_architect' é o nome do pattern no Fabric
    meta_prompt_output = execute_fabric(
        pattern="deep_research_architect",
        input_data=user_input,
        flags="--search"
    )
    
    # ----------------------------------------------------------------
    # PASSO 2: Persistência do Prompt Gerado no Obsidian (Módulo de Memória)
    # ----------------------------------------------------------------
    
    # O prompt gerado é a metodologia de trabalho e deve ser salvo no Obsidian
    # para rastreabilidade e auditoria.
    
    # Assumindo uma função 'save_to_obsidian' no Módulo de Memória
    # O título da nota deve ser claro e vinculado ao caso.
    
    note_title = f"Metodologia_Adversarial_{case_id}"
    note_content = f"# Prompt Gerado pelo Architect\n\n## Input Original\n{user_input}\n\n## Prompt Estruturado\n{meta_prompt_output}"
    
    # Assumindo que 'save_to_obsidian' existe no escopo
    save_to_obsidian(title=note_title, content=note_content, tags=["metodologia", "adversarial", case_id])
    
    print(f"Prompt de Metodologia salvo no Obsidian: {note_title}")
    
    # ----------------------------------------------------------------
    # PASSO 3: Execução do Pattern de Análise Final
    # ----------------------------------------------------------------
    
    # O prompt gerado (meta_prompt_output) é usado como o prompt principal
    # para um pattern de análise final (ex: 'legal_analysis').
    
    print("Executando análise final com o prompt estruturado...")
    
    # Nota: O pattern 'legal_analysis' deve ser adaptado para receber o prompt
    # completo gerado pelo Architect como seu input principal.
    
    final_analysis = execute_fabric(
        pattern="legal_analysis",  # Pattern de análise final (pode ser outro)
        input_data=meta_prompt_output,
        flags="" # Sem busca, pois a pesquisa já foi feita no Passo 1
    )
    
    # ----------------------------------------------------------------
    # PASSO 4: Retorno do Resultado
    # ----------------------------------------------------------------
    
    return {
        "status": "success",
        "meta_prompt": meta_prompt_output,
        "final_analysis": final_analysis,
        "obsidian_note": note_title
    }

# ... (código existente para outros pipelines)
```

## 3. Detalhe da Função `save_to_obsidian`

Para o Passo 2 funcionar, é crucial que o Módulo de Memória (`src/lex_os_server.py` ou um módulo auxiliar) contenha uma função para salvar o conteúdo no Vault do Obsidian.

```python
# Exemplo de assinatura (a ser implementada ou adaptada)
def save_to_obsidian(title: str, content: str, tags: list):
    """
    Salva o conteúdo como um novo arquivo Markdown no Vault do Obsidian.
    """
    # Lógica para formatar o frontmatter (tags, data, etc.)
    # Lógica para escrever o arquivo no caminho configurado em paths.yaml
    pass
```

## 4. Configuração de Uso no Sistema de Equipes Paralelas

Para utilizar o novo pipeline no Sistema de Equipes Paralelas (`src/parallel_teams_system.py`), o `cases_data` deve ser atualizado para incluir o novo tipo de pipeline:

```python
# Exemplo de cases_data atualizado:
cases_data = [
    {
        "case_id": "HC-001",
        "name": "Habeas Corpus 001",
        "type": "criminal",
        "zotero_collection": "HC_Precedents",
        "pipeline_type": "pesquisa_adversarial", # <--- NOVO TIPO DE PIPELINE
        "user_input": "Responsabilidade civil do Estado por reconhecimento fotográfico irregular no Brasil"
    }
]
```
