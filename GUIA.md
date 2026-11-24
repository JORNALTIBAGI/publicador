# Publicador JK Studio — Guia para colocar online

São 3 partes. Faça na ordem. Leva uns 30–40 minutos na primeira vez.

| Parte | Onde | Para quê |
|---|---|---|
| A | Supabase | Banco de dados, artes e o robô que publica |
| B | GitHub | O site do Publicador |
| C | Publicador | Primeiro acesso, importar seus dados, conectar Instagram e Claude |

> **Nunca** coloque o token da Meta, a chave do Claude ou a CRON_KEY no GitHub ou em prints.

---

## PARTE A — Supabase

### A1. Criar o projeto
1. Entre em **supabase.com** → **Start your project** → entre com a sua conta do **GitHub** (mais fácil).
2. Clique em **New project**:
   - **Name:** `publicador-jk`
   - **Database Password:** clique em **Generate a password** e guarde num lugar seguro.
   - **Region:** **South America (São Paulo)**.
3. Clique em **Create new project** e espere uns 2 minutos.

### A2. Criar o banco
1. Menu da esquerda → **SQL Editor** → **New query**.
2. Abra o arquivo `supabase/1-banco.sql`, copie **tudo** e cole.
3. Clique em **Run**. Deve aparecer **Success**.

### A3. Criar o seu login
1. Menu → **Authentication** → **Users** → **Add user** → **Create new user**.
2. Coloque o seu e-mail e uma senha forte. Marque **Auto Confirm User**. Clique em **Create user**.
3. Ainda em **Authentication**, procure a opção **Allow new users to sign up** (em *Sign In / Providers* ou *Settings*) e **desligue**. Assim ninguém mais consegue criar conta no seu Publicador.

### A4. Criar o robô (função `publicador`)
1. Menu → **Edge Functions** → **Deploy a new function** → **Via Editor**.
2. Nome da função: **`publicador`** (exatamente assim, minúsculo).
3. Apague o código de exemplo, abra `supabase/functions/publicador/index.ts`, copie **tudo** e cole.
4. Clique em **Deploy function** e espere a mensagem de sucesso.
5. Abra a função → aba de **configurações/detalhes** → **desligue "Verify JWT"** (pode aparecer como *Enforce JWT verification*). O robô faz a própria verificação de segurança.
   - Se não achar essa opção, me mande um print da tela da função.

### A5. Guardar as senhas do robô
1. Menu → **Edge Functions** → **Secrets** → adicione:
   - **CRON_KEY** = uma senha longa inventada por você (30+ letras e números misturados). **Anote**, vai usar no passo A6.
   - **ANTHROPIC_API_KEY** = a chave do Claude (pode deixar para a Parte C).
2. Clique em **Save**.

### A6. Ligar o agendamento (a cada 5 minutos)
1. Pegue o endereço do projeto: **Project Settings** → **Data API** (ou **General**) → **Project URL** (algo como `https://abcdefgh.supabase.co`).
2. Abra `supabase/3-robo-agendado.sql` e troque:
   - `<<<https://SEU-PROJETO.supabase.co>>>` → o seu Project URL (sem os `<<< >>>`)
   - `<<<COLE-AQUI-A-CRON_KEY>>>` → a mesma CRON_KEY do passo A5 (sem os `<<< >>>`)
3. **SQL Editor** → **New query** → cole → **Run**.
4. Para conferir: rode `select * from cron.job;` — deve aparecer `publicador-jk`.

### A7. Copiar a chave pública
**Project Settings** → **API Keys** → copie a **Publishable key** (começa com `sb_publishable_`).
Ela é pública por natureza: pode ir para o site sem problema.

---

## PARTE B — GitHub (o site)

1. Entre em **github.com** → botão **+** (canto de cima) → **New repository**.
   - **Repository name:** `publicador`
   - Marque **Public** (o GitHub Pages grátis exige público — o código não tem nenhuma senha, seus dados ficam no Supabase).
   - Clique em **Create repository**.
2. Na página do repositório, clique em **uploading an existing file**.
3. Arraste os arquivos **`index.html`** e **`config.js`** (pode arrastar a pasta `supabase` e este guia também, para ficar guardado). Clique em **Commit changes**.
4. **Settings** → **Pages** → em **Source** escolha **Deploy from a branch** → Branch **main** / **(root)** → **Save**.
5. Espere 1–2 minutos. O endereço aparece no topo da página Pages:
   `https://SEU-USUARIO.github.io/publicador/`

> Opcional: antes de subir, você pode abrir `config.js` e colar o Project URL e a Publishable key. Se deixar vazio, o Publicador pergunta na primeira vez que abrir (em cada aparelho).

---

## PARTE C — Primeiro acesso

1. **No Publicador do computador (versão antiga):** clique em **Fazer backup** e salve o arquivo.
2. Abra o endereço do GitHub Pages. Se pedir, cole o **Project URL** e a **Publishable key**.
3. Entre com o **e-mail e senha** do passo A3.
4. Clique em **Importar** (no quadro *Backup dos dados*) e escolha o backup do passo 1. Ele sobe clientes, posts, legendas e artes para o online.
5. **🚀 Publicar no Instagram** → cole o token longo da Meta → **Buscar contas** → ligue cada conta ao cliente → **Salvar**.
6. **✨ Legendas com IA:**
   - Em **console.anthropic.com**: crie a conta, coloque créditos em **Billing** e crie uma chave em **API Keys**.
   - No Supabase: **Edge Functions → Secrets** → `ANTHROPIC_API_KEY` = a chave → **Save**.
   - No Publicador: **Testar** → **Salvar e ligar**.

Pronto! Posts com status **Aprovado** saem sozinhos no horário, mesmo com o computador desligado.

---

## Bom saber

- **O robô confere a cada 5 minutos.** Um post das 18:00 sai entre 18:00 e 18:05.
- **Plano grátis do Supabase:** 500 MB de banco e 1 GB de arquivos. O robô apaga sozinho as artes de posts publicados há mais de 60 dias.
- **Pausa por inatividade:** o Supabase grátis pausa projetos parados por 1 semana. O robô rodando a cada 5 minutos conta como atividade. Se mesmo assim pausar, chega um e-mail do Supabase avisando — é só entrar e clicar em **Resume project**.
- **Token da Meta:** precisa ser gerado de novo a cada ~2–3 meses. Quando vencer, os posts mostram erro em vermelho.
- **Vídeo/Reels:** ainda é publicado manualmente.
- **Ver o que o robô fez:** Supabase → Edge Functions → `publicador` → **Logs**.
