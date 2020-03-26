---
title: 教學課程 - 使用 Ansible 從 Azure 共用映像庫建立 VM 或虛擬機器擴展集
description: 了解如何使用 Ansible，根據共用映像庫中的一般化映像來建立 VM 或虛擬機器擴展集。
keywords: ansible, azure, devops, bash, 腳本, 虛擬機器, 虛擬機器擴展集, 共用映像庫
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155834"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>教學課程：使用 Ansible 從 Azure 共用映像庫建立 VM 或虛擬機器擴展集

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[共用映像庫](/azure/virtual-machines/windows/shared-image-galleries)服務可讓您輕鬆地管理、共用和組織自訂的受控映像。 此功能對於需要維護和共用許多映像的案例很有用。 您可以在訂用帳戶之間以及 Azure Active Directory 租用戶之間共用自訂映像。 映像也可以複寫到多個區域，以便更快速地調整部署規模。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 建立一般化 VM 和自訂映像
> * 建立共用映像庫
> * 建立共用映像和映像版本
> * 使用一般化映像來建立 VM
> * 使用一般化映像來建立虛擬機器擴展集
> * 取得共用映像庫、映像和版本的相關資訊。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>取得劇本範例

有兩種方式可取得一組完整的劇本範例：

- [下載 SIG 資料夾](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image)，並將其儲存至本機電腦。
- 為每個區段建立新的檔案，然後將劇本範例複製到其中。

`vars.yml` 檔案包含本教學課程所有劇本範例所使用的變數。 您可以編輯此檔案，以提供唯一的名稱和值。

第一個劇本範例 `00-prerequisites.yml` 會建立要完成本教學課程所需的內容：
- 資源群組，此為邏輯容器，您會在其中部署和管理 Azure 資源。
- VM 的虛擬網路、子網路、公用 IP 位址和網路介面卡。
- 來源虛擬機器，用於建立一般化映像。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 00-prerequisites.yml
```

在 [Azure 入口網站](https://portal.azure.com) 中，檢查您在 `vars.yml` 中指定的資源群組，以查看新的虛擬機器和您建立的各種資源。

## <a name="generalize-the-vm-and-create-a-custom-image"></a>將 VM 一般化並建立自訂映像

下一個劇本 `01a-create-generalized-image.yml` 會將上一個步驟所建立的來源 VM 一般化，然後據此建立自訂映像。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 01a-create-generalized-image.yml
```

檢查您的資源群組，並確認 `testimagea` 有出現。

## <a name="create-the-shared-image-gallery"></a>建立共用映像庫

映像庫是用於共用和管理映像的存放庫。 `02-create-shared-image-gallery.yml` 中的劇本程式碼範例會在資源群組中建立共用映像庫。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

現在，您會在資源群組中看到新的映像庫 `myGallery`。

## <a name="create-a-shared-image-and-image-version"></a>建立共用映像和映像版本

下一個劇本 `03a-create-shared-image-generalized.yml` 會建立映像定義和映像版本。

映像定義包括映像類型 (Windows 或 Linux)、版本資訊以及最小和最大的記憶體需求。 映像版本是映像的版本。 映像庫、映像定義和映像版本可協助您組織邏輯群組中的映像。 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

現在，您的資源群組已擁有映像庫的映像定義和映像版本。

## <a name="create-a-vm-based-on-the-generalized-image"></a>根據一般化映像來建立 VM

最後請執行 `04a-create-vm-using-generalized-image.yml`，以根據您在上一個步驟建立的一般化映像來建立 VM。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>根據一般化映像來建立虛擬機器擴展集

您也可以根據一般化映像來建立虛擬機器擴展集。 請執行 `05a-create-vmss-using-generalized-image.yml` 來進行操作。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>取得映像庫的相關資訊

您可以執行 `06-get-info.yml` 來取得映像庫、映像定義和版本的相關資訊。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>刪除共用映像

若要刪除映像庫資源，請參閱劇本範例 `07-delete-gallery.yml`。 請以相反順序刪除資源。 先刪除映像版本。 在刪除所有映像版本之後，就可以刪除映像定義。 在刪除所有映像定義之後，就可以刪除映像庫。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

本節中的範例劇本程式碼會用來：

- 刪除先前建立的兩個資源群組

請下列腳本儲存為 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

以下是使用範例劇本時所應考量的幾項要點：

- 將 `{{ resource_group_name }}` 預留位置取代為您的資源群組名稱。
- 兩個指定資源群組內的所有資源都將刪除。

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible/)