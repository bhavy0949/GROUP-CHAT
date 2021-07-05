# GroupChatMessenger
**by Felipe Pestana** (usuário asd19)

Documentação criada para dar suporte ao projeto desenvolvido como parte da Lista 1 da disciplina de Sistemas Distribuídos no período  2019.1.
Esse arquivo se encontra disponível em duas versões diferentes, uma definida em Markdown e a outra em Html, a ser utilizada de acordo com a preferência do usuário.

## Sumário do Projeto
Esse projeto consiste de um sistema de troca de mensagens curtas visando estabelecer uma funcionalidade de chat em grupo utilizando comunicações via RMI e sockets Multicast como meios intermediários. A arquitetura do programa foi desenvolvido de acordo com as especificações definidas no enunciado do trabalho e se encontra dividida principalmente em três módulos diferentes, cada um referente a um processo a ser executado para funcionamento do sistema:

* Um módulo **cliente**, presente dentro do diretório GroupChatClient;
* Um módulo **servidor**, presente dentro do diretório GroupChatServer;
* Um módulo para **gerência do Registro RMI** necessário para execução das comunicações, presente dentro do diretório GroupChatRMI.
 
Além disso, temos algumas classes de suporte compartilhadas e utilizados por mais de um desses processos, arquivos de configurações individuais para cada módulo e scripts utilizados para inicialização e compilação adequada dos códigos e execução individual de cada um dos processos principais.

## Organização de diretórios nas máquinas virtuais

Esse tópico detalha como os códigos e arquivos gerados estão distribuídos dentro das máquinas virtuais disponibilizadas .11 e .16.

Todos os arquivos necessários para execução do program encontram-se dentro da pasta Trabalho, localizada no diretório principal do aluno. Para cada um dos módulos desenvolvidos, dentro de suas respectivas pastas, os seus arquivos relacionados estão organizados da seguinte maneira:

* Um diretório **bin**, contendo as classes Java compiladas;
* Um diretório **resources**, contendo o arquivo de configuração referente a cada um dos módulos;
* Um diretório **src**, contendo os códigos desenvolvidos em Java.

Além disso, temos um diretório extra Stubs, destinado a conter as classes Stubs geradas e utilizadas no processo de execução.

Para arquivos Java de classes a serem reaproveitadas e utilizadas em diferentes módulos, optou-se por replicar o código utilizado dentro dos diretórios de cada módulo, visando uma maior facilidade no momeno de avaliação e entendimento geral dos processos. Dessa forma todos os códigos necessários para funcionamento de um módulo estão presentes internamente na sua pasta *src*.

## Passos para Execução

Esse tópico detalha um pouco das etapas necessárias para execução do programa, assim como descreve as funcionalidades presentes nos scripts desenvolvidos, e detalha mais um pouco da organização atual aplicada ao projeto.

### Scripts de Inicialização

No diretório principal do trabalho, encontram-se, além das pastas descritas anteriormente, um total de quatro scripts diferentes: um para preparação dos códigos e outros três para a execução individual de cada um dos processos definidos. Todos já se encontram corretamente configurados para serem usados e foram desenvolvidos para se adequar com a organização de diretórios definidas acima, indentificando onde deveriam encontrar cada um dos arquivos esperados e para quais diretórios deveriam ser gerados cada um dos novos.

Todos os arquivos necessários já se encontram previamente compilados, podendo ser facilmente recompilados pelo processo descrito a seguir se desejado. Atualmente, todos os Scripts possuem as devidas permissões para serem executados dentro da pasta de usuário.

### Compilação e preparação dos códigos

Para compilação de todos os códigos necessários, assim como geração das classes Stubs para o RMI, basta navegar para o diretório Trabalho citado e executar o script CompileCode através do comando abaixo:

    ./CompileCode
    
Esse Script, além de compilar separadamente as classes para cada um dos processos, gera também os Stubs envolvidos na execução. 

**Obs**: Apesar da nova versão do RMI Java se utilizar da geração dinâmica desses arquivos, optou-se por incluir o comando explícito *rmic* para geração dessas classes no Script de inicialização para garantir a execução correta do programa como um todo.

### Execução dos processos

O primeiro passo para execução de fato do programa é, após terminada a etapa de compilação, executar em consoles diferentes e separadamente os comandos para ativação de cada um dos scripts responsáveis pela inicialização de cada processo. Ressaltando que os mesmos devem ser executados na ordem em que são listados a seguir:

* Execução do processo de inicialização e gerência do *RMIRegistry* local. Deve permanecer em funcionamento para manter o Registro RMI corretamente conectado e acessível pelos demais:

      ./runRegistry
    
* Execução do processo **Servidor** local da máquina. Necessita que o comando anterior tenha sido executado ou que exista um Registro RMI funcionando na porta configurada:

      ./runServer
    
* Execução de um processo **Cliente** na máquina. Necessita que o Registro RMI esteja rodando na porta configurada e que o processo Servidor esteja em execução:

      ./runClient
    
**Obs**: Esse último script é utilizado para iniciar a execução de um único cliente em uma máquina, entretanto, diferentemente dos anteriores, o mesmo pode ser executado diversas vezes. Dessa forma, podemos utilizá-lo para se ter *n* clientes diferentes em execução.

## Arquivos de configuração e parâmetro alteráveis

Para facilitar o gerenciamento e implantação desse programa em diferentes máquinas, foram separados algumas variáveis de ambiente que podem ser configuráveis no momento de execução para se adequar a cada caso. Para isso, cada um dos três processos principais possui o seu próprio arquivo de configuração, denominado de **config.properties** e localizado dentro da sua respectiva pasta *resource*.

Esse arquivo é organizado em uma estrutura de chave e valor, semelhante a um dicionário, e suas informações são extraídas diretamente pelo código Java no momento de execução de um programa. Suas localizações dentro da árvore de diretórios da máquina são passadas pelo script de execução no momento de chamada do programa a ser executado.

Observou-se a necessidade de definir três parâmetros diferentes para execução:

1. Endereço Ip Multicast (**multicastIP**): Utilizado somente pelo processo Servidor para o envio e recebimento de mensagens via Multicast. Definido atualmente como *230.0.0.19* ;

2. Número de porta para Multicast (**multicastPort**): Também presesnte somente na configuração do processo Servidor para a passagem de informações via Sockets. Definido atualmente como *9819* ;

3. Número de porta para o Registro RMI (**registryPort**): Utilizado pelo processo gerenciador do Registro para inicializá-lo dentro dessa porta predefinida e pelos demais processo, Clientes e Servidor, para acessar o Registro criado. Atualmente definido como *9919* .

## Descrição individual de cada Módulo e decisões de Design

Esse tópico detalha as organizações internas de cada um dos módulos definidos anteriormente e trabalha algumas decisões de Design adotadas durante a elaboração desse projeto.

A estruturação geral desse projeto busca a definição de programas, que, embora necessitam ser rodados em conjunto para o funcionamento adequado e a execução de fato do sistema como um todo, visando o objetivo proposto de um Chat em grupo, os mesmos ainda possuam suas organizações bem separadas e explicitamente definidas, com um certo grau de independência entre módulos e de acordo com as funções atribuídas a cada um deles.  

### Classes compartilhadas

Neste tópico, temos as classes que funcionam como interfaces a serem utilizadas para comunicação entre diferentes tipos de processos ou que encapsulam métodos e buscam fornecer funcionalidades que podem ser aplicadas e reaproveitadas em diversas etapas do projeto. Todas as classe definidas aqui devem estar presentes para a execução de cada um dos módulos do programa.

#### Message.java

Essa classe, busca simplesmente agrupar os campos que devem estar presentes em uma mensagem a ser enviada, recebida ou propagada dentro desse Chat, agrupando-os dentro da estrutura de um Objeto. Além de definir um construtor padrão para iniciação de uma instância dessa classe, possui também os seguintes campos especificados:

* **Username**: String contendo a identificação, através do nome de usuário, de quem a enviou;

* **Body**: String contendo o conteúdo (corpo) da mensagem;

* **SendDate**: Variável do tipo Date, inicializado no momento de envio da mensagem;

* **ReceiveDate**: Variável do tipo Date, atribuída no momento de recebimento da mensagem pelo cliente final;  

* **isSystemMessage**: Variável do tipo Booleano que identifica se a mensagem se trata ou não de uma definida e enviada por um Servidor gerenciador do Chat.

#### ServerInterface.java

Define as assinaturas dos métodos a serem oferecidos pela parte *Servidor RMI* dentro do processo de Servidor principal do Chat. Essa interface deve ser compilada e estar presente na execução de todos os processos que se utilizarem da comunicação por RMI com esse processo ou que interajam com o Registo local RMI. 

Possibilita a chamada das seguintes funções via comunicação com o protocolo RMI e objetos remotos:

* sendMessage: Recebe um objeto do tipo **Mensagem** e não retorna nada;

* logIn: Recebe o nome do usuário que entrou na sala e informações de data e horário relativos a esse acontecimento. Retorna um inteiro identificador do usuário que está se conectando;

* logOff: Recebe os mesmo parâmetros do método anterior, nome de usuário e data/horário do momento de chamada da função. Não retorna informação.

#### ClientInterface.java

Semelhante ao anterior, porém define agora as assinaturas dos métodos a serem oferecidos pela parte *Servidor RMI* dentro do processo principal de Cliente do Chat. Novamente, essa interface deve ser compilada e estar presente na execução de todos os processos que se utilizarem da comunicação por RMI com o processo Cliente ou que interajam com o Registo local RMI. 

Possibilita a chamada somente de uma única função via comunicação RMI com esse módulo:

* printMessage: Recebe um objeto do tipo **Mensagem** como parâmetro de entrada e não retorna informação.

#### GetConfig.java

Essa classe, encapsula funcionalidades de abertura e leitura de arquivos de configuração como os definidos para essa arquitetura (do tipo *.properties*), oferecendo abstrações e métodos que facilitam o acesso e a obtenção de determinados valores presentes neles. Ela foi desenvolvida buscando generalizar operações de acessos a arquivos externos que estavam presente de forma semelhante em todos os processos principais do projeto.

Seu construtor recebe como parâmetro o nome do arquivo a ser buscado dentro dos ClassPaths definidos em tempo de execução, buscando salvar dentro de uma variável de tipo Properties, interna ao objeto, as informações contidas e obtidas a partir do arquivo encontrado (caso o mesmo exista e contenha algum dado). Essas informações podem então ser exibidas através da chamada de um método GetProperties, que recebe uma string contendo a chave identificadora a qual se deseja consultar e retorna o seu valor associado caso essa chave seja encontrada no conteúdo do arquivo. 

### GroupChatRMI

O mais simples dos módulos, foi desenvolvido visando separar a execução e inicialização de um RMI Registry local, necessário a execução do programa, dos demais componentes. Apesar do mesmo poder ser gerado dentro do mesmo processo principal do Servidor, até porque este registro deve estar rodando para que este último possa ser executado corretamente, optou-se por instanciá-lo separadamente, de forma a deixar claro que trata-se de um serviço separado e que pode funcionar sem a existência de um servidor, no caso de acessos para outras aplicações.

Ao mesmo tempo, ao invés de simplesmente utilizar a chamada que possibilita a criação de um registro desse tipo via prompt de comando, *rmiregistry portNumber*, buscou-se utilizar dos recursos já desenvolvidos em Java para leitura de arquivos de configuração, buscando melhor automatizar e dinamizar sua execução. Além disso, se adicionou uma funcionalidade de um **ShutdownHook** definido em Java, o qua seria executado no momento de finalização dessa aplicação, garantindo a interrupção correta da sua execução em caso de encerramento do programa, o que inclui a realização de um unexport do objeto remoto de Registro e encerramento do loop de execução que mantinha o Registro funcionando e acessível às demais aplicações.

#### RunRegistry.java

Como descrito, essa classe possui apenas a leitura do arquivo de configuração, a inicialização do registro e a manuntenção de um loop que o mantenha aberto até que uma interrupção o encerre, além dos devidos tratamentos de erros e mensagens de comunicação associados a essas etapas. Necessita ter acesso as interfaces a serem exportadas no registro e pode ser corretamente encerrada no prompt através do comando **Ctrl + c**

### GroupChatServer

Passando agora de fato para as implementações principais do projeto, temos nesse tópico as classes relativas a parte Servidor do serviço de Chat em grupo. Esse módulo se utiliza tanto da comunicação via Sockets Multicast, para propagar informações para outras instância e receber dados de servidores que estão executando em diversas máquinas diferentes; como também do middleware RMI para realizar a troca de mensagens com  os clientes que estão em execução dentro da mesma máquina. Somente uma instância desse módulo deve estar ativa ao mesmo tempo dentro de uma mesma máquina.

Essa aplicação é divida em duas classes diferentes: uma responsável por atuar como a parte *servidor* de mensagens vindas através do  socket Multicast e *cliente* RMI, realizando chamadas a métodos remotos; e a outra como *servidor* RMI, respondendo chamadas remotas advindas de Clientes, e *cliente* no envio de mensagens via canais Multicast.

#### ChatServer.java

Essa classe é responsável por implementar a *InterfaceServer* definida anteriormente, oferecendo implementações para as chamadas de métodos especificados dentro desta, a serem utilizadas via chamadas remotas usando o protocolo RMI, além de executar o envio de mensagens via Socket Multicast. Possui como variáveis internas um valor de IP Multicast, um Socket utilizado para comunicação e o número de porta onde está inicializado o RegistroRMI local.

Essa classe implementa uma nova função conhecida como **BroadcastMessage**, que recebe um objeto do tipo Mensagem e simplesmente o encaminha para todos os processos que estão escutando no endereço de broadcast formado pelo IP Multicast e pela porta associada ao socket, ambos os valores passados como parâmetro no momento de criação e exportação da classe remota. Foi definido um valor máximo de tentativas, para tentar novamente no caso de ocorrer alguma exceção no momento de envio de alguma mensagem.

Além disso, essa classe implementa todas as funções definidas anteriormente pela interface. Para o caso de **sendMessage**, ela simplesmente executa o broadcast da mensagem. Já no caso de **logOff**, ela define uma mensagem padrão de saída do sistema a ser enviada aos demais participantes e então executa o broadcast. O mesmo ocorre no caso de **logIn**, só que com uma mensagem de entrada. Além disso, nessa última função optou-se por retornar um inteiro identificador diretamente ao cliente que realizou a chamada como parâmetro de retorno. Isso ocorre para que este possa por sua vez realizar o seu próprio registro dentro do RMIRegistry local com um endereço único, identificado pelo prefixo "Client" + esse valor inteiro de retorno devidamente formatado. Para garantir que esse valor seja único, o servidor obtém a lista de todos os participantes presentes nesse registro e verifica o menor número que seja maior ou igual a zero e que ainda não seja utilizado por outros participantes locais do Grupo. 

Por último, esse processo de logIn é definido como um método sincronizado, para garantir que, apesar de diferentes chamadas simultâneas a ele poderem ocorrer ao mesmo tempo, vindas de diferentes clientes, somente um método desse tipo pode estar em execução para um mesmo objeto remoto exportado. Isso visa garantir que um cliente esteja devidamente logado e inserido dentro do sistema de Chat em grupo antes que um próximo possar realizar a mesma operação, devendo esse primeiro receber as notificações relevantes a associações(entradas no grupo) realizadas em um segundo momento.

É utilizada como base para a geração da classe de ServerStub e a cada execução de um desses métodos remotos, uma mensagem de log é gerada no prompt, identificando o tipo de chamada e o usuário que responsável, para acompanhamento das requisições e de seu funcionamento.

#### RunServer.java

Esse código representa a classe principal, com o método main, para execução dos Servidores do serviço de Chat criado, sendo ela a responsável pela inicialização das conexões e dos objetos remotos, além de atuar como servidor recebedor de mensagens via Socket e cliente de chamadas RMI para os Clientes locais desse Chat.

Ela se inicia utilizando-se das funções padrões de leitura do arquivo de configuração, com seus devidos tratamentos de erros. Em seguida, ela executa uma conexão ao endereço IP de Multicast definido nos parâmetros, para então instanciar um objeto do tipo Socket, devidamente associado a porta para troca de mensagens definida no arquivo de configuração. Por fim, esse objeto criado é utilizado para se juntar ao grupo que está se utilizando do serviço de Multicast nesse endereço, estando apto então para realizar a comunicação através desse serviço. 

Para encerrar a etapa de inicialização, a aplicação verifica a existência do registro RMI na porta passada, define um objeto do tipo *ChatServer*, passando os parâmetros necessários e configurados do arquivo extra de properties, e o exporta sobre o endereço adequadamente e explicitamente definido para evitar erros durante a execução. Optou-se por realizar um teste inicial para checar a viabilidade do registro, para que se possa melhor identificar erros nessa etapa da execução. Todas essas etapas são acompanhadas por suas próprias mensagens de erro e verificações particulares.

Além disso, é definido um **ShutdownHook** próprio a ser executado no momento de interrupção da execução desse processo Servidor. Essa rotina irá, nesse momento, encerrar corretamente a participação desse processo no grupo referente ao Socket Multicast definido anteriormente, remover o objeto remoto exportado do registro de endereços RMI local e por último notificar o Status de saída via a impressão de uma mensagem que descreve se o mesmo foi executado corretamente ou não. Novamente, o processo pode ser corretamente encerrada no prompt através do comando **Ctrl + c**.

Por último a rotina principal do programa, irá executar um loop indefinidamente, escutando e esperando receber novas mensagens através da conexão criada anteriormente no objeto Socket definido, devendom, no caso do recebimento, extrair os bytes obtidos a partir dessa conexão. Essa rotina deve então tentar interpretar esses dados recebidos como um objeto do tipo Mensagem, devendo gerar uma mensagem de erro no caso de não conseguir. Caso seja bem sucedida, ela inicia então uma nova Thread, a qual deve se responsabilizar pelo envio dessa mensagem para todos os processos Clientes locais, passando à ela a mensagem recebida, para que possa então voltar a escutar no endereço definido no Socket, aguardando a chegada de novas mensagens.

Essas Threads são definidas dentro de uma classe interna denominada ListenThread, que possuem em seu método principal *run*, a ser chamado pela classe principal e executado paralelamente, o objetivo de encaminhar uma mensagem a todos os Clientes do Chat que se encontram devidamente registrados no registro local da máquina. Para isso, essa função recupera todos os valores presentes no Registro RMI local, e para cada entrada que possua o identificador *'Client'*, ele obtém o seu endereço previamente exportado. Com esse endereço conhecido, ele pode então importar uma referência ao objeto remoto que atua como *servidor RMI do cliente* e realizar a chamada do seu método printMessage, passando o objeto Mensagem recebido como parâmetro. Esse processo é realizado para todos os Clientes encontrados no registro, com o devido número máximo de tentativas e tratamentos de erros associados. Todas as Threads são devidamente encerradas ao fim de sua execução interna.

### GroupChatClient

O módulo Cliente definido para esse serviço de Chat em Grupo possui uma arquitetura organizacional geral similar a definida para um Servidor nesse projeto, no sentido de possuir também uma classe que representa a parte do processo que atua como *servidor RMI*, implementando uma interface e podendo agir como um objeto remoto, e outra atuando como a parte principal. Sendo que é essa última que contém o método main a ser iniciado, além de executar a devida inicialização necessária para a outra parte e funcionando como a parte *cliente do RMI*, podendo realizar chamadas remotas para os Servidores locais.

Porém, ao contrário do caso anterior, esse Cliente só se utiliza da comunicação via protocolos RMI, sem a parte de Sockets Multicast presente no módulo anterior, e interagindo somente com o servidor local, que deve estar registrado dentro do mesmo servidor de registro local RMI. Além disso, um total de n diferentes instâncias desse processo podem estar em execução ao mesmo tempo em uma mesma máquina.

#### ChatClient.java

Essa é a classe do Cliente responsável por implementar a *InterfaceClient* definida acima e, que por esse motivo oferecem implementações para as assinaturas dos métodos definidos dentro dela, as quais foram detalhadas anteriormente, permitindo que as mesmas sejam devidamente executadas em caso de chamadas remotas.

Nesse caso, como variável interna ao objeto, essa classe possui somente uma lista de objetos do tipo Mensagem, que se trata mais especificamente de um ArrayList sincronizado. Essa característica faz com que essa lista seja compartilhada por todas as instâncias de threads que tentam atuar sobre o objeto exportado, as quais são geradas por diferentes chamadas RMI ao método de um mesmo objeto exportado. Essas chamadas devem, portanto, coordenar suas operações pontuais de acesso que ocorram sobre esse objeto de forma paralela entre si, buscando sincronizar suas alterações e garatindo a geração correta de resultados obtido a partir de operações pontuais envolvendo essa estrutura. De forma geral, essa lista é utilizada para armazenar Mensagens recebidas durante a execução do método remoto de printMessage, cuja execução é melhor detalhada a seguir.

Na execução desse método, é recebido como parâmetro de entrada um objeto do tipo Mensagem que deve ter suas informações impressas no prompt de comando do usuário onde o processo Cliente está executando. Para isso, o valor do campo receiveDate é atribuído com as informações locais de data e hora no momento de início da chamada recebida remotamente, para que em seguida essa mensagem recebida seja adicionada a lista de Mensagens internas desse objeto. A rotina que está executando essa função deve então aguardar por um intervalo de tempo de algun milisegundos, definido arbitrariamente, interrompendo sua execução até que a duração dessa espera termine. Atualmente o tempo está definido como *0.5 segundos*. 

Ao fim desse período, é realizado uma ordenação sobre a lista de mensagens disponíveis, através de um Comparator definido internamente na função, e que possui com o objetivo a ordenação das mensagens de forma crescente a partir da sua data de envio associada. Esse sort é executado dentro de uma sincronização externa da lista, visando garantir que não ocorra problemas com outras rotinas em execução, já que  operações desse tipo podem vir a causar problemas de paralelismo mesmo em objetos sincronizados, já que envolvem mais do que simples operações pontuais sobre elementos. Ao fim dessa ordenação, o elemento que corresponde a mensagem mais antigo, localizada então na primeira posição dessa estrutura, é então extraído dessa lista.
Com esse objeto obtido, sua mensagem é então formatada, juntando seus campos em uma única String que pode então ser exibida na tela. As exibições finais das mensagens são construídas de acordo com o seguinte padrão:

    [Data de Envio - Data de Chegada] Nome de usuário: Corpo da mensagem
    
**Obs**: No caso de mensagens marcadas como vindas de um dos própios servidores do sistema, o nome de usuário é substituído pelo texto *Server Message* e o corpo trata-se de uma mensagem predefinida montado pelo processo responsável, de acordo com a sua natureza e objetivo.

**Obs2**: Para facilitar exibição e comparação, todas as datas são formatadas da seguinte forma: dd/MM/yyyy HH:mm:ss.SSS

A utilização dessa lista sincronizada e a execução de um processo de ordenação dentro desse método é uma tentativa de implementar uma solução para a **questão bônus 1** definida no enunciado do trabalho. Para isso, busca-se aguardar um tempo para que mensagens atrasadas possam ter tempo de chegar e ser inseridas na lista, garantingo sempre que na hora da impressão final do conteúdo, a mensagem a ser impressa seja aquela que tenha já tenha sido recebida e que tenha sido enviada a mais tempo. Dessa forma, espera-se que, supondo que todas as mensagens consigam chegar dentro desse período de tempo determinado, a ordem de exibição seria a mesma para qualquer cliente. Além disso, se pudéssemos garantir que todos os relógios estejam corretamente sincronizados, todas as mensagens seriam exibidas na ordem correta de envio.

#### RunClient.java

Por último, temos a classe principal para o módulo Cliente, contendo o método main, o qual é responsável pela instanciação e inicialização de um Cliente dentro das estruturas de comunicação presentes e utilizadas dentro desse serviço de Chat aqui definido. Novamente, como no caso do servidor, é essa classe a responsável pela leitura do arquivo de configuração e pela inicialização de um objeto remoto a ser exportado, além de atuar como *cliente RMI* e realizar a leitura inicial das mensagens enviadas pelo usuário via linha de comando. 

Inicialmente, o processo executa a leitura do arquivo de configuração através da classe GetConfig definida anteriormente. Uma vez que os dados necessários sejam obtidos do mesmo, que nesse caso inclui somente o número da porta através da qual o Registro RMI local previamente iniciado pode ser acessado, é iniciado então o processo para realizar a conexão. Primeiro é verificado se esse registro encontra-se de fato disponível e em execução na porta recebida, e, caso esteja, em seguida é realizada uma consulta dentro de seus dados em busca do endereço padrão onde o servidor local do Chat deve ser encontrado, que deve ter em sua referência o identificador *'Server'*. Novamente optou-se por passar a referência completa explicitamente do endereço a ser consultado no registro e realizar separadamente a verificação de existência do Registro. Caso algum deles não seja encontrado, é gerada a mensagem de erro correspondente.

O programa então requisita que o usuário entre com um nome de usuário, o qual não necessita ser único e diferente dos demais conectados ao serviço, visto que essa informação não é usado para registro, e aguarda que o mesmo entre com essa informação via linha de comando. Em seguida, com a referência ao servidor obtida anteriormente, a qual é usada e carregada em um Stub local que simula o objeto remoto exportado pelo Serivdor, é realizado uma chamada ao método de logIn oferecido remotamente, passando-se o nome de usuário desejado pelo usuário e informaçoes referente a data e hora local no momento da requisição. 

Esse método, por sua vez, retorna um inteiro utilizado como identificador para que essa instância do cliente possa se registrar no servidor RMI local, utilizando uma referência composta de *'Client'* + id e garantindo que não exita localmente dois clientes se registrando sobre a mesma referência. Uma vez que essa referência é obtida, é então utilizada em conjunto com o número da porta para montar o endereço completo e exportar um objeto remoto do tipo ChatClient, conforme definido acima e que será poderá ter seus métodos invocados por um processo do tipo Servidor.

Terminada essa etapa de iniciação, o programa avisa ao usuário que o mesmo está devidamente conectado ao chat, estando pronto a receber mensagens vindas de outros participantes, já que seu objeto remoto que irá ser responsável pela impressão das mesmas na tela já está devidamente registrada, exportada e disponível remotamente. Esse processo principal então entra em um loop de duração indefinida, aguardando que o usuário entre com uma mensagem de texto. 

Ao ser lido uma nova mensagem por linha de comando, o program imprime então um comando para retirar da exibição do prompt a última linha adicionada, buscando remover o texto de input do usuário que foi automaticamente impresso na tela e garantindo que essa mensagem tenha que percorrer o mesmo percurso das demais mensagens antes que possa ser mostrada para o usuário final. Esse caminho foi escolhido para tentar viabilizar e facilitar a exibição de todas as mensagens na mesma ordem para todos os participantes do grupo, além de garantir a exibição de todas elas sobre a mesma formatação, inclusive com o acréscimo de informações relativas a datas e horas de envio e recebimento e do nome de usuário associado a mesma. Cada linha lida é então transformada em um novo objeto mensagem, com o nome de usuário definido na inicialização e informações de data/hora local referentes ao momento em que ela é escrita pelo usuário. Essa mensagem é enviada então via RMI, através da função *sendMessage*, para o Servidor local, que se encarrega de redistribuí-la para os demais clientes e servidores.

Esse loop do Cliente permanece até que a mensagem de encerramento, definida como **'/exit'** e informada ao cliente após a execução bem-sucedida do processo de logIn e antes do início da execução do loop, é lida como entrada. No momento que isso ocorre é encerrada então a execução desse loop de leitura, sendo que o objeto remoto referente a parte *servidor RMI* do Cliente, responsável pela exibição de mensagens, é então removida do registro, através de uma operação que desfaz a exportação do mesmo e outra que remove do Registro RMI local, a refência ao endereço que foi registrado e associado a essa instância atual de processo Cliente. Por último a função de logOff, disponível na implementação do ChatServer é chamada via invocação remota, enviando uma mensagem de saída aos demais participantes. Com essas etapas terminadas, o programa exibe então ao usuário o Status da operação de saída por meio da impressão de uma mensagem final na tela.
