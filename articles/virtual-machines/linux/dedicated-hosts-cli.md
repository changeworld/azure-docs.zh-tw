---
title: 使用 CLI 將 Linux VM 部署至專用主機
description: 使用 Azure CLI 將 VM 部署到專用主機。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: dc772368de1a0f7d8a7d4f44b47ecafda70f0a70
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714843"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>使用 Azure CLI 將 VM 部署到專用主機
 

本文會引導您瞭解如何建立 Azure [專用主機](dedicated-hosts.md)來裝載您的虛擬機器 (VM)。 

請確定您已安裝 Azure CLI 2.0.70 或更新版本，並已使用 `az login`登入 Azure 帳戶。 


## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。
- 專用主機可用的大小和硬體類型因區域而異。 若要深入瞭解，請參閱主機[定價頁面](https://aka.ms/ADHPricing)。

## <a name="create-resource-group"></a>建立資源群組 
Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 az group create 建立資源群組。 下列範例會在「East US」(美國東部) 位置建立名為 myResourceGroup 的資源群組。

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>列出區域中可用的主機 SKU
並非所有的主機 SKU 都適用於所有區域和可用性區域。 

在您開始佈建專用主機之前，列出可用的主機及任何供應項目的限制。 

```bash
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>建立主機群組 

**主機群組**是代表專用主機集合的資源。 您會在區域和可用性區域中建立主機群組，並在其中新增主機。 在規劃高可用性時，還有其他選項。 您可以使用下列其中一個或兩個選項搭配您的專用主機： 
- 跨越多個可用性區域。 在此情況下，您必須在想要使用的每個區域中都有一個主機群組。
- 跨越多個對應至實體機架的容錯網域。 
 
不論是哪一種情況，您都必須為主機群組提供容錯網域計數。 如果您不想在您的群組中跨越容錯網域，請使用容錯網域計數 1。 

您也可以決定同時使用可用性區域和容錯網域。 

在此範例中，我們將使用 [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 建立一個同時使用可用性區域和容錯網域的主機群組。 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>其他範例

您也可以使用 [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 在可用性區域 1 中建立主機群組 (且沒有容錯網域)。

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
下列範例使用 [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 僅使用容錯網域來建立主機群組 (在不支援可用性區域的區域中使用)。 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>建立主機 

現在，讓我們在主機群組中建立專用主機。 除了主機的名稱之外，您還必須提供主機的 SKU。 主機 SKU 會擷取支援的 VM 系列，以及專用主機的硬體世代。  

如需主機 SKU 和定價的詳細資訊，請參閱 [Azure 專用主機定價](https://aka.ms/ADHPricing)。

使用 [az vm host create](/cli/azure/vm/host#az-vm-host-create) 建立主機。 如果您為主機群組設定容錯網域計數，系統會要求您指定主機的容錯網域。  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>建立虛擬機器 
使用 [az vm create](/cli/azure/vm#az-vm-create) 在專用主機內建立虛擬機器。 如果您在建立主機群組時指定了可用性區域，則必須在建立虛擬機器時使用相同的區域。

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> 如果您在沒有足夠資源的主機上建立虛擬機器，虛擬機器將會以「失敗」狀態建立。 


## <a name="check-the-status-of-the-host"></a>檢查主機的狀態

您可以使用 [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view)，檢查主機的健全狀態，以及您仍然可以部署到主機的虛擬機器數目。

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 輸出類似如下範例：
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>以範本形式匯出 
如果您現在想要使用相同的參數來建立其他開發環境，或想要建立與其相符的生產環境，可以匯出範本。 Resource Manager 可使用定義了所有環境參數的 JSON 範本。 您可以藉由參考此 JSON 範本來建置整個環境。 您可以手動建立 JSON 範本，或將現有的環境匯出來為您建立 JSON 範本。 使用 [az group export](/cli/azure/group#az-group-export) 匯出您的資源群組。

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

此命令會在您目前的工作目錄中建立 `myDHResourceGroup.json` 檔案。 當您從這個範本建立環境時，系統會提示您輸入所有資源名稱。 您可以藉由將 `--include-parameter-default-value` 參數新增到 `az group export` 命令中，在您的範本檔案中填入這些名稱。 請編輯您的 JSON 範本以指定資源名稱，或 建立 parameters.json 檔案 來指定資源名稱。
 
若要從您的範本建立環境，請使用 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)。

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>清除 

即使您沒有部署虛擬機器，仍會向您收取專用主機的費用。 您應該刪除目前未使用的任何主機以節省成本。  

只有當不再有任何虛擬機器使用主機時，才能刪除主機。 使用 [az vm delete](/cli/azure/vm#az-vm-delete) 刪除 VM。

```bash
az vm delete -n myVM -g myDHResourceGroup
```

刪除 VM 之後，您可以使用 [az vm host delete](/cli/azure/vm/host#az-vm-host-delete) 刪除主機。

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
刪除您的所有主機之後，您可以使用 [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete) 刪除主機群組。  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
您也可以在單一命令中刪除整個資源群組。 這麼做會刪除群組中建立的所有資源，包括所有 VM、主機和主機群組。
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[專用主機](dedicated-hosts.md)概觀。

- 您也可以使用 [Azure 入口網站](dedicated-hosts-portal.md)來建立專用主機。

- [這裡](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有範例範本，範例中使用區域和容錯網域來獲得區域中的最大復原。
