---
title: 教學課程 - 使用 Ansible 在 Azure 中設定虛擬機器擴展集
description: 了解如何使用 Ansible 在 Azure 中建立及設定虛擬機器擴展集
keywords: ansible, azure, devops, bash, 腳本, 虛擬機器, 虛擬機器擴展集, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156546"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>教學課程：使用 Ansible 在 Azure 中設定虛擬機器擴展集

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 為 VM 設定資源
> * 設定擴展集
> * 藉由增加 VM 執行個體來調整擴展集 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>設定擴展集

本節中的劇本程式碼會定義下列資源︰

* 您的所有資源所將部署到的**資源群組**。
* 10.0.0.0/16 位址空間中的**虛擬網路**
* 虛擬網路內的**子網路**
* 允許您透過網際網路存取資源的**公用 IP 位址**
* 可對進出於擴展集的網路流量進行控制的**網路安全性群組**
* 會使用負載平衡器規則將流量分散於一組已定義 VM 的**負載平衡器**
* 使用所有已建立資源的**虛擬機器擴展集**

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)並將其儲存至 `vmss-create.yml`。
* 建立名為 `vmss-create.yml` 的新檔案，並在其中複製下列內容：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

在執行劇本之前，請參閱下列注意事項：

* 在 `vars` 區段中，將 `{{ admin_password }}` 預留位置取代為您自己的密碼。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vmss-create.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>檢視 VM 執行個體的數目

[已設定的擴展集](#configure-a-scale-set)目前有兩個執行個體。 下列步驟可用來確認該值：

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 瀏覽至您所設定的擴展集。

1. 您會看到以括號將執行個體數目括住的擴展集名稱：`Standard_DS1_v2 (2 instances)`

1. 您也可以執行下列命令，以使用 [Azure Cloud Shell](https://shell.azure.com/) 確認執行個體數目：

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    在 Cloud Shell 中執行 Azure CLI 命令的結果，會顯示目前有三個執行個體存在： 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>擴增擴展集

本節中的劇本程式碼會擷取擴展集的相關資訊，並將其容量從兩個變更為三個。

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)並將其儲存至 `vmss-scale-out.yml`。
* 建立名為 `vmss-scale-out.yml` 的新檔案，並在其中複製下列內容：

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook vmss-scale-out.yml
```

執行劇本後，您會看到類似下列結果的輸出：

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>驗證結果

透過 Azure 入口網站確認您工作的結果：

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 瀏覽至您所設定的擴展集。

1. 您會看到以括號將執行個體數目括住的擴展集名稱：`Standard_DS1_v2 (3 instances)` 

1. 您也可以執行下列命令，以使用 [Azure Cloud Shell](https://shell.azure.com/) 確認變更：

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    在 Cloud Shell 中執行 Azure CLI 命令的結果，會顯示目前有三個執行個體存在： 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [教學課程：使用 Ansible 將應用程式部署至 Azure 中的虛擬機器擴展集](./ansible-deploy-app-vmss.md)