# **Introdução**
O armazenamento de dados é um elemento essencial na era digital, sendo fundamental tanto para usuários domésticos quanto para empresas de diversos portes. A busca por soluções que ofereçam segurança, fácil usabilidade e autonomia tem levado ao desenvolvimento de alternativas que fogem do modelo centralizado em grandes provedores de serviços, como a Amazon, Azure, Microsoft e Google. Nesse contexto, tecnologias NAS - Network-Attached Storage vêm à tona, pois permitem a criação de sistemas de armazenamento distribuído acessíveis e eficientes de forma ‘artesanal’.

A implementação de uma solução baseada no TrueNAS permite explorar conceitos de descentralização, garantindo maior controle sobre os dados e promovendo a privacidade dos usuários/empresas que os utilizam. No entanto, a adoção desse tipo de sistema pode apresentar desafios, como a complexidade de configuração e limitações em funcionalidades específicas, que muitas vezes estão disponíveis apenas em versões pagas da plataforma, como o TrueNAS Scale. Além disso, apesar de simples de utilizar, não são plug-and-play como as soluções das gigantes tecnológicas citadas, obrigando que a configuração seja feita por pessoas especializadas.

---

# **Objetivos**
O projeto tem como objetivo geral a implementação de uma plataforma para a gestão de arquivos pessoais. Para alcançar esse propósito, foram definidos objetivos específicos que abrangem aspectos fundamentais do sistema, tais como:
- Assegurar a tolerância a falhas do sistema de arquivos;
- Garantir a escalabilidade vertical da solução;
- Viabilizar o acesso remoto aos arquivos armazenados;
- Explorar ferramentas Open-Source;
- Proporcionar uma implementação que contribua para o aprofundamento dos conhecimentos em Sistemas Distribuídos.

---

# **Materiais**
Para desenvolvimento do projeto, foram utilizados:
- Um computador com acesso à internet e pelo menos 100GB disponíveis;
- Um ou mais dispositivos móveis com acesso à internet;
- Sistema operacional TrueNAS Core em formato ISO;
- Software Oracle VM VirtualBox;
- Software VPN ZeroTier.

---

# **Desenvolvimento**
## **Download do sistema operacional do NAS**
O primeiro passo foi a escolha do TrueNAS, sistema operacional para servidores NAS (Network-Attached Storage). O download da ISO foi feito diretamente do site oficial, uma vez que a versão community é gratuita e open-sourced.

**Imagem 1:** Site TrueNAS

## **Escolha da máquina utilizada no projeto**
Para continuação do projeto, foi decidido em qual máquina seria instalado o sistema operacional NAS. A escolha deveria ser feita entre instalação em um computador dedicado para o servidor - que seria mais custoso - ou em uma máquina virtual. Dado que o servidor será usado somente para a disciplina, optamos pela segunda opção e instalamos o software Oracle VM VirtualBox.

**Imagem 2:** Software Oracle VM VirtualBox em execução

## **Configuração da Máquina**
Foi feita a configuração da máquina virtual usando as seguintes especificações:
- Memória RAM: 8192 MB
- Processadores: 8 núcleos
- Disco Rígido principal: 32GB

**Imagem 3:** Especificações da Máquina Virtual

Antes de iniciar a máquina pela primeira vez, colocamos a ISO dentro do disco rígido da máquina.

## **Instalação do Sistema Operacional**
A instalação do sistema operacional é feita por meio de um passo a passo dentro do terminal. O processo levou cerca de 4h para ser executado. Ao final desse processo, o sistema já pode ser inicializado e executado.

## **Criação dos usuários e permissões**
Nesta etapa, criamos usuários e permissões no sistema TrueNAS. Isso é necessário porque o sistema pode ser utilizado por múltiplos usuários na mesma rede. Com os usuários criados, além do usuário root, podemos gerenciar as permissões de leitura e escrita no sistema, garantindo segurança e privacidade para o sistema.

**Imagem 4:** Criação de usuários e permissões

## **Criação de novos discos de armazenamento**
Após a criação dos usuários, é preciso efetivamente configurar os HDs que farão parte do sistema. Entenda que, nessa etapa, é onde entraria a decisão de espaço de armazenamento total que seria necessário e a relação entre a redundância desse armazenamento a fim de criar um sistema mais robusto e tolerante a falhas. Nessa etapa, criamos dois HDs, cada um com 20GB.

**Imagem 5:** Criação dos discos virtuais

## **Criação de uma Pool de Armazenamento**
Usando os discos rígidos acima, criamos uma Pool de arquivos RAID 1, que espelha o conteúdo dos HDs envolvidos, garantindo maior segurança aos arquivos.

**Imagem 6:** Criação da Pool de Arquivos

## **Testagem do NAS**
A funcionalidade principal de um servidor NAS é o armazenamento de arquivos. Para testar o NAS, queremos verificar três pilares principais: velocidade, integridade e estabilidade.

### **Teste de Upload**
Foi realizada a transferência de arquivos para a máquina. Para isso, criamos três arquivos diferentes e realizamos o upload deles. Após isso, oscilamos a rede e reiniciamos a máquina, garantindo que não houvesse perda de dados.

### **Teste de Download**
O download de arquivos armazenados no NAS foi testado a partir de uma máquina diferente, conectada na mesma rede. As conclusões foram:
- **Velocidade:** Depende da rede que se encontra.
- **Integridade:** Os arquivos ainda estavam dentro do NAS e conseguiram ser baixados.
- **Estabilidade:** Em caso de oscilção da rede, o arquivo não era baixado e exigia reconexão à máquina.

**Imagem 7:** Testes de desempenho da máquina NAS

## **Instalar o ZeroTier no sistema NAS**
Para permitir o acesso remoto ao NAS, foi utilizada a VPN ZeroTier, que configura uma rede privada e gratuita para até 10 dispositivos. Os scripts usados para instalação do ZeroTier foram:

```bash
sed -i .orig 's/enabled: yes/enabled: no/' /usr/local/etc/pkg/repos/local.conf
sed -i .orig 's/enabled: no/enabled: yes/' /usr/local/etc/pkg/repos/FreeBSD.conf
pkg update
pkg install -y zerotier
service zerotier onestatus
service zerotier onestart
```

## **Criação de uma conta no ZeroTier**
Usando o portal web do ZeroTier, criamos uma conta gratuita e copiamos seu endereço hash para uso nas próximas configurações.

**Imagem 9:** Interface do ZeroTier configurada

## **Conectar o TrueNAS à rede ZeroTier**
Executamos scripts no terminal para conectar à rede e autorizamos o dispositivo na interface restrita por senha.

```bash
zerotier-cli join <NETWORK-ID>
zerotier-cli info
cp /usr/local/etc/rc.d/zerotier /mnt/cefetmg/zerotier/zerotier.rc.d
```

---

# **Resultados**
Foi possível acessar os arquivos em diversos dispositivos. Os testes foram realizados em três sistemas operacionais distintos:
- **Windows:** Sistema hospedeiro da máquina virtual TrueNAS;
- **iOS:** Com restrições quanto a personalizações;
- **Android:** Maior liberdade de customização.

A taxa máxima de upload registrada foi de apenas 2 Mbps, evidenciando uma limitação significativa do NAS caseiro. Contudo, a segurança proporcionada pelo ZeroTier foi um aspecto positivo.

---

# **Conclusão**
O projeto proporcionou uma compreensão aprofundada sobre sistemas de arquivos distribuídos, aplicando conceitos como redes P2P, escalabilidade vertical e privacidade com VPNs privadas. Apesar das limitações da versão gratuita do TrueNAS, o aprendizado adquirido foi significativo.
