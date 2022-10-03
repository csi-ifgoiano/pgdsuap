Instalação e Configurações
==========================

Aplicação
---------

Clonar repositório do programa de gestão <https://gitlab.ifgoiano.edu.br/>

.. code-block:: console

   git clone nome_do_repositorio

Copiar pastas do programa de gestão do repositório clonado: programa_gestao, api_pgd, integra_api

Habilitar aplicações no arquivo de configuração do SUAP (suap/settings.py)

.. code-block:: console

   INSTALLED_APPS_SUAP = (
   .
   .
   .
   .
   'programa_gestao',
   'api_pgd',
   'integra_api'
   )
   
Sincronizar SUAP

API
---------

.. code-block:: console

   python manage.py sync
