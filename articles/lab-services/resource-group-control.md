---
title: 在 Azure DevTest Labs 中指定 VM 的資源群組 | Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中指定實驗室內 VM 的資源群組。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134537"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中指定實驗室虛擬機器的資源群組

身為實驗室擁有者，您可以將實驗室虛擬機器設定在特定資源群組中建立。 此功能可在下列情況中協助您：

- 透過訂用帳戶中的實驗室建立的資源群組較少。
- 讓實驗室在您配置的一組固定資源組中運行。
- 解決在 Azure 訂用帳戶內建立資源群組所需的限制和核准。
- 將所有實驗室資源整合到單個資源組中，以簡化跟蹤這些資源並應用[策略](../governance/policy/overview.md)來管理資源組級別的資源。

使用此功能，可以使用腳本為所有實驗室 VM 在 Azure 訂閱中指定新的資源組或現有資源組。 目前，Azure 開發人員測試實驗室通過 API 支援此功能。

> [!NOTE]
> 在 DevTest 實驗室中創建實驗室時，所有訂閱限制都適用。 將實驗室視為訂閱中的任何其他資源。 對於資源組，限制[為每個訂閱 980 個資源組](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
按照以下步驟為實驗室中創建的所有 VM 指定資源組。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇左側導航功能表上**的所有服務**。 
3. 從清單中選擇**開發人員測試實驗室**。
4. 從實驗室清單中，選擇您的**實驗室**。  
5. 在左側功能表的 **"設置"** 部分中選擇 **"配置"和"策略**"。 
6. 選擇左側功能表上的**實驗室設置**。 
7. 選擇**一個資源組中的所有虛擬機器**。 
8. 在下拉清單中選擇現有資源組（或）選擇 **"創建新**"，輸入資源組**的名稱**，然後選擇 **"確定**"。 

    ![為所有實驗室 VM 選擇資源組](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下面的示例演示如何使用 PowerShell 腳本創建新資源組中的所有實驗室虛擬機器。

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

使用以下命令調用腳本。 ResourceGroup.ps1 是包含上述腳本的檔：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本
如果使用 Azure 資源管理器範本創建實驗室，請使用範本的實驗室屬性部分中的**vmCreateResourceGroupId**屬性，如以下示例所示：

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>為實驗室 VM 配置資源組的 API
使用此 API 時，您具有以下作為實驗室擁有者的選項：

- 為所有虛擬機器選擇**實驗室的資源組**。
- 為所有虛擬機器選擇實驗室資源組以外的**現有資源組**。
- 輸入所有虛擬機器**的新資源組**名稱。
- 繼續使用現有行為，其中為實驗室中的每個 VM 創建一個資源組。
 
此設定適用於實驗室中建立的新虛擬機器。 在自己的資源組中創建的實驗室中較舊的 VM 不受影響。 在實驗室中創建的環境將繼續保留在自己的資源組中。

如何使用此 API：
- 使用 API 版本**2018_10_15_preview**。
- 如果指定新的資源組，請確保對訂閱中**的資源組具有寫入權限**。 如果缺少寫入權限，請在指定的資源組中創建新虛擬機器將失敗。
- 在使用 API 時，請傳入**完整的資源群組識別碼**。 例如：`/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`。 確保資源組與實驗室處於同一訂閱中。 


## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
