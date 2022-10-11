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

