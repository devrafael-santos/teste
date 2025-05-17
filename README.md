
# <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/angular/angular-original.svg" width=100 align="center" width="100%"/>  Arquitetura Angular

<br/>

Este documento descreve a estrutura de pastas e a arquitetura que será adotada no desenvolvimento do Front-End com Angular moderno (versão 19+), de acordo com as boas práticas e recomendações oficiais do Angular Team.

<br/>

## 🎯 Objetivo

Estabelecer uma base sólida e escalável para o desenvolvimento de aplicações Angular robustas, organizadas por domínio de negócio (DDD), com separação clara de responsabilidades, alta reusabilidade de componentes e foco em performance.

---
<br/>

## 📐 Estrutura de Pastas

<pre>

src/
└── app/
    ├── core/ → 🌐 Serviços globais (auth, api, guards, interceptors)
    ├── shared/ → 📦 Componentes e utilitários reutilizáveis
    ├── layout/ → 🧱 Componentes estruturais da UI (navbar, shell)
    ├── features/ → 🎯 Funcionalidades do sistema organizadas por domínio
    ├── app.routes.ts → 🛣️ Roteamento principal (100% standalone + lazy)
    └── app.component.ts → 🧠 Componente raiz do app
</pre>

---
<br/>

## 🔍 Explicação das Pastas

### 🌐 `core/` – Coração da aplicação

Contém toda a infraestrutura compartilhada e singleton: serviços de autenticação, interceptadores HTTP, guards, modelos e tokens de injeção. Nada aqui deve ser específico de uma feature.

**Exemplos:**

- `auth.service.ts`
- `logger.service.ts`
- `auth.interceptor.ts`
- `auth.guard.ts`
- `user.model.ts`

> Tudo em `core/` pode ser usado globalmente e é injetável via `inject()`.

---
<br/>

### 📦 `shared/` – Utilitários reutilizáveis
Agrupa todos os elementos visuais ou funcionais **reutilizáveis**, que não dependem do domínio de negócio.

**Exemplos:**

- `ButtonComponent`, `SpinnerComponent`
- `ModalComponent`, `FormErrorComponent`
- `*hasRole` Directive
- `capitalize.pipe.ts`
- `email.validator.ts`

> Regra prática: se pode ser usado em mais de uma feature, vai no `shared/`.

---
<br/>

### 🧱 `layout/` – Estrutura da interface

Responsável pelos componentes que estruturam a aplicação visualmente: cabeçalho, menus laterais, rodapé e shells.

**Exemplos:**

- `NavbarComponent`
- `SidebarComponent`
- `AppShellComponent`

> Ideal para estruturar o app em diferentes layouts conforme o tipo de usuário (`AdminShell`, `StudentShell`, etc).

---
<br/>

### 🎯 `features/` – Domínios funcionais do sistema

Cada pasta representa uma **feature isolada e lazy-loadable**, agrupando seus próprios componentes, serviços, rotas, models e estado.

**Exemplos:**  
<pre>
features/
└── offers/  
    ├── offer-list.component.ts     → 📄 Lista de ofertas exibidas ao usuário  
    ├── offer-create.component.ts   → ➕ Formulário para criação de novas ofertas  
    ├── offer.service.ts            → 🔧 Serviço com lógica de acesso à API de ofertas  
    ├── offer.routes.ts             → 🛣️ Arquivo de rotas standalone da feature  
    └── offer.model.ts              → 🧾 Interface/tipo da entidade "Offer"

</pre>

> Organize por domínio de negócio e não por tipo de arquivo (seguindo DDD).

---
<br/>

## 🧠 Boas práticas adotadas

| Prática | Descrição |
|--------|-----------|
| ✅ Standalone-only | Componentes e rotas standalone (`standalone: true`) |
| ✅ Signals | Gerenciamento de estado local/global com `signal`, `computed`, `effect` |
| ✅ Lazy Loading | Cada feature é carregada sob demanda via `loadChildren()` |
| ✅ Smart/Dumb Components | Separação clara de lógica e visual |
| ✅ Feature Encapsulation | Nenhuma feature acessa arquivos de outra |
| ✅ Guards e Diretivas Customizadas | Controle de acesso com `roleGuard`, `*hasRole` |
| ✅ App modularizado por domínio | Clean Architecture + DDD na prática |

---
<br/>

## 📌 Quando usar cada pasta?

| O que você precisa criar? | Pasta correta | Por quê? |
|---------------------------|---------------|----------|
| Serviço de autenticação | `core/services` | Global, singleton |
| Componente `Button` reutilizável | `shared/components` | Visual, usado em várias features |
| Página de listagem de cursos | `features/courses/components` | Parte da feature `courses` |
| Pipe de formatação de texto | `shared/pipes` | Puro, usado em qualquer lugar |
| Guard de role do usuário | `core/guards` | Infraestrutura global |
| Componente de navegação (navbar) | `layout/components` | Estrutura do app |

---
<br/>

## 📄 Exemplo de rota lazy standalone

```ts
// app.routes.ts
export const appRoutes: Routes = [
  {
    path: '',
    component: HomeComponent,
  },
  {
    path: 'offers',
    loadChildren: () => import('./features/offers/offer.routes').then(m => m.ROUTES),
  }
];

// features/offers/offer.routes.ts
export const ROUTES: Routes = [
  {
    path: '',
    component: OfferListComponent,
  },
  {
    path: 'create',
    component: OfferCreateComponent,
    canActivate: [roleGuard('coordinator')],
  },
];
