<h1>Laboratório — Sessão 6 (Desafio MiniCTF)</h1>
 
Desafio Prático Integrador — Mini-CTF Defensivo Linux
Cenário
O servidor Ubuntu da empresa fictícia "Linux Agency" apresenta indícios de atividade suspeita e configurações severamente inseguras. A sua missão é auditar, conter os danos, aplicar as correções e documentar toda a intervenção — como se fosse chamado a responder a um incidente real.
Ambiente Virtual
 
•	TryHackMe — Linux Agency (gratuito): https://tryhackme.com/room/linuxagency

Metodologia de Resposta (Roteiro de Ações Exigidas)

Fase 1 — Identificação e Triagem

Análise de Rede e Portas: identificar quais os portos e serviços ativos que estão expostos desnecessariamente.
ss -tuln 

Análise dos Principais Resultados do comando ss -tuln

Porta	Protocolo	Endereço Local	Serviço Provável	Nível de Risco	Descrição da Vulnerabilidade / Exposição
53	UDP/TCP	0.0.0.0 / [::]	DNS (Domain Name System)	Médio	Serviço DNS exposto publicamente; pode ser usado para amplificação de DDoS se aberto para redes externas.
22	TCP	0.0.0.0 / [::]	SSH (Secure Shell)	Baixo	Acesso remoto SSH exposto em todas as interfaces. Recomenda-se uso de chaves SSH e restrição por firewall/fail2ban.
139	TCP	0.0.0.0 / [::]	NetBIOS Session Service	Alto	Protocolo legado vulnerável a enumeração de usuários e ataques de relay (SMB/NetBIOS).
445	TCP	0.0.0.0 / [::]	Microsoft-DS (SMB)	Crítico	Serviço SMB exposto na rede. Historicamente associado a graves vulnerabilidades de execução de código remoto (EternalBlue, etc.).
111	UDP/TCP	127.0.0.1 / [::1] (Local)	RPCbind / rpcbind	Baixo	Vinculado apenas ao localhost, baixo risco de exposição externa direta.
137	UDP	Múltiplos (0.0.0.0 / 172.x / 10.x)	NetBIOS Name Service	Alto	Serviço de resolução de nomes NetBIOS exposto em várias interfaces de rede, permitindo vazamento de informações.
138	UDP	Múltiplos (0.0.0.0 / 172.x / 10.x)	NetBIOS Datagram Service	Alto	Tráfego NetBIOS Datagram aberto em interfaces de rede, permitindo spoofing e enumeração.
323	UDP	127.0.0.1 / [::1]	Chrony (NTP)	Baixo	Serviço de sincronização de hora interno (localhost).
8443	UDP/TCP	0.0.0.0 / [::]	HTTPS Alternativo / Proxy / WebApp	Médio	Serviço HTTPS alternativo aberto para todas as interfaces. Necessário auditar a aplicação rodando nesta porta.
5433	TCP	127.0.0.1	PostgreSQL (Alt)	Baixo	Banco de dados restrito ao localhost.
20640	TCP	127.0.0.1	Serviço Interno / Daemon	Baixo	Porta efêmera/interna vinculada exclusivamente ao localhost.
7777	TCP	0.0.0.0 / [::]	Aplicação Customizada / Gestão	Médio	Porta não padronizada aberta em todas as interfaces. Risco de exposição de painéis de administração ou backdoors.
7778	TCP	0.0.0.0 / [::]	Aplicação Customizada / Gestão	Médio	Porta adicional aberta em todas as interfaces, associada à porta 7777.

 Recomendações de Segurança

Categoria	Ação Recomendada	Impacto
Serviços SMB/NetBIOS (445, 139, 137, 138)	Desativar o protocolo NetBIOS e restringir o acesso ao SMB (porta 445) apenas para redes confiáveis via firewall (UFW/iptables), ou desativar o serviço se não for necessário.	Elimina vetores de ataques de ransomware,
 enumeração e execução remota.
Exposição de Portas Customizadas (7777, 7778)	Verificar quais aplicações estão rodando nestas portas. Se forem serviços internos, restringir o acesso para 127.0.0.1 ou utilizar VPN/Tunnel para acesso externo.	Reduz a superfície de ataque para aplicações
 desconhecidas ou não auditadas.
Serviço DNS (Porta 53)	Garantir que o servidor DNS (como BIND ou systemd-resolved) não responda a consultas recursivas públicas para evitar ataques de amplificação DDoS.	Protege contra uso indevido da infraestrutura
 em ataques de negação de serviço.
Hardening de SSH (Porta 22)	Desativar autenticação por senha, permitir apenas chaves SSH, alterar a porta padrão se viável, e configurar o Fail2ban contra ataques de força bruta.	Aumenta significativamente a segurança
contra invasões via força bruta.
Auditoria Geral de Firewall	Implementar uma política restritiva de firewall (ex: UFW default deny incoming), libertando  apenas os serviços essenciais para endereços IP autorizados.	Garante o princípio do menor privilégio na rede.

nmap -sV localhost
 

ANÁLISE DETALHADA DAS PORTAS E SERVIÇOS EXPOSTOS
- Porta 22/tcp (OpenSSH):
  Serviço: OpenSSH
   Risco Potencial: Exposição direta do serviço SSH na rede. Se o acesso estiver configurado com autenticação por senha padrão ou se a versão estiver desatualizada. 

-Porta 53/tcp (dnsmasq):
   Serviço: dnsmasq (DNS)
   Risco Potencial: O dnsmasq exposto na porta TCP pode indicar um serviço de resolução ou cache DNS mal configurado.

- Porta 111/tcp (rpcbind 2-4):
   Serviço: rpcbind (SUNRPC)
  Risco Potencial: O rpcbind gerencia serviços RPC (Remote Procedure Call). 


- Portas 139/tcp e 445/tcp (Samba / NetBIOS & SMB):
   Serviço: Samba (SMB)
   Risco Potencial: Altamente crítico se exposto diretamente. O protocolo SMB é alvo histórico de vulnerabilidades graves (como EternalBlue / CVE-2017-0144).

- Portas 7777/tcp e 7778/tcp (nginx):
   Serviço: nginx (Servidor Web)
   Risco Potencial: Múltiplas instâncias ou portas web não padrão expostas. Podem hospedar aplicações web vulneráveis.

- Porta 8443/tcp (dcv):
  Serviço: NICE DCV (Remote Desktop / Streaming de Área de Trabalho)
 Risco Potencial: Solução de acesso remoto gráfico. Caso utilize certificados autoassinados, senhas fracas ou possua vulnerabilidades na versão do DCV.

Auditoria de Contas: procurar por utilizadores com permissões excessivas, contas sem palavra-passe associada ou chaves públicas suspeitas em authorized_keys

sudo cat /etc/shadow | awk -F':' '$2=="" {print $1}'
Não retornou nenhum resultado, significa que não existem usuários no sistema com a senha vazia, o que é um bom  sinal de segurança.

cat ~/.ssh/authorized_keys
Lista de chaves públicas SSH (no formato ssh-rsa), encontradas dentro do arquivo  
cat ~/.ssh/authorized_keys















Fase 2 — Contenção

Ativar a firewall UFW, bloqueando todas as portas que não sejam estritamente necessárias para o negócio


O comando executado alterou a política padrão do UFW (Uncomplicated Firewall) para o tráfego de entrada (incoming) para negado (deny). 
Isso significa que, a partir de agora, qualquer conexão externa que tente entrar no seu servidor será bloqueada por padrão, devendo criar  regras específicas permitindo portas ou serviços essenciais.








Oservidor agora está com o tráfego de entrada bloqueado por padrão, mantendo abertas apenas as portas explicitamente permitidas o SSH na porta 22.

Fase 3 — Enriquecimento / Remediação
a)	Restringir Acesso por Firewall (UFW / iptables): Bloquear o acesso externo às portas 
   sensíveis (445, 139, 137, 111, 53), permitindo-as apenas via VPN ou rede interna confiável.

b)	Desativar Serviços Desnecessários: Desabilitar o suporte a NetBIOS/SMB caso a máquina 
   não pertença a um domínio Windows ou não compartilhe arquivos na rede pública.

c)	Endurecimento do SSH (Hardening): Desativar autenticação por senha no SSH, permitindo 
   apenas chaves públicas, e alterar a porta padrão ou utilizar ferramentas como Fail2ban.

d)	Auditoria de Aplicações: Verificar quais serviços estão associados às portas 7777, 
   7778 e 8443 para garantir que possuem controle de acesso restrito.















output de ufw status verbose








Resumo
	8443/tcp e 8443/udp: ALLOW IN Anywhere (IPv4 e IPv6) — Tráfego permitido para porta 8443 (Nice DCV ou HTTPS alternativo).
	443: ALLOW IN Anywhere (IPv4 e IPv6) — Tráfego web seguro padrão HTTPS.
	80/tcp: ALLOW IN Anywhere (IPv4 e IPv6) — Tráfego web padrão HTTP.
	22/tcp: ALLOW IN Anywhere (IPv4 e IPv6) — Acesso remoto via SSH.

output de ufw status verbose

•	Firewall Ativo: O UFW está ligado ("Status: active"), o que significa que o sistema não está otalmente exposto sem filtragem de pacotes.

•	 Controle Granular por Portas e Protocolos: Apenas serviços específicos e necessários (portas 22, 80, 443 e 8443) possuem tráfego de entrada permitido, bloqueando todo o resto por padrão.

•	 Suporte Dual-Stack (IPv4 e IPv6): Há proteção e regras explícitas tanto para o protocolo IPv4 (regras 1 a 5) quanto para o IPv6 (regras 6 a 10), garantindo segurança em ambas as redes.
