# Plano SDD — Auditoria de NFS-e

Responsável: Marcos — INFIS Consultoria.

## Etapa inicial: documentação (histórico)

Entregar somente a constituição, os requisitos, a arquitetura proposta, este plano, as tarefas e as orientações para agentes. Não implementar código, instalar dependências, provisionar infraestrutura ou conectar serviços.

## Sequência futura condicionada à aprovação

| Fase | Entrega esperada | Condição de saída |
| --- | --- | --- |
| 1. Refinamento | Escopo fiscal, exemplos anonimizados autorizados, regras e políticas de dados. | Marcos resolve as pendências relevantes de `requirements.md` e aprova a linha de base. |
| 2. Contratos e desenho | Contrato SOFIA, modelo de ameaças, isolamento, estados e decisões técnicas. | Contratos validados pelos responsáveis e controles vinculados aos requisitos. |
| 3. Planejamento da validação | Cenários funcionais, segurança, revisão humana e recuperação. | Cada requisito tem cenário e evidência de aceite definidos. |
| 4. Implementação futura | Incrementos pequenos conforme especificações aprovadas. | Autorização explícita para implementar e revisão de cada incremento. |
| 5. Homologação | Relatórios de validação com dados autorizados e integração em ambiente isolado. | Critérios de aceite atendidos, falhas relevantes resolvidas e aprovação de Marcos. |
| 6. Operação controlada | Procedimentos de acesso, monitoramento, incidentes, retenção e recuperação. | Aprovação operacional e de integração com a SOFIA. |

## Estratégia de validação futura

- Usar primeiro dados sintéticos ou anonimizados autorizados.
- Cobrir documento válido, inválido, incompleto e formato não suportado.
- Verificar seleção de regras por vigência e jurisdição e ausência de evidência.
- Verificar acesso cruzado entre clientes, trabalhos e agentes, identidade inválida e expiração de autorização.
- Verificar instruções maliciosas em documentos, vazamento em logs e destinos externos não autorizados.
- Verificar idempotência, mensagens incompatíveis, cancelamento, indisponibilidade e falha de auditoria.
- Verificar proveniência de achados, versões e aprovação humana antes de concluir.

## Dependências e riscos

Variações municipais e ausência de regras aprovadas podem limitar a cobertura. Contratos desconhecidos da SOFIA impedem afirmar compatibilidade. Dados fiscais exigem políticas de acesso e retenção antes de qualquer uso real. Esses pontos devem ser resolvidos nas fases 1 e 2, sem assumir padrões técnicos ou fiscais não confirmados.

## Controle de mudanças

Alterações de escopo atualizam primeiro requisitos e critérios de aceite; depois arquitetura, plano e tarefas. Marcos aprova impactos em segurança, isolamento, regras fiscais e integração. O histórico deve preservar as decisões e as evidências de validação correspondentes.


## Etapa atual: estrutura da solução

Etapa atual autorizada: criar a estrutura da solução .NET 10, projetos vazios, referências entre projetos e projetos xUnit sem casos de teste. Permitir somente o bootstrap mínimo da API, sem endpoints ou funcionalidades. Não implementar regras, serviços, contratos de negócio, persistência, integrações ou controles operacionais. Restauração e compilação são permitidas para verificar a estrutura; funcionalidades futuras dependem de autorização e especificações aplicáveis aprovadas.
