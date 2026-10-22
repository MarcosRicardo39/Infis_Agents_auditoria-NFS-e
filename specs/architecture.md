# Arquitetura proposta — Auditoria de NFS-e

## Estado e fronteiras

Arquitetura com a stack definida para a implementação futura. Responsável: Marcos, INFIS Consultoria. Os contratos abaixo são propostas para validação com a SOFIA. A etapa atual autoriza somente a solução, projetos vazios, referências e bootstrap mínimo da API; funcionalidades e integrações permanecem pendentes.

## Estrutura da solução

Solução: `Infis.AuditoriaNfse.sln`. Todos os projetos utilizam `net10.0`.

| Diretório/projeto | Responsabilidade futura | Referências de projeto |
| --- | --- | --- |
| src/Infis.AuditoriaNfse.Api | Host ASP.NET Core e composição da aplicação | Application, Infrastructure, Contracts |
| src/Infis.AuditoriaNfse.Domain | Entidades e regras de domínio independentes de tecnologia | Nenhuma |
| src/Infis.AuditoriaNfse.Application | Casos de uso e abstrações de acesso a serviços | Domain, Contracts |
| src/Infis.AuditoriaNfse.Contracts | Contratos públicos versionados, ainda não definidos | Nenhuma |
| src/Infis.AuditoriaNfse.Infrastructure | Adaptadores OpenAI, Semantic Kernel, pgvector, MongoDB e SOFIA | Application |
| tests/Infis.AuditoriaNfse.UnitTests | Testes xUnit de domínio e aplicação | Domain, Application |
| tests/Infis.AuditoriaNfse.IntegrationTests | Testes xUnit da API e adaptadores | Api, Infrastructure |

Os projetos de biblioteca estão vazios. A API contém somente inicialização do host, sem endpoints, inclusive sem o exemplo Hello World. Os projetos xUnit não contêm casos de teste. Swagger, conectores, bancos, autenticação, isolamento e auditoria não estão implementados. As dependências específicas das integrações serão adicionadas na etapa correspondente; somente os pacotes gerados pelo template xUnit são referenciados para testes.

Referências entre projetos organizam dependências de compilação e não implementam isolamento de dados ou de execução. A validação estrutural consiste em restaurar e compilar a solução, sem iniciar serviços ou chamar provedores externos.

## Stack tecnológica definida

| Camada | Tecnologia | Papel previsto |
| --- | --- | --- |
| API | .NET 10 com ASP.NET Core API | Expor operações autenticadas de auditoria e integração com a SOFIA. |
| LLM | OpenAI | Apoiar interpretação e elaboração de achados, sempre sujeitos à validação e revisão humana. |
| RAG | PostgreSQL com a extensão pgvector | Armazenar e recuperar representações vetoriais de conteúdo autorizado para fundamentar as respostas. |
| Memória conversacional | MongoDB | Persistir histórico e contexto de conversas separados por cliente, trabalho, agente e sessão. |
| Coordenação semântica interna | Microsoft Semantic Kernel | Coordenar chamadas ao LLM, recuperação de contexto e funções explicitamente autorizadas dentro do agente. |
| Documentação da API | Swagger / OpenAPI | Documentar contratos HTTP e permitir exploração controlada em ambientes autorizados. |
| Testes | xUnit | Estruturar testes futuros de regras, serviços, integração e controles de segurança. |

As tecnologias indicadas são decisões de arquitetura; versões de pacotes, modelos OpenAI, estratégia de embeddings e detalhes de implantação permanecem pendentes. A escolha de OpenAI não autoriza o envio de dados reais sem aprovação da política de tratamento de dados.

## Fluxo lógico

SOFIA → entrada autenticada → autorização e contexto isolado → validação de documentos → normalização → avaliação por regras aprovadas → achados e relatório → revisão humana → retorno autorizado à SOFIA.

Todos os passos auditáveis geram eventos correlacionados. Entradas inválidas ou ações não autorizadas terminam em erro controlado antes do processamento afetado.

Na implementação futura, a ASP.NET Core API receberá as solicitações. Os serviços de aplicação aplicarão autorização, validação e regras; o Semantic Kernel coordenará o uso de contexto recuperado no pgvector e de memória autorizada no MongoDB para chamadas à OpenAI. As saídas do LLM serão validadas antes de compor achados e seguir para revisão humana. A SOFIA continuará responsável pela coordenação entre agentes; o Semantic Kernel terá escopo interno ao agente Auditoria de NFS-e.

## Componentes e responsabilidades

| Componente | Responsabilidade |
| --- | --- |
| Adaptador SOFIA | Validar contrato e identidade, receber trabalhos e comunicar estados e referências autorizadas. |
| Controle de acesso e contexto | Vincular cliente, trabalho, permissões, prazos e limites; verificar autorização em cada acesso. |
| Ingestão e validação | Verificar conteúdo, limites, integridade e formatos autorizados; preservar originais. |
| Normalização | Preparar representação comum com proveniência por campo e registro de lacunas. |
| Catálogo e avaliador de regras | Selecionar regras aprovadas por vigência e jurisdição, registrando suas versões. |
| API ASP.NET Core em .NET 10 | Expor contratos HTTP, validar solicitações e encaminhar operações aos serviços de aplicação. |
| Semantic Kernel | Coordenar funções e chamadas de IA com escopo explícito; verificar autorização antes de cada ferramenta e impedir que o modelo amplie permissões. |
| OpenAI | Auxiliar interpretação dentro de limites; suas saídas passam por validação e não concedem autoridade ou acesso. |
| Recuperação RAG com pgvector | Recuperar trechos autorizados com fonte, versão, vigência e contexto de acesso para fundamentar análises. |
| Memória conversacional MongoDB | Manter contexto de sessão isolado, com minimização de dados, expiração e controle de acesso. |
| Swagger / OpenAPI | Descrever contratos versionados sem expor segredos ou exemplos reais de clientes; restringir acesso à interface e suas operações. |
| Gestão de achados e revisão | Consolidar evidências, versões e decisões humanas. |
| Armazenamento isolado | Manter originais, dados derivados e resultados separados por cliente e trabalho. |
| Auditoria e observabilidade | Registrar eventos mínimos, integridade e falhas, com acesso restrito e sem conteúdo fiscal desnecessário. |

## Contrato conceitual de integração

Não representa uma API existente. Campos, tipos, transporte e versões finais serão acordados com a SOFIA.

- Solicitação: versão do contrato, identificador da solicitação, correlação, chave de idempotência, cliente, trabalho, operação, referências de documentos, escopo e prazo.
- Identidade e permissões: obtidas por mecanismo autenticado; identificadores declarados na mensagem não constituem autorização.
- Resultado: identificadores de contexto, estado, versão do relatório, referências autorizadas a achados e evidências, limitações e indicação de revisão humana.
- Erro: código estável, etapa, correlação e possibilidade de retentativa; sem segredos, caminhos internos ou dados fiscais desnecessários.
- Idempotência: chave vinculada a cliente, trabalho, operação e integridade da solicitação; política de validade e persistência ainda pendente.
- Evolução: validar compatibilidade explicitamente; rejeitar contratos desconhecidos sem executar o trabalho.

## Ciclo de vida proposto

Recebido → validando → em análise → aguardando revisão → concluído. Estados alternativos: rejeitado, falhou ou cancelado. Conclusão exige decisão do revisor autorizado; análise automatizada encerrada permanece aguardando revisão. Transições, erros e cancelamentos devem ser registrados. Retomadas preservam histórico e não repetem efeitos já confirmados.

## Fronteiras de confiança e múltiplos agentes

- SOFIA coordena o trabalho; cada agente mantém sua própria autorização e validação de entrada.
- Nenhum agente herda permissões de outro, compartilha memória global de clientes ou lê armazenamento alheio diretamente.
- Transferências usam canais autorizados, referências com escopo e validação no destino. Delegação não amplia privilégios.
- Documentos e mensagens são conteúdo não confiável, inclusive quando encaminhados por outro agente.
- Acesso externo e ferramentas obedecem a uma lista aprovada de destinos e operações. Credenciais ficam fora de prompts e artefatos.
- Trilha de auditoria deve registrar origem e destino de transferências, versões e correlação, sem replicar documentos completos em logs.

## Dados, auditoria e recuperação

### RAG com PostgreSQL e pgvector

- Indexar somente fontes aprovadas, preservando referência ao original, versão, vigência, jurisdição e integridade de cada trecho.
- Aplicar autorização e filtros de cliente, trabalho e escopo antes de selecionar conteúdo para o LLM. A busca por similaridade não substitui controles de acesso.
- Separar o acervo normativo compartilhável de documentos privados de clientes; compartilhamento exige classificação e autorização explícitas.
- Definir o modelo de embeddings e sua compatibilidade com o índice antes da implementação; registrar versões e prever reindexação quando necessário.
- Vincular conteúdo recuperado aos achados. Ausência de evidência suficiente deve gerar limitação explícita, sem conclusão fiscal presumida.

### Memória conversacional com MongoDB

- Vincular registros a cliente, trabalho, agente, sessão e identidade autorizada; validar esse escopo em cada leitura e escrita.
- Armazenar somente o contexto necessário, conforme política de retenção e descarte aprovada. A memória não substitui originais, evidências nem a trilha formal de auditoria.
- Tratar o histórico recuperado como dados não confiáveis e verificar sua autorização antes de incluí-lo em prompts.
- Não compartilhar coleções ou credenciais entre agentes como atalho para comunicação; a estratégia concreta de segregação será definida antes da operação.

### OpenAI e Semantic Kernel

Enviar somente dados mínimos autorizados. Manter credenciais fora de prompts e logs; configurar modelos, limites de custo, tempo e retentativas conforme política aprovada. Registrar identificadores de modelo e versões de prompts e configurações, evitando registrar conteúdo sensível desnecessário.

Funções disponibilizadas ao Semantic Kernel devem ter escopo restrito e autorização na execução. Conteúdo recuperado e respostas do LLM não podem habilitar ferramentas, alterar regras ou autorizar ações. O avaliador de regras e a revisão humana permanecem responsáveis pela validação dos achados.

### Trilha de auditoria e recuperação

Manter referências de integridade dos originais e versões dos artefatos derivados. Eventos devem permitir reconstruir quem acessou o quê, sob qual autorização e com qual resultado. Proteger registros contra alteração, controlar seu acesso e definir retenção própria, compatível com a política de dados.

Falhas de autorização, isolamento ou auditoria obrigatória interrompem a ação afetada. Retentativas são limitadas e dependem de classificação do erro e idempotência. Estratégias de backup, restauração, expiração e descarte seguro dependem de aprovação antes da operação.

## Estratégia de testes e documentação da API

Os testes futuros utilizarão xUnit para verificar regras por vigência e jurisdição, proveniência dos achados, isolamento no pgvector e MongoDB, autorização de funções no Semantic Kernel, falhas e limites nas chamadas à OpenAI, idempotência e contratos SOFIA. Simulações de LLM permitirão cenários controlados; verificações com serviços reais dependerão de ambiente isolado e dados autorizados.

O contrato OpenAPI e a interface Swagger deverão refletir as operações aprovadas, incluindo autenticação, estados e erros. Sua exposição e execução de operações deverão seguir o controle de acesso do ambiente. Nesta etapa existem somente projetos xUnit vazios; casos de teste e configuração OpenAPI/Swagger permanecem para implementação futura.

## Decisões arquiteturais pendentes

Transporte SOFIA; gestão de identidades e segredos; modelos OpenAI e embeddings; versões de bibliotecas; política de envio e retenção de dados pelo provedor; esquema e segregação do PostgreSQL/pgvector e MongoDB; armazenamento de originais, relatórios e trilha de auditoria; mecanismo de isolamento de execução; exposição do Swagger; hospedagem e metas operacionais. Registrar cada decisão futura com contexto, alternativas, impactos e aprovação, atualizando estas especificações antes da implementação afetada.

## Histórico de revisão

- 22/10/2026 — Stack definida por solicitação do responsável: .NET 10 / ASP.NET Core API, OpenAI, PostgreSQL com pgvector, MongoDB, Microsoft Semantic Kernel, Swagger / OpenAPI e xUnit. Detalhamento de responsabilidades e controles associados a RF-03 a RF-08 e RNF-01 a RNF-08, sem implementação.
