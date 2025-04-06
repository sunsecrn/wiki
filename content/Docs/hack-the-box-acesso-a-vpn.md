## Guia de Instalação de VPN para o HTB em ambiente Windows

### Passo 1: Baixar o OpenVPN

1. Acesse o site oficial do OpenVPN: [OpenVPN](https://openvpn.net/community-downloads/)
2. Baixe o instalador do OpenVPN para Windows.

### Passo 2: Instalar o OpenVPN

1. Execute o instalador baixado.
2. Siga as instruções na tela para completar a instalação.

### Passo 3: Obter o Arquivo de Configuração do Hack The Box

1. Acesse sua conta no Hack The Box.
2. Vá para a seção de VPN e gere um novo arquivo de configuração (`.ovpn`).

### Passo 4: Configurar o OpenVPN

1. Abra o OpenVPN GUI.
2. Clique em "Add a New VPN Connection".
3. Selecione o arquivo `.ovpn` que você baixou do Hack The Box.
4. Clique em "Save" para adicionar a conexão.

### Passo 5: Conectar-se à VPN

1. No OpenVPN GUI, selecione a conexão que você acabou de adicionar.
2. Clique em "Connect".
3. Insira suas credenciais do Hack The Box quando solicitado.

### Passo 6: Verificar a Conexão

1. Abra o Prompt de Comando (CMD) ou PowerShell.
2. Digite `ping hackthebox.com` para verificar se você está conectado corretamente.

Você já deve estar conectado a VPN do Hack The Box e pronto para começar suas atividades.

---

## Tutorial: Configurar OpenVPN no Linux para Hack The Box

### Passo 1: Instalar o OpenVPN

1. Abra o terminal.
2. Execute o comando para instalar o OpenVPN:
   `sudo apt-get update`
   `sudo apt-get install openvpn`

### Passo 2: Obter o Arquivo de Configuração do Hack The Box

1. Acesse sua conta no Hack The Box.
2. Vá para a seção de VPN e gere um novo arquivo de configuração (.ovpn).

### Passo 3: Configurar o OpenVPN

1. Crie uma pasta para armazenar o arquivo de configuração:
   `mkdir -p ~/openvpn/hackthebox`

2. Mova o arquivo .ovpn para a pasta criada:
   `mv ~/Downloads/nome_do_arquivo.ovpn ~/openvpn/hackthebox/`

### Passo 4: Conectar-se à VPN

1. Navegue até a pasta onde o arquivo .ovpn está localizado:
   `cd ~/openvpn/hackthebox`
2. Inicie a conexão VPN usando o OpenVPN:
   `sudo openvpn --config nome_do_arquivo.ovpn`

### Passo 5: Verifique a Conexão

1. Abra um novo terminal.
2. Digite `ping hackthebox.com` para verificar se você está conectado corretamente
