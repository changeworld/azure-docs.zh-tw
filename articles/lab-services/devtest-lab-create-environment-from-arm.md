---
title: 使用範本創建多 VM 環境和 PaaS 資源
description: 了解如何從 Azure Resource Manager 範本在 Azure DevTest Labs 中建立多個 VM 環境和 PaaS 資源
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169639"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源

Azure 開發人員測試實驗室環境允許使用者在實驗室範圍內以一致的方式輕鬆部署複雜的基礎結構。 可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/template-syntax.md)使用 DevTest 實驗室中的資源集創建環境。 這些環境可以包含資源管理器範本可以創建的任何 Azure 資源。

通過使用[Azure 門戶](https://portal.azure.com)，一[次可以輕鬆地將一個虛擬機器 （VM） 添加到](devtest-lab-add-vm.md)實驗室。 但是，多層 Web 應用或 SharePoint 伺服器場等方案需要一種機制，在單個步驟中創建多個 VM。 通過使用 Azure 資源管理器範本，可以定義 Azure 解決方案的基礎結構和配置，並反復部署處於一致狀態的多個 VM。

Azure 資源管理器範本還提供以下優點：

- Azure 資源管理器範本直接從 GitHub 或 Azure 存儲庫原始程式碼管理存儲庫載入。
- 使用者可以通過從 Azure 門戶中選取已配置的 Azure 資源管理器範本來創建環境，就像使用其他類型的 VM[基](devtest-lab-comparing-vm-base-image-types.md)一樣。
- 可以從 Azure 資源管理器範本預配 Azure PaaS 資源以及環境中的 IaaS VM。
- 除了其他類型的基創建的各個 VM 之外，您還可以跟蹤實驗室中環境的成本。 PaaS 資源將創建並顯示在成本跟蹤中。 不過，VM 自動關機不適用於 PaaS 資源。

要瞭解有關使用資源管理器範本在單個操作中部署、更新或刪除許多實驗室資源的好處的詳細資訊，請參閱[使用資源管理器範本的好處](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)。

> [!NOTE]
> 使用資源管理器範本作為創建實驗室 VM 的基礎時，創建多個 VM 或單個 VM 之間存在一些差異。 有關詳細資訊，請參閱[使用虛擬機器的 Azure 資源管理器範本](devtest-lab-use-resource-manager-template.md)。
>

## <a name="use-devtest-labs-public-environments"></a>使用開發人員測試實驗室公共環境
Azure 開發人員測試實驗室具有[Azure 資源管理器範本的公共存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments)，可用於創建環境，而無需自己連接到外部 GitHub 源。 此公共存儲庫類似于 Azure 門戶中為所創建的每個實驗室提供的專案的公共存儲庫。 通過環境存儲庫，您可以快速使用具有很少輸入參數的預創作環境範本。 這些範本為實驗室內的 PaaS 資源提供了平穩的入門體驗。

在公共存儲庫中，DevTest Labs 團隊和其他團隊創建並共用了常用範本，如 Azure Web 應用、服務結構群集和開發 SharePoint 伺服器場環境。 您可以直接使用這些範本，也可以自訂這些範本以滿足您的需要。 如需詳細資訊，請參閱[在 DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)。 創建自己的範本後，可以將其存儲在此存儲庫中，以便與他人共用它們，或設置自己的 Git 存儲庫。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>創建您自己的範本存儲庫

作為基礎架構即代碼和配置即代碼的最佳做法之一，您應該在原始程式碼管理中管理環境範本。 Azure 開發人員測試實驗室遵循此做法，直接從 GitHub 或 Azure 存儲庫存儲庫載入所有 Azure 資源管理器範本。 因此，您可以在整個發佈週期（從測試環境到生產環境）使用資源管理器範本。

在存儲庫中組織 Azure 資源管理器範本需要遵循以下幾種規則：

- 您必須命名主範本檔*azuredeploy.json*。

- 如果要使用參數檔中定義的參數值，參數檔必須命名為*azuredeploy.parameter.json*。

  您可以使用 `_artifactsLocation` 和 `_artifactsLocationSasToken` 參數來建構 parametersLink URI 值，讓 DevTest Labs 自動管理巢狀範本。 有關詳細資訊，請參閱[部署嵌套 Azure 資源管理器範本以測試環境](deploy-nested-template-environments.md)。

- 可以定義中繼資料以在名為*metadata.json*的檔中指定範本顯示名稱和說明，如下所示：

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![金鑰 Azure Resource Manager 範本檔案](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>將範本存儲庫添加到實驗室

創建和配置存儲庫後，可以使用 Azure 門戶將其添加到實驗室：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選擇所需的實驗室。
1. 在實驗室的 **"概述"** 窗格中，選擇 **"配置"和"策略**"。

   ![組態和原則](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 從 **"配置和策略**設置"清單中，選擇**存儲庫**。 **公共專案存儲庫**存儲庫將自動生成所有實驗室，並連接到[DevTest Labs 公共 GitHub 存儲庫](https://github.com/Azure/azure-devtestlab)。

1. 要添加 Azure 資源管理器範本存儲庫，請選擇"**添加**"。

   ![公用儲存機制](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 在 **"存儲庫"** 窗格中，輸入以下資訊：

   - **名稱**：輸入要在實驗室中使用的存儲庫名稱。
   - **Git 克隆 URL**： 從 GitHub 或 Azure 存儲庫輸入 Git HTTPS 克隆 URL。
   - **分支**（可選）：輸入分支名稱以訪問 Azure 資源管理器範本定義。
   - **個人訪問權杖**：輸入用於安全訪問存儲庫的個人訪問權杖。
     - 要從 Azure 存儲庫獲取權杖，請在設定檔下選擇 **"使用者設置** > **安全** > **個人訪問權杖**"。
     - 要從 GitHub 獲取權杖，請在設定檔下選擇 **"** > **設置開發人員設置** > **個人訪問權杖**"。
   - **資料夾路徑**：輸入與 Git 克隆 URI 相關的資料夾路徑，用於專案定義或 Azure 資源管理器範本定義。

1. 選取 [儲存]****。

   ![添加新存儲庫](./media/devtest-lab-create-environment-from-arm/repo-values.png)

將 Azure 資源管理器範本添加到實驗室後，實驗室使用者可以使用該範本創建環境。

## <a name="configure-access-rights-for-lab-users"></a>為實驗室使用者配置存取權限

預設情況下，實驗室使用者具有**Reader**角色，因此無法更改環境資源組中的資源。 例如，他們無法停止或啟動其資源。

要向實驗室使用者**提供參與者**角色，以便他們可以編輯其環境中的資源，請按照以下步驟操作：

1. 在[Azure 門戶](https://portal.azure.com)中，在實驗室的 **"概述"** 窗格中，選擇 **"配置"和"策略**"，然後選擇 **"實驗室設置**"。

1. 在 **"實驗室設置"** 窗格中，選擇 **"參與者**"，然後選擇 **"保存"** 以向實驗室使用者授予寫入權限。

   ![設定實驗室使用者存取權限](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

下一節將介紹從 Azure 資源管理器範本創建環境。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>從 Azure 門戶中的範本創建環境

將 Azure 資源管理器範本添加到實驗室後，實驗室使用者可以按照以下步驟在 Azure 門戶中創建環境：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。

1. 從實驗室清單中，選擇所需的實驗室。

1. 在實驗室的頁面上，選擇 **"添加**"。

1. **"選擇基本**窗格"顯示可以使用的基本映射，首先列出 Azure 資源管理器範本。 選擇所需的 Azure 資源管理器範本。

   ![選擇基底](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. 在 **"添加**"窗格中，輸入要向環境使用者顯示**的環境名稱**值。

   Azure 資源管理器範本定義輸入欄位的其餘部分。 如果範本*azuredeploy.parameter.json*檔定義預設值，則輸入欄位將顯示這些值。

   對於類型*安全字串*的參數，可以使用 Azure 金鑰保存庫中的秘密。 要瞭解如何在金鑰保存庫中存儲機密並在創建實驗室資源時使用它們，請參閱在[Azure 金鑰保存庫中存儲機密](devtest-lab-store-secrets-in-key-vault.md)。  

   ![新增窗格](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 即使範本指定了以下參數值，輸入欄位中也不會顯示以下參數值。 相反，表單顯示空白輸入欄位，其中實驗室使用者必須在創建環境時輸入值。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. 選取 [新增]**** 以建立環境。

   環境立即開始預配，狀態將顯示在 **"我的虛擬機器"** 清單中。 該實驗室會自動創建一個新資源組，以預配 Azure 資源管理器範本中定義的所有資源。

1. 創建環境後，選擇 **"我的虛擬機器**"清單中的環境以打開資源組窗格並流覽環境預配的所有資源。

   ![環境資源](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   您還可以擴展環境，以便只查看環境預配的 VM 清單。

   ![我的虛擬機器清單](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 選擇要查看可用操作的任何環境，例如應用工件、附加資料磁片、更改自動關閉時間等。

   ![環境動作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>使用 PowerShell 自動創建環境

使用 Azure 門戶將單個環境添加到實驗室是可行的，但當開發或測試方案必須創建多個環境時，自動部署是一種更好的體驗。

在繼續操作之前，請確保具有定義要創建的資源的 Azure 資源管理器範本。 [在 Git 存儲庫中添加和配置範本](#configure-your-own-template-repositories)，並將[存儲庫添加到實驗室](#add-template-repositories-to-the-lab)。

以下示例腳本在實驗室中創建環境。 注釋可説明您更好地瞭解腳本。

1. 將以下示例 PowerShell 腳本作為*deployenv.ps1*保存到硬碟。

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. 使用訂閱 Id、LabName、資源組名稱、存儲庫名稱、範本名稱（Git 存儲庫中的資料夾）和環境名稱的特定值運行腳本，如下所示。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

您還可以使用 Azure CLI 使用資源管理器範本部署資源。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)。

> [!NOTE]
> 只有具備實驗室擁有者權限的使用者才可以使用 Azure PowerShell 從 Resource Manager 範本建立 VM。 如果要使用資源管理器範本自動創建 VM，並且只有使用者許可權，則可以使用 CLI 命令[az 實驗室 vm 創建](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>開發人員測試實驗室中的資源管理器範本限制

在開發人員測試實驗室中使用資源管理器範本時，請考慮這些限制：

- 資源管理器範本不能引用大多數現有資源。 他們只能創建新資源。 如果您有在引用現有資源的 DevTest Labs 之外使用的資源管理器範本，則不能在 DevTest 實驗室中使用它們。 唯一的例外是可以引用現有的虛擬網路。

- 不能從從資源管理器範本創建的實驗室 VM 創建公式或自訂映射。

- 部署資源管理器範本時，不會評估大多數策略。

  例如，您可能具有一個實驗室策略，使用者可以只創建五個 VM。 不過，使用者可以部署會建立數十個 VM 的 Resource Manager 範本。 未評估的策略包括：

  - 每位使用者的 VM 數目

  - 每位實驗室使用者的進階 VM 數目

  - 每位實驗室使用者的進階磁碟數目

## <a name="next-steps"></a>後續步驟
- 創建 VM 後，可以通過在 VM 的管理窗格中選擇 **"連接**"連接到 VM。
- 在實驗室的 [我的虛擬機器]**** 清單中選取環境，以檢視和管理環境中的資源。
- 從[Azure 快速入門範本庫流覽 Azure 資源管理器範本](https://github.com/Azure/azure-quickstart-templates)。
