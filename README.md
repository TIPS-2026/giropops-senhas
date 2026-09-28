# Giropops Senhas: Desafio IaC & Pipelines

Repositório central do desafio da turma **IaC & Pipelines Specialist**. Aqui está a aplicação que vamos colocar em produção e a visão geral do projeto.

📋 **Kanban:** https://github.com/orgs/TIPS-2026/projects/1

---

## A aplicação

Um gerador de senhas em Flask que guarda as últimas senhas geradas no Redis.

| Endpoint | Método | O que faz |
|---|---|---|
| `/` | GET/POST | Interface web |
| `/api/gerar-senha` | POST | Gera uma senha (JSON) |
| `/api/senhas` | GET | Lista as 10 últimas senhas |
| `/metrics` | GET | Métricas Prometheus |

**Regras sobre a aplicação:**
- **O código da aplicação não será alterado.** O desafio é a plataforma ao redor dela.
- **Não há persistência.** O Redis é **efêmero** e roda junto da aplicação, sem volume. Se a task reiniciar, as senhas somem, e isso é esperado.

## O desafio

Em 8 semanas, a turma trabalha como um time de plataforma de uma empresa que acabou de começar e não tem nenhuma infraestrutura. Ao final, esta aplicação precisa estar rodando na AWS (**ECS Fargate**), publicada por **pipelines compartilhados** e com toda a infraestrutura em **Terraform**, aplicada pelo **Atlantis**.

## Repositórios

| Repositório | Para quê |
|---|---|
| [`giropops-senhas`](https://github.com/TIPS-2026/giropops-senhas) | A aplicação, o empacotamento e os workflows **consumidores** |
| [`pipelines`](https://github.com/TIPS-2026/pipelines) | Workflows reutilizáveis (CI, CD, testes e scans) |
| [`terraform-modules`](https://github.com/TIPS-2026/terraform-modules) | Módulos Terraform reutilizáveis e versionados |
| [`infra-live`](https://github.com/TIPS-2026/infra-live) | Composição dos módulos por ambiente, state, identidade e Atlantis |

## Times

| Trilha | Time | Team no GitHub | Squads (sub-teams) | Responsabilidade |
|---|---|---|---|---|
| Actions | **CI/Gates** | `@TIPS-2026/ci-gates` | `ci-a` Pipeline de PR · `ci-b` Governança | Tudo que decide se um PR **pode entrar**. Dono do `Dockerfile` |
| Actions | **CD/Push** | `@TIPS-2026/cd-push` | `cd-a` Build & Push · `cd-b` Deploy & Promoção | Tudo **depois do merge**: publicar a imagem, deploy, promoção e rollback |
| Actions | **Testes/Scans** | `@TIPS-2026/testes-scans` | `ts-a` Código & IaC · `ts-b` Imagem & Runtime | Ferramentas de teste e segurança usadas pelo CI e pelo CD. Dono do `docker-compose.yml` |
| Terraform | **Redes** | `@TIPS-2026/redes` | `red-a` VPC · `red-b` DNS & Certificados | VPC, subnets, NAT, DNS e certificados |
| Terraform | **Runtime** | `@TIPS-2026/runtime` | `run-a` State & Identidade · `run-b` Atlantis | Onde o Terraform roda: state, OIDC e Atlantis |
| Terraform | **Compute** | `@TIPS-2026/compute` | `cmp-a` Cluster & Borda · `cmp-b` Serviço | Onde a aplicação roda: cluster, load balancer, registry e serviço |

**Permissões:** cada time tem *write* nos repositórios de que é dono e *triage* nos demais (para se atribuir issues e aplicar labels). Os squads herdam as permissões do time. Mentores: `@TIPS-2026/mentores`.

| Repositório | Write | Triage |
|---|---|---|
| `giropops-senhas` | ci-gates, cd-push, testes-scans | redes, runtime, compute |
| `pipelines` | ci-gates, cd-push, testes-scans | redes, runtime, compute |
| `terraform-modules` | redes, runtime, compute | ci-gates, cd-push, testes-scans |
| `infra-live` | redes, compute (runtime: *maintain*) | ci-gates, cd-push, testes-scans |

## O que esperamos que seja entregue neste repositório

| Entrega | Dono |
|---|---|
| `Dockerfile` que gere uma imagem segura e enxuta, com a app acessível de fora do container | CI/Gates |
| `docker-compose.yml` que suba a app e o Redis efêmero para testes locais e no pipeline | Testes/Scans |
| Workflow de **PR** que só consome o `pipelines` (lint, build, scans, smoke e gate) | CI/Gates |
| Workflow da **main** que só consome o `pipelines` (build, scan, push, assinatura e deploy em dev, com promoção para prod) | CD/Push |
| Workflows consumidores **curtos**: quem adota a plataforma não precisa entender como ela funciona | CI/Gates + CD/Push |
| `CODEOWNERS` e templates de issue/PR | CI/Gates |
| `TEAM.md` com os membros de cada squad | Todos |
| `docs/postmortems/` com os postmortems do game day | Todos |
| Diagrama da arquitetura final e guia de adoção da plataforma | Todos |

## Regras do jogo

1. **Tudo por PR.** Fork → branch → PR → review de um colega **e** do CODEOWNER → merge.
2. **Conventional commits** no título dos PRs (`feat:`, `fix:`, `docs:`, `ci:`...).
3. **Todo PR fecha uma issue** (`Closes #N`).
4. **Nenhuma credencial estática.** Autenticação na AWS só via OIDC.
5. **Infra só é aplicada pelo Atlantis.** Ninguém roda `apply` da própria máquina.
6. **Contrato primeiro.** Antes de implementar algo que outro time consome, publique a interface (inputs, outputs, nomes).
7. Travou? Registre na issue e traga para a weekly ou para o Coreto.

## Cronograma

| Semana | Tema | Até |
|---|---|---|
| 1 | Onboarding & contratos | 04/10 |
| 2 | Fundação | 11/10 |
| 3 | CI completo & rede base | 18/10 |
| 4 | Imagem no ECR & borda | 25/10 |
| 5 | Deploy em dev | 01/11 |
| 6 | Produção | 08/11 |
| 7 | Operação & hardening | 15/11 |
| 8 | Incidentes & entrega | 22/11 |

## Definição de pronto

- Critérios de aceite da issue atendidos
- Pipeline verde
- README do repositório atualizado
- Pelo menos um consumidor usando a mudança (quando se aplica)

---

Aplicação original: [badtuxx/giropops-senhas](https://github.com/badtuxx/giropops-senhas).
