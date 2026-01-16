# Documentação Técnica do Sistema Lex-OS

## Visão Geral do Projeto
O Lex-OS (Legal Operating System) é um servidor Python que implementa um Sistema Operacional Jurídico baseado no Model Context Protocol (MCP). O sistema atua como middleware inteligente entre o Zotero, Obsidian e a engine de IA local (Fabric), permitindo a automação completa de workflows jurídicos com alta eficiência e rastreabilidade.

## Propósito
O projeto Lex-OS fornece uma plataforma integrada para:
- Análise automatizada de precedentes jurídicos
- Processamento de coleções específicas do Zotero
- Fichamento estruturado de processos e decisões
- Produção assistida de peças jurídicas
- Validação cruzada entre sistemas (Zotero, Obsidian, Fabric)
- Processamento paralelo de múltiplos casos jurídicos

## Arquitetura do Sistema

### 1. Servidor MCP Lex-OS (`src/lex_os_server.py`)
Implementa três módulos principais:

#### Módulo de Memória (Obsidian First)
- `check_local_memory`: Verifica a memória local (Obsidian) antes de buscar externamente
- Implementa lógica "Memory First" para evitar buscas redundantes
- Usa fuzzy matching para encontrar notas relevantes no vault do Obsidian

#### Módulo de Execução (Fabric Wrapper)
- `run_fabric_pipeline`: Executa pipelines do Fabric para processamento de texto jurídico
- Tipos de pipeline suportados:
  - `analise_precedente`: Análise FIRAC+ e extração de notas
  - `fichamento_simples`: Sumarização e fichamento de processos
  - `verificacao_tese`: Verificação de fatos e acordo

#### Módulo de Orquestração Zotero
- `process_zotero_collection`: Processa coleções específicas do Zotero
- Restringe operações por coleção (não permite leitura da biblioteca inteira)
- Aplica pipelines do Fabric e salva resultados diretamente no Obsidian
- Implementa persistência desacoplada da conversação com o LLM

### 2. Sistema de Validação (`src/validation_system.py`)
- Validação da integridade do link entre itens do Zotero e notas do Obsidian
- Verificação da aplicação correta dos padrões Fabric nas notas
- Sistema de auditoria para garantir consistência entre os sistemas
- Geração de relatórios de validação em Markdown

### 3. Sistema de Equipes Paralelas (`src/parallel_teams_system.py`)
- Criação de equipes especializadas para diferentes funções jurídicas
- Processamento paralelo de múltiplos casos
- Atribuição de tarefas com diferentes prioridades
- Monitoramento de status e progresso das equipes
- Validação cruzada dos resultados

## Recursos Principais

### Ferramentas Disponíveis
- **check_local_memory**: Verifica informações localmente antes de buscar externamente
- **run_fabric_pipeline**: Executa pipelines do Fabric para processamento jurídico
- **process_zotero_collection**: Processa coleções específicas do Zotero com pipeline do Fabric

### Padrões de Análise Jurídica
- **FIRAC+ Aprimorado**: Estrutura para análise de precedentes jurídicos
- **Matriz de Comparação de Precedentes**: Comparação sistemática entre decisões
- **Análise de Proporcionalidade**: Avaliação de medidas cautelares
- **Análise de Medidas Cautelares Alternativas**: Checklist para aplicação

### Sistema de Equipes
- **Equipe de Pesquisa de Precedentes**: Especializada em análise jurisprudencial
- **Equipe de Análise de Caso**: Foco na análise dos elementos do caso específico
- **Equipe de Planejamento Estratégico**: Definição da estratégia jurídica
- **Equipe de Redação de Documentos**: Produção de peças jurídicas
- **Equipe de Processamento de Evidências**: Tratamento de provas e cadeia de custódia

## Instalação e Configuração

### Pré-requisitos
- Ambiente Python 3.8+
- Aplicativo Zotero Desktop (para acesso à API local) OU credenciais da API Web do Zotero
- Obsidian Vault configurado
- Fabric patterns instalado

### Configuração do Sistema
1. Configure o caminho do seu vault do Obsidian no arquivo `paths.yaml`
2. Configure as credenciais do Zotero (ou habilite o modo local)
3. Verifique se o caminho para os patterns customizados está correto

### Variáveis de Ambiente
- `ZOTERO_LOCAL=true`: Usa a API local do Zotero (padrão: false)
- `ZOTERO_API_KEY`: Sua chave de API do Zotero (não é necessária para a API local)
- `ZOTERO_LIBRARY_ID`: Seu ID de biblioteca do Zotero (seu ID de usuário para bibliotecas de usuário, não é necessário para a API local)

### Integração com Qwen Code
Para usar com o Qwen Code, adicione o seguinte à sua configuração mcpServers:

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

## Uso do Sistema

### Executar o Servidor
```bash
python src/lex_os_server.py --host localhost --port 8000
```

### Exemplo de Uso das Ferramentas
```python
# Verificar memória local antes de buscar externamente
await check_local_memory("prisão preventiva estelionato", threshold=0.7)

# Executar pipeline do Fabric para análise de precedente
await run_fabric_pipeline(texto_decisao, "analise_precedente")

# Processar coleção específica do Zotero
await process_zotero_collection("HC_Precedents", "Analisar casos de estelionato")
```

## Contexto do Projeto para Qwen Code
Este diretório é especificamente para configurar e usar o Lex-OS com o Qwen Code. O objetivo é integrar a funcionalidade do Zotero, Obsidian e Fabric com o Qwen Code para permitir fluxos de trabalho jurídicos que aproveitem tanto o gerenciamento de referências quanto as capacidades de IA.

## Estrutura .ai/ — Infraestrutura Cognitiva do Projeto

A pasta `.ai/` define a **infraestrutura cognitiva do projeto Lex-OS**.

Ela descreve **como os agentes pensam, classificam, extraem e estruturam
informação jurídica**, servindo como contrato formal entre:

- código MCP
- agentes de IA
- fluxos documentais
- integrações com Zotero e Obsidian

Nenhum conteúdo aqui é código executável.
Tudo aqui é **especificação, governança e reprodutibilidade**.

### Estrutura e Componentes

- `.ai/README.md` - Documentação geral da infraestrutura cognitiva
- `.ai/identity.md` - Identidade e escopo do projeto e do usuário
- `.ai/rules.md` - Regras gerais de comportamento do sistema cognitivo
- `.ai/GOVERNANCE.md` - Princípios e processos de governança cognitiva
- `.ai/agents/` - Especificações funcionais de agentes
- `.ai/flows/` - Pipelines documentais completos
- `.ai/patterns/` - Padrões cognitivos reutilizáveis (Fabric)
- `.ai/schemas/` - Esquemas JSON para validação formal de saídas
- `.ai/prompts/` - Prompts especializados (ex: orquestrador MCP)
- `.ai/AGENT_PROMPT.md` - Prompt-base comum a todos os agentes

### Princípios Fundamentais

A infraestrutura cognitiva segue estes princípios:

1. **Separação Estrita de Responsabilidades**: A pasta `.ai/` contém exclusivamente lógica cognitiva e especificações; o código do MCP contém exclusivamente lógica de execução.

2. **Transparência e Auditabilidade Total**: Toda decisão cognitiva relevante deve estar documentada em `.ai/`.

3. **Reprodutibilidade e Determinismo Cognitivo**: Fluxos descritos em `.ai/flows/` devem ser completos, ordenados e reproduzíveis.

4. **Saídas Estruturadas e Validáveis**: Nenhum agente pode produzir saída livre sem formato definido; toda saída relevante deve obedecer a um schema JSON ou seguir um formato formal documentado.

5. **Patterns como Unidades Mínimas de Raciocínio**: Patterns representam unidades cognitivas reutilizáveis, com intenção clara, definindo entrada e saída.

### Governança Cognitiva

A governança cognitiva existe para garantir que:
- o raciocínio do sistema seja explícito,
- os agentes sejam auditáveis,
- os fluxos sejam reproduzíveis,
- e nenhuma lógica cognitiva fique implícita no código executável.

## Integração com MCP e Subagentes do Qwen Code
O sistema Lex-OS implementa um MCP com três módulos principais que podem ser usados por subagentes do Qwen Code:

- **check_local_memory**: Para verificação de informações locais antes de busca externa
- **run_fabric_pipeline**: Para processamento de texto jurídico usando padrões Fabric
- **process_zotero_collection**: Para processamento de coleções específicas do Zotero

A arquitetura permite que um subagente orquestrador identifique o ponto específico do fluxo jurídico e delegue as funções para os módulos MCP pré-configurados, organizando-os diante do contexto específico.

As saídas dos módulos MCP podem ser organizadas para aproveitar o potencial de usar cada uma delas em outros processos paralelamente.

## Fase 1: Configuração do MCP do Obsidian

### Soluções prontas disponíveis
- Obsidian MCP Server da Sunwood AI Labs
- Configuração manual usando Node.js 20+ e o comando `npx obsidian-mcp`
- Utilização do Smithery CLI para instalação automática

### Configuração do servidor MCP
- O servidor fornece acesso de leitura e escrita completo ao vault do Obsidian
- Requer configuração no arquivo `claude_desktop_config.json` ou `settings.json` do Qwen Code
- Configuração típica usando o transporte stdio com o comando `npx`:
```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "obsidian-mcp", "/path/to/your/vault"]
    }
  }
}
```

### Habilitar plugin Local REST API no Obsidian
1. No Obsidian, vá para Configurações > Plugins da Comunidade
2. Clique em "Procurar" e pesquise por "Local REST API" de `coddingtonbear`
3. Clique em "Instalar" e, após a instalação, ative o plugin
4. O plugin fornece uma API REST segura para interagir com suas notas do Obsidian
5. O plugin permite automação e integração com outras ferramentas via interface REST

### Recursos disponíveis
- Acesso a conteúdo: Ler e pesquisar notas em todo o vault
- Criação de conteúdo: Criar novas notas e diretórios conforme necessário
- Gestão de conteúdo: Editar notas existentes, mover arquivos e excluir conteúdo
- Organização de tags: Adicionar, remover e renomear tags em todo o vault
- Capacidades de pesquisa: Realizar pesquisas abrangentes no conteúdo do vault

## Fase 2: Integração do Lex-OS MCP

### Configuração do servidor Lex-OS
- Utilizar o arquivo `src/lex_os_server.py` como base
- Instalar dependências com `pip install fastmcp pyzotero pyyaml`
- Criar arquivo .env com as variáveis de ambiente necessárias

### Variáveis de ambiente necessárias
- `ZOTERO_LOCAL=true`: Usa a API local do Zotero (padrão: false)
- `ZOTERO_API_KEY`: Sua chave de API do Zotero (não é necessária para a API local)
- `ZOTERO_LIBRARY_ID`: Seu ID de biblioteca do Zotero (seu ID de usuário para bibliotecas de usuário, não é necessária para a API local)

### Configuração da API do Zotero
#### Opção 1: API local (recomendada para uso local)
1. Abra o Zotero e vá para "Configurações do Zotero"
2. Na aba "Avançado", marque a caixa que diz "Permitir que outros aplicativos neste computador se comuniquem com o Zotero"

#### Opção 2: API Web
1. Crie uma chave de API e encontre seu ID de Biblioteca (geralmente seu ID de Usuário) nas configurações da sua conta Zotero em: https://www.zotero.org/settings/keys

### Configuração no Qwen Code
- Adicionar a configuração do servidor MCP no arquivo `settings.json` do Qwen Code:
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

### Ferramentas disponíveis
- `check_local_memory`: Verifica a memória local (Obsidian) antes de buscar externamente
- `run_fabric_pipeline`: Executa pipelines do Fabric para processamento de texto jurídico
- `process_zotero_collection`: Processa coleções específicas do Zotero com pipeline do Fabric

## Fase 3: Desenvolvimento dos agentes especializados

### Agente de Precedentes
- **FIRAC+** (Facts, Issue, Rule, Analysis, Conclusion + Critical Review): Estruturação lógica de decisões judiciais
  - Extrai e organiza os elementos essenciais de uma decisão judicial
  - Inclui análise crítica e avaliação da aplicabilidade
- **NER Jurídico** (Named Entity Recognition): Identificação de entidades específicas em textos jurídicos
  - Pessoas, órgãos judiciais, datas, dispositivos legais, temas jurídicos
  - Classificação de tipos de entidades para indexação e recuperação
- **Classificação de precedentes**:
  - Leading case (jurisprudência pioneira)
  - Julgado de consolidação (jurisprudência estabelecida)
  - Tese emergente (novas posições jurisprudenciais)
  - Distinguishing possível (precedentes que podem ser distinguidos)
- **Geração automática de BibTeX**:
  - Criação de entradas bibliográficas no padrão BibTeX
  - Formatação adequada para diferentes tipos de documentos jurídicos
- **Fichamento em Markdown**:
  - Criação de notas estruturadas com metadados YAML
  - Formatação consistente para facilitar a consulta e revisão

### Agente de Contexto
- Consulta à base de conhecimento no Obsidian
- Recuperação de histórico do caso, teses já trabalhadas e decisões anteriores
- Produção de contexto sintético e operacional para uso por outros agentes
- Integração com as anotações e notas manuais ou geradas por assistentes

### Agente de Estratégia
- Análise de precedentes validados e contexto do caso
- Definição da estratégia jurídica central
- Validação da existência real dos precedentes que sustentam a estratégia
- Autorização da etapa de redação somente após validação
- Verificação de atualidade e vigência das teses jurídicas
- Utilização de ferramentas MCP para verificar informações em tempo real
  - Uso de `serper` (google_search) e `tavily-search` para validação de jurisprudência
  - Aplicação de padrões `fact-check` para confirmar a vigência de enunciados de súmula
  - Busca por "distinguishing [TEMA] STJ 2024" para garantir que teses não estejam superadas
- Avaliação crítica de riscos e oportunidades da estratégia proposta
- Geração de alertas quando teses estão superadas ou em risco

### Agente de TOC (Índice de Tópicos)
- Geração de esqueleto argumentativo da peça processual
- Criação de estrutura lógica e hierárquica de argumentos
- Produção de mapa lógico da petição com tópicos interligados
- Formatação compatível com o sistema Longform do Obsidian
- Integração com os resultados dos agentes de estratégia e precedentes
- Criação de tópicos como nós independentes que se tornarão notas no Obsidian
- Geração de links diretos para precedentes e referências no Zotero
- Verificação de coerência lógica entre os tópicos e subtemas
- Padronização da nomenclatura e hierarquia dos tópicos

### Agente de Redação Jurídica
- Desenvolvimento dos tópicos em três dimensões complementares:
  1. Direito (lei e doutrina): fundamentação legal e constitucional
     - Identificação explícita de pontos a serem prequestionados
     - Discussão de questões em Recursos Especiais e Extraordinários
     - Formulação clara das questões de direito
  2. Jurisprudência: encaixe dos precedentes por categoria e posição temporal
     - Classificação por tema, órgão julgador e tese adotada
     - Posicionamento em linha do tempo para mostrar evolução jurisprudencial
     - Apoio a teses inovadoras e julgados de vanguarda
     - Distinguishing fundamentado quando necessário
  3. Questões de fato e prova: extração e vinculação de elementos probatórios
     - Vinculação explícita entre fato, prova e tese jurídica
     - Geração de citações ABNT do documento original
- Integração com os resultados dos agentes de precedentes e prova
- Geração de conteúdo jurídico estruturado e fundamentado
- Aplicação de padrões de linguagem jurídica e estilo forense
- Verificação de coerência lógica e textual entre seções
- Criação de seções interligadas com referências cruzadas
- Revisão de estilo e adequação ao tribunal de destino

### Agente de Prova e Cadeia de Custódia
- Extração de fatos de documentos, imagens e vídeos
- Seleção de frames relevantes e recortes de documentos
- Vinculação explícita entre fato, prova e tese jurídica
- Geração de citações ABNT do documento original
- Criação de hashes criptográficos e carimbos de tempo
- Normalização de arquivos (tamanho, formato, resolução)
- Utilização de ferramentas MCP para investigação OSINT
  - Uso de `brave_video_search` e `brave_image_search` para encontrar mídias públicas
  - Aplicação de `tavily-extract` para obter texto bruto de páginas web
  - Mapeamento de sites com `tavily-map` para preservação de conteúdo
- Preservação da cadeia de custódia digital
- Armazenamento seguro com criptografia e versionamento
- Geração de relatórios de auditoria detalhados
- Verificação de autenticidade e integridade dos elementos probatórios
- Integração com sistemas de armazenamento (Git LFS, GitHub privado)

### Agente Zotero
- Sincronização bidirecional com a biblioteca Zotero
- Criação automática de itens bibliográficos
- Geração de links cruzados entre referências e notas
- Exportação e importação de metadados e anotações
- Integração com as ferramentas do Lex-OS MCP:
  - Uso de `process_zotero_collection` para processamento de coleções específicas
  - Aplicação de `run_fabric_pipeline` para análise de itens do Zotero
  - Utilização de `check_local_memory` para verificação de informações locais
- Conversão automática de formatos bibliográficos (BibTeX, ABNT, etc.)
- Validação de integridade dos metadados e referências
- Atualização automática de informações de itens existentes
- Indexação de anotações e marcações para recuperação eficiente
- Geração de relatórios de utilização e análise de bibliografia

### Integração com ferramentas existentes
- Conexão do Agente de Estratégia com ferramentas MCP para validação externa:
  - Conexão com `serper` (google_search) e `tavily-search` para verificação de jurisprudência
  - Aplicação de padrões `fact-check` para confirmar a vigência de enunciados de súmula
  - Uso de `compare-sources` para confrontar diferentes interpretações de teses jurídicas

- Integração do Agente de Prova e Cadeia de Custódia com ferramentas de investigação OSINT:
  - Conexão com `brave_video_search` e `brave_image_search` para encontrar mídias públicas
  - Aplicação de `tavily-extract` para obter texto bruto de páginas web
  - Mapeamento de sites com `tavily-map` para preservação de conteúdo
  - Uso de `brave_local_search` para investigação de empresas e locais

- Integração do Agente Zotero com as ferramentas do Lex-OS MCP:
  - Conexão com `process_zotero_collection` para processamento de coleções específicas
  - Aplicação de `run_fabric_pipeline` para análise de itens do Zotero
  - Utilização de `check_local_memory` para verificação de informações locais

- Configuração de segurança e controle:
  - Estabelecimento de níveis de confiança para diferentes servidores MCP
  - Implementação de listas de permissões e negações para controle de acesso
  - Configuração de timeouts e limites de recursos para cada conexão
  - Criação de logs de auditoria para todas as interações com ferramentas externas

- Gerenciamento de servidores MCP:
  - Configuração via `settings.json` no formato:
    - Transporte stdio para processos locais
    - Transporte HTTP para servidores remotos
    - Transporte SSE para servidores legados
  - Uso de comandos CLI como `qwen mcp add`, `qwen mcp list` e `qwen mcp remove`
  - Armazenamento de configurações em escopos de projeto e usuário

## Fase 4: Orquestração com Subagentes

### Criação do subagente orquestrador
- Desenvolvimento de um subagente central com conhecimento das subrotinas do fluxo jurídico
- Implementação de capacidade para identificar o ponto específico do fluxo em que se encontra
- Criação de mecanismo de delegação para subagentes pré-configurados baseado no contexto
- Definição de critérios claros para seleção do subagente mais adequado para cada tarefa
- Implementação de lógica de fallback para situações em que o subagente principal não é adequado

### Arquitetura de subagentes
- Cada subagente é configurado como um arquivo Markdown com YAML frontmatter
- Definição de ferramentas específicas disponíveis para cada subagente
- Especificação de prompts de sistema personalizados para cada tipo de tarefa
- Configuração de níveis de confiança e permissões para diferentes subagentes
- Implementação de mecanismos de feedback entre subagentes

### Comandos CLI para gerenciamento
- Criação de subagentes com `/agents create`
- Gerenciamento de subagentes existentes com `/agents manage`
- Armazenamento em dois níveis: projeto (`.qwen/agents/`) e usuário (`~/.qwen/agents/`)

### Organização do contexto específico
- Desenvolvimento de sistema para organizar subagentes diante do contexto específico
- Criação de mecanismos para passação de contexto entre subagentes
- Implementação de histórico de contexto compartilhado
- Definição de protocolos de comunicação entre subagentes
- Estabelecimento de limites claros de responsabilidade para cada subagente

### Processamento paralelo das saídas dos subagentes
- Implementação de sistema para organizar as saídas dos subagentes
- Criação de mecanismos para usar as saídas em outros processos paralelamente
- Desenvolvimento de filas de processamento para gerenciar múltiplas tarefas simultâneas
- Estabelecimento de prioridades para diferentes tipos de saídas
- Implementação de mecanismos de sincronização para garantir consistência
- Criação de buffers para armazenamento temporário de saídas intermediárias
- Definição de protocolos de tratamento de erros e exceções em processos paralelos

## Fase 5: Implementação do Pipeline Completo

### Integração dos componentes
- Conexão de todos os agentes especializados no fluxo jurídico contínuo
- Implementação de interfaces de comunicação entre os diferentes agentes
- Criação de mecanismos de validação entre etapas do pipeline
- Estabelecimento de protocolos de erro e recuperação automática
- Implementação de sistema de logging e monitoramento do pipeline

### Automação do fluxo completo
- Desenvolvimento do pipeline de automação desde a ingestão de documentos até a geração da petição final
- Criação de gatilhos automáticos para ativação de agentes específicos
- Implementação de filas de tarefas para processamento contínuo
- Estabelecimento de critérios de qualidade para aprovação entre etapas
- Criação de mecanismos de parada e retomada do pipeline

### Rastreabilidade e cadeia de custódia
- Implementação de sistema de rastreabilidade em todas as etapas
- Criação de logs detalhados de todas as operações realizadas
- Estabelecimento de mecanismos de auditoria para verificação de integridade
- Implementação de carimbos de tempo criptograficamente seguros
- Geração de relatórios de cadeia de custódia para cada documento/processo
- Integração com sistemas de versionamento (Git) para controle de alterações

## Fase 5: Testes e validação com casos reais

### Planejamento dos testes
- Seleção de casos reais para validação do sistema
- Definição de métricas de sucesso e qualidade
- Criação de cenários de teste representativos
- Estabelecimento de critérios de aceitação para cada componente

### Execução dos testes
- Testes de integração entre todos os agentes
- Validação da precisão dos resultados gerados
- Verificação da rastreabilidade e cadeia de custódia
- Avaliação do desempenho e tempo de processamento
- Testes de carga e estresse do sistema

### Validação jurídica
- Revisão por especialistas das saídas geradas
- Verificação da adequação jurídica das teses e argumentos
- Confirmação da conformidade com padrões forenses
- Análise da qualidade da fundamentação legal e jurisprudencial

### Ajustes e refinamento
- Identificação de gargalos e problemas no sistema
- Otimização de processos e algoritmos
- Ajuste de parâmetros e configurações
- Melhoria contínua com base no feedback

## Fase 6: Otimização e Expansão
- Refinamento dos agentes com base no feedback
- Expansão das capacidades do sistema
- Documentação das melhores práticas e padrões de uso
- Implementação de melhorias de desempenho
- Expansão para novos domínios jurídicos
- Integração com ferramentas adicionais
- Criação de módulos especializados para áreas específicas do direito

## Referências e Documentação Adicional

Para carregar o contexto completo do projeto em sessões futuras, consulte:

- `README_LEX_OS.md` - Visão geral do sistema Lex-OS
- `INSTALL_LEX_OS.md` - Instruções detalhadas de instalação e configuração do Lex-OS
- `MCP_SERVER_SETUP.md` - Instruções de configuração do servidor MCP
- `CONTEXT.md` - Contexto completo do sistema Lex-OS
- `SUMMARY.md` - Sumário dos componentes principais do Lex-OS
- `paths.yaml` - Configuração de caminhos e variáveis do sistema
- `.ai/` - Infraestrutura cognitiva (agentes, fluxos, patterns, schemas, prompts)

## Referências e Documentação do Fabric

Para detalhes sobre a estrutura de diretórios e comandos úteis do Fabric, consulte:

- `.ai/GOVERNANCE.md` - Princípios de governança cognitiva
- Documentação oficial do Fabric: https://github.com/danielmiessler/Fabric