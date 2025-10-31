.. _h.js2b7t82mute:

Acessando o Cluster
-------------------

Para acessar o cluster é necessário ter um login cin.ufpe.br e ter o
acesso habilitado as máquinas de acesso do cluster, também é necessário
estar na VPN do CIn. O primeiro passo é solicitar o acesso ao cluster através do \ `formulário de cadastro <https://docs.google.com/forms/d/1bDDI9WIi1aipfp9BqoCOYne7CFQtOGjgdDzh9kr0drw/edit>`__\.

Acessando a VPN do CIn
------------------------
Mesmo fazendo a conexão utilizando a rede interna do CIn, é necessário usar a VPN do CIn. Para maiores informações sobre o uso da VPN do CIn, consultar a documentação disponível na página do HelpDesk para VPN do CIn (https://helpdesk.cin.ufpe.br/redes/vpn). O canal para tirar dúvidas sobre conexão com a VPN é através do HelpDesk (helpdesk@cin.ufpe.br)


Conectando a Maquina de Acesso
------------------------------

O envio de tarefas para o cluster precisa ser feito a partir de uma
maquina de acesso, a entrada na maquina de acesso é realizado através do
SSH

        ssh <login>@slurm-client1.cin.ufpe.br

Uma vez conectado na máquina de acesso é possível alocar e editar seus
jobs a partir de comandos
\ `slurm <https://www.google.com/url?q=https://slurm.schedmd.com/documentation.html&sa=D&source=editors&ust=1683228690609179&usg=AOvVaw2aSvDJwDiM7wf_WKq0h5lp>`__\ .
Primeiramente, você precisará preparar seu ambiente de trabalho no
servidor que você utilizará. Jobs que utilizam máquinas virtuais (Docker
e afins) não serão aprovados, pois abrem brechas de segurança. Qualquer
biblioteca que necessite de instalação, por favor, nos contate e faremos
a instalação o mais rápido possível. Para acessar o servidor que
realizará o processamento, utilize o comando “salloc”, como no exemplo
abaixo:

salloc

Nossos servidores já tem o gerenciador de pacotes
\ `conda <https://www.google.com/url?q=https://www.anaconda.com/products/distribution&sa=D&source=editors&ust=1683228690610341&usg=AOvVaw3cLVSQY5yUsKcLch94qn4r>`__\  para
qualquer projeto que for realizado em Python. Os comandos de git também
estão disponíveis e prontos para uso. Após a preparação do seu ambiente,
volte para o nodo de login com o comando “exit” e rode o seu job com o
comando
\ `srun <https://www.google.com/url?q=https://slurm.schedmd.com/srun.html&sa=D&source=editors&ust=1683228690610939&usg=AOvVaw3QWOdTZiBB1V14-VrnC1a2>`__\ .
Exemplo:

.. code-block:: console

	srun --gpus=N_GPUS --cpus-per-task=N_CPUS nvidia-smi

.. _h.fnvohiub06p3:

Ambientes Virtuais Python
-------------------------

Um ambiente virtual em Python é um ambiente local e isolado no qual você
pode instalar ou desinstalar pacotes Python sem interferir no ambiente
global (ou em outros ambientes virtuais). Geralmente reside em um
diretório. Para usar um ambiente virtual, você deve ativá-lo. Ativar um
ambiente essencialmente define variáveis ​​de ambiente em seu shell para
que:

-  ‘python’ aponta para a versão correta do Python para aquele ambiente
-  ‘python’ procura pelos pacotes no ambiente virual
-  ‘pip install’ instala pacotes no ambiente virtual
-  Qualquer comando shell instalado via ‘pip install’ é disponibilizado

.. _h.t03rdi3pn51o:

Pip/Virtualenv
--------------

Pip é o gerenciador de pacotes preferido para Python e cada cluster
fornece várias versões do Python por meio do módulo associado que vem
com o pip. Para instalar novos pacotes, primeiro você terá que criar um
espaço pessoal para armazená-los. A solução preferida é usar ambientes
virtuais.

Primeiro, carregar o modulo Python que deseja utilizar

 module load Python3.10

Modulos Disponiveis

-  Python3.10 -> Python/3.10.8
-  Python3.9 -> Python/3.9.6
-  Python3.8 -> Python/3.8.6

Depois crie um ambiente virutal (onde <env> é o nome do ambiente) no seu
diretório home:

.. code-block:: console

	python -m venv $HOME/<env>  

Para ativar o ambiente criado:

.. code-block:: console

		source $HOME/<env>/bin/activate

Você agora pode instalar qualquer pacote Python utilizando o comando
pip, exemplo pytorch:

.. code-block:: console

	pip install torch torchvision

Recomendamos que para a criação do ambiente para rodar o seus job,
alocar uma maquina de forma interativa utilizando o comando salloc, nele
você pode criar e testar o ambiente, uma vez criado e funcional, para
utilizar você deve carregar o modulo e ativar o ambiente no seu script.

.. _h.elx4sixwhp8c:

Execução de jobs
----------------

Ao escrever um script .sh, é necessário ativar o ambiente no início e
instalar os pacotes necessários. Um exemplo de um script com tensorflow
é mostrado a seguir:


.. code-block:: console

	#!/bin/bash
	#SBATCH --job-name=test_job
	#SBATCH --ntasks=1
	#SBATCH --mem 16G
	#SBATCH -c 8
	#SBATCH -o job.log
	#SBATCH --output=job_output.txt
	#SBATCH --error=job_error.txt

	# carregar versão python
	module load Python/3.10
	# ativar ambiente
	source $HOME/env_teste/bin/activate
	# executar .py
	python $HOME/test_dir/test.py


.. _h.h11648s05oky:

Comandos básicos de gerenciamento de jobs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Para agendar o job faça:


.. code-block:: console

	sbatch test_slurm.sh 


Para verificar erros no job faça (dentro do diretório do arquivo
job_error.txt):

.. code-block:: console

	cat job_error.txt 

Para observar os outputs do job faça (dentro do diretório do arquivo
job_output.txt):

.. code-block:: console

	cat job_output.txt 

Para verificar a posição do job na fila faça:

.. code-block:: console

	squeue 

Para cancelar o job faça:

.. code-block:: console

	scancel job_id 

.. _h.16ozdksheroh:

.. _h.p9bbx0k61d7n:

Exemplo com repositório público do GitHub
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Primeiro é necessário clonar o repositório. Obs.: o diretório home do
usuário é sincronizado entre todas as máquinas.

.. code-block:: console

	git clone https://github.com/username/repoName.git

Depois de clonar o repositório, é criado um script .sh. Uma das
alternativas é utilizando nano:

.. code-block:: console

	nano test_slurm.sh 

Em seguida, o usuário preenche o script com as diretivas do SBATCH que
ele acha necessário e depois com os comandos que devem ser executados no
node. Primeiro, será apresentado um script que faz uso de Pytorch.

test_slurm.sh

.. code-block:: console

	#!/bin/bash
	#SBATCH --job-name=test_job
	#SBATCH --ntasks=1
	#SBATCH --mem 16G
	#SBATCH -c 8
	#SBATCH -o job.log
	#SBATCH --output=job_output.txt
	#SBATCH --error=job_error.txt

	# carregar versão python
	module load Python/3.9.6 
	# criar ambiente
	python -m venv $HOME/env_teste
	# ativar ambiente
	source $HOME/env_teste/bin/activate
	# instalar pacotes desejados
	pip install pytorch
	pip install pandas
	pip install matplotlib
	pip install seaborn
	Pip install IPython
	# executar .py
	python $HOME/repoName/thisScript.py


Perceba a criação de um novo ambiente e em seguida sua ativação. Aqui
foi realizado o dowgrade da versão do Python de 3.10 para 3.9. Isto foi
feito porque ainda existem bugs da classe DataLoader do PyTorch ao
utilizar o python 3.10. O cluster é bastante versátil neste aspecto,
pois pode-se escolher a versão do python (dentre as listadas acima) e
das dependências mais adequadas para o funcionamento do seu código no
ambiente virtual.

.. _h.u2s1lukw4mc7:

Exemplo com repositório privado do GitHub
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Clonando um repositório privado :

Para clone repositorios privados recomendamos conectar na maquina de
login (slurm-client1) passando adiante o seu agente SSH, permitindo a
você utilizar a chave SSH configurada na sua maquina local na sessão
SSH, para tal é preciso utilizar o parametro -A

.. code-block:: console

	ssh -A <login>@slurm-client1.cin.ufpe.br

.. code-block:: console

	git clone git@github.com:username:token@github.com/username/repoName.git

.. _h.kbo1jo3m9z40:

Sincronização de Arquivos Entre o Cluster a Sua Máquina
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Para a sincronização/transferência de arquivos entre sua máquina e o
cluster deve ser utilizado o comando rsync
(\ `Documentação <https://www.google.com/url?q=https://download.samba.org/pub/rsync/rsync.1&sa=D&source=editors&ust=1683228690625949&usg=AOvVaw1h4cJRq-mEcdnOojWx8lGE>`__\ ,
\ `Tutorial <https://www.google.com/url?q=https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories-pt&sa=D&source=editors&ust=1683228690626302&usg=AOvVaw2x91BoV8AGqpGJzdYZulSa>`__\ )
através da diretório home da máquina de login, lembrando que este é
sincronizado com os nós de computação do cluster.

Exemplos:

-  Sincronizando pasta da máquina local para o cluster:
   rsync --bwlimit=1000 -azP pasta-1 <login>@slurm-client1.cin.ufpe.br:~

-  A pasta de nome “pasta-1” vai ser copiada/sincronizada para a pasta
   de nome “pasta-1” no seu diretório home “~/pasta-1”
-  O argumento --bwlimit limita a velocidade de transferência, dado em
   KBytes por segund
-  O argumento -a é para archive mode, sendo equivalente a utilização
   dos argumentos “ -rlptgoD “
-  O argumento -z realiza a compressão dos dados para transferência
-  O argumento -P mostra o progresso da transferência e resume
   transferências interrompidas

-  Sincronizando pasta do cluster para máquina local:
   rsync --bwlimit=1000 -azP <login>@slurm-client1.cin.ufpe.br:~/pasta-1
   ~

-  Mesmo do exemplo anterior, porém a ordem é invertida, a pasta-1 do
   diretório home do cluster é copiada para o diretório home local

A utilização do rsync também é recomendada para transferências dentro da
própria máquina do cluster quando for realizada entre volumes em rede,
como por exemplo transferências do diretório home para o diretório /tmp
local do nó de computação

.. _h.6xrj2d7u8h14:

Partições e limites de recursos
---------------------------------

Fairshare
~~~~~~~~~~~~

No cluster Apuana o Fairshare está implementado de forma simples, onde o
cada orientador possui uma cota de recursos pré definida.
Esta cota é dividida igualmente entre seus orientandos ativos que 
estão associados a ele. Ou seja, Se os outros orientandos do seu professor não 
estiverem usando o cluster, sua "fatia" disponível de rescursos será maior.

QoS
~~~~~~~~~~~~

No Slurm, uma Qualidade de Serviço (QoS) é uma configuração que atribui 
limites de recursos específicos, como tempo máximo de CPU, tempo de espera e memória 
para trabalhos e controla sua prioridade de despacho em um cluster.
Foram definidos 2 (dois) QoS principais para os usuários com as seguintes limitações:

+----------+----------+---------+----------+
|   QoS    | Máx CPUs | Máx RAM | Máx GPUs |
+----------+----------+---------+----------+
| simple   |    16    |  64GB   |     2    |
+----------+----------+---------+----------+
| complex  |    48    |  500GB  |     4    |
+----------+----------+---------+----------+

Partições
~~~~~~~~~~~~

Anteriormente, o cluster continha duas partições: long e short. A partição
long era a partição padrão, adequada para processar cargas com poucos
recursos em longos períodos de tempo. Já a partição short era adequada
para processar cargas com mais recursos, porém, em um menor período de
tempo. Um resumo destas informações, juntamente com seus recursos, é apresentado na tabela abaixo.

.. A partição long roda jobs por até 7 dias, onde cada job sofre preempção,
.. se houver jobs na fila, a partir de 2 dias. A partição short roda jobs
.. por até 2 dias, onde cada job sofre preempção, se houve jobs na fila, a
.. partir de 2 horas de execução do job. A preempção é uma suspensão
.. temporária que força jobs a voltarem para a fila. Os limites de CPU, MEM
.. e GPU para cada partição são apresentados na tabela abaixo.

+-------+--------------+---------------------------------+-----+-----+-----+------------+
| Nome  | Tempo máximo | Preempção                       | CPU | MEM | GPU | Prioridade |
+-------+--------------+---------------------------------+-----+-----+-----+------------+
| long  | 7 dias       | A partir de 2 dias de execução  | 16  | 32  | 1   | 100        |
+-------+--------------+---------------------------------+-----+-----+-----+------------+
| short | 2 dias       | A partir de 2 horas de execução | 32  | 64  | 2   | 50         |
+-------+--------------+---------------------------------+-----+-----+-----+------------+

O cluster foi atualizado e possui agora sete partições: ``emergency``, ``install``, 
``debug``, ``short-simple``, ``short-complex``, ``long-simple``, ``long-complex``. Isso foi realizado 
para deixar mais específico o propósito de cada job, e as únicas partições que 
aceitam execução de forma interativa são as ``install`` e ``debug``.

+---------------+-------------+---------------------+-----------------+------------+----------------+
|    Partição   | Interativa  |       Recursos      | Máximo de tempo | Prioridade | Máximo de jobs |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|   emergency   |      ❌     | Completo (override) |      1 dia      |    1000    |       N/A      |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|    install    |      ✅     |      Meio node      |     30 mins     |     100    |        1       |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|     debug     |      ✅     |    Completo node    |     30 mins     |     10     |        1       |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|  short-simple |      ❌     |      Meio node      |      2 dias     |     100    |        4       |
+---------------+-------------+---------------------+-----------------+------------+----------------+
| short-complex |      ❌     |    Completo node    |      2 dias     |     50     |        1       |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|  long-simple  |      ❌     |      Meio node      |      7 dias     |     50     |        4       |
+---------------+-------------+---------------------+-----------------+------------+----------------+
|  long-complex |      ❌     |    Completo node    |      7 dias     |     25     |        1       |
+---------------+-------------+---------------------+-----------------+------------+----------------+


.. Para rodar um script em uma determinada partição:

.. sbatch -p nome_particao –cpus-per-task n_cpus --mem=memoria
.. --gpus=n_gpus script.sh

.. Também é possível ajustar os limites de recursos no cabeçalho do
.. script.sh:

.. .. code-block:: console

..  #!/bin/sh                                                             
..  #SBATCH --cpus-per-task=n_cpus                                                                                                 
..  #SBATCH --gpus=n_gpus                                                                                                                
..  #SBATCH --mem=memoria                                                 


⚠️ Atualização de scripts!
~~~~~~~~~~~~~~~~~~~~~~~~~
Com as atualizações realizadas no cluster, o padrão dos scripts foi modificado.
O template padrão para execução de jobs via sbatch é:

.. code-block:: console

	#!/bin/bash
	#SBATCH --mem 2G
	#SBATCH -c 1
	#SBATCH -p short-complex
	#SBATCH --gpus=1
	#SBATCH --mail-type=FAIL,END
	#SBATCH --mail-user=user@cin.ufpe.br

	ENV_NAME=$1
	module load Python3.10 Xvfb freeglut glew
	python -m venv $HOME/doc/$ENV_NAME
	source $HOME/doc/$ENV_NAME/bin/activate
	which python
	pip install -r ../requirements.txt
	pip list

Já para a partição interativa, pode ser utilizado o comando:

.. code-block:: console

	salloc --mem 64G -c 48 --gpus 1

.. _h.isb7a7i9a70r:

.. _h.r8jgqwv0re1s:

Políticas de priorização de jobs
-----------------------------------------------------

.. Cada partição possui um fator de prioridade. As partições long
.. (prioridade=100) possui prioridade maior que as partições short
.. (prioridade=50). Além disto, como a quantidade de recursos pode variar
.. em cada job, considera-se o fator JobSize. Este fator prioriza jobs que
.. solicitam menos recursos computacionais. Considere dois usuários que
.. submetem jobs utilizando a partição 'long'. O usuário A solicita X de
.. CPU e o usuário B solicita 2X de CPU. O usuário A, possui maior
.. prioridade

.. Portanto, para cada job é calculado um fator de prioridade de acordo com
.. a partição e recursos solicitados. Este fator de prioridade varia de 0.0
.. a 1.0. Por enquanto, considera-se dois fatores: Partition e JobSize.
.. Estes fatores possuem pesos iguais.

Cada partição possui um fator de prioridade. A partição que pode ser utilizada
para submissão de job com maior prioridade é a **``short-simple``** (prioridade=100)
enquanto a que possui menor prioridade é a **long-complex** (prioridade=25). 
Além disso, como a quantidade de recursos pode variar em cada job, considera-se 
o fator **JobSize**. Este fator prioriza jobs que solicitam menos recursos computacionais.

Considere dois usuários que submetem jobs utilizando a partição 'long-complex'.
O usuário A solicita X de CPU e o usuário B solicita 2X de CPU. O usuário A, 
possui maior prioridade. Portanto, para cada job é calculado um fator de 
prioridade  de acordo com a partição e recursos solicitados. 
Este fator de prioridade varia de 0.0 a 1.0.

.. FAQ
.. -----------------------------------------------------
.. **1. Entrei na lista de usuários, e agora?**

.. - Siga os passos abaixo para começar a utilizar o cluster:

..     - Conecte-se à VPN da UFPE para acessar os recursos do cluster. Consulte as instruções em https://helpdesk.cin.ufpe.br/servicos/conectividade/vpn.

..     - Acesse o nó de login: `$ssh [SEU-LOGIN-AQUI]@slurm-client1.cin.ufpe.br`

..     - Crie um script executável (por exemplo, `meu_job.sh`).

..     - Submeta seu job ao Slurm: `$sbatch --partition=short --mem=2G --gpus=0 --cpus-per-task 1 meu_job.sh`

.. **2. Não consigo lançar meu Slurm-job. O que eu faço?**

.. - Verifique se você está no nó de login.

.. - Consulte o erro e procure por soluções em https://google.com ou https://chat.openai.com/. Este cluster é gerenciado por estudantes, e a verificação de e-mails não é frequente.

.. - Se o erro incluir algo como:
..   ```shell
..   sbatch: error: QOSMaxCpuPerJobLimit # Pode ser QOSMaxMemoryPerJobLimit

..   sbatch: error: Batch job submission failed: Job violates accounting/QOS policy (job submit limit, user's size and/or time limits)
..   ```
..   Isso indica que você está tentando alocar recursos além do esperado. Tente reduzir alguns recursos ou entre em contato conosco para análise.

.. **3. Realizei todo o processo de entrar em um nó de computação, mas não consigo carregar meu módulo de desenvolvimento (modulefile). E agora?**

.. - Verifique se você está realmente em um nó de computação e não no nó de login.

.. - Confira se o seu módulo está instalado:
..     - Às vezes, instalamos o módulo, mas esquecemos de deixá-lo disponível no `MODULEPATH` estabelecido.
..     - Se não encontrar o módulo usando `$module avail`:
..         - Execute os seguintes comandos:
..         ```shell
..         $module use /opt/easybuild/modules/all/
..         $module avail
..         ```
..         - Entre em contato conosco para garantir a disponibilidade do módulo, seja instalando-o ou utilizando o caminho "/opt/easybuild/modules/all/" para carregá-lo.

.. **4. Como posso monitorar o progresso do meu job no cluster?**

.. - Utilize o comando `squeue` para verificar o status dos jobs em execução no cluster.

.. **5. O que fazer se o meu job está demorando mais do que o esperado para iniciar?**

.. - Verifique a fila de jobs (`squeue`) para ver se há algum atraso ou se a fila está muito ocupada. Caso necessário, ajuste as configurações do seu job para uma fila menos congestionada.

.. **6. Como faço para transferir arquivos para e do cluster?**

.. - Use o comando `scp` para transferir arquivos entre o seu computador local e o cluster. Exemplo: `$scp arquivo.txt [SEU-LOGIN-AQUI]@slurm-client1.cin.ufpe.br:/caminho/destino/`.

.. **7. O cluster suporta GPUs? Como faço para usar GPUs nos meus jobs?**

.. - Certifique-se de incluir a opção `--gpus` no seu comando `sbatch` para solicitar o número desejado de GPUs. Consulte a documentação do cluster para obter mais detalhes sobre a configuração de jobs com GPUs.


.. **9. O que fazer se eu esquecer minha senha ou encontrar problemas de autenticação?**

.. - Entre em contato com a equipe de suporte do CIn (helpdesk@cin.ufpe.br).
