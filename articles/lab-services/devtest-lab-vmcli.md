---
title: 使用 Azure CLI 在開發人員測試實驗室中創建和管理虛擬機器
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167061"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器
此快速入門將指導您完成實驗室中的開發電腦的創建、啟動、連接、更新和清理。 

開始之前：

* 若尚未建立實驗室，請參閱[這裡](devtest-lab-create-lab.md)的指示。

* [安裝 Azure CLI](/cli/azure/install-azure-cli)。 若要開始，請執行 az login 建立 Azure 連線。 

## <a name="create-and-verify-the-virtual-machine"></a>建立並確認虛擬機器 
在執行 DevTest Labs 相關命令之前，請使用以下`az account set`命令設置相應的 Azure 上下文：

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

創建虛擬機器的命令是： `az lab vm create`。 實驗室、實驗室名稱和虛擬機器名稱的資源組都是必需的。 其餘參數會根據虛擬機器的類型而變化。

以下命令從 Azure 市場位置創建基於 Windows 的映射。 映射的名稱與使用 Azure 門戶創建虛擬機器時看到的名稱相同。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

以下命令基於實驗室中可用的自訂映射創建虛擬機器：

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**圖像類型**參數已由**庫**更改為**自訂**。 映射的名稱與在 Azure 門戶中創建虛擬機器時看到的內容匹配。

以下命令從具有 ssh 身份驗證的市場映射創建 VM：

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

還可以通過將**映射類型**參數設置為**公式**，根據公式創建虛擬機器。 如果需要為虛擬機器選擇特定的虛擬網路，請使用**vnet 名稱**和**子網**參數。 有關詳細資訊，請參閱創建[的 az 實驗室 vm。](/cli/azure/lab/vm#az-lab-vm-create)

## <a name="verify-that-the-vm-is-available"></a>確認有可用的 VM。
在啟動`az lab vm show`並連接到 VM 之前，使用 命令驗證 VM 是否可用。 

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
以下示例命令啟動 VM：

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

連接到 VM：[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 或[遠端桌面](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>更新虛擬機器
以下示例命令將工件應用於 VM：

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

### <a name="list-artifacts-available-in-the-lab"></a>列出實驗室中可用的專案

要列出實驗室中 VM 中可用的專案，運行以下命令。

**雲殼 - PowerShell**： 請注意在 $$expand （ 即 '$expand） 之前使用回勾 （\`） ：

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**雲殼 - Bash**： 注意在命令\\的 $ 前面使用斜杠 （ ） 字元。 

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
以下示例命令將停止 VM。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

刪除 VM。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>後續步驟
請參閱以下內容[：Azure 開發人員測試實驗室的 Azure CLI 文檔](/cli/azure/lab?view=azure-cli-latest)。 
