# 🗺 Roteirizador — Guia de Deploy (Sem Terminal)

Tudo feito pelo navegador, sem instalar nada no computador.

---

## 📋 Passo 0 — Descompactar o ZIP

1. Baixe o arquivo **roteirizador-deploy.zip**
2. Descompacte no seu computador (clique direito → Extrair)
3. Você terá esta pasta:

```
roteirizador-deploy/
├── public/
│   ├── index.html       ← O sistema completo
│   └── 404.html         ← Página auxiliar
├── firebase.json
├── vercel.json
├── package.json
├── .gitignore
└── README.md
```

---

## 🚀 OPÇÃO 1 — Vercel (⭐ Recomendado — Mais fácil)

Tempo estimado: **5 minutos**

### Passo 1: Criar conta

1. Acesse **[vercel.com](https://vercel.com)**
2. Clique **Sign Up**
3. Escolha **Continue with GitHub** (ou Email)
4. Autorize o acesso

### Passo 2: Subir o projeto no GitHub (pela web)

1. Acesse **[github.com/new](https://github.com/new)**
2. Faça login (ou crie conta)
3. Preencha:
   - **Repository name:** `roteirizador`
   - Marque **Public** (ou Private)
   - ✅ Marque **Add a README file**
4. Clique **Create repository**
5. No repositório criado, clique em **Add file → Upload files**
6. Arraste **TODOS os arquivos** da pasta descompactada:
   - Primeiro arraste a **pasta `public`** inteira
   - Depois arraste: `firebase.json`, `vercel.json`, `package.json`, `.gitignore`
7. Em "Commit changes", escreva: `Deploy inicial`
8. Clique **Commit changes**

> ⚠️ Verifique se a pasta `public/` com o `index.html` aparece no repositório.

### Passo 3: Importar na Vercel

1. Acesse **[vercel.com/new](https://vercel.com/new)**
2. Na seção "Import Git Repository", encontre **roteirizador**
3. Clique **Import**
4. Na tela de configuração:
   - **Framework Preset:** selecione `Other`
   - **Root Directory:** deixe `./ ` (padrão)
   - **Build Command:** deixe vazio (apague se tiver algo)
   - **Output Directory:** digite `public`
5. Clique **Deploy**
6. Aguarde ~30 segundos ✅

### Passo 4: Pronto!

Sua URL será algo como: `https://roteirizador-xxxx.vercel.app`

### Atualizar no futuro:

1. No GitHub, navegue até o arquivo (ex: `public/index.html`)
2. Clique no **lápis ✏️** → edite → **Commit changes**
3. A Vercel atualiza automaticamente em ~30 segundos

---

## 🚀 OPÇÃO 2 — GitHub Pages (Gratuito)

Tempo estimado: **5 minutos**

### Passo 1: Criar repositório e subir arquivos

Siga os **mesmos passos 1 e 2** da Opção 1 acima.

### Passo 2: Criar o workflow de deploy

1. No repositório, clique **Add file → Create new file**
2. No campo de nome, digite: `.github/workflows/deploy.yml` (cria as pastas automaticamente)
3. Cole este conteúdo:

```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches: [ main ]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: "pages"
  cancel-in-progress: false
jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: 'public'
      - id: deployment
        uses: actions/deploy-pages@v4
```

4. Clique **Commit changes**

### Passo 3: Ativar GitHub Pages

1. No repositório → **Settings** (aba superior)
2. Menu lateral → **Pages**
3. Em **Source** → selecione **GitHub Actions**
4. Aguarde ~2 minutos

### Passo 4: Pronto!

URL: `https://SEU-USUARIO.github.io/roteirizador/`

O link aparece em **Settings → Pages**.

---

## 🚀 OPÇÃO 3 — Firebase Hosting

Tempo estimado: **10 minutos**

### Passo 1: Criar projeto Firebase

1. Acesse **[console.firebase.google.com](https://console.firebase.google.com)**
2. Login com Google → **Criar projeto** → Nome: `roteirizador`
3. Desative Analytics → **Criar projeto** → **Continuar**

### Passo 2: Subir no GitHub

Siga os **mesmos passos da Opção 1** para criar repositório e subir arquivos.

### Passo 3: Editar .firebaserc no GitHub

1. No repositório, clique no arquivo `.firebaserc`
2. Clique no **lápis ✏️**
3. Substitua `SEU-PROJETO-ID-AQUI` pelo ID do projeto
   (Firebase Console → ⚙️ Configurações → ID do projeto)
4. **Commit changes**

### Passo 4: Criar Service Account

1. Firebase Console → ⚙️ **Configurações** → **Contas de serviço**
2. Clique **Gerar nova chave privada** → **Gerar chave**
3. Salve o arquivo JSON que baixou

### Passo 5: Adicionar Secret no GitHub

1. No repositório GitHub → **Settings** → **Secrets and variables** → **Actions**
2. Clique **New repository secret**
   - **Name:** `FIREBASE_SERVICE_ACCOUNT`
   - **Secret:** abra o JSON baixado num editor de texto, copie **tudo**, cole aqui
3. Clique **Add secret**

### Passo 6: Criar workflow de deploy

1. No repositório → **Add file → Create new file**
2. Nome: `.github/workflows/firebase-deploy.yml`
3. Cole (substituindo o ID do projeto):

```yaml
name: Deploy to Firebase
on:
  push:
    branches: [ main ]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: ${{ secrets.GITHUB_TOKEN }}
          firebaseServiceAccount: ${{ secrets.FIREBASE_SERVICE_ACCOUNT }}
          channelId: live
          projectId: SEU-PROJETO-ID-AQUI
```

4. **Commit changes** → deploy automático!

### Passo 7: Pronto!

URL: `https://SEU-PROJETO-ID.web.app`

---

## ⚙️ Configuração Pós-Deploy (OBRIGATÓRIO)

### Liberar domínio na Google Maps API Key

1. Acesse **[console.cloud.google.com/apis/credentials](https://console.cloud.google.com/apis/credentials)**
2. Clique na sua **API Key**
3. **Restrições de aplicativo** → **Referenciadores HTTP (sites)**
4. Clique **Adicionar** e insira:

| Plataforma | Adicionar |
|---|---|
| Vercel | `https://roteirizador-*.vercel.app/*` |
| GitHub Pages | `https://SEU-USUARIO.github.io/*` |
| Firebase | `https://SEU-PROJETO.web.app/*` |
| Local | `http://localhost:*/*` |

5. **Restrições de API** → marque **Maps JavaScript API** + **Geocoding API**
6. **Salvar**

### Primeiro acesso

1. Abra a URL do site
2. Login: **admin** / **admin**
3. Troque a senha em **Usuários → Editar admin**
4. Configure a API Key em **Configuração**

---

## 🏆 Qual escolher?

| | Vercel ⭐ | GitHub Pages | Firebase |
|---|---|---|---|
| Facilidade | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| Setup 100% web | ✅ | ✅ | ⚠️ JSON secret |
| Deploy auto | ✅ | ✅ | ✅ |
| HTTPS | ✅ | ✅ | ✅ |

**Recomendação: Vercel** — mais rápida, mais fácil, zero configuração extra.
