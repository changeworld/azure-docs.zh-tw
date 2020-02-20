---
title: 教學課程 - 使用 Ansible 設定 Azure 資源的動態清查
description: 了解如何使用 Ansible 來管理 Azure 動態清查
keywords: ansible, azure, devops, bash, cloudshell, 動態清查
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: d2ebf202cfc9f94b28fc7a512e1fea452401aec6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193594"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>教學課程：使用 Ansible 設定 Azure 資源的動態清查

您可以使用 Ansible 從各種來源 (包括如 Azure 等雲端來源) 將清查資訊提取至動態清查  。 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 設定兩個測試虛擬機器。 
> * 標記其中一個虛擬機器
> * 在標記的虛擬機器上安裝 Nginx
> * 設定包含已設定 Azure 資源的動態清查

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>建立測試 VM

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 開啟 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

1. 建立 Azure 資源群組來保存此教學課程中的虛擬機器。

    > [!IMPORTANT]  
    > 您在此步驟中建立的 Azure 資源群組必須使用全部都是小寫的名稱。 否則將無法產生動態清查。

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. 使用下列其中一項技術，在 Azure 上建立兩個 Linux 虛擬機器：

    - **Ansible 腳本** - [使用 Ansible 在 Azure 中建立基本虛擬機器](./ansible-create-vm.md)一文中說明如何從 Ansible 腳本建立虛擬機器。 如果您使用腳本來定義一個或兩個虛擬機器，請確定您是使用 SSH 連線而不是使用密碼。

    - **Azure CLI** - 在 Cloud Shell 中發出以下每一個命令，以建立兩個虛擬機器：

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>標記 VM

您可以依使用者定義的類別[使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli)。 

### <a name="using-ansible-version--28"></a>使用 < 2.8 的 Ansible 版本
輸入下列 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) 命令來標記具有 `nginx` 金鑰的虛擬機器 `ansible-inventory-test-vm1`：

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>使用 >= 2.8 的 Ansible 版本
輸入下列 [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) 命令來標記具有 `Ansible=nginx` 金鑰的虛擬機器 `ansible-inventory-test-vm1`：

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>產生動態清查

一旦您定義虛擬機器 (並標記) 後，就可以產生動態清查。

### <a name="using-ansible-version--28"></a>使用 < 2.8 的 Ansible 版本

Ansible 提供名為 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) 的 Python 指令碼，會產生 Azure 資源的動態清單。 下列步驟將引導您使用 `azure_rm.py` 指令碼，以連線到您的兩部測試 Azure 虛擬機器：

1. 使用 GNU `wget` 命令來取出 `azure_rm.py` 指令碼：

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. 使用 `chmod` 命令將存取權限變更為 `azure_rm.py` 指令碼。 下列命令會使用 `+x` 參數，以允許執行 (執行) 指定的檔案 (`azure_rm.py`)：

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. 使用 [ansible 命令](https://docs.ansible.com/ansible/2.4/ansible.html)連線到您的資源群組： 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. 一旦連線後，您會看到類似下列輸出的結果：

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>>= 2.8 的 Ansible 版本

從 Ansible 2.8 開始，Ansible 提供 [Azure 動態清查外掛程式](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py)。 下列步驟會引導您使用此外掛程式：

1. 清查外掛程式需要組態檔。 組態檔的結尾必須是 `azure_rm`，且必須具有副檔名 `yml` 或 `yaml`。 在此教學課程範例中，請將下列劇本儲存為 `myazure_rm.yml`：

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. 執行下列命令以在資源群組中偵測 VM：

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. 執行上述命令時，您可能會收到下列錯誤：

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    如果您收到「主機金鑰驗證」錯誤，請將下面這一行新增至 Ansible 組態檔。 Ansible 組態檔位於 `/etc/ansible/ansible.cfg` 或 `~/.ansible.cfg`。

    ```bash
    host_key_checking = False
    ```

1. 執行劇本後，您會看到類似下列輸出的結果：
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>啟用 VM 標記

### <a name="if-youre-using-ansible--28"></a>如果您使用 Ansible < 2.8，

- 設定標記後，您必須「啟用」該標記。 啟用標記的方式之一，是透過 `export` 命令將標記匯出至環境變數 `AZURE_TAGS`：

    ```azurecli-interactive
    export AZURE_TAGS=nginx
    ```
    
- 執行以下命令：

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    您現在只會看到一個虛擬機器 (標記符合匯出至 `AZURE_TAGS` 環境變數之值的虛擬機器)：

    ```Output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>如果您使用 Ansible >= 2.8

- 請執行命令 `ansible-inventory -i myazure_rm.yml --graph` 取得下列輸出：

    ```Output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- 您也可以執行下列命令測試 Nginx VM 的連線：
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>設定已標記 VM 上的 Nginx

標記的目的是要讓您能夠快速且輕鬆地使用您虛擬機器的子群組。 例如，假設您只需要在已指派 `nginx` 標記的虛擬機器上安裝 Nginx。 下列步驟說明可如何輕鬆完成：

1. 建立名為 `nginx.yml` 的檔案：

   ```azurecli-interactive
   code nginx.yml
   ```

1. 將下列範例程式碼貼到編輯器中：

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. 儲存檔案並結束編輯器。

1. 使用 `ansible-playbook` 命令執行劇本：

   - Ansible < 2.8：

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2.8：

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. 執行劇本後，您會看到類似下列結果的輸出：

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>測試 Nginx 安裝

本節說明的一項技術，可測試 Nginx 已安裝在您的虛擬機器上。

1. 使用 [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) 命令來取出 `ansible-inventory-test-vm1` 虛擬機器的 IP 位址。 接著，傳回值 (虛擬機器的 IP 位址) 會用來作為連線到虛擬機器之 SSH 命令的參數。

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. 在連線至 `ansible-inventory-test-vm1` 虛擬機器後，執行 [nginx-v](https://nginx.org/en/docs/switches.html) 命令以確認 Nginx 是否已安裝。

    ```azurecli-interactive
    nginx -v
    ```

1. 一旦您執行 `nginx -v` 命令後，會看到 Nginx 版本 (第二行)，指出已安裝 Nginx。

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. 按一下 `<Ctrl>D` 鍵盤組合，將 SSH 工作階段中斷連線。

1. 為 `ansible-inventory-test-vm2` 虛擬機器執行上述步驟時會產生參考訊息，指出您可以取得 Nginx (表示您此時未安裝) 的位置：

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [快速入門：使用 Ansible 在 Azure 中設定 Linux 虛擬機器](./ansible-create-vm.md)