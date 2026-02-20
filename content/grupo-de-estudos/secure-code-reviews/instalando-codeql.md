---
title: Instalando CodeQL
type: docs
weight: 1
---

Aí pra galera que nunca usou o CodeQL CLI, configurei e testei dessa forma na minha máquina.

Baixe a release do bundle do CodeQL CLI em https://github.com/github/codeql-action/releases.
Descompacte a pasta; ela deve conter um binário chamado "codeql".
Adicione o binário ao seu PATH. No Linux, adicione a seguinte linha ao `~/.bashrc`:

```bash
export PATH="$HOME/codeql:$PATH"
```

Em seguida, execute:

```bash
source ~/.bashrc
```

Teste o binário com o seguinte comando:

```bash
codeql --version
```

Para validar a resolução de pacotes, execute:

```bash
codeql resolve packs
```

> Atenção: é importante saber exatamente onde está instalado o seu CodeQL e onde ficam os seus query packs. Isso facilita quando for necessário adicionar novas queries feitas manualmente.

Instale a extensão SARIF Viewer no VS Code: https://marketplace.visualstudio.com/items?itemName=MS-SarifVSCode.sarif-viewer
Crie e acesse um diretório para os testes:

```bash
mkdir test_codeql && cd test_codeql
```

Salve o código abaixo como "app.py".

```python
import requests
from flask import Flask, request

app = Flask(__name__)

@app.route('/fetch', methods=['GET'])
def fetch():
  url = request.args.get('url')
  if not url:
    return "Missing URL parameter", 400

  try:
    response = requests.get(url)
    return response.text
  except requests.exceptions.RequestException as e:
    return str(e), 500

if __name__ == '__main__':
  app.run(debug=True)
```

Opcional (recomendado): baixe o pacote de queries oficial da linguagem.

```bash
codeql pack download codeql/python-queries
```

Crie um banco de dados com o CodeQL para Python com o nome "appdb" executando o comando:

```bash
codeql database create appdb --language=python --source-root=.
```

Em seguida, analise o código com o comando:

```bash
codeql database analyze appdb codeql/python-queries:codeql-suites/python-code-scanning.qls --format=sarif-latest --output=output.sarif
```

No VS Code, acesse a pasta test_codeql e clique no arquivo output.sarif. A extensão SARIF Viewer deve exibir uma aba com as labels "Locations" e "Rules", que informam sobre as falhas encontradas.
