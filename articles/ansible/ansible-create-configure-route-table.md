---
title: 教學課程 - 使用 Ansible 設定 Azure 路由表
description: 了解如何使用 Ansible 建立、管理和刪除 Azure 路由表。 另請了解如何建立和刪除路由。
keywords: ansible, azure, devops, bash, 劇本, 網路, 路由, 路由表
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659792"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>教學課程：使用 Ansible 設定 Azure 路由表

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果需要進一步掌控環境的路由，您可以建立[路由表](/azure/virtual-network/virtual-networks-udr-overview)。 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> 建立路由表 建立虛擬網路和子網路 建立路由表與子網路的關聯 將路由表與子網路取消關聯 建立和刪除路由 查詢路由表 刪除路由表

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>建立路由表

本節中的劇本程式碼會建立路由表。 如需路由表限制的相關資訊，請參閱 [Azure 限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)。 

請下列腳本儲存為 `route_table_create.yml`：

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

本節中的劇本程式碼會：

* 建立虛擬網路
* 在虛擬網路內建立子網路
* 建立路由表與子網路的關聯

路由表不會與虛擬網路相關聯。 路由表會與虛擬網路的子網路相關聯。

虛擬網路和路由表必須並存於相同的 Azure 位置和訂用帳戶中。

子網路和路由表具有一對多的關聯性。 子網路可以定義為沒有相關聯的路由表，或具有一個路由表。 路由表可以與一或多個子網路相關聯，或沒有相關聯的子網路。 

來自子網路的流量會根據下列項目進行路由：

- 路由表中定義的路由
- [預設路由](/azure/virtual-network/virtual-networks-udr-overview#default)
- 從內部部署網路傳播的路由

虛擬網路必須連線至 Azure 虛擬網路閘道。 此閘道可以是 ExpressRoute，或是 VPN (如果搭配使用 BGP 與 VPN)。

請下列腳本儲存為 `route_table_associate.yml`：

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>從子網路中斷與路由表的關聯

本節中的劇本程式碼會中斷路由表與子網路的關聯。

中斷路由表與子網路的關聯時，請將子網路的 `route_table` 設為 `None`。 

請下列腳本儲存為 `route_table_dissociate.yml`：

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>建立路由

本節中的劇本程式碼會在路由表內建立路由。 

請下列腳本儲存為 `route_create.yml`：

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

在執行劇本之前，請參閱下列注意事項：

* `virtual_network_gateway` 定義為 `next_hop_type`。 如需關於 Azure 如何選取路由的詳細資訊，請參閱[路由概觀](/azure/virtual-network/virtual-networks-udr-overview)。
* `address_prefix` 定義為 `10.1.0.0/16`。 此前置詞在路由表內不可重複。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>刪除路由

本節中的劇本程式碼會從路由表中刪除路由。

請下列腳本儲存為 `route_delete.yml`：

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>取得路由表資訊

本節中的劇本程式碼會使用 Ansible 模組 `azure_rm_routetable_facts` 擷取路由表資訊。

請下列腳本儲存為 `route_table_facts.yml`：

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>刪除路由表

本節中的劇本程式碼會刪除路由表。

路由表刪除後，其所有的路由也會一併刪除。

路由表若與子網路相關聯，則無法刪除。 請先[中斷路由表與任何子網路的關聯](#dissociate-a-route-table-from-a-subnet)，再嘗試刪除路由表。 

請下列腳本儲存為 `route_table_delete.yml`：

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible/)