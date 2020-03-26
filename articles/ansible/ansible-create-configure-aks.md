---
title: 教學課程 - 使用 Ansible 在 Azure 中設定 Azure Kubernetes Service (AKS) 叢集
description: 了解如何使用 Ansible 在 Azure 中建立並管理 Azure Kubernetes Service 叢集
keywords: ansible, azure, devops, bash, cloudshell, 劇本, aks, 容器, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156676"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>教學課程：使用 Ansible 在 Azure 中設定 Azure Kubernetes Service (AKS) 叢集

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS 可設定為使用 [Azure Active Directory (AD)](/azure/active-directory/) 進行使用者驗證。 設定之後，您必須使用 Azure AD 驗證權杖來登入 AKS 叢集。 RBAC 可用使用者的身分識別或目錄群組成員資格作為基礎。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立 AKS 叢集
> * 設定 AKS 叢集

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>建立受控 AKS 叢集

範例劇本會建立一個資源群組，和位於該資源群組內的 AKS 叢集。

請下列腳本儲存為 `azure_create_aks.yml`：

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

在執行劇本之前，請參閱下列注意事項：

- `tasks` 內的第一個區段會定義名為 `myResourceGroup` 且位於 `eastus` 位置內的資源群組。
- `tasks` 內的第二個區段會定義名為 `myAKSCluster` 且位於 `myResourceGroup` 資源群組內的 AKS 叢集。
- 針對 `your_ssh_key` 預留位置，以單行格式且開頭為 "ssh-rsa" (不含引號) 的方式輸入 RSA 公開金鑰。
- 針對 `aks_version` 預留位置，請使用 [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) 命令。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook azure_create_aks.yml
```

執行劇本後會顯示類似下列輸出的結果：

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>調整 AKS 節點

上一節中的劇本範例會定義兩個節點。 您可以修改 `agent_pool_profiles` 區塊中的 `count` 值，以調整節點數目。

請下列腳本儲存為 `azure_configure_aks.yml`：

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

在執行劇本之前，請參閱下列注意事項：

- 針對 `your_ssh_key` 預留位置，以單行格式且開頭為 "ssh-rsa" (不含引號) 的方式輸入 RSA 公開金鑰。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook azure_configure_aks.yml
```

執行劇本後會顯示類似下列輸出的結果：

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>刪除受控 AKS 叢集

範例劇本會刪除 AKS 叢集。

請下列腳本儲存為 `azure_delete_aks.yml`：


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook azure_delete_aks.yml
```

執行劇本後會顯示類似下列輸出的結果：

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：調整 Azure Kubernetes Service (AKS) 中的應用程式](/azure/aks/tutorial-kubernetes-scale)