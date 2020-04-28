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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77134537"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中指定實驗室虛擬機器的資源群組

身為實驗室擁有者，您可以將實驗室虛擬機器設定在特定資源群組中建立。 此功能可在下列情況中協助您：

- 透過訂用帳戶中的實驗室建立的資源群組較少。
- 讓您的實驗室在您所設定的一組固定資源群組內運作。
- 解決在 Azure 訂用帳戶內建立資源群組所需的限制和核准。
- 將您所有的實驗室資源合併在單一資源群組中，以簡化追蹤這些資源，並套用[原則](../governance/policy/overview.md)來管理資源群組層級的資源。

透過這項功能，您可以使用腳本，在您的 Azure 訂用帳戶內為您所有的實驗室 Vm 指定新的或現有的資源群組。 目前，Azure DevTest Labs 透過 API 支援這項功能。

> [!NOTE]
> 當您在 DevTest Labs 中建立實驗室時，適用所有訂用帳戶限制。 將實驗室視為您訂用帳戶中的任何其他資源。 在資源群組的情況下，限制為[每個訂用帳戶980個資源群組](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
請遵循下列步驟，為在實驗室中建立的所有 Vm 指定資源群組。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側導覽功能表上的 [**所有服務**]。 
3. 從清單中選取 [ **DevTest Labs** ]。
4. 從實驗室清單中，選取您的**實驗室**。  
5. 在左側功能表的 [設定] 區段中，選取 [**設定****和原則**]。 
6. 選取左側功能表上的 [**實驗室設定**]。 
7. 選取**一個資源群組中的所有虛擬機器**。 
8. 在下拉式清單中選取現有的資源群組（或）選取 [**新建**]，輸入資源群組的**名稱**，然後選取 **[確定]**。 

    ![選取所有實驗室 Vm 的資源群組](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>使用 PowerShell 
下列範例顯示如何使用 PowerShell 腳本，在新的資源群組中建立所有實驗室虛擬機器。

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

使用下列命令叫用腳本。 ResourceGroup. ps1 是包含上述腳本的檔案：

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
如果您使用 Azure Resource Manager 範本來建立實驗室，請使用範本之 [實驗室屬性] 區段中的 [ **vmCreationResourceGroupId** ] 屬性，如下列範例所示：

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>用於設定實驗室 Vm 之資源群組的 API
使用此 API 時，您會有下列選項作為實驗室擁有者：

- 為所有虛擬機器選擇**實驗室的資源群組**。
- 針對所有虛擬機器，選擇實驗室的資源群組以外的**現有資源群組**。
- 為所有虛擬機器輸入**新的資源組**名。
- 繼續使用現有的行為，其中會為實驗室中的每個 VM 建立資源群組。
 
此設定適用於實驗室中建立的新虛擬機器。 在自己的實驗室中，您在自己的資源群組中建立的舊版 Vm 不會受到影響。 在您的實驗室中建立的環境會繼續保留在自己的資源群組中。

如何使用此 API：
- 使用 API 版本**2018_10_15_preview**。
- 如果您指定新的資源群組，請確定您對訂用帳戶中的**資源群組具有寫入權限**。 如果您沒有寫入權限，在指定的資源群組中建立新的虛擬機器將會失敗。
- 在使用 API 時，請傳入**完整的資源群組識別碼**。 例如： `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 。 請確定資源群組與實驗室位於相同的訂用帳戶中。 


## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
