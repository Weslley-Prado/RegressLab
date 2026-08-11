# Guia de Configuração de Ambiente Python com Pipenv e VS Code

Este documento descreve o processo completo para configurar um ambiente de desenvolvimento Python usando **Pipenv** como gerenciador de pacotes e **VS Code** como IDE.

---

## 📋 Pré-requisitos

- Python 3.8+ instalado
- VS Code instalado
- Git instalado (opcional, mas recomendado)

---

## 🚀 Passo a Passo

### 1. Instalar o Pipenv

```bash
pip install --user pipenv
```

Ou, se preferir usar o pipx (recomendado para isolar ferramentas CLI):

```bash
pipx install pipenv
```

Verifique a instalação:

```bash
pipenv --version
```

### 2. Criar/Navegar até o Diretório do Projeto

```bash
mkdir meu-projeto
cd meu-projeto
```

### 3. Inicializar o Pipenv

```bash
pipenv --python 3.12
```

Isso cria:
- Um ambiente virtual isolado
- Um arquivo `Pipfile` (dependências declarativas)
- Um arquivo `Pipfile.lock` (versões exatas para reproducibilidade)

### 4. Instalar Dependências

#### Instalar um pacote de produção:
```bash
pipenv install requests
pipenv install numpy==2.0.0  # versão específica
```

#### Instalar um pacote de desenvolvimento:
```bash
pipenv install --dev pytest
pipenv install --dev black pylint
```

#### Instalar todas as dependências do Pipfile:
```bash
pipenv install
```

### 5. Configurar o VS Code

#### 5.1. Instalar a extensão Python
- Abra o VS Code
- Vá para `Extensions` (Ctrl+Shift+X)
- Busque por "Python" (da Microsoft)
- Instale a extensão

#### 5.2. Selecionar o Interpretador do Pipenv

**Opção A - Automática:**
1. Abra a paleta de comandos: `Ctrl+Shift+P`
2. Digite: `Python: Select Interpreter`
3. Escolha a opção que contém `.venv` ou `pipenv`

**Opção B - Manual:**
1. Descubra o caminho do ambiente:
   ```bash
   pipenv --venv
   ```
2. No VS Code: `Ctrl+Shift+P` → `Python: Select Interpreter` → `Enter interpreter path...`
3. Cole o caminho retornado pelo comando acima

#### 5.3. Configuração Recomendada (.vscode/settings.json)

Crie o arquivo `.vscode/settings.json` no seu projeto:

```json
{
    "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
    "python.terminal.activateEnvironment": true,
    "python.linting.enabled": true,
    "python.linting.pylintEnabled": true,
    "python.formatting.provider": "black",
    "[python]": {
        "editor.defaultFormatter": "ms-python.black-formatter",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.organizeImports": "explicit"
        }
    }
}
```

> **Nota:** O caminho do interpretador pode variar:
> - Linux/Mac: `.venv/bin/python`
> - Windows: `.venv\\Scripts\\python.exe`

### 6. Trabalhando com o Ambiente

#### Ativar o ambiente virtual no terminal:
```bash
pipenv shell
```

#### Rodar um comando dentro do ambiente (sem ativar):
```bash
pipenv run python meu_script.py
pipenv run pytest tests/
```

#### Iniciar um Jupyter Notebook:
```bash
pipenv run jupyter notebook
```

### 7. Comandos Úteis do Pipenv

| Comando | Descrição |
|---------|-----------|
| `pipenv install <pacote>` | Instala e adiciona ao Pipfile |
| `pipenv install --dev <pacote>` | Instala como dependência de desenvolvimento |
| `pipenv uninstall <pacote>` | Remove do Pipfile e do ambiente |
| `pipenv graph` | Mostra árvore de dependências |
| `pipenv check` | Verifica vulnerabilidades de segurança |
| `pipenv lock` | Atualiza o Pipfile.lock |
| `pipenv scripts` | Lista scripts disponíveis |
| `pipenv --venv` | Mostra caminho do ambiente virtual |
| `pipenv --py` | Mostra caminho do Python |
| `pipenv clean` | Remove pacotes não declarados no Pipfile |

---

## 📁 Estrutura de Projeto Recomendada

```
meu-projeto/
├── .vscode/
│   └── settings.json       # Configurações do VS Code
├── src/
│   └── __init__.py
├── tests/
│   └── __init__.py
├── notebooks/              # Jupyter notebooks
├── docs/                   # Documentação
├── Pipfile                 # Dependências (atualizado manualmente)
├── Pipfile.lock            # Lock das versões (gerado automaticamente)
├── .gitignore              # Ignore .venv/ e __pycache__/
└── README.md
```

### .gitignore Recomendado

```gitignore
# Ambiente virtual
.venv/
venv/
env/

# Pipenv
Pipfile.lock  # Opcional: manter se quiser reproducibilidade exata

# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.Python
*.so

# IDE
.vscode/
.idea/
*.swp
*.swo

# Jupyter
.ipynb_checkpoints/

# Geral
.DS_Store
*.log
```

---

## 🔧 Troubleshooting

### Problema: Pipenv não atualiza o Pipfile

**Causa:** Usar `pip install` dentro do ambiente em vez de `pipenv install`

**Solução:**
```bash
# ❌ Errado
pipenv shell
pip install requests

# ✅ Correto
pipenv install requests
```

### Problema: VS Code não reconhece o ambiente

**Solução:**
1. Recarregue a janela: `Ctrl+Shift+P` → `Developer: Reload Window`
2. Recrie o ambiente:
   ```bash
   pipenv --rm
   pipenv --python 3.12
   ```

### Problema: Conflito de versões do Python

**Solução:** Especifique a versão exata:
```bash
pipenv --python /usr/bin/python3.12
```

### Problema: Permissão negada ao instalar pacotes

**Solução:**
```bash
# Usar --user (se instalando pipenv via pip)
pip install --user pipenv

# Ou usar pipx
pipx install pipenv
```

---

## 📌 Boas Práticas

1. **Sempre use `pipenv install`** para adicionar novas dependências
2. **Commit o `Pipfile.lock`** no git para garantir reproducibilidade
3. **Use dependências de desenvolvimento separadas** (`--dev`)
4. **Execute `pipenv check`** periodicamente para verificar vulnerabilidades
5. **Mantenha o Pipfile limpo** - remova dependências não usadas com `pipenv clean`

---

## 📚 Recursos Adicionais

- [Documentação Oficial do Pipenv](https://pipenv.pypa.io/)
- [Extensão Python do VS Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
- [Black Formatter](https://marketplace.visualstudio.com/items?itemName=ms-python.black-formatter)
- [Pylint](https://marketplace.visualstudio.com/items?itemName=ms-python.pylint)

---

**Última atualização:** 2026-08-11
