Faq
===

.. contents:: Índice
   :depth: 1
   :local:
   :backlinks: none

1. Como obter as credenciais de acesso?
---------------------------------------------------

As credenciais de acesso ao cluster são mesmas credienciais utilizadas para acessar os serviços do CIn (como acessar os computadores dos laboratórios de graduação). Caso o usuário tenha esquecido os seus acessos, eles podem ser redefinidos em: `https://intranet.cin.ufpe.br/ <https://intranet.cin.ufpe.br/auth/realms/intranet/protocol/openid-connect/auth?client_id=intranet-front&redirect_uri=https%3A%2F%2Fintranet.cin.ufpe.br&response_type=code&scope=openid&state=4ec313e5651246238d4db37f693ab8bd&code_challenge=nBIbId7-z5Tt0DtMeCT70s6Vn5gtohgsJW5tvRU539o&code_challenge_method=S256&response_mode=query>`_.

.. raw:: html

    <br><br>

2. Onde encontrar as instruções de uso/documentação do Apuana?
-------------------------------------------------------------------

A documentação do Apuana completo está disponível nesta presente plataforma `https://apuana.cin.ufpe.br/ <https://apuana.cin.ufpe.br/>`_. Além disso, informações adicionais de uso e lançamento de jobs podem ser encontrados nesse `vídeo <https://www.youtube.com/watch?v=Odp2rDH-HOQ/>`_.

.. raw:: html

    <br><br>

3. É possível acessar o Apuana fisicamente no CIn ou apenas remotamente?
------------------------------------------------------------------------------

O clsuter pode ser acessado apenas remotamente, através de conexão VPN e SSH. 

* Para se conectar à VPN do CIn, siga as intruções disponíveis em `https://helpdesk.cin.ufpe.br/servicos/conectividade/vpn <https://helpdesk.cin.ufpe.br/servicos/conectividade/vpn>`_.

* Para criar uma chave ssh e conseguir se conectar ao cluster corretamente, seguindo o tutorial da `Oracle <https://docs.oracle.com/en/cloud/cloud-at-customer/occ-get-started/generate-ssh-key-pair.html>`_, basta executar esse comando na pasta raiz do seu terminal:
    .. code-block:: console

        $ ssh-keygen -t rsa
    
    * Não é necessária a criação de novos diretórios ou arquivos, apenas deve-se seguir as intruções mantendo todas as configurações sugeridas como ``default``.

.. raw:: html

    <br><br>

4. Há restrição no número de GPUs por job?
-----------------------------------------------

Sim, existe um número de restrição de GPU a cada job, dependendo da partição que o usuário estiver usando. 

* Para a partição ``short``: o número máximo de **GPUs por job é 2**.
* Para a partição ``long``: o número máximo de **GPUs por job é 1**. 

Além disso, todos os outros parâmetros também possuem limites por partição:

* Para a partição ``short``: limite de **CPU - 32**; limite de memória **RAM - 64**; além de ter menor prioridade em relação aos jobs ``long``.
* Para a partição ``long``: limite de **CPU - 16**; limite de memória **RAM - 32**; maior prioridade de jobs em relação aos jobs ``short``.

.. raw:: html

    <br><br>

5. Qual o mapeamento dos nós aos tipos de GPU e a VRAM disponível em cada nó?
------------------------------------------------------------------------------------------------------

Atualmente, o cluster Apuana conta com 16 GPUs (sendo 11 NVIDIA RTX 3090 e 5 NVIDIA A100) distribuídas entre 9 nós. 
A relação entre a distribuição de GPUs e sua memória VRAM pode ser encontrada na tabela abaixo.

+----+--------------------+------------------+
| Nó | GPUs               | VRAM             |
+----+--------------------+------------------+
| 1  | 2 x NVIDIA RTX 3090| 24576 MiB cada   |
+----+--------------------+------------------+
| 2  | 3 x NVIDIA RTX 3090| 24576 MiB cada   |
+----+--------------------+------------------+
| 3  | 2 x NVIDIA RTX 3090| 24576 MiB cada   |
+----+--------------------+------------------+
| 4  | 2 x NVIDIA RTX 3090| 24576 MiB cada   |
+----+--------------------+------------------+
| 5  | 2 x NVIDIA RTX 3090| 24576 MiB cada   |
+----+--------------------+------------------+
| 6  | 1 x NVIDIA A100    | 81920 MiB        |
+----+--------------------+------------------+
| 7  | 1 x NVIDIA A100    | 81920 MiB        |
+----+--------------------+------------------+
| 8  | 1 x NVIDIA A100    | 81920 MiB        |
+----+--------------------+------------------+
| 9  | 2 x NVIDIA A100    | 81920 MiB cada   |
+----+--------------------+------------------+

6. O que significa o status "PD" de um job?
----------------------------------------------------------

O status "PD" é um acrônimo para o status de "Pending", ou seja, o job está em espera para ser iniciado. As razões mais comuns para que isso aconteça podem ser:

* **AssocGrp** — A associação ao qual o usuário está vinculado atingiu seu limite agregado de jobs.
* **AssocMax** — Os recursos que o job solicita excedem o limite máximo permitido pela associação.
* **BeginTime** — O tempo mínimo de início do job não foi atingido.
* **Dependency** — O job tem uma depedência em outro job que ainda não foi finalizado.
* **Max*PerAccount** — Os recursos que o job solicita excede o limite máximo permitido por conta.
* **Priority** — Um ou mais jobs com maior prioridade podem estar na fila.
* **QOSGrp*** — O QOS do job atingiu seu limite associado.
* **QOSMax*** — Os recursos solicitados pelo job atingiu o limite máximo para o QOS solicitado.
* **Resources** — Os recursos solicitados pelo job não estão disponíveis.

Mais detalhes podem ser encontrados na documentação do slurm em `https://slurm.schedmd.com/job_reason_codes.html <https://slurm.schedmd.com/job_reason_codes.html>`_.

.. raw:: html

    <br><br>


7. É possível solicitar mais memória para um job?
------------------------------------------------------

O usuário pode definir a memória necessária para o seu job através do parâmetro ``--mem``, dentro do seu ``script.sh``. Por exemplo:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=job_name
    #SBATCH --output=job_output.txt
    #SBATCH --error=job_error.txt
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=4
    #SBATCH --mem=16G  # solicita 16 GB de memória RAM

    python3 main.py

No entanto, não é possível solicitar mais memória do que o estebelecido pelos limites do cluster, que dependem da partição utilizada (``short`` ou ``long``).
Além disso, o slurm aloca os recursos que serão utilizados estaticamente no momento da submissão do job, não sendo possível alterá-los em tempo de execução do job.

.. raw:: html

    <br><br>

8. Como redirecionar um job em execução para outro nó, especialmente durante uma manutenção?
--------------------------------------------------------------------------------------------

No slurm não é possível fazer o redirecionamento de um job em execução para outro nó. Por isso, é importante que os usuários implementem rotinas de checkpoint em seus jobs, 
para que seja possível retomar a execução do job em outro nó, caso o nó atual fique indisponível.

Para implementar essa rotina, pode-se por exemplo:

* Habilitar a sinalização no job (dentro do `script.sh`):

.. code-block:: bash

    #SBATCH --signal=B:USR1@60

* Implementar o tratamento do sinal no código e salvar o estado do job (exemplo com o ``pytorch``):

.. code-block:: python

    import signal
    import torch

    def handle_signal(signum, frame):
        checkpoint_path = 'checkpoint.pth'
        state = {
            'epoch': epoch + 1,
            'state_dict': net.state_dict(),
            'optimizer': optimizer.state_dict()
        }
        torch.save(state, checkpoint_path)

    signal.signal(signal.SIGUSR1, handle_signal)

Outro fator importante a ser considerado é que quando um nó entra em estado de **DRAIN**, os jobs que estão em execução nesse nó continuam funcionando normalmente até serem finalizados.
No entanto, nenhum outro job será aceito nesse nó específico.

.. raw:: html

    <br><br>

9. Como verificar o consumo de GPU e CPU de um job para otimizar as configurações?
-----------------------------------------------------------------------------------------

No slurm é possível verificar os recursos alocados de GPU e CPU de um job através do comando ``sacct``:

.. code-block:: bash

    sacct -j <job_id> --format=JobID,State,Elapsed,MaxRSS,AveCPU,ReqMem,AllocCPUs

Porém, para fazer uma análise mais detalhada de como seu algoritmo está utilizando os recursos computacionais,
é necessário usar o profiling da biblioteca que está sendo utilizada. Por exemplo, no ``pytorch``:

.. code-block:: python

    import torch
    import torch.profiler

    model = ...  # seu modelo
    data = ...   # seus dados

    with torch.profiler.profile(
        schedule=torch.profiler.schedule(wait=1, warmup=1, active=2),
        on_trace_ready=torch.profiler.tensorboard_trace_handler('./logs'),
        record_shapes=True,
        profile_memory=True,
        with_stack=True
    ) as prof:
        for step, batch in enumerate(data):
            output = model(batch)
            loss = output.sum()
            loss.backward()
            prof.step()

E depois visulizar no ``tensorboard``:

.. code-block:: bash

    tensorboard --logdir=./logs

Outra maneira que pode ser utilizada para verificar o uso de GPU é através do comando ``nvidia-smi``, dentro do nó onde o job está rodando:

.. code-block:: bash

    nvidia-smi
