# Conciliador de Extrato Bancário

Lê o extrato bancário (foto, print, OFX ou CSV), aplica uma lista de regras e gera os lançamentos de débito e crédito para importar na Domínio.

## O que tem no projeto

| Arquivo | Para que serve |
|---|---|
| `index.html` | O site inteiro: regras, plano de contas, extrato e lançamentos |
| `api/ler-extrato.js` | Função que manda as imagens do extrato para o Claude e devolve as linhas |
| `vercel.json` | Dá até 60 segundos para a leitura da imagem terminar |

Sem a função configurada, o site continua funcionando com OFX, CSV e texto colado. Só a leitura de imagem depende dela.

## Passo a passo para publicar

### 1. Subir no GitHub

1. Entre em [github.com](https://github.com) e clique em **New repository**.
2. Dê um nome (por exemplo `conciliador-extrato`) e marque **Private**, se não quiser o código público.
3. Clique em **uploading an existing file** e arraste os arquivos `index.html`, `vercel.json`, `README.md` e a pasta `api` inteira.
4. Clique em **Commit changes**.

### 2. Criar a chave da API do Claude

1. Entre em [console.anthropic.com](https://console.anthropic.com) e crie uma conta (é separada da assinatura do Claude.ai).
2. Em **Billing**, coloque créditos. US$ 5 dá para muitas leituras: cada página de extrato custa poucos centavos.
3. Em **API Keys**, clique em **Create Key** e copie a chave (começa com `sk-ant-`). Ela só aparece uma vez.

Nunca coloque essa chave dentro do `index.html` nem em nenhum arquivo do GitHub. Ela vai só na Vercel, no passo seguinte.

### 3. Publicar na Vercel

1. Entre em [vercel.com](https://vercel.com) com a sua conta do GitHub.
2. Clique em **Add New → Project** e escolha o repositório `conciliador-extrato`.
3. Antes de clicar em Deploy, abra **Environment Variables** e crie:
   - `ANTHROPIC_API_KEY` com a chave do passo 2
   - `SENHA_LEITURA` com uma senha sua (sem ela, qualquer pessoa com o link pode gastar seus créditos)
4. Clique em **Deploy**. Em um minuto o site fica no ar num endereço como `conciliador-extrato.vercel.app`.

Toda vez que você mudar um arquivo no GitHub, a Vercel publica a nova versão sozinha.

### 4. Usar

1. Abra o site, vá na aba **Extrato** e digite a senha de leitura (ela fica salva no navegador).
2. Clique em **Escolher imagens** e selecione as páginas do extrato.
3. Confira a **Conferência de saldo** no topo. "Bate" quer dizer que os valores lidos fecham com o saldo do extrato.
4. Resolva as linhas pendentes (em amarelo) e clique em **Baixar arquivo para a Domínio**.

## Arquivo de importação

Layout: `Data;Debito;Credito;Valor;Historico`, separado por ponto e vírgula, uma linha por lançamento. Configure o leiaute de importação de lançamentos da Domínio nessa ordem de campos.

## Onde ficam os dados

As regras, o plano de contas e os extratos ficam salvos só no navegador de quem usa (localStorage). Nada vai para o GitHub nem para a Vercel, exceto as imagens no momento da leitura, que passam pela função e pela API do Claude e não são guardadas.

Para fazer backup ou levar as regras para outro computador, use **Salvar regras em arquivo** e **Abrir arquivo de regras**, na aba Lista de regras.

## Trocar o modelo

Por padrão a leitura usa `claude-sonnet-5-5`. Para trocar, crie a variável `MODELO` na Vercel com outro nome de modelo da [lista de modelos](https://docs.claude.com/en/docs/about-claude/models/overview) e faça um novo deploy.
