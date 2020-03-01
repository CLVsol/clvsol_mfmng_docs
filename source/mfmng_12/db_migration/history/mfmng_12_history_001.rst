.. raw:: html

    <style> .red {color:red} </style>
    <style> .green {color:green} </style>
    <style> .bi {font-weight: bold; font-style: italic} </style>

.. role:: red
.. role:: green
.. role:: bi

====================================================================================
[2020-02-29] - Migração do banco de dados mfmng_12 - servidor [tkl-odoo12-mmfmng-vm]
====================================================================================

Criar uma nova instância do banco de dados **mfmng_12** (2020-02-29)
--------------------------------------------------------------------

	#. [tkl-odoo12-mmfmng-vm] Excluir, manualmente, o Banco de Dados **mfmng_12**:

	    #. Estabelecer uma sessão ssh com o servidor **tkl-odoo12-mmfmng-vm** e executar o *Odoo* no modo manual:

	    	::

		        # ***** tkl-odoo12-mmfmng-vm
		        #

		        ssh tkl-odoo12-mmfmng-vm -l root

		        /etc/init.d/odoo stop

		        su odoo

		        cd /opt/odoo
		        /usr/bin/odoo -c /etc/odoo/odoo-man.conf

	    #. Conectar-se, via *browser*, ao *Odoo* do servidor `tkl-odoo12-mmfmng-vm <https://tkl-odoo12-mmfmng-vm>`_

	    #. Excluir o Banco de Dados **mfmng_12** existente.

	    #. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mmfmng-vm** ao modo padrão:

		    ::

		        # ***** tkl-odoo12-mmfmng-vm
		        #

		        ^C

		        exit

		        /etc/init.d/odoo start

	#. [tkl-odoo12-mmfmng-vm] Criar uma nova instância do Banco de Dados **mfmng_12**:

		Lista de Módulos instalados:

			* CLVsol Odoo Addons:

				* clv_base
				* clv_global_log
				* clv_global_tag
				* clv_mfile
				* clv_external_sync

			* CLVsol Odoo Addons - MFmng customizations:

				* clv_base_mfmng
				* clv_global_log_mfmng
				* clv_global_tag_mfmng
				* clv_mfile_mfmng
				* clv_external_sync_mfmng

			* CLVsol Odoo Addons - MFmng customizations (External Sync):

				* clv_base_sync_mfmng
				* clv_mfile_sync_mfmng

	    #. Estabelecer uma sessão ssh (session 1) com o servidor **tkl-odoo12-mmfmng-vm** e executar o *Odoo* no modo manual:

		    ::

		        # ***** tkl-odoo12-mmfmng-vm (session 1)
		        #

		        ssh tkl-odoo12-mmfmng-vm -l root

		        /etc/init.d/odoo stop

		        su odoo
		        cd /opt/odoo
		        /usr/bin/odoo -c /etc/odoo/odoo-man.conf

	    #. Estabelecer uma sessão ssh (session 2) com o servidor **tkl-odoo12-mmfmng-vm** e executar o **install.py**:

		    ::

		        # ***** tkl-odoo12-mmfmng-vm (session 2)
		        #

		        ssh tkl-odoo12-mmfmng-vm -l odoo

		        cd /opt/odoo/clvsol_clvhealth_mfmng/project
		        
		        python3 install.py --super_user_pw "***" --admin_user_pw "***" --data_admin_user_pw "***" --db "mfmng_12"
	        
	    #. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mmfmng-vm** ao modo padrão:

		    ::

		        # ***** tkl-odoo12-mmfmng-vm (session 1)
		        #

		        ^C

		        exit

		        /etc/init.d/odoo start

Migrar os Usuários de **mfmng_pro** para **mfmng_12** (2020-02-29)
------------------------------------------------------------------

    #. Estabelecer uma sessão ssh com o servidor **tkl-odoo12-mfmng-vm** e executar o **res_users_migration.py**, acessando o servidor **tkl-odoo10-jcafb-vm** [base de dados **mfmng_pro**]:

        ::

            # ***** tkl-odoo12-mfmng-vm (session 2)
            #

            ssh tkl-odoo12-mfmng-vm -l odoo

            cd /opt/odoo/clvsol_mfmng/project
            
            python3 res_users_migration.py --rserver "https://192.168.25.177" --radmin_pw "***" --rdb "mfmng_pro" --lserver "https://192.168.25.185" --ladmin_pw "***" --ldb "mfmng_12"

     #. O "password" não foi migrado.
        
Criar o *External Sync Host* "https://192.168.25.177"
-----------------------------------------------------

    #. [tkl-odoo12-mfmng-vm] Criar, manualmente, o *External Sync Host* **https://192.168.25.177**:

        #. Conectar-se, via *browser*, ao *Odoo* do servidor `tkl-odoo12-mfmng-vm <https://tkl-odoo12-mfmng-vm>`_

        #. Criar o *External Sync Host* **https://192.168.25.177**:

            * Menu de acesso:
                * *External Sync* > *Configuration* > *Hosts* > Criar

            * Parâmetros utilizados:
                * External Host Name: "**https://192.168.25.177**"
                * External Database Name: "**mfmng_pro**"
                * External User: "**data.admin**"
                * External User Password: "*******"

.. _Lista de Schedules 20200229:

Lista de *Schedules* instalados (2020-02-29)
--------------------------------------------

    * Lista de *Schedules* instalados:
        * :green:`(Novo)` res.users (res.users)
        * :green:`(Novo)` clv.mfile.marker (clv.global_tag)
        * :green:`(Novo)` clv.mfile.category (clv.mfile.category)
        * :green:`(Novo)` clv.mfile.format (clv.mfile.format)
        * :green:`(Novo)` clv.mfile (clv.mfile)

Executar o *External Sync Batch* "*Default Batch*" (2020-02-29)
---------------------------------------------------------------

    #. [tkl-odoo12-mfmng-vm] Configurar os :bi:`External Sync Schedules` :green:`Novos`:

        #. Conectar-se, via *browser*, ao *Odoo* do servidor `tkl-odoo12-mfmng-vm <https://tkl-odoo12-mfmng-vm>`_

        #. Configurar, com a ajuda da ação :bi:`External Sync Schedule Mass Edit`, os :bi:`External Sync Schedules` :green:`Novos`:

            * Lista de *Schedules*:
                * :ref:`Lista de Schedules 20200229`

            * Menu de acesso:
                * :bi:`External Sync` » :bi:`External Sync` » :bi:`External Sync` » :bi:`Schedules` » **Ação** » :bi:`External Sync Schedule Mass Edit`

            * Parâmetros alterados:
                * *External Host*: "**https://192.168.25.177**"
                * *Max Task Registers*: "**20.000**"

    #. Estabelecer uma sessão ssh com o servidor **tkl-odoo12-mfmng-vm** e executar o *Odoo* no modo manual:

        ::

            # ***** tkl-odoo12-mfmng-vm
            #

            ssh tkl-odoo12-mfmng-vm -l root

            /etc/init.d/odoo stop

            su odoo

            cd /opt/odoo
            /usr/bin/odoo -c /etc/odoo/odoo-man.conf

    #. [tkl-odoo12-mfmng-vm] Executar o :bi:`External Sync Batch` "**Default Batch**":

        #. Conectar-se, via *browser*, ao *Odoo* do servidor `tkl-odoo12-mfmng-vm <https://tkl-odoo12-mfmng-vm>`_

        #. Executar a ação :bi:`External Sync Batch Exec` para o "**Default Batch**":

            * Menu de acesso:
                * :bi:`External Sync` » :bi:`External Sync` » :bi:`External Sync` » :bi:`Batches` » **Ação** » :bi:`External Sync Batch Exec`

            * *Members*:
                * :ref:`Lista de Schedules 20200229`

            * *Synchronization Log*:
                * :ref:`External Sync Batch - Default Batch - 20200229`

    #. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mfmng-vm** ao modo padrão:

        ::

            # ***** tkl-odoo12-mfmng-vm
            #

            ^C

            exit

            /etc/init.d/odoo start

Criar um backup do banco de dados **mfmng_12** (2020-02-29a)
------------------------------------------------------------

	#. [tkl-odoo12-mmfmng-vm] Estabelecer uma sessão ssh com o servidor **tkl-odoo12-mmfmng-vm** e paralizar o *Odoo*:

	    ::

	        # ***** tkl-odoo12-mmfmng-vm
	        #

	        ssh tkl-odoo12-mmfmng-vm -l root

	        /etc/init.d/odoo stop

	        su odoo

	#. [tkl-odoo12-mmfmng-vm] Executar os comandos de criação dos arquivos de backup:

	    ::

	        # ***** tkl-odoo12-mmfmng-vm
	        #
	        # data_dir = /var/lib/odoo/.local/share/Odoo
	        #

	        cd /opt/odoo
	        pg_dump mfmng_12 -Fp -U postgres -h localhost -p 5432 > mfmng_12_2020-02-29a.sql

	        gzip mfmng_12_2020-02-29a.sql
	        pg_dump mfmng_12 -Fp -U postgres -h localhost -p 5432 > mfmng_12_2020-02-29a.sql

	        cd /var/lib/odoo/.local/share/Odoo/filestore
	        tar -czvf /opt/odoo/filestore_mfmng_12_2020-02-29a.tar.gz mfmng_12

	#. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mmfmng-vm** ao modo desejado:

	    ::

	        # ***** tkl-odoo12-mmfmng-vm
	        #

	        cd /opt/odoo
	        /usr/bin/odoo -c /etc/odoo/odoo-man.conf

	        ^C

	        exit

	        /etc/init.d/odoo start

    Criados os seguintes arquivos:
        * /opt/odoo/mfmng_12_2020-02-29a.sql
        * /opt/odoo/mfmng_12_2020-02-29a.sql.gz
        * /opt/odoo/filestore_mfmng_12_2020-02-29a.tar.gz

.. index:: mfmng_12_2020-02-29a.sql
.. index:: filestore_mfmng_12_2020-02-29a

Restaurar um backup do banco de dados **mfmng_12** (2020-02-29a)
----------------------------------------------------------------

    #. [tkl-odoo12-mmfmng-vm] Estabelecer uma sessão ssh com o servidor **tkl-odoo12-mmfmng-vm** e paralizar o *Odoo*:

        ::

            # ***** tkl-odoo12-mmfmng-vm
            #

            ssh tkl-odoo12-mmfmng-vm -l root

            /etc/init.d/odoo stop

            su odoo

    #. [tkl-odoo12-mmfmng-vm] Executar os comandos de restauração dos arquivos de backup:

        ::

            # ***** tkl-odoo12-mmfmng-vm
            #

            cd /opt/odoo
            # gzip -d mfmng_12_2020-02-29a.sql.gz

            dropdb -i mfmng_12

            createdb -O odoo -E UTF8 -T template0 mfmng_12
            psql -f mfmng_12_2020-02-29a.sql -d mfmng_12 -U postgres -h localhost -p 5432 -q

            # mkdir /var/lib/odoo/.local/share/Odoo/filestore
            cd /var/lib/odoo/.local/share/Odoo/filestore
            rm -rf mfmng_12
            tar -xzvf /opt/odoo/filestore_mfmng_12_2020-02-29a.tar.gz

    #. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mmfmng-vm** ao modo desejado:

        ::

            # ***** tkl-odoo12-mmfmng-vm
            #

            cd /opt/odoo
            /usr/bin/odoo -c /etc/odoo/odoo-man.conf

            ^C

            exit

            /etc/init.d/odoo start

    #. [tkl-odoo12-mmfmng-vm] Configurar o parâmetro "**web.base.url**":

        #. Conectar-se, via *browser*, ao *Odoo* do servidor `tkl-odoo12-mmfmng-vm <https://tkl-odoo12-mmfmng-vm>`_

        #. Acessar a *View* **Parâmetros do Sistema**:

            * Menu de acesso:
                * **Configurações** » **Técnico** » **Parâmetros** » **Parâmetros do Sistema**

        #. Pesquisar pelo registro com a **Chave** "**web.base.url**";

        #. Editar o registro apresentado (**Chave**: "**web.base.url**")

        #. Alterar o campo **Valor** para:

            * "**http://tkl-odoo12-mmfmng-vm**".

        #. Salvar o registro editado.

Atualizar o(s) módulo(s) [clv_mfile, clv_mfile_mfmng] (2020-02-29)
------------------------------------------------------------------

    #. [tkl-odoo12-mmfmng-vm] Lista de Módulos:

        * clv_mfile
        * clv_mfile_mfmng

    #. [tkl-odoo12-mmfmng-vm] **Executar** a atualização do(s) Módulo(s):

        #. Estabelecer uma sessão ssh (session 1) com o servidor **tkl-odoo12-mmfmng-vm** e executar o *Odoo* no modo manual:

            ::

                # ***** tkl-odoo12-mmfmng-vm (session 1)
                #

                ssh tkl-odoo12-mmfmng-vm -l root

                /etc/init.d/odoo stop

                su odoo
                cd /opt/odoo
                /usr/bin/odoo -c /etc/odoo/odoo-man.conf

        #. Estabelecer uma sessão ssh (session 2) com o servidor **tkl-odoo12-mmfmng-vm** e executar o **install.py**:

            ::

                # ***** tkl-odoo12-mmfmng-vm (session 2)
                #

                ssh tkl-odoo12-mmfmng-vm -l odoo

                cd /opt/odoo/clvsol_clvhealth_jcafb/project
                
                python3 install.py --super_user_pw "***" --admin_user_pw "***" --data_admin_user_pw "***" --db "clvhealth_jcafb_2020" - m clv_mfile
            
        #. Retornar a execução do *Odoo* do servidor **tkl-odoo12-mmfmng-vm** ao modo desejado:

            ::

                # ***** tkl-odoo12-mmfmng-vm (session 1)
                #

                cd /opt/odoo
                /usr/bin/odoo -c /etc/odoo/odoo-man.conf

                ^C

                exit

                /etc/init.d/odoo start

.. toctree::
   :maxdepth: 2

   mfmng_12_history_001_sync_log

