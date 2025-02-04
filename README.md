# raspery-pi-client-vpn-client-proxy-zabbix
Criar um cliente VPN Wireguard e Cliente Proxy Zabbix

Sim, você pode configurar um Raspberry Pi 4 para rodar uma VPN WireGuard, fazer o cliente se conectar ao servidor e ainda configurar um proxy Zabbix. O processo envolve algumas etapas para garantir que tudo funcione corretamente. Aqui está um guia básico para você começar:

### Passo 1: Instalar o WireGuard no Raspberry Pi

1. **Instalar o WireGuard**:

   Execute os seguintes comandos no Raspberry Pi para instalar o WireGuard:

   ```bash
   sudo apt update
   sudo apt install wireguard
   ```

2. **Configurar a chave do servidor WireGuard**:

   Gere as chaves privadas e públicas para o servidor:

   ```bash
   wg genkey | tee privatekey | wg pubkey > publickey
   ```

   As chaves geradas serão necessárias para configurar o servidor.

### Passo 2: Configurar o Servidor WireGuard

1. **Criação do arquivo de configuração do WireGuard (exemplo: `/etc/wireguard/wg0.conf`)**:

   O arquivo de configuração do servidor WireGuard pode ser assim:

   ```ini
   [Interface]
   PrivateKey = [ChavePrivadaDoServidor]
   Address = 10.0.0.1/24
   ListenPort = 51820

   [Peer]
   PublicKey = [ChavePublicaDoCliente]
   AllowedIPs = 10.0.0.2/32
   ```

   Substitua `[ChavePrivadaDoServidor]` pela chave privada do servidor e `[ChavePublicaDoCliente]` pela chave pública do cliente (que será gerada na máquina cliente).

2. **Habilitar e iniciar o WireGuard**:

   Agora, ative e inicie o WireGuard no Raspberry Pi:

   ```bash
   sudo wg-quick up wg0
   ```

   Para que o serviço seja iniciado automaticamente após reiniciar o sistema:

   ```bash
   sudo systemctl enable wg-quick@wg0
   ```

### Passo 3: Configurar o Cliente WireGuard

No dispositivo cliente (que pode ser outro computador ou mesmo um Raspberry Pi):

1. **Instalar o WireGuard** (caso ainda não tenha instalado):

   No cliente, instale o WireGuard usando o mesmo comando:

   ```bash
   sudo apt install wireguard
   ```

2. **Configurar o cliente WireGuard (arquivo de configuração `/etc/wireguard/wg0.conf`)**:

   O arquivo de configuração do cliente pode ser assim:

   ```ini
   [Interface]
   PrivateKey = [ChavePrivadaDoCliente]
   Address = 10.0.0.2/32

   [Peer]
   PublicKey = [ChavePublicaDoServidor]
   Endpoint = [IPDoServidor]:51820
   AllowedIPs = 0.0.0.0/0
   ```

   Substitua as chaves e o IP do servidor.

3. **Ativar a VPN no cliente**:

   Execute no cliente para ativar a VPN:

   ```bash
   sudo wg-quick up wg0
   ```

### Passo 4: Configuração do Proxy Zabbix

1. **Instalar o Zabbix Proxy no Raspberry Pi**:

   Primeiro, instale o Zabbix Proxy:

   ```bash
   sudo apt install zabbix-proxy-sqlite3
   ```

2. **Configurar o arquivo de configuração do Zabbix Proxy** (geralmente em `/etc/zabbix/zabbix_proxy.conf`):

   O arquivo pode ser configurado assim:

   ```ini
   Server=[IPdoServidorZabbix]
   Hostname=RaspberryPiProxy
   DBName=/var/lib/zabbix/zabbix_proxy.db
   LogFile=/var/log/zabbix/zabbix_proxy.log
   LogFileSize=0
   ```

   O IP do servidor Zabbix será o endereço do servidor Zabbix onde você deseja enviar as informações de monitoramento.

3. **Habilitar e iniciar o Zabbix Proxy**:

   Ative o serviço Zabbix Proxy:

   ```bash
   sudo systemctl enable zabbix-proxy
   sudo systemctl start zabbix-proxy
   ```

### Passo 5: Testar a Conexão

1. Certifique-se de que o cliente e o servidor estejam conectados à VPN.
2. Verifique se o Zabbix Proxy está recebendo as informações de monitoramento.
3. A VPN deve ser usada para que o tráfego do Zabbix passe de forma segura entre o cliente e o servidor Zabbix.

### Considerações Finais

- Verifique se o firewall do Raspberry Pi está configurado para permitir tráfego na porta do WireGuard (51820 por padrão).
- Certifique-se de que o servidor Zabbix está configurado para aceitar dados de proxies.

Isso cria uma rede segura para o tráfego da VPN e ainda permite que o Raspberry Pi use o Zabbix Proxy para coletar dados de monitoramento. Se precisar de mais ajuda em algum desses passos, me avise!
