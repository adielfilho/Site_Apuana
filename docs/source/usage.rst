.. _h.js2b7t82mute:

Acessando o Cluster
-------------------

Para acessar o cluster é necessário ter um login cin.ufpe.br e ter o
acesso habilitado as máquinas de acesso do cluster, também é necessário
estar na VPN do CIn.

.. _h.krbkxr4n0rks:

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

Exemplo com repositório do público do GitHub
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

Exemplo com repositório do privado do GitHub
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

Atualmente o cluster contém duas partições: long e short. A partição
long é a partição padrão e  é adequada para processar cargas com poucos
recursos em longos períodos de tempo. Já a partição short é adequada
para processar cargas com mais recursos, porém, em um menor período de
tempo.

A partição long roda jobs por até 7 dias, onde cada job sofre preempção,
se houver jobs na fila, a partir de 2 dias. A partição short roda jobs
por até 2 dias, onde cada job sofre preempção, se houve jobs na fila, a
partir de 2 horas de execução do job. A preempção é uma suspensão
temporária que força jobs a voltarem para a fila. Os limites de CPU, MEM
e GPU para cada partição são apresentados na tabela abaixo.

+-------+--------------+---------------------------------+-----+-----+-----+------------+
| Nome  | Tempo máximo | Preempção                       | CPU | MEM | GPU | Prioridade |
+-------+--------------+---------------------------------+-----+-----+-----+------------+
| long  | 7 dias       | A partir de 2 dias de execução  | 16  | 32  | 1   | 100        |
+-------+--------------+---------------------------------+-----+-----+-----+------------+
| short | 2 dias       | A partir de 2 horas de execução | 32  | 64  | 2   | 50         |
+-------+--------------+---------------------------------+-----+-----+-----+------------+

Para rodar um script em uma determinada partição:

sbatch -p nome_particao –cpus-per-task n_cpus --mem=memoria
--gpus=n_gpus script.sh

Também é possível ajustar os limites de recursos no cabeçalho do
script.sh:

.. code-block:: console

 #!/bin/sh                                                             
 #SBATCH --cpus-per-task=n_cpus                                                                                                 
 #SBATCH --gpus=n_gpus                                                                                                                
 #SBATCH --mem=memoria                                                 


.. _h.isb7a7i9a70r:

.. _h.r8jgqwv0re1s:

Políticas de priorização de jobs
-----------------------------------------------------

Cada partição possui um fator de prioridade. A partição long
(prioridade=100) possui prioridade maior que a partição short
(prioridade=50). Além disto, como a quantidade de recursos pode variar
em cada job, considera-se o fator JobSize. Este fator prioriza jobs que
solicitam menos recursos computacionais. Considere dois usuários que
submetem jobs utilizando a partição 'long'. O usuário A solicita X de
CPU e o usuário B solicita 2X de CPU. O usuário A, possui maior
prioridade

Portanto, para cada job é calculado um fator de prioridade de acordo com
a partição e recursos solicitados. Este fator de prioridade varia de 0.0
à 1.0. Por enquanto, considera-se dois fatores: Partition e JobSize.
Estes fatores possuem pesos iguais.

 
