
<p>Mapeamento e análise da superfície de exposição de um servidor alvo na rede local.<br/>
Nesta sessão assume o papel de auditor de sistemas: <br/>O objetivo é identificar a
interface de rede do próprio ambiente, listar os serviços em escuta e, de seguida,
mapear um alvo remoto com o Nmap.</p>




<h1 align ="center">1- Número de portas abertas identificadas no KillerCoda Ubuntu comando (ip a)</h1>

No código ip a  foram identificadas 3 interfaces de rede.

<img width="886" height="355" alt="image" src="https://github.com/user-attachments/assets/14ab39d4-46ab-4dd6-9c01-611b4ea00551" />

 
<h1 align ="center">2)	Serviços em execução em cada porta no KillerCoda Ubuntu comando  (ss -tuln) </h1>

 
<li>ort 53 (TCP/UDP): DNS (Serviço de nomes local do sistema).</li>
<li>Port 22 (TCP): SSH (Acesso remoto ao terminal).</li>
<li>Port 68 (UDP): DHCP (Configuração automática de IP do servidor).</li>
<li>Port 546 (UDP): DHCPv6 (Configuração automática de IP versão 6).</li>
<li>Port 35595 (TCP): Serviço interno (Restrito ao uso da própria máquina).</li>



<h1 align ="center">3)	Versões exatas detetadas pelo Nmap</h1>

<img width="963" height="1205" alt="image" src="https://github.com/user-attachments/assets/a337142e-98c7-4682-8cdb-3b80cdba3fc8" />



<h1 align ="center">4)	Output completo do comando ip a e ss -tuln do ambiente local (TryHackMe Further Nmap)</h1>

Comando ip a no TryHackMe Further Nmap 

<img width="974" height="490" alt="image" src="https://github.com/user-attachments/assets/267e39f8-0701-4dc3-a7d0-e2bd9d85d2fe" />

 

<h2 align ="center">Resultado</h2>


<img width="944" height="386" alt="image" src="https://github.com/user-attachments/assets/8228db20-0781-4436-8615-d6d62e96da50" />




<h1 align ="center">Output completo do comando  ss – tuln no TryHackMe Further Nmap</h1>

<img width="975" height="770" alt="image" src="https://github.com/user-attachments/assets/b9ec534c-149e-49ff-89f3-65e6c0682d53" />

<h2 align ="center">Resultado</h2>


<img width="716" height="312" alt="resultado " src="https://github.com/user-attachments/assets/1e42240d-be69-4744-adf1-fd0742c7e5f2" />

