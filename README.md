# CRM - SDDR

CRM de agendas (Agendas, Kanban e Comissão) em um único `index.html`, com login por e-mail/senha (Firebase Authentication) e dados no Firestore.

```
docs/index.html          o app
docs/firebase-config.js  configuração do seu projeto Firebase
firestore.rules            regras de segurança do banco (domínio da empresa + e-mail confirmado)
firebase.json              Hosting + Firestore
```

## 1. Criar o projeto no Firebase

1. Em https://console.firebase.google.com crie um projeto.
2. **Authentication** > Começar > Método de login > ative **E-mail/senha**.
3. **Firestore Database** > Criar banco de dados > modo **produção**. Região sugerida: `southamerica-east1` (São Paulo). A região não muda depois.
4. **Configurações do projeto** > Seus apps > **Web (</>)** > registre o app e copie a configuração para `docs/firebase-config.js`.

## 2. Quem tem acesso

Entra quem tem e-mail **@veri.com.br** e **confirmou o e-mail**. No primeiro acesso a pessoa cria a senha, recebe um e-mail de confirmação, clica no link e volta ao CRM. Não precisa cadastrar ninguém.

A confirmação é obrigatória: sem ela, qualquer pessoa poderia criar uma conta digitando o e-mail de outra.

Para liberar alguém de **fora** da empresa (por exemplo, um consultor externo), crie no Firestore a coleção `equipe`, com um documento por pessoa:

- ID do documento: o e-mail **em minúsculas**
- Um campo qualquer, por exemplo `nome` (o Console exige pelo menos um)

Para tirar o acesso dessa pessoa, apague o documento. Para trocar o domínio da empresa, edite a linha `veri[.]com[.]br` em `firestore.rules` e publique de novo.

## 3. Publicar (regras + site)

```bash
npm install -g firebase-tools
firebase login
firebase use --add        # escolha o projeto
firebase deploy           # publica as regras e o site
```

O endereço será `https://SEU-PROJETO.web.app`. Esse domínio já vem autorizado no Authentication. Se usar outro (domínio próprio, GitHub Pages), adicione em Authentication > Configurações > Domínios autorizados.

## 4. Primeiro acesso

Cada pessoa abre o site, digita o e-mail da empresa e cria a senha. Em seguida o Firebase envia um e-mail de confirmação (às vezes cai no spam); depois de clicar no link, a pessoa volta ao CRM e toca em "Já confirmei". "Esqueci minha senha" envia o e-mail de redefinição.

## 5. Subir no GitHub

```bash
git init
git add .
git commit -m "CRM - SDDR"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/crm-sddr.git
git push -u origin main
```

Ou, com a GitHub CLI: `gh repo create crm-sddr --private --source=. --push`.

Prefira repositório **privado**. A `apiKey` do Firebase Web pode ficar no repositório (não é segredo), mas nunca suba chaves de conta de serviço (`*-adminsdk-*.json`); o `.gitignore` já bloqueia esses arquivos.

## Observações

- Os dados dos leads (nome, telefone, e-mail) são dados pessoais. Mantenha a equipe enxuta e remova quem sai.
- Sem configurar o `firebase-config.js`, o app abre em modo local e guarda os dados só no navegador.
- Qualquer pessoa da equipe lê e altera todas as agendas. Se precisar de perfis com permissões diferentes, as regras do Firestore precisam ser ampliadas.
