---
title: 教學課程 - 使用 Ansible 以 Azure 應用程式閘道管理 Web 流量
description: 了解如何使用 Ansible 建立並設定 Azure 應用程式閘道以管理 Web 流量
keywords: ansible, azure, devops, bash, 劇本, 應用程式閘道, 負載平衡器, web 流量
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156621"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>教學課程：使用 Ansible 以 Azure 應用程式閘道管理 Web 流量

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure 應用程式閘道](/azure/application-gateway/overview)是網路流量負載平衡器，可讓您管理 Web 應用程式的流量。 根據來源 IP 位址和連接埠，傳統負載平衡器會將流量路由至目的地 IP 位址和連接埠。 應用程式閘道可讓您更精細地控制可根據 URL 路由流量的位置。 例如，您可以定義如果 `images` 是 URL 的路徑，則流量可以路由至一組針對映像而設定的特定伺服器 (也稱為集區)。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 設定網路
> * 使用 HTTPD 映像建立兩個 Azure 容器執行個體
> * 在伺服器集區中建立與 Azure 容器執行個體搭配運作的應用程式閘道

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>建立資源群組

本節中的劇本程式碼會建立 Azure 資源群組。 資源群組是 Azure 資源設定所在的邏輯容器。  

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

- 資源群組名稱為 `myResourceGroup`。 本教學課程中一律使用此值。
- 此資源群組會建立在 `eastus` 位置中。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>建立網路資源

本節中的劇本程式碼會建立可讓應用程式閘道與其他資源進行通訊的虛擬網路。

請下列腳本儲存為 `vnet_create.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

在執行劇本之前，請參閱下列注意事項：

* `vars` 區段包含用來建立網路資源的值。 
* 您必須為特定環境變更這些值。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>建立伺服器

本節中的劇本程式碼會使用 HTTPD 映像建立兩個 Azure 容器執行個體，作為應用程式閘道的 Web 伺服器。  

請下列腳本儲存為 `aci_create.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

本節中的劇本程式碼會建立名為 `myAppGateway` 的應用程式閘道。  

請下列腳本儲存為 `appgw_create.yml`：

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

在執行劇本之前，請參閱下列注意事項：

* `appGatewayIP` 定義於 `gateway_ip_configurations` 區塊中。 閘道的 IP 設定需要子網路參考。
* `appGatewayBackendPool` 定義於 `backend_address_pools` 區塊中。 應用程式閘道必須至少有一個後端位址集區。
* `appGatewayBackendHttpSettings` 定義於 `backend_http_settings_collection` 區塊中。 它會指定以連接埠 80 和 HTTP 通訊協定來進行通訊。
* `appGatewayHttpListener` 定義於 `backend_http_settings_collection` 區塊中。 這是與 appGatewayBackendPool 相關聯的預設接聽程式。
* `appGatewayFrontendIP` 定義於 `frontend_ip_configurations` 區塊中。 它會將 myAGPublicIPAddress 指派給 appGatewayHttpListener。
* `rule1` 定義於 `request_routing_rules` 區塊中。 這是與 appGatewayHttpListener 相關聯的預設路由規則。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook appgw_create.yml
```

建立應用程式閘道可能需要幾分鐘的時間。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

1. 在 [[建立資源群組](#create-a-resource-group)] 區段中，您會指定位置。 請記下其值。

1. 在 [[建立網路資源](#create-network-resources)] 區段中，您會指定網域。 請記下其值。

1. 針對測試 URL，請將下列格式取代為位置和網域：`http://<domain>.<location>.cloudapp.azure.com`。

1. 瀏覽至測試 URL。

1. 如果您看到下列頁面，表示應用程式閘道正常運作。

    ![運作中的應用程式閘道測試成功](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

將下列程式碼儲存為 `cleanup.yml`：

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