---
title: 教學課程 - 使用 Ansible 在適用於 MySQL 的 Azure 資料庫中設定資料庫
description: 深入了解如何使用 Ansible 來建立及設定適用於 MySQL 的 Azure 資料庫伺服器
keywords: ansible, azure, devops, bash, 劇本, mysql, 資料庫
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f068b3022c94466a20b524240dc293392b1f42ff
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603121"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>教學課程：使用 Ansible 在適用於 MySQL 的 Azure 資料庫中設定資料庫

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[適用於 MySQL 的 Azure 資料庫](/azure/mysql/overview)是以 MySQL Community Edition 為基礎的關聯式資料庫服務。 適用於 MySQL 的 Azure 資料庫可讓您管理 Web 應用程式中的 MySQL 資料庫。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立 MySQL 伺服器
> * 建立 MySQL 資料庫
> * 設定防火牆規則，讓外部應用程式可連線至您的伺服器
> * 從 Azure Cloud Shell 連線至您的 MySQL 伺服器
> * 查詢可用的 MySQL 伺服器
> * 列出您已連線的伺服器中所有的資料庫

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>建立資源群組

本節中的劇本程式碼會建立 Azure 資源群組。 資源群組是一種邏輯容器，您會在其中部署與管理 Azure 資源。  

請下列腳本儲存為 `rg.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

在執行劇本之前，請參閱下列注意事項：

* 已建立名為 `myResourceGroup` 的資源群組。
* 此資源群組會建立在 `eastus` 位置中：

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>建立 MySQL 伺服器和資料庫

本節中的劇本程式碼會建立 MySQL 伺服器和「適用於 MySQL 的 Azure 資料庫」執行個體。 新的 MySQL 伺服器是第 5 代的基本用途伺服器，具有一個虛擬核心，且名稱為 `mysqlserveransible`。 資料庫執行個體的名稱為 `mysqldbansible`。

如需定價層的詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫定價層](/azure/mysql/concepts-pricing-tiers)。 

請下列腳本儲存為 `mysql_create.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

在執行劇本之前，請參閱下列注意事項：

* 在 `vars` 區段中，`mysqlserver_name` 的值必須是唯一的。
* 在 `vars` 區段中，請`<server_admin_password>` 取代為密碼。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>設定防火牆規則

伺服器層級防火牆規則允許外部應用程式通過 Azure MySQL 服務防火牆連線至您的伺服器。 舉例來說，`mysql` 命令列工具和 MySQL Workbench 皆為外部應用程式。

本節中的劇本程式碼會建立名為 `extenalaccess` 的防火牆規則，以允許來自任何外部 IP 位址的連線。 

請下列腳本儲存為 `mysql_firewall.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

在執行劇本之前，請參閱下列注意事項：

* 在 [變數] 區段中，取代 `startIpAddress` 和 `endIpAddress`。 請使用與您的連線來源範圍相對應的 IP 位址範圍。
* Azure Database for MySQL 的連線透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 3306 的輸出流量。 若是如此，IT 部門若未開啟連接埠 3306，您就無法連線至您的伺服器。
* 劇本會使用 `azure_rm_resource` 模組，此模組允許直接使用 REST API。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>連接到伺服器

本節中，您會使用 Azure Cloud Shell 連線至您先前建立的伺服器。

1. 在下列程式碼中選取 [試試看]  按鈕：

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. 出現提示時，請輸入下列命令以查詢伺服器狀態：

    ```sql
    mysql> status
    ```
    
    若一切順利運作，您會在輸出中看到如下的結果：
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>查詢 MySQL 伺服器

本節中的劇本程式碼會查詢 `myResourceGroup` 中的 MySQL 伺服器，並列出找到的伺服器所包含的資料庫。

請下列腳本儲存為 `mysql_query.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook mysql_query.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

您也會看到 MySQL 資料庫的下列輸出：

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

請下列腳本儲存為 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible/)