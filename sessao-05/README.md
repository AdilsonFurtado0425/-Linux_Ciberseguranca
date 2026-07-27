<h1 align ="center">Mini-relatório técnico.</h1>


Analisando o resultado  do comando <u> sudo lynis audit system </u> apresentado, é possível chegar a seguinte resultado.

<h1>1)	Hardening Score Inicial</h1>
<p>No entanto, pela quantidade de verificações aprovadas e pelo número de sugestões (50) e apenas 1 Warning, estima-se que o sistema tenha um Hardening Score inicial entre 60 e 70 pontos.</p>
Analisando pelo comando também apresentou o resultado dentro do intervalo.

 <img width="500" height="43" alt="hardening" src="https://github.com/user-attachments/assets/8a106a08-d531-4acb-b9ca-f341b6ca9027" />

Essa estimativa baseia-se em:
<li>a)	Apenas 1 Warning crítico;</li>
<li>b)	Diversas recomendações de melhoria;</li>
<li>c)	Muitos parâmetros do kernel ainda com valores diferentes dos recomendados;</li>
<li>d)	Firewall ativo;</li>
<li>e)	AppArmor ativo;</li>
<li>f)	SSH configurado, mas ainda com opções de endurecimento pendentes.</li>

<h1>2)	Os avisos encontrados (Warnings/Suggestions relevantes)</h1>

O relatório apresenta apenas 1 Warning:

<img width="609" height="153" alt="warning" src="https://github.com/user-attachments/assets/edecd45a-c330-403a-b00c-c1617d1ac454" />





Este é o aviso mais importante do relatório, pois pode permitir exploração por atacantes caso os pacotes não sejam atualizados.


<h2>Principais fragilidades</h2>

Boot
1)	GRUB sem senha
2)	UEFI desativado
3)	Muitos serviços classificados como UNSAFE pelo systemd-analyze security

Autenticação

Não existe política de complexidade de senha
Tamanho mínima da senha desativada
Tamanho  máxima da senha desativada
Contas sem data de expiração
Umask padrão pouco restritiva

Sistema de Arquivos
Não existem partições separadas para:
	/home
	/tmp
 /var
 
Além disso:

swap não está criptografada
disco não está criptografado
Kernel Hardening
Diversos parâmetros estão diferentes do recomendado, entre eles:
kernel.modules_disabled
kernel.kptr_restrict
kernel.sysrq
fs.suid_dumpable
dev.tty.ldisc_autoload
kernel.perf_event_paranoid
net.ipv4.conf.all.forwarding
net.ipv4.conf.all.send_redirects
net.ipv6.conf.all.accept_redirects
Esses parâmetros representam oportunidades de melhoria do sistema.

SSH
Ainda podem ser fortalecidos:
PermitRootLogin
MaxAuthTries
MaxSessions
TCPKeepAlive
AllowAgentForwarding
AllowUsers
AllowGroups
Porta padrão (22)


<h2>Sugestões prioritárias</h2>

Recomenda-se implementar as melhorias na seguinte ordem:


<li>Atualizar pacotes vulneráveis</li>
<li>Instalar Fail2Ban</li>
<li>Configurar senha no GRUB</li>
<li>Endurecer configuração do SSH</li>
<li>Aplicar parâmetros de hardening via sysctl</li>
<li>Configurar política de senhas (PAM)</li>
<li>Definir umask 027</li>
<li>Instalar auditd</li>
<li>Criar partições separadas para /tmp, /home e /var</li>
<li>Criptografar disco e swap</li>



<h1>3)	As medidas corretivas propostas para as duas vulnerabilidades selecionadas</h1>


Correção recomendada para BOOT-5122

O controlo BOOT-5122 do Lynis indica que o GRUB2 não está protegido por palavra-passe.
 Sem essa proteção, um utilizador com acesso físico ao computador pode editar os parâmetros de arranque, iniciar o sistema em modo de utilizador único (single-user mode) ou alterar opções do kernel para obter privilégios elevados.
  <h1> Correção recomenda</h1> 
                  
A recomendação é configurar uma palavra-passe para o GRUB2, impedindo alterações não autorizadas ao menu de arranque.

Correção recomendada para BOOT-5264

O Lynis detetou serviços do sistema classificados como UNSAFE, indicando que não utilizam todas as funcionalidades de isolamento e proteção disponibilizadas pelo systemd.
                                                <h2>  Correção recomendada:</h2>
                           
Analisar cada serviço com systemd-analyze security e aplicar diretivas de hardening, como NoNewPrivileges=yes, PrivateTmp=yes, ProtectSystem=strict, ProtectHome=yes e PrivateDevices=yes, de acordo com a compatibilidade de cada serviço.

<h1 align ="center">Excerto do relatório Lynis  /var/log/lynis-report.dat </h1>

<img width="1032" height="800" alt="image" src="https://github.com/user-attachments/assets/5a2c5305-397c-4c36-8fb4-8d6c5d50a11d" />

