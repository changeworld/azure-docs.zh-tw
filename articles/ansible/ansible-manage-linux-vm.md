---
title: 快速入門 - 使用 Ansible 在 Azure 中管理 Linux 虛擬機器
description: 在本快速入門中，您將了解如何使用 Ansible 在 Azure 中管理 Linux 虛擬機器
keywords: ansible、azure、devops、bash、cloudshell、腳本、bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202430"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>快速入門：使用 Ansible 在 Azure 中管理 Linux 虛擬機器

Ansible 可讓您將環境中的資源部署和設定自動化。 在本文中，您將使用 Ansible 劇本來啟動和停止 Linux 虛擬機器。 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>停止虛擬機器

在本節中，您將使用 Ansible 來解除配置 (停止) Azure 虛擬機器。

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 開啟 [Cloud Shell](/azure/cloud-shell/overview)。

1. 建立名為 `azure-vm-stop.yml` 的檔案，並在編輯器中將其開啟：

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. 將下列範例程式碼貼到編輯器中：

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. 以您的值取代 `{{ resource_group_name }}` 和 `{{ vm_name }}` 預留位置。

1. 儲存檔案並結束編輯器。

1. 使用 `ansible-playbook` 命令執行劇本：

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. 執行劇本後，您會看到類似下列結果的輸出：

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>啟動虛擬機器

在本節中，您將使用 Ansible 來啟動已解除配置 (停止) 的 Azure 虛擬機器。

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 開啟 [Cloud Shell](/azure/cloud-shell/overview)。

1. 建立名為 `azure-vm-start.yml` 的檔案，並在編輯器中將其開啟：

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. 將下列範例程式碼貼到編輯器中：

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. 以您的值取代 `{{ resource_group_name }}` 和 `{{ vm_name }}` 預留位置。

1. 儲存檔案並結束編輯器。

1. 使用 `ansible-playbook` 命令執行劇本：

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. 執行劇本後，您會看到類似下列結果的輸出：

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [教學課程：使用 Ansible 管理 Azure 動態清查](./ansible-manage-azure-dynamic-inventories.md)