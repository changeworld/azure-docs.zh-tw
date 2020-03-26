---
title: 教學課程 - 使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定 Azure CNI 網路
description: 了解如何使用 Ansible 在 Azure Kubernetes Service(AKS) 叢集中設定 kubenet 網路
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156908"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>教學課程：使用 Ansible 在 Azure Kubernetes Service (AKS) 中設定 Azure CNI 網路

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

透過 AKS，您可以部署使用下列網路模型的叢集：

- [Kubenet 網路](/azure/aks/configure-kubenet) - 在部署 AKS 叢集時通常會建立並設定網路資源。
- [Azure CNI 網路](/azure/aks/configure-azure-cni) - AKS 叢集會連線至現有的虛擬網路 (VNET) 資源和組態。

若要進一步了解您的應用程式在 AKS 中的網路，請參閱 [AKS 中應用程式的網路概念](/azure/aks/concepts-network)。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立 AKS 叢集
> * 設定 Azure CNI 網路

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>建立虛擬網路和子網路

本節中的範例劇本程式碼會用來：

- 建立虛擬網路
- 在虛擬網路內建立子網路

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

本節中的範例劇本程式碼會用來：

- 在虛擬網路內建立 AKS 叢集。

請下列腳本儲存為 `aks.yml`：

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
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
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

以下是使用範例劇本時所應考量的幾項要點：

- 使用 `azure_rm_aks_version` 模組尋找支援的版本。
- `vnet_subnet_id` 是在上一節中建立的子網路。
- 劇本會從 `~/.ssh/id_rsa.pub` 載入 `ssh_key`。 如果您加以修改，請使用單行格式，且開頭應為 "ssh-rsa" (不含引號)。
- `client_id` 和 `client_secret` 值會從 `~/.azure/credentials` 載入，這是預設的認證檔案。 您可以將這些值設為服務主體，或從環境變數中載入這些值：

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>執行範例劇本

本節中的範例劇本程式碼會用來測試本教學課程中說明的各種功能。

請下列腳本儲存為 `aks-azure-cni.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

以下是使用範例劇本時所應考量的幾項要點：

- 將 `aksansibletest` 值變更為您的資源群組名稱。
- 將 `aksansibletest` 值變更為您的 AKS 名稱。
- 將 `eastus` 值變更為您的資源群組位置。

使用 ansible-playbook 命令執行劇本：

```bash
ansible-playbook aks-azure-cni.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

本節中的範例劇本程式碼會用來：

- 刪除 `vars` 區段中參考的資源群組。

請下列腳本儲存為 `cleanup.yml`：

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

以下是使用範例劇本時所應考量的幾項要點：

- 將 `{{ resource_group_name }}` 預留位置取代為您的資源群組名稱。
- 指定資源群組內的所有資源都將刪除。

使用 ansible-playbook 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Ansible 在 AKS 中設定 Azure Active Directory](./ansible-aks-configure-rbac.md)