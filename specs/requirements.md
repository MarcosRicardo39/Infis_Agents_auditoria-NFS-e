# Requisitos — Auditoria de NFS-e

## Identificação e escopo

Agente da INFIS Consultoria, sob responsabilidade de Marcos, com futura integração à SOFIA. Esta versão especifica o comportamento pretendido; não implementa processamento ou integrações.

O escopo futuro é receber documentos autorizados, verificar sua qualidade, aplicar regras aprovadas e apresentar achados com evidências para revisão humana. Municípios, layouts, fontes, volumes e regras tributárias permanecem pendentes de definição.

## Requisitos funcionais e critérios de aceite

| ID | Requisito | Critério de aceite futuro |
| --- | --- | --- |
| RF-01 | Receber uma solicitação com identidade autenticada, cliente, trabalho e escopo autorizado. | Solicitações sem contexto válido são recusadas antes do acesso aos documentos. |
| RF-02 | Validar formato, integridade, origem e campos necessários dos documentos. | Entradas inválidas são identificadas com motivo; dados ausentes não são inventados. |
| RF-03 | Normalizar dados preservando o vínculo com o documento original. | Cada campo utilizado pode ser relacionado à fonte; o original permanece íntegro. |
| RF-04 | Executar apenas regras aprovadas e aplicáveis à jurisdição e vigência. | O resultado identifica regra, versão, fonte e período; regra não aplicável não produz conclusão fiscal. |
| RF-05 | Produzir achados com evidência, justificativa, severidade e limitações. | Cada achado pode ser reconstruído a partir das referências registradas; incertezas são explícitas. |
| RF-06 | Disponibilizar relatório para revisão humana. | O relatório distingue achados, limitações e estado de revisão, sem equivaler automaticamente a parecer aprovado. |
| RF-07 | Registrar aprovação, rejeição ou pedido de esclarecimento do revisor autorizado. | Decisão, autor, data e justificativa ficam vinculados à versão revisada sem apagar o histórico. |
| RF-08 | Receber trabalhos e devolver estado e resultados à SOFIA por contrato versionado. | Mensagens válidas preservam correlação e escopo; versões incompatíveis são recusadas com erro controlado. |
| RF-09 | Tratar repetição, cancelamento e falhas de execução. | Reenvio idêntico não duplica efeitos; reutilização da chave com conteúdo diferente é recusada; cancelamento preserva rastreabilidade. |

## Requisitos não funcionais e de segurança

| ID | Requisito | Critério de aceite futuro |
| --- | --- | --- |
| RNF-01 | Isolar clientes, trabalhos e agentes em todas as camadas. | Tentativas de acesso cruzado a arquivos, memória, resultados e logs são negadas e auditadas. |
| RNF-02 | Autenticar identidades e autorizar cada operação pelo menor privilégio. | Identidade ausente, expirada ou sem escopo é recusada; credenciais não aparecem em resultados ou logs. |
| RNF-03 | Manter trilha de auditoria protegida contra alteração não autorizada. | Eventos têm identificador, data UTC, ator, cliente, trabalho, correlação, ação e resultado; adulterações são detectáveis. |
| RNF-04 | Minimizar e proteger dados pessoais e fiscais. | Dados sensíveis são mascarados nos logs; transporte e armazenamento são protegidos; retenção e descarte são verificáveis segundo política aprovada. |
| RNF-05 | Resistir a instruções maliciosas nas entradas. | Conteúdo de documentos ou de outros agentes não altera permissões, regras ou destinos de saída. |
| RNF-06 | Permitir reprodução e diagnóstico das análises. | Registrar versões de regras, configuração, modelo quando utilizado, referências de entrada e integridade; variações não determinísticas são declaradas. |
| RNF-07 | Aplicar limites e recuperação segura. | Limites de tamanho, tempo, custo e retentativas são definidos antes da operação e seus excessos geram estados controlados. |
| RNF-08 | Impedir execução sem trilha de auditoria disponível. | Falha no registro obrigatório bloqueia novas ações auditáveis e sinaliza falha sem divulgar dados sensíveis. |

## Fora do escopo

- Implementação da aplicação nesta etapa.
- Emissão, alteração, cancelamento ou transmissão de NFS-e e obrigações fiscais.
- Decisão fiscal definitiva sem revisão humana.
- Acesso direto a dados ou memória de outros agentes e execução de comandos recebidos em documentos.
- Consulta a sistemas externos e compartilhamento de dados sem autorização e contrato aprovados.

## Decisões pendentes de Marcos

- Municípios, regimes, períodos, layouts e fontes de documentos atendidos.
- Catálogo inicial de regras, fontes oficiais e processo de atualização e aprovação.
- Papéis autorizados e critérios de severidade e revisão.
- Política de retenção, descarte, classificação e uso de provedores de IA.
- Contrato, autenticação, transporte e limites efetivos da SOFIA.
- Metas de volume, latência, disponibilidade, custo e qualidade.

As pendências impedem assumir cobertura fiscal ou prontidão operacional; a implementação depende da aprovação da linha de base correspondente.
