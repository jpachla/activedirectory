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

<b>Instalação RAS/NAT</b>

1) Configuraremos o roteamento para que os clientes na rede privada possam acessar a Internet por meio do controlador de domínio. A configuração de RAS/NAT(RAS = Servidor de Acesso Remoto e NAT = tradução de endereço de rede) permite que máquinas clientes estejam em uma rede privada virtual ou VPN, mas ainda consigam acessar a Internet por meio do Controlador de Domínio.

   <a href="https://ibb.co/0j47HCpC"><img src="https://i.ibb.co/Kj4PT2c2/ws2019-7.jpg" alt="ws2019-7" border="0"></a>

   <a href="https://ibb.co/MD52jBp2"><img src="https://i.ibb.co/d0JW3m6W/ws2019-8.jpg" alt="ws2019-8" border="0"></a>

<b>Configuração do DHCP no Controlador de Domínio</b>

   <a href="https://ibb.co/tpn6Nndq"><img src="https://i.ibb.co/23RrLRbv/ws2019-9.jpg" alt="ws2019-9" border="0"></a>

1) Iremos configurar o nosso DHCP(Scope 1) conforme o diagrama do laboratório. Note que 172.16.0.1 é o endereço IP do controlador de domínio que tem o NAT configurado.

   <a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/2yPp0hYL/ws2019-10.jpg' border='0' alt='ws2019-10'/></a>

<b>Script PowerShell 1k Users</b>

1) Nesta etapa do processo, iremos utilizar um script PowerShell para adicionar mais de mil usuários no Active Directory, isso agilizará o processo ao invés de adicionar manualmente um a um. Neste link, utilizaremos o Script elaborado por Josh Madakor [Power Shell script for creating users](https://github.com/joshmadakor1/AD_PS)

2) Antes de executar este script, precisamos habilitar a execução de todos os scripts neste servidor, caso contrário, ocorrerá um erro. No PowerShell, digite o comando e pressione Enter : Set-ExecutionPolicy Unrestricted. Selecione "Sim para todos" . 

   <a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/L8SQm2H1/ws2019-11.jpg' border='0' alt='ws2019-11'/></a>

3) Executaremos o Script, irá aparecer uma lista de nomes de usuários que será importado no módulo do Active Directory, depois de pronto, posteriormente podemos separar em grupos como se fosse em uma empresa.

   <a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/fy3vDWqY/ws2019-12.jpg' border='0' alt='ws2019-12'/></a>

   


 

 
     



