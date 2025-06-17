# Active Directory - Laboratório Doméstico
Este laboratório consiste em simular um ambiente corporativo utilizando o Oracle VirtualBox, criaremos um servidor controlador de domínio(Windows Server 2019) e uma máquina cliente(Windows 10). Para haver essa comunicação entre servidor e máquina cliente, o servidor utilizará dois controladores de interface de rede(NIC): a rede Internet será configurada no VirtualBox pegando a internet doméstica(Home), já a rede Interna fornecerá conexão para a máquina cliente. Vamos configurar o servidor de domínio, bem como a conexão de rede. Adicionaremos o Remote Access Server(RAS) e o DHCP.
A interação entre essas duas máquinas virtuais terá como principal finalidade utilizar o Active Directory, aliado a isso, utilizaremos um script PowerShell para adicionar mais de 1.000 usuários, assim, esse laboratório nos proporcionará a prática das políticas de segurança, podendo restringir ou permitir o acesso à rede para determinados usuários de uma corporação, por exemplo.

# Linguagem e Ferramentas Utilizadas
- Oracle VirtualBox
- PowerShell
- CMD

# Links
- VM Oracle VirtualBox https://www.virtualbox.org/wiki/Downloads 
- Windows Server 2019 ISO https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
- Windows 10 ISO https://www.microsoft.com/en-us/software-download/windows10

# Diagrama
<a href="https://ibb.co/C512HrwR"><img src="https://i.ibb.co/6RnBWdyj/Active-Directory-Lab.jpg" alt="Active-Directory-Lab" border="0"></a>

# Passo a passo
<b>Criando o DC(Domain Control)</b>

1) Vamos iniciar esse laboratório instalando na nossa máquina doméstica o VirtualBox.
2) Depois de instalado o VirtualBox, podemos baixar a imagem ISO do Windows Server 2019, será a nossa primeira máquina virtual que iremos configurar.
3) Para receber a imagem ISO do Windows Server ao VirtualBox, primeiramente, iremos configurar a VM:
   - Clicaremos em Novo, para criar uma nova VM
   - Nomearemos essa VM de WS-2019
   - Selecionaremos a versão, no caso, Windows Server 2019 64 bit
     
   <a href="https://ibb.co/Pvnz7702"><img src="https://i.ibb.co/mVwFLLgn/ws2019.jpg" alt="ws2019" border="0"></a>

4) Na aba Avançado em Geral, vamos mudar para Bi-Direcional, isso nos permitirá a transferência de arquivos e conteúdo da área de transferência entre a máquina virtual e a máquina host

   <a href="https://ibb.co/vCDC1N7N"><img src="https://i.ibb.co/nqRqrXHX/ws2019-1.jpg" alt="ws2019-1" border="0"></a>

5) Na sessão Rede, deixaremos em NAT

   <a href="https://ibb.co/7xhLqbxR"><img src="https://i.ibb.co/WNCjLsNP/ws2019-2.jpg" alt="ws2019-2" border="0"></a>

6) Após feito as configurações da VM, vamos adicionar a imagem ISO Windows Server 2019 à nossa respectiva VM "WS-2019" e fazer instalação.
7) Depois de ter iniciado o Windows Server 2019, primeiramente iremos abrir as Configurações de Redes, depois em Alterar Opções de Adaptador, vai percerber que aparecem duas redes Ethernet.
   - Clique em uma das duas redes, clique com o botão direito Status e depois em Detalhes, se o endereço IP estiver 10.0.2.15, ou algo similar, essa será a internet host do nosso laboratório, então iremos renomear para "Internet".
   - Faça o mesmo processo para a outra rede, porém irá perceber que o endereço IP é diferente, algo parecido como 164.254.136.74. Isso se deve ao fato que este adaptador de rede procurou algum servidor DHCP para obter um IP e não achou em lugar algum. Essa rede renomearemos como "Internal"
 
  <a href="https://ibb.co/gcfY6Ct"><img src="https://i.ibb.co/sXN7trs/ws2019-3.jpg" alt="ws2019-3" border="0"></a>

8) Com as duas redes renomeadas, clique com o botão direito da rede Internal, vá em Propriedades e clique duas vezes em Internet Protocol Version 4(TCP/IPv4). Entrando nas propriedades do Internet Protocol Version 4, iremos colocar 172.16.0.1 no endereço IP e 255.255.255.0 na máscara de sub-rede. Note que o gateway padrão não será preenchido, pois o próprio controlador de domínio servirá como gateway padrão. Para o servidor DNS, quando instalamos o Active Directory, ele instalou automaticamente o DNS, então este se utilizará como servidor DNS. Para fazer isso, insira seu próprio endereço IP ou use o endereço de loopback 127.0.0.1, que é um endereço genérico que se refere a si mesmo. Sempre que um computador envia um ping para esse endereço, ele está essencialmente enviando um ping para si mesmo automaticamente.

  <a href="https://imgbb.com/"><img src="https://i.ibb.co/DgLRh0cG/ws2019-4.jpg" alt="ws2019-4" border="0"></a>

9) Agora vá até o Menu Iniciar, Configurações, Sistema, Sobre e clique em Renomear este PC. Renomearemos como DomainControl para atribuir a nossa máquina Windows Server.
 
 <a href="https://ibb.co/3ycMKyMD"><img src="https://i.ibb.co/bgX2tg2S/ws2019-15.jpg" alt="ws2019-15" border="0"></a>

<b>Instalando o Active Directory no Controlador de Domínio</b>

1) Após a reinicialização do sistema, decorrente as configurações do usuário, iremos instalar o Active Directory. Primeiramente, iremos acessar o painel do Gerenciador do Servidor, e em seguida clicaremos em "Adicione Funções e Recursos". Vamos avançando até chegar na tabela de funções, marcaremos "Serviços de Domínio do Active Directory" e por fim, clicaremos em "Adicionar Recursos".

   <a href="https://ibb.co/xK8JvwGD"><img src="https://i.ibb.co/6cJghGbm/ws2019-5.jpg" alt="ws2019-5" border="0"></a>

2) Vamos avançando a instalação até a sua conclusão, depois clicaremos em "Fechar".

3) Já de volta no dashboard do Gerenciador do Servidor, observaremos que há um icone de uma bandeira na parte superior com um aviso amarelo. Este aviso significa que o software para serviços de domínio do Active Directory foi instalado, mas o domínio real ainda não foi criado. Esse vai ser o nosso próximo passo: clicaremos no ícone da bandeira e clicaremos em "Promover este servidor a um controlador de domínio".

4) Selecione "Adicionar uma nova floresta" e escolha um nome de domínio raiz . Para este laboratório, usaremos "mydomain.com" e clicaremos em Avançar.

  <a href="https://imgbb.com/"><img src="https://i.ibb.co/C5Y7KXf5/ws2019-6.jpg" alt="ws2019-6" border="0"></a>
 

 
     



