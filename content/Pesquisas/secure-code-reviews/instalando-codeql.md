---
title: Instalando CodeQL
type: docs
weight: 1
---

Aí pra galera que nunca usou o  codeql, configurei e testei dessa forma na minha máquina.

Baixe a release do BUNDLE CodeQL em https://github.com/github/codeql-action/releases.
Descompacte a pasta; ela deve conter um binário chamado "codeql".
Adicione o binário ao seu PATH. No Linux, adicione a seguinte linha ao `~/.bashrc`:

```bash
export PATH=$PATH:$HOME/codeql/
```

Em seguida, execute:

```bash
source ~/.bashrc
```

Teste o binário com o seguinte comando:

```bash
codeql resolve qlpacks
```

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

Crie um banco de dados com o CodeQL para Python com o nome "appdb" executando o comando:

```bash
codeql database create appdb --language=python -s .
```

Em seguida, analise o código com o comando:

```bash
codeql database analyze appdb --format=sarif-latest --output=output.sarif ~/codeql/qlpacks/codeql/python-queries/1.4.2/codeql-suites/python-code-scanning.qls
```

No VS Code, acesse a pasta test_codeql e clique no arquivo output.sarif. A extensão SARIF Viewer deve exibir uma aba com as labels "Locations" e "Rules", que informam sobre as falhas encontradas.
