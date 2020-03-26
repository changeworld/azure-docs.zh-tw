---
title: 教學課程 - 使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定 kubenet 網路
description: 了解如何使用 Ansible 在 Azure Kubernetes Service(AKS) 叢集中設定 kubenet 網路
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156888"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>教學課程：使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定 kubenet 網路

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

透過 AKS，您可以部署使用下列網路模型的叢集：

- [Kubenet 網路](/azure/aks/configure-kubenet) - 在部署 AKS 叢集時通常會建立並設定網路資源。
- [Azure 容器網路介面 (CNI) 網路](/azure/aks/configure-azure-cni) - AKS 叢集會連線至現有的虛擬網路資源和組態。

若要進一步了解您的應用程式在 AKS 中的網路，請參閱 [AKS 中應用程式的網路概念](/azure/aks/concepts-network)。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立 AKS 叢集
> * 設定 Azure Kubenet 網路

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>建立虛擬網路和子網路

本節中的劇本程式碼會建立下列 Azure 資源：

- 虛擬網路
- 虛擬網路內的子網路

請下列腳本儲存為 `vnet.yml`：

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>在虛擬網路中建立 AKS 叢集

本節中的劇本程式碼會在虛擬網路內建立 AKS 叢集。 

請下列腳本儲存為 `aks.yml`：

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

以下是使用範例劇本時所應考量的幾項要點：

- 使用 `azure_rm_aks_version` 模組尋找支援的版本。
- `vnet_subnet_id` 是在上一節中建立的子網路。
- `network_profile` 會定義 kubenet 網路外掛程式的屬性。
- `service_cidr` 會用來將 AKS 叢集中的內部服務指派給 IP 位址。 此 IP 位址範圍應該是未在您網路中的其他位置使用的位址空間。 
- `dns_service_ip` 位址應該是您服務 IP 位址範圍的 ".10" 位址。
- `pod_cidr` 應該是未在您網路環境中的其他位置使用的大型位址空間。 此位址範圍必須夠大而足以容納您要擴大的節點數目。 部署叢集後，即無法變更此位址範圍。
- Pod IP 位址範圍會用來為叢集中的每個節點指派 /24 位址空間。 在下列範例中，192.168.0.0/16 的 `pod_cidr` 會指派第一個節點 192.168.0.0/24、第二個節點 192.168.1.0/24 和第三個節點 192.168.2.0/24。
- 隨著叢集調整或升級，Azure 會繼續為每個新的節點指派 Pod IP 位址範圍。
- 劇本會從 `~/.ssh/id_rsa.pub` 載入 `ssh_key`。 如果您加以修改，請使用單行格式，且開頭應為 "ssh-rsa" (不含引號)。
- `client_id` 和 `client_secret` 值會從 `~/.azure/credentials` 載入，這是預設的認證檔案。 您可以將這些值設為服務主體，或從環境變數中載入這些值：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>建立網路資源的關聯

當您建立 AKS 叢集時，會建立網路安全群組和路由表。 這些資源由 AKS 管理，且會在您建立和公開服務時進行更新。 讓網路安全性群組和路由資料表與虛擬網路子網路產生關聯，如下所示。 

將下列劇本儲存為 `associate.yml`。

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

以下是使用範例劇本時所應考量的幾項要點：

- `node_resource_group` 是 AKS 節點建立所在的資源群組名稱。
- `vnet_subnet_id` 是在上一節中建立的子網路。


## <a name="run-the-sample-playbook"></a>執行範例劇本

本節列出會對本文中建立的工作進行呼叫的完整範例劇本。 

請下列腳本儲存為 `aks-kubenet.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

在 `vars` 區段中，進行下列變更：

- 針對 `resource_group` 索引鍵，將 `aksansibletest` 值變更為您的資源群組名稱。
- 針對 `name` 索引鍵，將 `aksansibletest` 值變更為您的 AKS 名稱。
- 針對 `Location` 索引鍵，將 `eastus` 值變更為您的資源群組位置。

使用 `ansible-playbook` 命令執行完整劇本：

```bash
ansible-playbook aks-kubenet.yml
```

執行劇本後會顯示類似下列輸出的結果：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

將下列程式碼儲存為 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

在 `vars` 區段中，將 `{{ resource_group_name }}` 預留位置取代為您的資源群組名稱。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程 - 使用 Ansible 在 AKS 中設定 Azure 容器網路介面 (CNI) 網路功能](./ansible-aks-configure-cni-networking.md)