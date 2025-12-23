
# Senaicast — Painel de TV com Supabase

Sistema de **comunicação interna** para TVs com um **player** (Painel) e um **gerenciador** (Admin), hospedado em **GitHub Pages** e usando **Supabase** (Database, Realtime e Storage).

- `index.html` — **Painel TV (Player)**: exibe slides com título, mensagem, mídia (imagem/vídeo/iframe), letreiro de avisos e relógio.
- `admin.html` — **Gerenciador**: cria/edita slides, configura tema (cores/zoom), letreiro (texto/separador/velocidade), nome da escola e faz upload de mídia (Supabase Storage).

> Autor: **Rafael Pereira da Silva** — *COPY SENAI*  
> Local: São Paulo, SP

---

## Sumário
- [Arquitetura](#arquitetura)
- [Recursos](#recursos)
- [Tecnologias](#tecnologias)
- [Estrutura](#estrutura)
- [Supabase](#supabase)
  - [Tabela `app_config`](#tabela-app_config)
  - [Políticas RLS](#políticas-rls)
  - [Bucket `senaicast-media`](#bucket-senaicast-media)
- [Variáveis/Chaves](#variáveischaves)
- [Executar localmente](#executar-localmente)
- [Publicação no GitHub Pages](#publicação-no-github-pages)
- [Uso](#uso)
- [Boas práticas](#boas-práticas)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Contribuição](#contribuição)
- [Segurança](#segurança)
- [Licença](#licença)

---

## Arquitetura
Front-end **estático** (HTML/CSS/JS vanilla) hospedado em GitHub Pages. Comunicação com **Supabase** para:
- **Database**: tabela `app_config` guarda configuração (tema, letreiro, slides).
- **Realtime**: atualiza a TV assim que o Admin salva alterações.
- **Storage**: armazena imagens e vídeos; usa **public URL** na TV.

Fluxo:
1. Admin edita configurações e `slides` → salva no `app_config`.
2. Player (TV) lê `app_config` e se **inscreve no Realtime**.
3. Exibição sequencial por **duração**, filtrando por **início/término**.

---

## Recursos
- Tema/branding (cores primárias e acento) e **zoom** de texto.
- Letreiro (ticker): fundo/texto, separador (bolinha), tamanho, espaçamento, velocidade.
- Slides: **categoria+ícone**, título, mensagem, layout (padrão/tela cheia), mídia (imagem/vídeo/iframe), janela de exibição e duração.
- Upload no Admin → Supabase Storage `senaicast-media`.
- Relógio/Data `pt-BR`. Preview no Admin.

---

## Tecnologias
- HTML5 / CSS / **JavaScript (vanilla)**
- **TailwindCSS** (CDN)
- **Font Awesome** (CDN)
- **SweetAlert2** (Admin)
- **Supabase**: Database + Realtime + Storage

---

## Estrutura
```
/
├── index.html           # Painel TV (player)
├── admin.html           # Gerenciador (admin)
├── README.md            # este documento
├── SECURITY.md          # diretrizes de segurança
├── CONTRIBUTING.md      # guia de contribuição
└── LICENSE              # licença (MIT)
```

---

## Supabase

### Tabela `app_config`
Use uma tabela simples com um JSON de configuração:
```sql
create table if not exists public.app_config (
  id bigint primary key generated always as identity,
  data jsonb not null,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

create or replace function set_updated_at() returns trigger as $$
begin
  new.updated_at = now();
  return new;
end;
$$ language plpgsql;

create trigger trg_set_updated_at
before update on public.app_config
for each row execute function set_updated_at();
```

Exemplo de `data` esperado:
```json
{
  "primaryColor": "#E30613",
  "accentColor": "#0047B6",
  "zoom": 1.0,
  "separatorColor": "#ffffff",
  "separatorSize": 1.5,
  "separatorSpacing": 0.5,
  "tickerBg": "#0047B6",
  "tickerColor": "#ffffff",
  "tickerSpeed": 30,
  "tickerText": "Aviso 1 | Aviso 2",
  "schoolName": "SENAI",
  "slides": [
    {
      "id": 1700000000000,
      "category": "Aviso",
      "categoryIcon": "fas fa-info-circle",
      "title": "Título",
      "message": "Mensagem",
      "layout": "default",
      "mediaType": "image",
      "url": "https://...",
      "duration": 10,
      "startDate": "2025-12-20T09:00",
      "endDate":   "2025-12-24T18:00"
    }
  ]
}
```

### Políticas RLS
Ative **Row Level Security (RLS)** e crie políticas:
```sql
alter table public.app_config enable row level security;

-- Leitura pública (player)
create policy "read_public_app_config"
on public.app_config for select to public using (true);

-- Escrita restrita (admin autenticado)
create policy "write_admin_app_config"
on public.app_config for insert to authenticated with check (true);

create policy "update_admin_app_config"
on public.app_config for update to authenticated using (true) with check (true);
```
> Caso use registro único, fixe `id=1` e restrinja updates a esse ID:
```sql
create policy "update_only_id_1"
on public.app_config for update to authenticated using (id = 1) with check (id = 1);
```

### Bucket `senaicast-media`
Crie um **bucket público** para armazenar mídias. O Admin faz upload e obtém `publicUrl` para exibição na TV.

---

## Variáveis/Chaves

Nos arquivos:
```js
const SUPABASE_URL = "https://<YOUR-PROJECT>.supabase.co";
const SUPABASE_KEY = "sb_publishable_xxx"; // chave pública (publishable)
```
- **Nunca** exponha **service role** no front-end.
- Para escrita segura, use **Supabase Auth** no `admin.html`.

---

## Executar localmente
Abra `index.html` (player) e `admin.html` (gerenciador) em um navegador. Opcionalmente, use um servidor estático:
```bash
npx serve .
```

---

## Publicação no GitHub Pages
1. Suba os arquivos no repositório.
2. **Settings → Pages** → *Deploy from a branch* → `main` (raiz `/`).
3. URLs:
   - Player: `https://usuario.github.io/repositorio/index.html`
   - Admin:  `https://usuario.github.io/repositorio/admin.html`

---

## Uso
**Admin (`admin.html`)**
1. Aparência (cores/zoom).
2. Letreiro (texto com `|`, cor, tamanho, espaçamento, velocidade).
3. Escola (nome).
4. Slides: categoria/ícone, título, mensagem, layout, mídia (upload/URL), janela e duração. **CONFIRMAR** → **SALVAR NA TV**.

**Player (`index.html`)**
- Carrega tema, letreiro, escola e slides; exibe sequência com filtros de período e duração; vídeos somam +5s.

---

## Boas práticas
- Mensagens curtas e legíveis (≥ 24–32px, alto contraste).
- Tempo por slide: 10–15s.
- URLs confiáveis; evite HTML livre no ticker.

---

## Troubleshooting
- Player não atualiza: verifique **ID** do registro e políticas **RLS**.
- Upload falhou: bucket `senaicast-media` e permissões.
- YouTube: use `watch?v=` ou `youtu.be/` (o Admin converte para `embed`).

---

## Roadmap
- Autenticação (Supabase Auth) no Admin.
- Reordenação (drag & drop) e "Publicar agora".
- Sanitização reforçada do ticker.
- Releitura periódica do `app_config` no player.

---

## Contribuição
Veja **CONTRIBUTING.md**.

---

## Segurança
Veja **SECURITY.md**.

---

## Licença
Licença **MIT** — veja **LICENSE**.
