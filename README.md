# 📺 Senaicast – Sistema de TV Corporativa do SENAI

O **Senaicast** é um sistema de **TV Corporativa** desenvolvido para exibição de avisos, notícias, imagens e vídeos em telas institucionais do **SENAI**, com gerenciamento remoto via navegador.

---

## 🚀 Visão Geral

- 📡 Atualização automática nas TVs
- ☁️ Backend em **Supabase** (dados e arquivos)
- 🌐 Frontend hospedado no **GitHub Pages**
- ⚙️ Painel administrativo simples e intuitivo
- 🖥️ Ideal para TVs Android, navegadores e mini PCs

---

## 🧱 Arquitetura do Sistema

| Componente | Função |
|-----------|--------|
| GitHub Pages | Hospedagem do site |
| Supabase | Banco de dados e armazenamento |
| index.html | Tela de exibição da TV |
| admin.html | Painel de administração |

---

## 🛠️ Instalação e Configuração (Técnico)

### 1️⃣ Configurar o Supabase

1. Crie uma conta em 👉 https://supabase.com
2. Crie um **novo projeto**
3. Acesse **SQL Editor > New Query**
4. Cole o **script SQL** de criação das tabelas
5. Clique em **RUN**

Isso criará:
- Tabela de configurações
- Estrutura de arquivos (Storage)

---

### 🔑 Obter Credenciais

1. Vá em **Project Settings > API**
2. Copie:
   - **Project URL**
   - **anon / public key**

⚠️ **Importante**  
Cole essas credenciais nos arquivos:
- `index.html`
- `admin.html`

Nas constantes indicadas como:

```js
// CONSTANTES DO SUPABASE
2️⃣ Hospedar no GitHub Pages

Crie um repositório (ex: senaicast)

Envie os arquivos:

index.html

admin.html

Vá em Settings > Pages

Em Build and deployment:

Branch: main

Clique em Save

⏱️ Após ~1 minuto, o GitHub fornecerá o link do sistema.

🔗 Links de Acesso

Substitua seu-usuario pelo seu usuário do GitHub:

📺 Tela da TV

https://seu-usuario.github.io/senaicast/


⚙️ Painel Administrativo

https://seu-usuario.github.io/senaicast/admin.html

🧑‍💼 Uso do Painel Administrativo
🎨 Configurações Visuais

Cor principal e de destaque

Nome da escola/unidade

Letreiro inferior (rodapé)

Separador

Notícias (use | para separar)

Velocidade do texto

Exemplo de letreiro:

Inscrições Abertas | Visite a Biblioteca | Use EPI

🖼️ Gerenciamento de Slides

➕ Criar novo slide

✏️ Editar slide existente

🗑️ Excluir slide

📤 Mídias Suportadas
Upload (Recomendado)

Imagens: JPG, PNG

Vídeos: MP4

Tamanho recomendado: até 15MB

Links Externos

YouTube (autoplay automático, mudo)

Google Drive (arquivo público)

⏱️ Agendamento

Início (opcional)

Término automático

Duração do slide (em segundos)

💾 Publicação na TV

⚠️ Passo obrigatório

Confirme o rascunho do slide

Clique em 💾 SALVAR NA TV

Aguarde a mensagem de sucesso

📡 As TVs serão atualizadas automaticamente.

🎨 Boas Práticas de Conteúdo
Tipo	Resolução	Observação
Imagem Full	1920×1080	Tela cheia
Imagem Quadrada	1080×1080	Texto lateral
Vídeo	1920×1080	Evite vídeos longos

⚠️ Se imagens do Google Drive não aparecerem, verifique se não estão como Restritas.

📌 Observações Finais

O sistema foi projetado para uso interno

Pode ser expandido com autenticação, logs e permissões

Funciona em qualquer TV com navegador moderno

📄 Licença

Uso interno institucional – SENAI
Distribuição e adaptação conforme política da unidade.
