---
title: 使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器
description: 了解如何使用 Azure CLI 在 Azure DevTest Labs 中建立及管理虛擬機器
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76167061"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器
本快速入門將引導您在實驗室中建立、啟動、連接、更新和清理開發電腦。 

開始之前：

* 若尚未建立實驗室，請參閱[這裡](devtest-lab-create-lab.md)的指示。

* [安裝 Azure CLI](/cli/azure/install-azure-cli)。 若要開始，請執行 az login 建立 Azure 連線。 

## <a name="create-and-verify-the-virtual-machine"></a>建立並確認虛擬機器 
執行 DevTest Labs 相關的命令之前，請使用`az account set`命令來設定適當的 Azure 內容：

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

建立虛擬機器的命令為： `az lab vm create`。 實驗室、實驗室名稱和虛擬機器名稱的資源群組都是必要的。 其餘的引數會根據虛擬機器的類型而變更。

下列命令會從 Azure 市集中建立以 Windows 為基礎的映射。 映射的名稱與使用 Azure 入口網站建立虛擬機器時所看到的相同。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

下列命令會根據實驗室中可用的自訂映射來建立虛擬機器：

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**映射類型**引數已從**圖庫**變更為**custom**。 如果您在 Azure 入口網站中建立虛擬機器，映射的名稱會與您看到的內容相符。

下列命令會使用 ssh 驗證從 marketplace 映射建立 VM：

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

您也可以將**影像類型**參數設定為**公式**，以根據公式建立虛擬機器。 如果您需要為虛擬機器選擇特定的虛擬網路，請使用**vnet 名稱**和**子網**參數。 如需詳細資訊，請參閱[az lab vm create](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="verify-that-the-vm-is-available"></a>確認有可用的 VM。
您可以`az lab vm show`使用命令來確認 VM 可供使用，然後再進行連接。 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>啟動並連接至虛擬機器
下列範例命令會啟動 VM：

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

連接到 VM：[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 或[遠端桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虛擬機器
下列範例命令會將構件套用至 VM：

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>列出實驗室中可用的構件

若要列出實驗室中 VM 內可用的構件，請執行下列命令。

**Cloud Shell-PowerShell**：請注意在 $expand 之前使用倒引號\`（）（例如 ' $expand）：

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell-Bash**：請注意命令中 $ 前面的\\斜線（）字元的用法。 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

範例輸出： 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>停止並刪除虛擬機器    
下列範例命令會停止 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

刪除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>後續步驟
請參閱下列內容： [Azure DevTest Labs 的 Azure CLI 檔](/cli/azure/lab?view=azure-cli-latest)。 
