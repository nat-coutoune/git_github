# 🐍💖 Cola de Git & GitHub para Bioinformática

> Material de apoio da aula **Git & GitHub do zero ao primeiro Pull Request**.
> Para cada ação: o comando no **terminal** e o caminho no **VS Code**.
> Projeto de exemplo usado na aula: `rnaseq-levedura/` (scripts de controle de qualidade e contagem de reads).

---

## 0. Ideia geral em uma linha

```
pasta de trabalho  --git add-->  staging  --git commit-->  repositório local  --git push-->  GitHub
                                                                          <--git pull--
```

| Lugar | O que é |
|---|---|
| **Working directory** | Os arquivos que você está editando agora |
| **Staging area** | A "caixinha" com as mudanças escolhidas para o próximo commit |
| **Repositório local** | A pasta oculta `.git/` com todo o histórico |
| **Remoto (GitHub)** | Cópia do repositório na nuvem, para backup e colaboração |

---

## 1. Configuração inicial (uma vez por computador)

```bash
git --version                                   # confere se o Git está instalado
git config --global user.name  "Seu Nome"
git config --global user.email "voce@email.com" # use o mesmo e-mail da conta do GitHub
git config --global init.defaultBranch main     # branch padrão chamada "main"
git config --global core.editor "code --wait"   # VS Code como editor do Git
git config --list                               # mostra tudo o que foi configurado
```

**Autenticação no GitHub**
- **Mais fácil:** no VS Code, clique no ícone de conta (canto inferior esquerdo) → *Sign in with GitHub*. O VS Code guarda a credencial.
- **Token (PAT):** GitHub → *Settings → Developer settings → Personal access tokens*. Use o token no lugar da senha quando o terminal pedir.
- **SSH:**
  ```bash
  ssh-keygen -t ed25519 -C "voce@email.com"
  cat ~/.ssh/id_ed25519.pub     # copie e cole em GitHub → Settings → SSH and GPG keys
  ssh -T git@github.com         # teste
  ```

---

## 2. Criar um repositório local

```bash
mkdir rnaseq-levedura && cd rnaseq-levedura
mkdir -p data scripts results
git init                    # cria a pasta oculta .git/
git status                  # "On branch main, No commits yet"
```

**VS Code:** *File → Open Folder* → aba **Source Control** (`Ctrl+Shift+G`) → **Initialize Repository**.

---

## 3. O ciclo do dia a dia: editar → add → commit

```bash
echo "# Análise de RNA-seq de S. cerevisiae" > README.md
git status                  # README.md aparece como "untracked"
git add README.md           # coloca no staging
git add scripts/            # adiciona uma pasta inteira
git add .                   # adiciona tudo (cuidado!)
git commit -m "Adiciona README do projeto"
```

| Ação | Terminal | VS Code (aba Source Control) |
|---|---|---|
| Ver o que mudou | `git status` | Letras ao lado dos arquivos: **U** novo, **M** modificado, **D** apagado, **A** adicionado |
| Ver a diferença | `git diff` | Clique no arquivo → diff lado a lado |
| Preparar | `git add arquivo` | Botão **+** (Stage Changes) |
| Tirar do staging | `git restore --staged arquivo` | Botão **−** (Unstage) |
| Salvar versão | `git commit -m "msg"` | Escreva a mensagem → **✓ Commit** |
| Histórico | `git log --oneline --graph` | **Timeline** (Explorer) ou seção **Source Control Graph** |

---

## 4. `.gitignore`: o que **não** vai para o Git

Crie um arquivo chamado `.gitignore` na raiz do projeto:

```gitignore
# Dados brutos e grandes: guarde no SRA/ENA, Zenodo ou no servidor, não no Git
*.fastq
*.fastq.gz
*.fq.gz
*.bam
*.sam
*.vcf.gz
data/raw/

# Resultados que podem ser regenerados pelos scripts
results/

# Segredos e configurações pessoais
.env
*.key

# Lixo de sistema e de ferramentas
.DS_Store
__pycache__/
.ipynb_checkpoints/
.Rhistory
.RData
```

> O GitHub avisa em arquivos acima de **50 MB** e **bloqueia** acima de **100 MB**.

---

## 5. Conectar ao GitHub

### Enviar um repositório local para o GitHub
1. No GitHub: **New repository** → nome `rnaseq-levedura` → **sem** README (o seu já existe).
2. No terminal:
```bash
git remote add origin https://github.com/SEU-USUARIO/rnaseq-levedura.git
git remote -v               # confere o endereço
git push -u origin main     # primeiro envio (-u liga a branch local à remota)
git push                    # próximos envios
```
**VS Code:** botão **Publish Branch** (ele cria o repositório no GitHub para você).

### Trazer mudanças do GitHub
```bash
git pull                    # = git fetch + git merge
```
**VS Code:** botão **Sync Changes** (faz pull e push) ou o ícone 🔄 na barra inferior.

### Copiar um repositório existente
```bash
git clone https://github.com/USUARIO/REPOSITORIO.git
cd REPOSITORIO
```
**VS Code:** `Ctrl+Shift+P` → **Git: Clone** → cole a URL.

---

## 6. Branches

```bash
git branch                       # lista as branches (* = atual)
git switch -c filtro-qualidade   # cria e entra numa branch nova
# ... edita, add, commit ...
git switch main                  # volta para a main
git merge filtro-qualidade       # traz as mudanças para a main
git branch -d filtro-qualidade   # apaga a branch já integrada
git push -u origin filtro-qualidade  # envia a branch para o GitHub
```

**VS Code:** clique no nome da branch na **barra inferior** → *Create new branch…* ou escolha outra branch para trocar.

### Conflitos
Quando duas branches mudam a **mesma linha**, o Git marca o arquivo assim:

```python
<<<<<<< HEAD
QUALIDADE_MINIMA = 20
=======
QUALIDADE_MINIMA = 30
>>>>>>> filtro-qualidade
```

1. Escolha o que fica (e apague as marcas `<<<<<<<`, `=======`, `>>>>>>>`).
   - **VS Code:** botões *Accept Current* / *Accept Incoming* / *Accept Both*, ou **Resolve in Merge Editor**.
2. `git add arquivo` → `git commit`.

---

## 7. Colaboração no GitHub (Pull Request)

```
Fork  →  clone  →  branch  →  commit  →  push  →  Pull Request  →  revisão  →  merge
```

```bash
git clone https://github.com/SEU-USUARIO/repo-da-turma.git   # depois do Fork no site
cd repo-da-turma
git switch -c adiciona-meu-nome
# cria alunos/seu-nome.md
git add alunos/seu-nome.md
git commit -m "Adiciona apresentação de Fulana"
git push -u origin adiciona-meu-nome
# no GitHub: botão "Compare & pull request"
```

Manter o seu fork atualizado com o original:
```bash
git remote add upstream https://github.com/PROFESSORA/repo-da-turma.git
git pull upstream main
```

---

## 8. "Socorro, errei!" 🆘

| Situação | Comando |
|---|---|
| Quero descartar o que mudei num arquivo (não commitado) | `git restore arquivo` |
| Adicionei ao staging sem querer | `git restore --staged arquivo` |
| Errei a mensagem do último commit (não enviei ainda) | `git commit --amend -m "nova mensagem"` |
| Esqueci um arquivo no último commit (não enviei ainda) | `git add arquivo` → `git commit --amend --no-edit` |
| Quero desfazer um commit que **já enviei** | `git revert HASH` (cria um commit que desfaz o outro) |
| Quero ver como um arquivo era antes | `git log --oneline arquivo` → `git show HASH:arquivo` |
| Commitei um arquivo que devia estar no `.gitignore` | `git rm --cached arquivo` → commit |

> ⚠️ Evite `git reset --hard` e `git push --force` até ter mais prática: eles apagam histórico.
> ⚠️ Se você enviou uma senha/token para o GitHub, **troque a senha/revogue o token**. Apagar o commit não basta.

---

## 9. Sem instalar nada: github.dev e Codespaces

| | **github.dev** | **GitHub Codespaces** |
|---|---|---|
| Como abrir | Aperte **`.`** (ponto) na página de um repositório, ou troque `github.com` por `github.dev` na URL | Botão verde **Code → Codespaces → Create codespace on main** |
| O que é | VS Code no navegador, só para editar arquivos | Um computador Linux na nuvem com VS Code completo |
| Terminal | ❌ Não tem | ✅ Tem (roda `git`, `python`, `R`, `conda`…) |
| Custo | Grátis | Cota grátis mensal na conta pessoal; depois é pago |
| Bom para | Corrigir um README, editar um script, fazer commit pelo painel | Rodar o fluxo completo de Git e testar scripts |

> Lembre de **parar/apagar** o codespace quando terminar, para não gastar a cota.

---

## 10. Mensagens de commit que ajudam o seu "eu" do futuro

✅ `Filtra reads com qualidade Phred < 20 no script de QC`
✅ `Corrige caminho do genoma de referência (R64)`
✅ `Adiciona gráfico de cobertura por cromossomo`
❌ `update`  ❌ `mudanças`  ❌ `agora vai`  ❌ `asdfgh`

Regra prática: complete a frase *"Este commit…"* com um verbo no presente.

---

## 11. Lista de comandos :sparkles:

**1. Iniciar um repositório local na minha área de desenvolvimento:**

```bash
git init
```

:warning: **Atenção:** Se você vai clonar um repo, não precisa fazer este passo, pois usando o ```git clone```, já traz consigo a pasta ```.git```.

**2. Rotina básica do git:**

```bash
git status

git add *

git commit -m "meaningful message"
```

:bulb: É recomendável sempre começar com um ```git pull``` para já começar com as atualizações do projeto feitas por outras pessoas ;).

**3. Comandos para ver o histórico:**

```bash
git log

git log -n X

git log --abbrev-commit

git log --oneline
```

**4. Como renomear a branch master para main:**

```bash
git branch -m master main
```

:question: Por que mudar de *master* para *main*? Tem [aqui](https://www.droidunplugged.com/2025/03/from-master-to-main-meaning-behind-gits.html) uma leitura breve sobre o tema ;).

**5. Comando para criar uma branch:**

```bash
git branch <name>
```

**6. Criar a branch e se mover para a branch nova ao mesmo tempo (sempre que a branch não existir):**

```bash
git checkout -b <name>
```

**7. Mudar de branch quando a branch já existe:**

```bash
git checkout <name>
```

**8. Listar branches locais (existem só no seu pc):**

```bash
git branch
```

**9. Listar todos os branches:**

```bash
git branch -a
```

**10. Modelo de nome para branches novas:**

```bash
# Iniciais + nome da branch
JF_novobranch
WA_novobranch
NC_novobranch
```

**11. Atualizar o repositório local:**

```bash
git pull
```

**12. Como fazer o primeiro git push na branch main:**

```bash
git push origin main
```

**13. Como dar git push na nova branch:**

```bash
git push --set-upstream origin NC_teste
```

**14. Achou lindos os emojis?**

[Aqui](https://gist.github.com/rxaviers/7360908) tem uma lista completa de todos e como usar! [Aqui](https://www.youtube.com/watch?v=V7WpadAi3RI) está o vídeo do atalho que vimos hj juntas :).

---

## 12. Para continuar estudando
- Pro Git (livro grátis, em português): https://git-scm.com/book/pt-br/v2
- GitHub Skills (cursos interativos): https://skills.github.com
- Software Carpentry, *Version Control with Git*: https://swcarpentry.github.io/git-novice/
- Learn Git Branching (jogo visual): https://learngitbranching.js.org/?locale=pt_BR
- Oh Shit, Git!? (consertando erros): https://ohshitgit.com/pt_BR

 Feito com :yellow_heart: pelas PyLadies [**Dra. Natalia Coutouné**](nat.coutoune@gmail.com), [**MSc. Juliana Félix**](juliana.felix@unesp.br) e [**MSc. Wendy Abregu**](tabregu70@gmail.com). Nossos agradecimentos especiais à [**Dra. Bruna Pierick**](https://www.vibtrainingandconferences.be/trainer/bruna-piereck) que nos ajudou a criar e ordenar o conhecimento para este tópico num terinamento prévio :blue_heart:. 