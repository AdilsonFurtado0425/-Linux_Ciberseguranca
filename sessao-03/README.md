Laboratório — Sessão 3
Hardening de Redes Linux e Configuração de Firewalls

1)	Captura de ecrã (ou output de texto) das regras UFW ativas: sudo ufw status verbose
 
Mostra que o Uncomplicated Firewall  está atualmente inativo 
2)	Listagem completa: sudo iptables -L -v

3)	Breve explicação da política aplicada no output apresentada no ponto 2.
Chain INPUT (Tráfego Direto para o Host)
Política Padrão: ACCEPT (Aceita todo o tráfego que não seja explicitamente bloqueado).
Regras Aplicadas:
	Existe uma regra explícita de DROP para o endereço
	 IP 203.0.113.50, bloqueando qualquer pacote proveniente deste IP para o host.
 Chain FORWARD (Tráfego de Passagem / Roteamento)
Política Padrão: ACCEPT.
Regras Aplicadas:
O tráfego de encaminhamento é redirecionado para cadeias personalizadas do Docker (DOCKER-USER e DOCKER-FORWARD
 Chain OUTPUT (Tráfego Gerado pelo Host)
Política Padrão: ACCEPT.
Regras e Políticas do Docker
O output mostra uma estrutura de isolamento gerada pelo Docker para gerenciar os contentores:
Isolamento de Pontes (DOCKER / DOCKER-BRIDGE):
	Bloqueia tráfego indesejado que tente entrar na interface docker0 vindo de fora dela (!docker0 docker0).
Controle de Conexões (DOCKER-CT):
	ACCEPT para pacotes com estados RELATED e ESTABLISHED destinados à interface docker0, permitindo que respostas de conexões já iniciadas retornem aos containers.
Encaminhamento de containers (DOCKER-FORWARD):
	ACCEPT para todo o tráfego originado na interface docker0 (saída de containers para a rede externa).
