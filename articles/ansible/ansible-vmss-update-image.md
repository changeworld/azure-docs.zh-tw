---
title: 教學課程 - 使用 Ansible 更新 Azure 虛擬機器擴展集的自訂映像
description: 了解如何使用 Ansible 在 Azure 中更新虛擬機器擴展集的自訂映像
keywords: ansible, azure, devops, bash, 腳本, 虛擬機器, 虛擬機器擴展集, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b7d3053c09d2dcb667a4fc407035f4814f786932
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155842"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>教學課程：使用 Ansible 更新 Azure 虛擬機器擴展集的自訂映像

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

部署 VM 之後，您會以應用程式所需的軟體設定 VM。 您可以建立自訂映像，而不是為每個 VM 執行這項設定工作。 自訂映像是現有 VM 的快照集，其中包含任何已安裝的軟體。 當您[設定擴展集](./ansible-create-configure-vmss.md)時，會指定該擴展集的 VM 所要使用的映像。 藉由使用自訂映像，每個 VM 執行個體都會針對您的應用程式進行相同的設定。 有時候，您可能需要更新擴展集的自訂映像。 這項工作是本教學課程的重點。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 使用 HTTPD 設定兩個 VM
> * 從現有的 VM 建立自訂映像
> * 從映像建立擴展集
> * 更新自訂映像

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>設定兩個 VM

本節中的劇本程式碼會建立兩個虛擬機器，並且都安裝 HTTPD。 

每個 VM 的 `index.html` 頁面都會顯示測試字串：

* 第一個 VM 顯示值 `Image A`
* 第二個 VM 顯示值 `Image B`

此字串將用來模擬以不同軟體設定每個 VM 的情形。

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml)並將其儲存至 `create_vms.yml`。
* 建立名為 `create_vms.yml` 的新檔案，並在其中複製下列內容：

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

使用 `ansible-playbook` 命令，並將 `myrg` 取代為您的資源群組名稱，以執行劇本：

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

由於劇本有 `debug` 區段，`ansible-playbook` 命令將會列印每個 VM 的 IP 位址。 請複製這些 IP 位址以供後續使用。

![虛擬機器 IP 位址](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>連線至這兩個 VM

在本節中，您會連線至每個 VM。 如上一節所述，字串 `Image A` 和 `Image B` 會模擬兩個 VM 具有不同組態的情形。

使用上一節中的 IP 位址連線至這兩個 VM：

![虛擬機器 A 的螢幕擷取畫面](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![虛擬機器 B 的螢幕擷取畫面](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>從每個 VM 建立映像

此時，您會有組態 (其 `index.html` 檔案) 略有不同的兩個 VM。

本節中的劇本程式碼會為每個 VM 建立自訂映像：

* `image_vmforimageA` - 為在其首頁上顯示 `Image A` 的 VM 建立的自訂映像。
* `image_vmforimageB` - 為在其首頁上顯示 `Image B` 的 VM 建立的自訂映像。

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml)並將其儲存至 `capture-images.yml`。
* 建立名為 `capture-images.yml` 的新檔案，並在其中複製下列內容：

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

使用 `ansible-playbook` 命令，並將 `myrg` 取代為您的資源群組名稱，以執行劇本：

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>使用映像 A 建立擴展集

在本節中，會使用劇本設定下列 Azure 資源：

* 公用 IP 位址
* 負載平衡器
* 參考 `image_vmforimageA` 的擴展集

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml)並將其儲存至 `create-vmss.yml`。
* 建立名為 `create-vmss.yml` 的新檔案，並在其中複製下列內容：

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

使用 `ansible-playbook` 命令，並將 `myrg` 取代為您的資源群組名稱，以執行劇本：

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

由於劇本有 `debug` 區段，`ansible-playbook` 命令將會列印擴展集的 IP 位址。 請複製此 IP 位址以供後續使用。

![公用 IP 位址](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>連線至擴展集

在本節中，您會連線至擴展集。 

使用上一節中的 IP 位址連線至擴展集。

如上一節所述，字串 `Image A` 和 `Image B` 會模擬兩個 VM 具有不同組態的情形。

擴展集會參考名為 `image_vmforimageA` 的自訂映像。 自訂映像 `image_vmforimageA` 是從在其首頁顯示 `Image A` 的 VM 建立的。

因此，您會看到顯示 `Image A` 的首頁：

![此擴展集與第一個 VM 相關聯。](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

繼續進行下一節時，請將您的瀏覽器視窗保持開啟。

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>變更擴展集中的自訂映像，並將執行個體升級

本節中的劇本程式碼會變更擴展集的映像 - 從 `image_vmforimageA` 到 `image_vmforimageB`。 此外也會更新擴展集目前部署的所有虛擬機器。

有兩種方式可取得範例劇本：

* [下載劇本](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml)並將其儲存至 `update-vmss-image.yml`。
* 建立名為 `update-vmss-image.yml` 的新檔案，並在其中複製下列內容：

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

使用 `ansible-playbook` 命令，並將 `myrg` 取代為您的資源群組名稱，以執行劇本：

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

返回瀏覽器並重新整理頁面。 

您會看到虛擬機器的基礎自訂映像已更新。

![此擴展集與第二個 VM 相關聯](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>清除資源

如果不再需要本文中建立的資源，請予以刪除。 

將下列程式碼儲存為 `cleanup.yml`：

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

使用 `ansible-playbook` 命令執行劇本：

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible)