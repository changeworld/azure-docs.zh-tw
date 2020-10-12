---
title: 使用 Azure PowerShell 從 VHD 檔案建立自訂映射
description: 使用 PowerShell 在 Azure DevTest Labs 中從 VHD 檔案自動建立自訂映像
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288976"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>使用 PowerShell 從 VHD 檔案建立自訂映像

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>逐步指示

下列步驟將逐步引導您使用 PowerShell 從 VHD 檔案建立自訂映像：

1. 在 PowerShell 提示字元中，使用下列對 **disconnect-azaccount 指令程式** 的呼叫來登入您的 Azure 帳戶。

    ```powershell
    Connect-AzAccount
    ```

1.  藉由呼叫 **>select-azsubscription** Cmdlet 來選取所需的 Azure 訂用帳戶。 以有效的 Azure 訂用帳戶 ID 取代 **$subscriptionId** 變數的下列預留位置。

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  藉由呼叫 **get-azresource** Cmdlet 來取得實驗室物件。 以您環境的適當值取代 **$labRg** 和 **$labName** 變數的下列預留位置。

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  以您所上傳 VHD 檔案的 URI 取代 **$vhdUri** 變數的下列預留位置。 您可以從 Azure 入口網站中儲存體帳戶的 Blob 刀鋒視窗取得 VHD 檔案的 URI。

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  使用 **>new-azresourcegroupdeployment** Cmdlet 來建立自訂映射。 將 **$customImageName** 和 **$customImageDescription** 變數的下列預留位置取代為對您環境有意義的名稱。

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>可從 VHD 檔案建立自訂映像的 PowerShell 指令碼

您可以使用下列 PowerShell 指令碼從 VHD 檔案建立自訂映像。 以符合您需求的適當值取代預留位置 (開頭和結尾是角括弧)。

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>相關部落格文章

- [自訂映像或公式？](./devtest-lab-faq.md#blog-post)
- [在 Azure DevTest Labs 之間複製自訂映像](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>接下來的步驟

- [將 VM 新增到實驗室](devtest-lab-add-vm.md)
