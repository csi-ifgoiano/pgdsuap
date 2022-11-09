Instalação e Configurações
==========================

Apps
-----

Clonar repositório do programa de gestão <https://gitlab.ifrn.edu.br/>

.. code-block:: console

   git clone git@gitlab.ifrn.edu.br:cosinf/suap-ifgoiano.git
   
.. note::
   Para realizar a clonagem, deve-se adicionar a chave SSH ao seu perfil no Gitlab.


Copiar as apps: programa_gestao, pgd_api, pgd_integra_ifgoiano

**App programa_gestao**: implementa toda a lógica de negócio do PGD.

**App pgd_api**: implementa a integração com a API do PGD `<https://api-programadegestao.economia.gov.br/docs>`

**App pgd_integra_ifgoiano**: implementa a integração entre os dados da app programa_gestao e o envio através app pgd_api. Permite e visualização do histórico de envio dos planos cadastrados na app programa_gestao.

   * arquivo `integra/configuracao.py`: implementa a lista das situações dos planos e atividades aptas para envio pela app pgd_api.
   * arquivo `integra/dados.py`: implementa a obtenção de cada dado a ser enviado pela pgd_api.
   * arquivo `integra/integra.py`: envia os dados pela API utilizando a app pgd_api.
   * arquivo `management/commands/pgd_integra_ifgoiano_enviar_dados.py`: chama rotina de envio de dados pela app pgd_api. Comando deve ser inserido no sync_suap.py da instituição.
   * model PlanodeTrabalhoPgdIfgoiano: proxy para model PlanodeTrabalho da app programa_gestao que implementa métodos adicionais

Habilitar aplicações no arquivo de configuração do SUAP (suap/settings.py)

.. code-block:: console

   INSTALLED_APPS_SUAP = (
   .
   .
   .
   .
   'programa_gestao',
   'pgd_api',
   'pgd_integra_ifgoiano'
   )
   
Sincronizar SUAP

.. code-block:: console

   python manage.py sync

API
---------

Solicitar as credenciais via e-mail para pgd@economia.gov.br, com as seguintes informações:

   * Nome responsável pela integração:
   * E-mail
   * Matrícula SIAPE
   * Código SIORG do órgão
   * Sistema utilizado

Acesse a URL </comum/configuracao> no SUAP para configurar a API.
Localize a seção "Aplicação pgd_integra_ifgoiano" e informe os dados:
   * URL da API
   * Usuário API PGD Ministério da Economia
   * Senha API PGD Ministério da Economia

Para testar, pode rodar individualmente o comando "pgd_integra_ifgoiano_enviar_dados":

.. code-block:: console

   python manage.py pgd_integra_ifgoiano_enviar_dados
   
.. note::
   O comando deverá ser adicionado ao comando sync_suap da instituição para rodar periodicamente.

URLs
^^^^^

`admin/pgd_integra_ifgoiano/registroacaoenvioplanodetrabalho/`: visualizar registro de envios dos planos

`admin/pgd_integra_ifgoiano/planodetrabalho`: 

   * visualizar os planos de trabalho aptos a serem enviados; 
   * visualizar situação de envio de cada plano;
   * enviar planos individualmente.
   
   
Comandos
---------

Atualização faixa de complexidade
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando é para instituições que adotaram o módulo com a modelagem antiga, onde o campo da faixa de complexidade estava na tabela de atividades. Instituições que vão começar a utilizar o módulo após 07/10/2022 não precisam migrar dados e pode ignorar o comando. Para realizar a migração dos planos já enviados deve-se seguir os seguintes passos:

* Descomentar os campos  `tempo_execucao_presencial`, `tempo_execucao_remota`, `faixa_complexidade`, `parametros_complexidade`  e `ganho_produtividade`  e método `save` do modelo Atividade
* A fixture `programa_gestao/fixture/complexidade.json` foi criada de acordo com as faixas de complexidade adotadas no IF Goiano, deve ser verificado se o mesmo cadastro será utilizado.
* Aplicar individualmente a `migração manage.py migrate` programa_gestao `0006_auto_20220323_1106`. Ela cria o novo modelo Complexidade e popula com a fixture `complexidade.json`.
* O comando atualizar_faixa_complexidade faz um de-para na faixa de complexidade e deve ser analisado se atende a realidade da instituição.
* Acessar o arquivo `programa_gestao/migrations/0007_auto_20220323_1542` e descomentar a linha 16. Essa linha roda o comando para atualizar a faixa de complexidade quando aplicar a migração. 
* Aplicar individualmente a migração manage.py migrate programa_gestao `0007_auto_20220323_1542`. Ela executa o comando `atualizar_faixa_complexidade` e remove os campos do modelo Atividade.
* Apagar os campos e o método save do modelo Atividade.

Fechar planos automaticamente
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando finaliza os planos e homologa a carga-horária para planos avaliados a mais de sete dias. Pode ser agendado no cron periodicamente ou adicionado no sync_suap da instituição. Para executar o comando:

.. code-block:: console

   python manage.py fechar_plano_automaticamente


Migrar modalidade do edital para inscrição
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Este comando é para instituições que adotaram o módulo com a modelagem antiga, onde o campo de modalidade do programa de gestão estava na tabela de editais. Instituições que vão começar a utilizar o módulo após 07/10/2022 não precisam migrar dados e podem ignorar o comando. Para realizar a migração das inscrições deve-se executar o comando:

.. code-block:: console

   python manage.py migrar_modalidade_edital_para_inscricao


Notificar autorização atrasada
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando notifica todas as chefias **imediatas** sobre planos encaminhados e não autorizados após 2 dias. Pode ser agendado no cron periodicamente ou adicionado no sync_suap da instituição. Para executar o comando:

.. code-block:: console

   python manage.py notificar_autorizacao_atrasada
   

Notificar avaliação atrasada
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando notifica todas as chefias **imediatas** sobre planos entregues e não avaliados após 40 dias. Pode ser agendado no cron periodicamente ou adicionado no sync_suap da instituição. Para executar o comando:

.. code-block:: console

   python manage.py notificar_avaliacao_atrasada

Notificar entrega atrasada
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando notifica todos os servidores participantes do programa de gestão sobre planos autorizados e não entregues após 3 dias da data de finalização do plano. Pode ser agendado no cron periodicamente ou adicionado no sync_suap da instituição. Para executar o comando:

.. code-block:: console

   python manage.py notificar_avaliacao_atrasada
   
   
Desativar servidores com máximo de faltas
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Este comando desliga do programa da gestão as inscrições de edital vigente que atingiram o número máximo de faltas. Pode ser agendado no cron periodicamente ou adicionado no sync_suap da instituição. Para executar o comando:

.. code-block:: console

   python manage.py desativar_servidores_com_maximo_faltas



