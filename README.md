<div align="center">

# <img src="assets/images/logo.png" alt="Logo do SaveUs" height="40" style="vertical-align:middle;" /> SaveUs

**Refúgios climáticos para pessoas idosas durante as ondas de calor**

*Encontre um lugar fresco no seu bairro, reserve uma vaga e mostre o comprovante na entrada, tudo em um só app*

![React Native](https://img.shields.io/badge/React_Native-0.88-61DAFB?style=flat-square&logo=react&logoColor=white)
![Expo](https://img.shields.io/badge/Expo-58-000020?style=flat-square&logo=expo&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-6.0-3178C6?style=flat-square&logo=typescript&logoColor=white)
![NativeWind](https://img.shields.io/badge/NativeWind-4.2-38BDF8?style=flat-square&logo=tailwindcss&logoColor=white)
![React Query](https://img.shields.io/badge/TanStack_Query-5-FF4154?style=flat-square&logo=reactquery&logoColor=white)
![Jest](https://img.shields.io/badge/Jest-30-C21325?style=flat-square&logo=jest&logoColor=white)

> Projeto acadêmico desenvolvido para fins educacionais.

</div>

---

## 👥 Integrantes

| Nome | RM |
|------|----|
| 👩‍💻 Anna Clara Russo Luca | RM561928 |
| 👨‍💻 Gabriel Duarte Maciel | RM565754 |
| 👨‍💻 Gustavo Tavares da Silva | RM562827 |
| 👨‍💻 Tiago Guedes da Costa | RM564731 |

> **Turma:** 2TDSPW · FIAP 2026

---

## 📑 Sumário

- [Objetivo do Projeto](#-objetivo-do-projeto)
- [Perfis de Acesso](#-perfis-de-acesso)
- [Páginas do Aplicativo](#-páginas-do-aplicativo)
- [Integração com a API](#-integração-com-a-api)
- [Tecnologias Utilizadas](#-tecnologias-utilizadas)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Como Executar](#-como-executar)
- [Testes](#-testes)
- [Publicação (Firebase App Distribution)](#-publicação-firebase-app-distribution)
- [Demonstração](#-demonstração)

---

## 🎯 Objetivo do Projeto

O **SaveUs** ajuda pessoas idosas a atravessar as ondas de calor com segurança. O app mostra os **refúgios climáticos** abertos no bairro (bibliotecas, CEUs, UBSs e centros comunitários), permite reservar uma vaga para o dia e gera um comprovante com QR code para apresentar na entrada. Do outro lado, o **gestor** cadastra os refúgios e acompanha a ocupação.

Foi desenvolvido para a **Global Solution 2026/2 da FIAP**, alinhado aos ODS 3 (Saúde e Bem-Estar), 11 (Cidades e Comunidades Sustentáveis) e 13 (Ação Contra a Mudança Global do Clima), e integrado a dois backends reais (.NET e Java). O foco é:

- 📌 Mostrar onde há vagas hoje, amanhã ou depois, perto de onde o idoso mora
- 📌 Reservar sem risco de duplicar a vaga, mesmo com toque duplo ou internet instável
- 📌 Orientar o idoso com um **Guia do Calor** personalizado por IA, com sinais de alerta e ligação rápida para o SAMU e para o contato de emergência
- 📌 Dar ao gestor uma visão da ocupação por dia, para planejar reforço de água e equipe
- 📌 Ser fácil de usar por quem tem pouca familiaridade com celular: letras grandes, alvos de toque de 48px, leitores de tela e informação que não depende só de cor

---

## 🔑 Perfis de Acesso

O app tem dois papéis (`role`), cada um com seu grupo de telas no Expo Router, protegido por `Stack.Protected`:

| Perfil | Grupo de rotas | Como se cadastra |
|--------|----------------|------------------|
| **Idoso** | `(idoso)` | Autocadastro pelo app |
| **Gestor** | `gestor` | Conta criada pela operação, não é autocadastrável |

O login usa e-mail e senha. O app entra nos dois backends com as mesmas credenciais e guarda um **JWT de cada um** no `expo-secure-store`. Cada cliente HTTP envia o token emitido pelo seu próprio backend. Quando a sessão expira (401), o usuário volta para o login.

---

## 📱 Páginas do Aplicativo

### 🔐 Autenticação e conexão

| Página | Descrição |
|--------|-----------|
| **Conectando ao SaveUs** | Aparece ao abrir o app enquanto os servidores do Render acordam (podem levar até um minuto). Se não conectar, permite tentar de novo ou continuar mesmo assim. |
| **Login** | Entrada com e-mail e senha, levando para a área certa conforme o `role` retornado pela API. |
| **Cadastro** | Criação da conta do idoso, com indicador de força da senha e as mesmas regras de validação da API. |

---

### 👵 Área do Idoso

| Página | Descrição |
|--------|-----------|
| **Início** | Refúgios abertos no bairro do idoso, com filtro de data (hoje, amanhã e depois) e troca de bairro. Cada card mostra as vagas livres com cor e texto (livre, poucas vagas, lotado). |
| **Detalhe do refúgio** | Endereço, horário, acessibilidade e painel de vagas do dia escolhido, com o botão de reservar. |
| **Comprovante** | Reserva confirmada com QR code para apresentar na entrada do refúgio. |
| **Minhas reservas** | Reservas agrupadas por dia, com cancelamento. |
| **Guia do Calor** | Guia do dia gerado por IA (resumo, dicas e a próxima reserva), com escolha de hoje, amanhã ou depois. Traz cuidados diários, sinais de alerta e botões para ligar para o SAMU (192) e para o contato de emergência. |
| **Perfil** | Dados pessoais, bairro e contato de emergência. |

---

### 🏢 Área do Gestor

| Página | Descrição |
|--------|-----------|
| **Refúgios** | Lista com filtros por situação (todos, recebendo reservas, pausados) e por bairro, e escolha do dia para ver as vagas que sobram em cada refúgio, com barra de ocupação. Pausar e reativar reservas direto no card. |
| **Cadastrar refúgio** | Formulário com preenchimento automático do endereço pelo CEP, tipo do local, capacidade, horário e acessibilidade. |
| **Editar refúgio** | Mesmo formulário, com exclusão do refúgio após confirmação. |

---

## 🔌 Integração com a API

O app consome **dois backends** que usam o mesmo banco Oracle. Cada um tem seu cliente `axios` em [`src/services/api/client.ts`](src/services/api/client.ts), com o token da sessão no cabeçalho e os erros convertidos para `ApiError` (ProblemDetails, erros por campo e `Retry-After` no 429).

| Variável no `.env` | Backend | Usado em |
|--------------------|---------|----------|
| `EXPO_PUBLIC_API_URL` | .NET: [refugio-net.onrender.com](https://refugio-net.onrender.com) | Login, cadastro, perfil, refúgios e reservas do idoso |
| `EXPO_PUBLIC_JAVA_API_URL` | Java `refugio-service`: [saveus-refugio.onrender.com](https://saveus-refugio.onrender.com) | Login do Java, telas do gestor, CEP e Guia do Calor |

O app não chama o `ia-service` do Java diretamente: o `refugio-service` fala com ele por OpenFeign.

**Rotas consumidas:**

| Backend | Rotas |
|---------|-------|
| .NET | `POST /auth/login`, `POST /auth/registrar`, `GET/PUT /api/v1/usuarios/me`, `GET /api/v1/refugios`, `GET /api/v1/refugios/{id}`, `POST /api/v1/reservas`, `GET /api/v1/reservas/minhas`, `GET/DELETE /api/v1/reservas/{id}`, `GET /health/live` |
| Java | `POST /auth/login`, `GET/POST /api/v1/refugios`, `GET/PUT/DELETE /api/v1/refugios/{id}`, `GET /api/v1/cep/{cep}`, `POST /api/v1/ia/guia-do-calor`, `GET /actuator/health` |

Cada funcionalidade em `src/features/` tem seu service (chamadas HTTP) e seus hooks com **TanStack Query** (cache, refetch, paginação infinita e estados de carregando e erro). As respostas passam por schemas **Zod** que espelham os DTOs da API, então uma mudança de contrato aparece como erro claro em vez de tela quebrada.

As telas em `src/app/` só montam a interface: nenhuma chama a API ou contém regra de negócio diretamente.

**Detalhes do contrato:**

- Datas em `yyyy-MM-dd` no fuso de São Paulo, independentemente do fuso do aparelho.
- A reserva envia um `Idempotency-Key`, então toque duplo ou nova tentativa após timeout não criam duas reservas.
- Listas paginadas no envelope `{ itens, pagina, tamanho, totalItens, totalPaginas }`.

> ℹ️ Os serviços estão no plano gratuito do Render, que desliga após um tempo parado. A primeira requisição pode levar até um minuto; é para isso que existe a tela "Conectando ao SaveUs".

---

## 🧰 Tecnologias Utilizadas

| Área | Tecnologia |
|------|------------|
| Base | Expo SDK 58, React Native 0.88, React 19, TypeScript 6 |
| Navegação | Expo Router, com rotas protegidas por papel (`Stack.Protected`) e rotas tipadas |
| Estilo | NativeWind 4 (Tailwind), tokens de design em [`src/theme/colors.ts`](src/theme/colors.ts), fonte Plus Jakarta Sans |
| Dados | Axios + TanStack Query 5 |
| Formulários | React Hook Form + Zod 4 |
| Sessão | JWT no `expo-secure-store` |
| Recursos do aparelho | `expo-haptics` (retorno tátil), `expo-crypto` (chave de idempotência), `react-native-qrcode-svg` (comprovante), `Linking` (ligações) |
| Testes | Jest 30 (`jest-expo`) + React Native Testing Library |
| Build | EAS Build |

---

## 📁 Estrutura do Projeto

```
src/
├── 📂 app/                       # Rotas (Expo Router): só montam a interface
│   ├── (auth)/                   # Login e cadastro
│   ├── (idoso)/                  # Área do idoso
│   │   ├── (tabs)/               #    Início, Minhas reservas e Perfil
│   │   ├── refugio/[id].tsx      #    Detalhe do refúgio e reserva
│   │   ├── reserva/[id].tsx      #    Comprovante com QR code
│   │   └── guia-calor.tsx        #    Guia do Calor
│   ├── gestor/                   # Lista, cadastro e edição de refúgios
│   └── _layout.tsx               # Conexão com os servidores e roteamento por papel
├── 📂 features/                  # Código por funcionalidade (service, hooks, components e testes)
│   ├── auth/  conexao/  gestor/  guia-calor/
│   └── perfil/  refugios/  reservas/
├── 📂 components/                # Componentes compartilhados
│   ├── ui/                       #    Button, Card, Input, Badge, Segmentos, ProgressBar etc.
│   └── form/                     #    Campos ligados ao React Hook Form
├── 📂 hooks/                     # Hooks genéricos (escolha de dia, contagem regressiva, atraso)
├── 📂 services/
│   ├── api/                      #    Clientes HTTP, sessão e tratamento de erros
│   ├── query-client.ts           #    Configuração do React Query
│   └── query-keys.ts             #    Chaves de cache centralizadas
├── 📂 constants/                 # Tipos de refúgio com rótulo e ícone
├── 📂 theme/                     # Cores, sombras e tema da navegação
├── 📂 types/                     # Schemas Zod espelhando os DTOs da API
├── 📂 utils/                     # Datas no fuso de São Paulo, máscaras, ligações e textos
└── 📂 test-utils/                # API falsa, fábricas de dados e providers para os testes
```

As regras de negócio ficam em funções puras e testáveis, como `disponibilidade.ts`, `pode-reservar.ts`, `agrupar-reservas.ts` e `chaves-de-idempotencia.ts`.

---

## 🚀 Como Executar

### Pré-requisitos

- [Node.js](https://nodejs.org/) 22.13+ (ou 24.3+)
- Emulador Android **ou** celular com o app **Expo Go**

### Instalação

```bash
# Clone o repositório
git clone https://github.com/annaclrl/saveUs-mobile.git
cd saveUs-mobile

# Instale as dependências
npm install

# Inicie o projeto (o -c limpa o cache do Metro)
npx expo start -c
```

Escaneie o QR code com o Expo Go.

> ℹ️ O `.env` vai versionado porque só guarda as URLs públicas das APIs, sem segredos. Para apontar para APIs locais, crie um `.env.local` (ignorado pelo Git) sobrescrevendo as variáveis:
>
> | Onde o app roda | .NET | Java |
> |-----------------|------|------|
> | Emulador Android | `http://10.0.2.2:5159` | `http://10.0.2.2:8080` |
> | Celular físico | `http://<IP do seu PC>:5159` | `http://<IP do seu PC>:8080` |

### Usuários de teste

Todos com a senha `Senha@123`:

| E-mail | Papel |
|--------|-------|
| `maria@email.com` | Idoso (bairro Penha) |
| `jose@email.com` | Idoso (sem bairro) |
| `gestor@refugio.com` | Gestor |

### Scripts

| Comando | O que faz |
|---------|-----------|
| `npm start` | Inicia o servidor do Expo |
| `npm test` | Roda os testes |
| `npm run lint` | Verifica o código com ESLint |
| `npm run typecheck` | Verifica os tipos com TypeScript |

---

## 🧪 Testes

```bash
npm test
```

São 166 testes em 18 suítes, cobrindo as regras de negócio, a camada de API, os schemas, os componentes e os fluxos de login, reserva, filtros do gestor e Guia do Calor. Os mais importantes:

- Reserva idempotente: toque duplo ou nova tentativa após timeout não criam duas reservas.
- Datas sempre no fuso de São Paulo, independentemente do fuso do aparelho.
- Sessão: restauração, expiração e logout automático em 401.
- Erros da API (400, 401, 409, 422, 429 e sem rede) chegando na tela com a mensagem certa.

---

## 📦 Publicação (Firebase App Distribution)

O build de distribuição é feito com o [EAS Build](https://docs.expo.dev/build/introduction/), configurado em [`eas.json`](eas.json). O perfil `preview` gera um APK de distribuição interna, e as URLs das APIs vêm do `.env` versionado.

```bash
# Login (uma vez por máquina)
npx eas-cli@latest login

# Vincula o projeto à conta EAS (só na primeira vez)
npx eas-cli@latest init

# Gera o APK
npx eas-cli@latest build -p android --profile preview
```

Quando o build terminar, baixe o `.apk` pelo link que o EAS CLI mostra (ou com `npx eas-cli@latest build:list`) e envie para o [Firebase App Distribution](https://firebase.google.com/docs/app-distribution), pelo console do Firebase ou com `firebase appdistribution:distribute`. Cadastre o e-mail institucional do professor como tester antes da avaliação.

---

## 🎥 Demonstração

Para facilitar a validação do projeto, disponibilizamos uma demonstração em vídeo do app.

<a href="#" target="_blank">Assistir demonstração no YouTube</a> *(https://youtu.be/pvjqQMaKsKU?is=VSEZlNdzgiZVKSNv)*
