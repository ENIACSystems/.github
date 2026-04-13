# ENIAC Systems - Workspace Context

This file is automatically loaded by GitHub Copilot for all agents and chat sessions.
Read this BEFORE taking any action in this workspace.

---

## Padrão de Orquestração (Supervisor Pattern)

**O supervisor é o GitHub Copilot (modo Agent padrão), não um agente ENIAC.**

- O usuário fala diretamente com o GitHub Copilot no modo Agent
- O Copilot recebe a tarefa e **OBRIGATORIAMENTE roteia pelo ENIACCEO** antes de delegar
- O ENIACCEO decide qual(is) agente(s) especialista(s) executam
- Cada agente retorna output no formato SAÍDA ESTRUTURADA (seção em cada `.agent.md`)
- O Copilot revisa, corrige e sintetiza antes de apresentar ao usuário
- Se um agente errar ou "viajar", o Copilot corrige e reorienta

**Fluxo obrigatório:**
```
Usuário → Copilot (recebe)
  → ENIACCEO (analisa, decide quem executa)
    → ENIACTechLead (planejar, se necessário)
    → ENIACSeniorDev (implementar, se necessário)
    → ENIACQATester (testar, se necessário)
    → ENIACSecurity (auditar, se necessário)
  → Copilot (revisar, corrigir, sintetizar)
  → [Vault Update] Obsidian + Memory MCP (tarefas não-triviais)
  → Usuário (resposta final validada)
```

## Política ENIACCEO de Pull Request
- Toda PR que alterar fluxo de orquestração de agentes, regras de delegação ou documentação de processo deve ser rotulada com `ENIACCEO-approved`.
- Um workflow de validação (`.github/.github/workflows/validate-ceo-flow.yml`) verifica mudanças de diff em busca de palavras-chave de delegação.
- Se o workflow detectar alterações relacionadas a agentes/delegação e o label não estiver presente, o PR falhará até receber aprovação explícita do ENIACCEO.

**Responsabilidade do Copilot supervisor:**
- **SEMPRE invocar ENIACCEO** para qualquer tarefa técnica antes de delegar diretamente
- Verificar se o output do agente está em PT-BR
- Corrigir erros técnicos antes de apresentar
- Rejeitar outputs que violem OWASP, SLOC>24 ou complexity>10
- **Ao final de toda tarefa não-trivial**: atualizar vault Obsidian + Memory MCP
- Evoluir os agentes ao longo do tempo quando padrões de erro se repetirem

**Exceções (Copilot responde direto, sem CEO):**
- Perguntas conceituais / explicações
- Leitura de arquivos (read-only)
- Buscas no codebase
- Perguntas de status

**Vault de Conhecimento:**
- Caminho: `C:\Users\USUARIO\Documents\ENIAC-Knowledge\`
- Sessão do dia: `06-Erros-e-Aprendizados\Sessoes\YYYY-MM-DD.md`
- Bugs: `06-Erros-e-Aprendizados\Bugs-Conhecidos.md`
- Armadilhas: `06-Erros-e-Aprendizados\Armadilhas.md`

---

## Organization

- **GitHub Org**: ENIACSystems
- **Owner/Dev**: Joao Aschenbrenner (Joao-Aschenbrenner)
- **Hosting**: HostGator Plan M — PHP 8.3.30, Apache, NO Node.js runtime in production
- **Deploy pipeline**: GitHub Actions -> FTPS -> PHP untar at `/home2/eniacs42/public_html/`
- **FTP host**: `ftp://108.179.192.58` | user: `ENIACSystems@eniacsystems.com.br`
- **Web root**: `https://eniacsystems.com.br/`

## Stack (all Laravel projects)

- **Framework**: Laravel 10.50.2
- **PHP**: >= 8.1 (server runs 8.3.30)
- **Frontend**: Blade + Vite (compiled assets), NO React/Vue/Next.js in production
- **Landing pages**: Vanilla HTML + CSS + JS only (CDN: GSAP, Alpine.js, Tailwind CDN)
- **Auth**: Laravel Breeze / built-in Auth
- **DB**: MySQL (via HostGator)
- **Tests**: PHPUnit via `php artisan test`
- **CI**: GitHub Actions (`ci.yml` + `deploy.yml`) on all projects

---

## Project Map

### 1. AudespV-TJSON
- **Purpose**: B2B SaaS — generates AUDESP v1.10 JSON files for TCE-SP (municipalities & public bodies)
- **Local path**: `AudespV-TJSON/`
- **Server path**: `/home2/eniacs42/public_html/projects/audesp-tjson/`
- **Stack**: Laravel 10, PHP 8.1+, also has Flutter (`pubspec.yaml`)
- **Structure**: 8 controllers, 7 models, 7 services, 11 migrations
- **Services**: AudespJsonGenerator, AudespValidator, DocumentManagerService, ExcelParserService, MappingService, PreflightService, SubscriptionLimitService
- **Tests**: Feature: 10 | Unit: 10 | Total: ~22 files — **COVERAGE EXISTS**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2025-01-15)
- **Status**: Active — has .env configured

### 2. BlingStockSync
- **Purpose**: Bidirectional stock sync between multiple Bling accounts
- **Local path**: `BlingStockSync/`
- **Server path**: `/home2/eniacs42/public_html/projects/bling-sync/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 7 controllers, 4 models, 2 services, 8 migrations
- **Services**: BlingApiService, SyncService
- **Tests**: Feature: 8 | Unit: 7 | Total: ~17 files — **COVERAGE EXISTS**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2026-04-04)
- **Status**: Active — has .env configured
- **Gap**: Only 2 services for a sync app — likely needs more service layer coverage

### 3. ENIAC-SENTINEL
- **Purpose**: SaaS code audit platform — static analysis, security, OWASP, generates AI prompts. LLM-free.
- **Local path**: `ENIAC-SENTINEL/`
- **Server path**: `/home2/eniacs42/public_html/projects/eniac-sentinel/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 11 controllers, 9 models, 7 services, 14 migrations
- **app/ dirs**: Auditors, Console, Exceptions, Http, Models, Providers, Services
- **Services**: CodeScanner, GitWebhookService, PlanLimitService, ProjectContextService, PromptTemplateService, ReportGenerator, RuleEngine
- **Tests**: Feature: 13 | Unit: 28 | Total: ~45 files — **BEST COVERAGE IN WORKSPACE**
- **CI**: `ci.yml`, `deploy.yml`, `enforce-pr-requirement.yml`, `release.yml`
- **CHANGELOG**: v1.0.0 (2025-01-15)
- **Status**: Most mature project — reference for code quality standards
- **Note**: Has `Auditors/` — uses custom auditor pattern, not just standard MVC

### 4. InternalSystem
- **Purpose**: DEPRECATED — migrated to multi-repo. Now split into individual projects.
- **Local path**: `InternalSystem/`
- **Stack**: NOT Laravel (legacy PHP)
- **Tests**: Feature: 1 | Unit: 10 | Total: ~13 files — **LOW COVERAGE**
- **Status**: Read-only archive — do NOT add features here. Reference only.
- **Note**: No .env file present

### 5. internal-system-core
- **Purpose**: Core platform — Admin, Portal, User panels, public pages. Pure PHP MVC (not Laravel). Security score 98/100.
- **Local path**: `internal-system-core/`
- **Stack**: Pure PHP (no Laravel), existing MVC architecture
- **Tests**: Feature: 1 | Unit: 14 | Total: ~17 files — **LOW FEATURE COVERAGE**
- **Status**: Active but no .env — needs careful handling
- **Note**: Different stack — no Eloquent, no Artisan. Use raw PHP patterns.

### 6. MecanicSystem
- **Purpose**: Full ERP for auto repair shops
- **Local path**: `MecanicSystem/`
- **Server path**: `/home2/eniacs42/public_html/projects/mecanic-system/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 9 controllers, 8 models, 2 services, 12 migrations
- **Services**: DashboardService, ServiceOrderService
- **Tests**: Feature: 10 | Unit: 7 | Total: ~19 files — **COVERAGE EXISTS**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2026-04-04)
- **Status**: Active — has .env configured
- **Gap**: Only 2 services for a full ERP — service layer needs expansion

### 7. PhotoVaultCloud
- **Purpose**: Photo gallery management with watermark processing
- **Local path**: `PhotoVaultCloud/`
- **Server path**: `/home2/eniacs42/public_html/projects/photo-vault/`
- **Stack**: Laravel 10, PHP 8.1+ (README says Laravel 10/PHP 8.1)
- **Structure**: 10 controllers, 10 models, 1 service, 14 migrations
- **Services**: WatermarkService
- **Tests**: Feature: 4 | Unit: 3 | Total: ~9 files — **LOW COVERAGE — NEEDS ATTENTION**
- **CI**: `ci.yml`, `deploy.yml`
- **Status**: Active — has .env configured
- **Gap**: 1 service for 10 controllers — severely underserviced. Test coverage critical gap.

### 8. RABBIT
- **Purpose**: Multi-tenant file manager — secure uploads, public URLs via hash, error control
- **Local path**: `RABBIT/`
- **Server path**: `/home2/eniacs42/public_html/projects/rabbit/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 8 controllers, 8 models, 1 service, 13 migrations
- **Services**: FileService
- **Tests**: Feature: 9 | Unit: 7 | Total: ~18 files — **COVERAGE EXISTS**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2025-01-15)
- **Status**: Active — has .env configured
- **Gap**: 1 service for all file ops — FileService may be too large

### 9. SCHospitalManager
- **Purpose**: Full hospital ERP — largest project
- **Local path**: `SCHospitalManager/`
- **Server path**: `/home2/eniacs42/public_html/projects/sc-hospital/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 14 controllers, 20 models, 2 services, 26 migrations
- **Services**: DashboardService, StockService
- **Tests**: Feature: 13 | Unit: 9 | Total: ~24 files — **UNIT COVERAGE LOW for project size**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2026-04-04)
- **Status**: Active — has .env configured
- **Gap**: 20 models and only 2 services + 9 unit tests — major service layer gap

### 10. shared-lib
- **Purpose**: Private Composer package with shared libraries for all ENIAC projects
- **Local path**: `shared-lib/`
- **Stack**: Pure PHP (no Laravel)
- **Tests**: Unit: 2 | Total: ~2 files — **CRITICALLY LOW COVERAGE**
- **Status**: Active — no .env needed (library, not app)
- **Note**: Changes here affect ALL projects. Must test thoroughly before publishing.

### 11. SimpleFinance
- **Purpose**: Personal finance management
- **Local path**: `SimpleFinance/`
- **Server path**: `/home2/eniacs42/public_html/projects/simple-finance/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 16 controllers, 12 models, 1 service, 16 migrations
- **Services**: DashboardService
- **Tests**: Feature: 11 | Unit: 11 | Total: ~24 files — **COVERAGE EXISTS**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2026-04-04)
- **Status**: Active — has .env configured
- **Gap**: 16 controllers with 1 service — most logic probably in controllers

### 12. SnapSelect
- **Purpose**: Proofing system for photographers
- **Local path**: `SnapSelect/`
- **Server path**: `/home2/eniacs42/public_html/projects/snap-select/`
- **Stack**: Laravel 10, PHP 8.1+
- **Structure**: 8 controllers, 4 models, 2 services, 5 migrations
- **Services**: SelectionService, WatermarkService
- **Tests**: Feature: 9 | Unit: 3 | Total: ~14 files — **UNIT COVERAGE LOW**
- **CI**: `ci.yml`, `deploy.yml`
- **CHANGELOG**: v1.0.0 (2026-04-04)
- **Status**: Active — no .env.example (check before onboarding)
- **Gap**: Only 5 migrations for a multi-tenant app — likely not fully implemented

---

## Test Coverage Summary

| Project | Feature | Unit | Total | Status |
|---------|---------|------|-------|--------|
| ENIAC-SENTINEL | 13 | 28 | ~45 | BEST — reference standard |
| SCHospitalManager | 13 | 9 | ~24 | OK but unit low for 20 models |
| SimpleFinance | 11 | 11 | ~24 | OK |
| AudespV-TJSON | 10 | 10 | ~22 | OK |
| MecanicSystem | 10 | 7 | ~19 | OK |
| RABBIT | 9 | 7 | ~18 | OK |
| BlingStockSync | 8 | 7 | ~17 | OK |
| internal-system-core | 1 | 14 | ~17 | Feature gap |
| SnapSelect | 9 | 3 | ~14 | Unit gap |
| InternalSystem | 1 | 10 | ~13 | DEPRECATED |
| PhotoVaultCloud | 4 | 3 | ~9 | LOW — priority gap |
| shared-lib | 0 | 2 | ~2 | CRITICAL — affects all projects |

---

## Known Technical Debt

1. **PhotoVaultCloud**: 10 controllers, 1 service, 7 tests total — needs service layer + test coverage
2. **SCHospitalManager**: 20 models, 2 services, 9 unit tests — service layer severely missing
3. **SimpleFinance**: 16 controllers, 1 service — business logic likely in controllers
4. **MecanicSystem**: Full ERP with 2 services — same problem as SCHospitalManager
5. **shared-lib**: 2 unit tests — any change here risks all 9+ dependent projects
6. **SnapSelect**: No .env.example — setup documentation missing
7. **InternalSystem**: Legacy, pure PHP, archived — do not extend

---

## Coding Standards (all projects)

- **PSR-12** for PHP formatting
- **SLOC <= 24** per method (enforced by SENTINEL)
- **Cyclomatic complexity <= 10** per method
- **File <= 300 lines**
- **TDD**: tests written before or alongside code
- **No `$guarded = []`** — always use `$fillable`
- **No hardcoded secrets** — always use `.env` + `config()`
- **No `dd()` / `console.log`** left in code
- **Security**: OWASP Top 10 — see `security-rules.instructions.md`

## Running Tests

```bash
# From any Laravel project root:
php artisan test
php artisan test --filter=ClassName
php artisan test --coverage

# For pure PHP projects (internal-system-core, shared-lib):
./vendor/bin/phpunit
```

## Deploy Flow

```
git push origin main
  -> GitHub Actions ci.yml (tests must pass)
  -> GitHub Actions deploy.yml (if ci passes)
     -> FTPS upload to HostGator
     -> PHP script untars at /home2/eniacs42/public_html/
```

---

## Landing Pages

- **Local path**: `_landing_pages/`
- **Deployed**: `https://eniacsystems.com.br/` (homepage) and per-project landing pages
- **Stack**: Vanilla HTML + CSS + JS only — NO frameworks
- **CDN stack**: GSAP, Alpine.js, Tailwind CDN, Animate.css
- **Current homepage**: `_landing_pages/homepage_v3.html`
- **Deploy**: Python script (`deploy.py`) + `push.ps1`
