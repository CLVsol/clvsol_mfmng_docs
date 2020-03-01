.. raw:: html

    <style> .red {color:red} </style>
    <style> .green {color:green} </style>
    <style> .bi {font-weight: bold; font-style: italic} </style>

.. role:: red
.. role:: green
.. role:: bi

===========================================================================================
[2020-03-(01-02)] - Preparação do banco de dados mfmng_12 - servidor [tkl-odoo12-mmfmng-vm]
===========================================================================================

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

Atualizar o(s) módulo(s) [clv_mfile, clv_mfile_mfmng] (2020-03-01)
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

Criar um backup do banco de dados **mfmng_12** (2020-03-01a)
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
            pg_dump mfmng_12 -Fp -U postgres -h localhost -p 5432 > mfmng_12_2020-03-01a.sql

            gzip mfmng_12_2020-03-01a.sql
            pg_dump mfmng_12 -Fp -U postgres -h localhost -p 5432 > mfmng_12_2020-03-01a.sql

            cd /var/lib/odoo/.local/share/Odoo/filestore
            tar -czvf /opt/odoo/filestore_mfmng_12_2020-03-01a.tar.gz mfmng_12

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
        * /opt/odoo/mfmng_12_2020-03-01a.sql
        * /opt/odoo/mfmng_12_2020-03-01a.sql.gz
        * /opt/odoo/filestore_mfmng_12_2020-03-01a.tar.gz

.. index:: mfmng_12_2020-03-01a.sql
.. index:: filestore_mfmng_12_2020-03-01a

.. toctree::
   :maxdepth: 2
