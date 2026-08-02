<h1>Laboratório — Sessão 6 (Desafio MiniCTF)</h1>
 
Desafio Prático Integrador — Mini-CTF Defensivo Linux

Cenário

<p>O servidor Ubuntu da empresa fictícia "Linux Agency" apresenta indícios de atividade suspeita e configurações severamente inseguras. A sua missão é auditar, conter os danos, aplicar as correções e documentar toda a intervenção — como se fosse chamado a responder a um incidente real.</p>

Ambiente Virtual
 
•	TryHackMe — Linux Agency (gratuito): https://tryhackme.com/room/linuxagency

Metodologia de Resposta (Roteiro de Ações Exigidas)

<h1>Fase 1 — Identificação e Triagem</h1>

Análise de Rede e Portas: identificar quais os portos e serviços ativos que estão expostos desnecessariamente.
ss -tuln 
<img width="992" height="726" alt="image" src="https://github.com/user-attachments/assets/ba92e31d-8657-4bdd-b598-86ecaadb5594" />

Análise dos Principais Resultados do comando ss -tuln.

<img  width="613" height="484" alt="resuldado ss tuln" src="https://github.com/user-attachments/assets/d9e87285-01d5-4627-a74e-a301795ac321" />


 Recomendações de Segurança
 
 <img width="574" height="288" alt="recomendaçao segurança" src="https://github.com/user-attachments/assets/bde5c345-9118-4306-a2c2-69a1614a4f1f" />


nmap -sV localhost

 <img width="1031" height="361" alt="image" src="https://github.com/user-attachments/assets/c4caf86d-93ce-4097-9585-5bc026b08f8c" />


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

<h2>Auditoria de Contas: procurar por utilizadores com permissões excessivas, contas sem palavra-passe associada ou chaves públicas suspeitas em authorized_keys </h2>

sudo cat /etc/shadow | awk -F':' '$2=="" {print $1}'

Não retornou nenhum resultado, significa que não existem usuários no sistema com a senha vazia, o que é um bom  sinal de segurança.

cat ~/.ssh/authorized_keys

Lista de chaves públicas SSH (no formato ssh-rsa), encontradas dentro do arquivo  
cat ~/.ssh/authorized_keys

<img width="992" height="425" alt="image" src="https://github.com/user-attachments/assets/e41849ed-36df-4293-8fa7-0e6a693e216e" />


Fase 2 — Contenção

Ativar a firewall UFW, bloqueando todas as portas que não sejam estritamente necessárias para o negócio

<img width="938" height="108" alt="image" src="https://github.com/user-attachments/assets/70c70e18-866d-415d-81f6-a1041a343f18" />


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
