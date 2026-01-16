# Sistema Lex-OS - Sistema Operacional Jurídico

O Lex-OS é um Sistema Operacional Jurídico que atua como middleware inteligente entre o Zotero, o Obsidian e a engine de IA local (Fabric). Este sistema foi desenvolvido para automatizar e otimizar o workflow jurídico, permitindo a análise de precedentes, fichamento de processos e produção de peças jurídicas com alta eficiência e rastreabilidade.

## Arquitetura do Sistema

O sistema é composto por três componentes principais:

### 1. Servidor MCP Lex-OS (`src/lex_os_server.py`)

Implementa três módulos essenciais:

#### Módulo de Memória (Obsidian First)
- Função `check_local_memory`: Verifica a memória local (Obsidian) antes de buscar externamente
- Implementa lógica "Memory First" para evitar buscas redundantes
- Usa fuzzy matching para encontrar notas relevantes no vault do Obsidian

#### Módulo de Execução (Fabric Wrapper)
- Função `run_fabric_pipeline`: Executa pipelines do Fabric para processamento de texto jurídico
- Tipos de pipeline suportados:
  - `analise_precedente`: Executa `make_firac+` e `extract_key_notes`
  - `fichamento_simples`: Executa `summarize` e `fichamento_processo_autos`
  - `verificacao_tese`: Executa `fact-check` e `check_agreement`

#### Módulo de Orquestração Zotero
- Função `process_zotero_collection`: Processa coleções específicas do Zotero
- Restringe operações por coleção (não permite leitura da biblioteca inteira)
- Aplica pipelines do Fabric e salva resultados diretamente no Obsidian
- Implementa persistência desacoplada da conversação com o LLM

### 2. Sistema de Validação (`src/validation_system.py`)

Implementa mecanismos de validação para referência cruzada:

- Validação da integridade do link entre itens do Zotero e notas do Obsidian
- Verificação da aplicação correta dos padrões Fabric nas notas
- Sistema de auditoria para garantir consistência entre os sistemas
- Geração de relatórios de validação em Markdown

### 3. Sistema de Equipes Paralelas (`src/parallel_teams_system.py`)

Dimensiona o sistema para lidar com múltiplos casos jurídicos concorrentes:

- Criação de equipes especializadas para diferentes funções jurídicas
- Processamento paralelo de múltiplos casos
- Atribuição de tarefas com diferentes prioridades
- Monitoramento de status e progresso das equipes
- Validação cruzada dos resultados

## Configuração

### Dependências

Antes de executar o sistema, certifique-se de ter instalado:

1. **Python 3.8+**
2. **Fabric**: `pip install fabric`
3. **FastMCP**: `pip install fastmcp`
4. **PyZotero**: `pip install pyzotero`
5. **PyYAML**: `pip install pyyaml`

### Configuração do Ambiente

Configure o arquivo `paths.yaml` com os caminhos e credenciais necessários:

```yaml
obsidian_vault_path: "/caminho/do/seu/vault/obsidian"
zotero_library_id: "seu_id_de_biblioteca"  # Deixe vazio se usando modo local
zotero_api_key: "sua_chave_de_api"       # Deixe vazio se usando modo local
zotero_local: true  # Usa a API local do Zotero (recomendado para uso local)
custom_patterns_path: "/home/peixoto/peixoto-ops/costum_patterns"
server_host: "localhost"
server_port: 8000
default_threshold: 0.6
max_items_per_collection: 10
```

### Configuração do Zotero

Você pode usar o Zotero de duas formas:

1. **Modo Local (Recomendado)**: Habilite a opção "Permitir que outros aplicativos se comuniquem com o Zotero" nas configurações avançadas do Zotero Desktop
2. **Modo API**: Obtenha uma chave de API em https://www.zotero.org/settings/keys

## Uso

### Executar o Servidor MCP

```bash
python src/lex_os_server.py --host localhost --port 8000
```

### Configurar no Qwen Coder

Adicione a seguinte configuração ao seu arquivo de configuração do MCP:

```json
{
  "mcpServers": {
    "lex-os": {
      "command": "python",
      "args": ["/caminho/para/seu/projeto/src/lex_os_server.py"],
      "env": {
        "ZOTERO_LOCAL": "true",
        "ZOTERO_API_KEY": "",
        "ZOTERO_LIBRARY_ID": ""
      }
    }
  }
}
```

### Executar o Sistema de Equipes Paralelas

```python
import asyncio
from src.parallel_teams_system import initialize_parallel_teams_system

async def main():
    orchestrator = initialize_parallel_teams_system()
    
    # Dados dos casos para processar
    cases_data = [
        {
            "case_id": "HC-001",
            "name": "Habeas Corpus 001",
            "type": "criminal",
            "zotero_collection": "HC_Precedents",
            # ... outros dados do caso
        }
    ]
    
    # Processa os casos em paralelo
    results = await orchestrator.process_multiple_cases_parallel(cases_data)
    return results

# Executa o processamento
asyncio.run(main())
```

## Funcionalidades Avançadas

### Validação de Integridade

Execute a validação de referências cruzadas:

```python
from src.validation_system import run_cross_reference_validation

# Valida uma coleção específica
results = run_cross_reference_validation(
    collection_name="HC_Precedents",
    output_report="Relatórios/validacao_hc_precedents.md"
)
```

### Monitoramento de Equipes

Monitore o status das equipes em tempo real:

```python
# Status de uma equipe específica
status = orchestrator.get_team_status(team_id)

# Status geral de todas as equipes
overall_status = orchestrator.get_overall_status()
```

## Benefícios do Sistema

1. **Eficiência**: Processamento automatizado de coleções inteiras do Zotero
2. **Persistência**: Resultados salvos diretamente no Obsidian, garantindo integridade
3. **Escalabilidade**: Sistema de equipes paralelas para múltiplos casos
4. **Validação**: Sistema de verificação cruzada entre sistemas
5. **Integração**: Conexão fluida entre Zotero, Obsidian e Fabric
6. **Rastreabilidade**: Cadeia de custódia completa das análises

## Melhores Práticas

1. **Organização por Coleções**: Use coleções específicas no Zotero para diferentes tipos de casos
2. **Padronização de Nomenclatura**: Use nomes consistentes para facilitar o matching
3. **Validação Regular**: Execute o sistema de validação periodicamente
4. **Monitoramento**: Acompanhe o status das equipes e tarefas
5. **Backup**: Mantenha backups regulares do vault do Obsidian e da biblioteca Zotero

## Solução de Problemas

### Erros Comuns

- **"Caminho do vault do Obsidian não configurado"**: Verifique o arquivo `paths.yaml`
- **"Ferramenta 'fabric' não encontrada"**: Verifique se o Fabric está instalado e no PATH
- **"Credenciais do Zotero não configuradas"**: Configure as variáveis de ambiente ou habilite o modo local

### Modo Zotero Local vs API

O servidor suporta dois modos de conexão com o Zotero:

- **Modo Local**: Mais seguro, não requer credenciais, mas exige que o Zotero Desktop esteja rodando
- **Modo API**: Requer credenciais, mas pode ser usado remotamente

Para habilitar o modo local, defina `zotero_local: true` no `paths.yaml`.

## Contribuições

Para contribuir com o projeto:

1. Faça um fork do repositório
2. Crie uma branch para sua feature (`git checkout -b feature/nova-feature`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova feature'`)
4. Push para a branch (`git push origin feature/nova-feature`)
5. Abra um Pull Request

## Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo LICENSE para detalhes.