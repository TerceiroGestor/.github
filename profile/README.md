# Terceiro Gestor

**Plataforma SaaS multi-tenant para gestão do terceiro setor**, construída com NestJS (API REST) e Next.js (App Router). O objetivo é automatizar processos administrativos, indicadores de impacto e prestação de contas de organizações sociais, centralizando dados públicos para apoiar análises e tomada de decisão.

[![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)](#)
[![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)](#)
[![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=next.js&logoColor=white)](#)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)](#)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat&logo=postgresql&logoColor=white)](#)
[![Prisma](https://img.shields.io/badge/Prisma-2D3748?style=flat&logo=prisma&logoColor=white)](#)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](#)

---

## Arquitetura

```mermaid
flowchart LR
    subgraph Client
        A[Next.js App Router]
    end
    subgraph Server
        B[NestJS API - Fastify Adapter]
        C[(PostgreSQL)]
        B -->|Prisma ORM| C
    end
    A -->|Server Actions / REST| B
    B -->|JWT + httpOnly Cookies| A
```

- **Frontend:** Next.js (App Router), TypeScript, shadcn/ui — arquitetura *Server Component-first*, com Client Components tratados como "ilhas" isoladas apenas onde há interatividade.
- **Backend:** NestJS sobre adapter Fastify, expondo API REST, com autenticação via Server Actions e cookies httpOnly (sem exposição de token no client).
- **Banco de dados:** PostgreSQL modelado com Prisma, usando `uuid(7)` como estratégia de ID — ordenável por tempo de criação, importante no contexto multi-tenant para performance de índices.
- **Deploy:** Render (Next.js como static site, NestJS como web service, PostgreSQL gerenciado). Supabase avaliado como alternativa de banco, descartado como principal por causa do auto-pause em planos gratuitos.

## Decisões técnicas

- **Multi-tenant desde a modelagem de dados**, não como camada adicionada depois — decisão tomada para suportar múltiplas organizações sociais isoladas na mesma instância.
- **Server Actions em vez de lógica de autenticação client-side** — elimina race conditions de loading e reduz a superfície de ataque no frontend.
- **`AppLayout` como Server Component assíncrono**, resolvendo a sessão via `getCurrentUser()` no servidor e repassando os dados já resolvidos para o `AuthProvider` — o client nunca decide sozinho se o usuário está autenticado.
- **`uuid(7)` no Prisma** em vez de auto-increment ou `uuid(4)`, equilibrando unicidade global (necessária no multi-tenant) com ordenação temporal (necessária para performance de queries e paginação).
- **Fastify como adapter do NestJS** pela performance em relação ao Express, relevante dado o volume de requisições esperado de múltiplos tenants simultâneos.

## Stack

| Camada | Tecnologias |
|---|---|
| Frontend | Next.js (App Router), TypeScript, shadcn/ui |
| Backend | NestJS (Fastify), TypeScript, JWT |
| Banco de dados | PostgreSQL, Prisma ORM |
| Infraestrutura | Docker, Render |
| Versionamento | Git, Conventional Commits |

## Como rodar localmente

```bash
# Clonar o repositório
git clone https://github.com/TerceiroGestor/<nome-do-repo>.git
cd <nome-do-repo>

# Instalar dependências
npm install

# Configurar variáveis de ambiente
cp .env.example .env

# Rodar migrations do Prisma
npx prisma migrate dev

# Subir em modo desenvolvimento
npm run start:dev
```

> Ajuste os comandos acima conforme a estrutura final do(s) repositório(s) — API e Web, se estiverem separados.

## Roadmap

- [x] Fase 0 — Planejamento de arquitetura (backend + frontend + execução no GitHub)
- [x] Fase 1 — Modelagem multi-tenant e autenticação
- [ ] Fase 2 — Módulos de gestão administrativa
- [ ] Fase 3 — Indicadores de impacto e prestação de contas
- [ ] Fase 4 — Centralização de dados públicos e análises sociais/territoriais
- [ ] Fase 5 — Deploy de produção e observabilidade

## Sobre o projeto

Organizações da sociedade civil (OSCs) frequentemente não têm ferramentas acessíveis para acompanhar sua gestão do início ao fim — da abertura à prestação de contas exigida pelo Marco Regulatório do Terceiro Setor (MROSC, Lei 13.019/2014). O Terceiro Gestor nasce para preencher essa lacuna, oferecendo recursos gratuitos e centralizando dados sociais públicos que ajudam OSCs, Estado e sociedade civil a tomar decisões melhor embasadas.

## Autor

<div align="center">
  <img src="https://github.com/wevertoncamposdev.png" width="70" style="border-radius:50%;" />
  <br/>
  <b>Weverton Campos</b> — Desenvolvedor Backend (Node.js • NestJS • TypeScript)
  <br/>
  <a href="https://github.com/wevertoncamposdev">GitHub</a> · <a href="https://www.linkedin.com/in/wevertoncamposdev">LinkedIn</a>
</div>
