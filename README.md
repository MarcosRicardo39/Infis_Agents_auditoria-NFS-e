# Auditoria de NFS-e — INFIS Consultoria

Estrutura inicial de um agente de inteligência artificial para apoiar a auditoria de Notas Fiscais de Serviços Eletrônicas (NFS-e), com desenvolvimento orientado por especificações (SDD).

**Responsável:** Marcos.

**Organização:** INFIS Consultoria.

**Integração futura:** orquestrador central SOFIA.

## Estado atual

O repositório contém documentação SDD e uma solução .NET 10 com sete projetos. As bibliotecas estão vazias; a API possui somente o bootstrap do host ASP.NET Core, sem endpoints. Os projetos xUnit possuem referências aos pacotes de teste, mas não contêm casos de teste.

A estrutura foi compilada com sucesso, sem avisos ou erros. Essa verificação comprova somente a compilação: regras fiscais, autenticação, isolamento operacional, trilha de auditoria, persistência e integrações ainda não foram implementados.

Não existem chamadas à OpenAI, conexões a bancos, processamento de NFS-e, Swagger configurado ou integração ativa com a SOFIA. O projeto ainda não está pronto para operar com dados reais.

## Objetivo e comportamento futuro

O agente deverá receber documentos autorizados, validar e normalizar seus dados, aplicar regras aprovadas conforme vigência e jurisdição e produzir achados rastreáveis para revisão humana.

Fluxo previsto:

```text
SOFIA → solicitação autenticada → autorização e contexto isolado
      → validação e normalização → análise por regras e apoio de IA
      → achados com evidências → revisão humana → retorno à SOFIA
```

O agente não deverá emitir, alterar, cancelar ou transmitir documentos e obrigações fiscais autonomamente. Conclusões e relatórios dependerão de revisão humana autorizada.

## Stack definida

| Tecnologia | Uso previsto | Situação atual |
| --- | --- | --- |
| .NET 10 / ASP.NET Core API | Host HTTP e composição da aplicação | Projeto e bootstrap mínimo criados |
| OpenAI | LLM para apoio à interpretação e elaboração de achados | Integração pendente |
| PostgreSQL com pgvector | Recuperação de contexto para RAG | Integração pendente |
| MongoDB | Memória conversacional isolada por contexto | Integração pendente |
| Microsoft Semantic Kernel | Coordenação interna de chamadas e funções autorizadas | Integração pendente |
| Swagger / OpenAPI | Documentação e exploração controlada da API | Configuração pendente |
| xUnit | Testes unitários e de integração | Projetos e pacotes referenciados; sem testes |

A seleção de tecnologias não autoriza envio de dados reais a serviços externos. Modelos, embeddings, versões das bibliotecas de integração, credenciais e políticas de dados serão definidos nas próximas etapas.

## Estrutura do repositório

```text
Infis_Agents_auditoria-NFS-e/
├── Infis.AuditoriaNfse.sln
├── README.md
├── AGENTS.md
├── .gitignore
├── specs/
│   ├── constitution.md
│   ├── requirements.md
│   ├── architecture.md
│   ├── plan.md
│   └── tasks.md
├── src/
│   ├── Infis.AuditoriaNfse.Api/
│   ├── Infis.AuditoriaNfse.Application/
│   ├── Infis.AuditoriaNfse.Domain/
│   ├── Infis.AuditoriaNfse.Infrastructure/
│   └── Infis.AuditoriaNfse.Contracts/
└── tests/
    ├── Infis.AuditoriaNfse.UnitTests/
    └── Infis.AuditoriaNfse.IntegrationTests/
```

Pastas locais vazias, como `docs` e `samples`, não são versionadas pelo Git. Diretórios de compilação, resultados de testes e arquivos locais de ambiente estão excluídos pelo `.gitignore`.

## Projetos e dependências

Todos os projetos utilizam `net10.0`.

| Projeto | Responsabilidade futura | Referências de projeto |
| --- | --- | --- |
| Api | Host ASP.NET Core e composição das dependências | Application, Infrastructure, Contracts |
| Application | Casos de uso e abstrações de serviços | Domain, Contracts |
| Domain | Entidades e regras de domínio independentes de infraestrutura | Nenhuma |
| Infrastructure | Adaptadores para IA, bancos e SOFIA | Application |
| Contracts | Contratos públicos versionados | Nenhuma |
| UnitTests | Validação unitária de domínio e aplicação | Domain, Application |
| IntegrationTests | Validação da API e adaptadores | Api, Infrastructure |

Essas referências organizam a compilação. O isolamento entre clientes e agentes precisará ser implementado e validado separadamente. O Semantic Kernel coordenará recursos internos deste agente; a coordenação entre agentes continuará sob responsabilidade da SOFIA.

## Pré-requisitos

- Git para obter o repositório.
- SDK .NET 10 para restaurar e compilar a solução.
- Acesso às fontes NuGet configuradas para restaurar os pacotes xUnit e ferramentas de teste.
- Editor ou IDE compatível com projetos .NET 10, conforme preferência.

Não é necessário instalar PostgreSQL ou MongoDB nem configurar uma chave OpenAI para compilar a estrutura atual. O SDK utilizado na verificação inicial foi o `10.0.400`; o repositório ainda não fixa a versão do SDK em `global.json`.

## Obter e compilar

Na pasta em que deseja manter o projeto:

```sh
git clone https://github.com/MarcosRicardo39/Infis_Agents_auditoria-NFS-e.git
cd Infis_Agents_auditoria-NFS-e
dotnet restore Infis.AuditoriaNfse.sln
dotnet build Infis.AuditoriaNfse.sln --no-restore
```

Também é possível abrir `Infis.AuditoriaNfse.sln` em uma IDE compatível. A compilação não inicia a API, bancos ou integrações.

## Execução local do host

Para iniciar apenas o host vazio, em ambiente local de desenvolvimento:

```sh
dotnet run --project src/Infis.AuditoriaNfse.Api/Infis.AuditoriaNfse.Api.csproj --launch-profile http
```

Consulte o endereço exibido no terminal. Como não existem rotas mapeadas, requisições a `/` e `/swagger` não possuem recursos disponíveis e devem retornar HTTP 404. Encerre o processo com `Ctrl+C`. O host vazio não oferece controles de segurança operacionais e não deve ser exposto como serviço de produção.

## Testes

Os projetos estão preparados para receber testes xUnit. Quando houver casos implementados, eles poderão ser executados com:

```sh
dotnet test Infis.AuditoriaNfse.sln
```

Atualmente não há testes a descobrir ou executar. A compilação bem-sucedida não significa cobertura de testes nem validação das regras de segurança. A estratégia futura inclui regras fiscais, proveniência, isolamento, autorização, idempotência, falhas de integração e revisão humana.

## Documentação SDD

| Documento | Conteúdo |
| --- | --- |
| [Constituição](specs/constitution.md) | Princípios obrigatórios e governança |
| [Requisitos](specs/requirements.md) | Requisitos funcionais, não funcionais e critérios de aceite |
| [Arquitetura](specs/architecture.md) | Stack, responsabilidades, fronteiras e integração proposta |
| [Plano](specs/plan.md) | Sequência de evolução e condições de saída |
| [Tarefas](specs/tasks.md) | Entregas concluídas e pendências |
| [Orientações para agentes](AGENTS.md) | Escopo de trabalho e regras de colaboração |

Mudanças funcionais deverão começar pela revisão das especificações aplicáveis e manter rastreabilidade entre requisitos, implementação futura e evidências de validação.

## Isolamento, segurança e auditoria

Os princípios abaixo são obrigatórios para o desenvolvimento futuro; sua documentação não equivale a controles implementados:

- Separar dados, memória, arquivos, credenciais e execução por cliente e trabalho; restringir a memória conversacional também por agente e sessão.
- Aplicar autenticação, autorização por operação e menor privilégio. Identificadores recebidos em mensagens não constituem autorização.
- Registrar origem, versões, correlação, evidências e decisões humanas em trilha protegida contra alteração não autorizada.
- Tratar documentos, trechos RAG, histórico de conversa e mensagens de agentes como dados não confiáveis, sem permitir que alterem permissões ou instruções de controle.
- Compartilhar entre agentes somente o mínimo autorizado, por contratos versionados e canais aprovados pela SOFIA; delegação não amplia privilégios.
- Proteger e minimizar dados pessoais e fiscais; definir retenção e descarte antes da operação.
- Não versionar credenciais, tokens, documentos reais de clientes ou arquivos de ambiente com segredos. O `.gitignore` não substitui a revisão do conteúdo de cada commit.
- Interromper ações afetadas por falhas de autorização, isolamento ou indisponibilidade da auditoria obrigatória.

## Próximas etapas

1. Marcos revisar as especificações e definir municípios, layouts, regras e fontes fiscais atendidos.
2. Aprovar políticas de dados, papéis, revisores, retenção e uso de provedores de IA.
3. Validar contrato SOFIA, autenticação, idempotência, estados e limites.
4. Detalhar isolamento, armazenamento, auditoria e modelo de ameaças.
5. Autorizar e implementar incrementos funcionais com seus critérios de aceite e testes.

O escopo atual permanece limitado à documentação e à estrutura da solução, sem funcionalidades de negócio.
