---
title: 使用 CLI 將 Linux VM 部署到專用主機
description: 使用 Azure CLI 將 VM 部署到專用主機。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127690"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>使用 Azure CLI 將 VM 部署到專用主機
 

本文將指導您如何創建 Azure[專用主機](dedicated-hosts.md)來託管虛擬機器 （VM）。 

請確保已安裝 Azure CLI 版本 2.0.70 或更高版本，並使用 登錄到 Azure`az login`帳戶。 


## <a name="limitations"></a>限制

- 專用主機當前不支援虛擬機器縮放集。
- 專用主機可用的大小和硬體類型因地區而異。 請參閱主機[定價頁面](https://aka.ms/ADHPricing)以瞭解更多資訊。
 

## <a name="create-resource-group"></a>建立資源群組 
Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 az group create 建立資源群組。 下面的示例*在美國東部*位置創建名為*myDHResourceGroup*的資源組。

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>建立主機群組 

**主機組**是表示專用主機集合的資源。 在區域和可用性區域中創建主機組，並將主機添加到其中。 在規劃高可用性時，還有其他選項。 您可以將以下一個或兩個選項用於專用主機： 
- 跨多個可用性區域的跨範圍。 在這種情況下，您需要在每個要使用的區域中具有一個主機組。
- 跨映射到物理機架的多個容錯域。 
 
在這兩種情況下，都需要為主機組提供容錯域計數。 如果不想跨越組中的容錯域，請使用容錯域計數 1。 

您還可以決定同時使用可用性區域和容錯域。 

在此示例中，我們將使用 az [vm 主機組創建](/cli/azure/vm/host/group#az-vm-host-group-create)，使用可用性區域和容錯域創建主機組。 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>其他範例

您還可以使用 az [vm 主機組創建](/cli/azure/vm/host/group#az-vm-host-group-create)在可用性區域 1（無容錯域）中創建主機組。

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
以下使用[az vm 主機組創建](/cli/azure/vm/host/group#az-vm-host-group-create)，僅使用容錯域創建主機組（用於不支援可用性區域的區域）。 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>創建主機 

現在，讓我們在主機組中創建一個專用主機。 除了主機的名稱外，還需要為主機提供 SKU。 主機 SKU 捕獲支援的 VM 系列以及專用主機的硬體生成。  

有關主機 SKU 和定價的詳細資訊，請參閱[Azure 專用主機定價](https://aka.ms/ADHPricing)。

使用[az vm 主機創建](/cli/azure/vm/host#az-vm-host-create)創建主機。 如果為主機組設置容錯域計數，系統將要求您為主機指定容錯域。  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>建立虛擬機器 
使用[az vm 創建](/cli/azure/vm#az-vm-create)在專用主機中創建虛擬機器。 如果在創建主機組時指定了可用性區域，則創建虛擬機器時需要使用相同的區域。

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
> 如果在沒有足夠的資源的主機上創建虛擬機器，則虛擬機器將以 FAILED 狀態創建。 


## <a name="check-the-status-of-the-host"></a>檢查主機的狀態

您可以使用[az vm 主機獲取實例視圖](/cli/azure/vm/host#az-vm-host-get-instance-view)檢查主機運行狀況狀態以及仍可部署到主機的虛擬機器數。

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
如果要創建具有相同參數的其他開發環境或與其匹配的生產環境，則可以匯出範本。 Resource Manager 可使用定義了所有環境參數的 JSON 範本。 您可以藉由參考此 JSON 範本來建置整個環境。 您可以手動構建 JSON 範本或匯出現有環境，為您創建 JSON 範本。 使用[az 組匯出](/cli/azure/group#az-group-export)匯出資源組。

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

此命令會在您目前的工作目錄中建立 `myDHResourceGroup.json` 檔案。 當您從這個範本建立環境時，系統會提示您輸入所有資源名稱。 您可以藉由將 `--include-parameter-default-value` 參數新增到 `az group export` 命令中，在您的範本檔案中填入這些名稱。 請編輯您的 JSON 範本以指定資源名稱，或 建立 parameters.json 檔案 來指定資源名稱。
 
要從範本創建環境，請使用[az 組部署創建](/cli/azure/group/deployment#az-group-deployment-create)。

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>清除 

即使未部署虛擬機器，也會為專用主機向您收費。 您應該刪除當前不使用的任何主機以節省成本。  

僅當不再有虛擬機器使用主機時，才能刪除主機。 使用[az vm 刪除](/cli/azure/vm#az-vm-delete)VM 刪除 VM。

```bash
az vm delete -n myVM -g myDHResourceGroup
```

刪除 VM 後，可以使用[az vm 主機刪除](/cli/azure/vm/host#az-vm-host-delete)主機。

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
刪除所有主機後，可以使用[az vm 主機組刪除主機組](/cli/azure/vm/host/group#az-vm-host-group-delete)。  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
您還可以在單個命令中刪除整個資源組。 這將刪除組中創建的所有資源，包括所有 VM、主機和主機組。
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>後續步驟

- 有關詳細資訊，請參閱[專用主機](dedicated-hosts.md)概述。

- 您還可以使用[Azure 門戶](dedicated-hosts-portal.md)創建專用主機。

- [此處](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例範本，它同時使用區域和容錯域，以實現區域中的最大恢復能力。