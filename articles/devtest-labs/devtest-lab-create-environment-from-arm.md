---
title: 使用範本建立多個 VM 環境和 PaaS 資源
description: 了解如何從 Azure Resource Manager 範本在 Azure DevTest Labs 中建立多個 VM 環境和 PaaS 資源
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88182403"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源

Azure DevTest Labs 環境可讓使用者在實驗室範圍內以一致的方式，輕鬆地部署複雜的基礎結構。 您可以使用 [Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md) ，在 DevTest Labs 中建立具有一組資源的環境。 這些環境可以包含 Resource Manager 範本可建立的任何 Azure 資源。

您可以使用[Azure 入口網站](https://portal.azure.com)，輕鬆地[將一部虛擬機器 (VM) 一次新增](devtest-lab-add-vm.md)至實驗室。 不過，多層式 web 應用程式或 SharePoint 伺服器陣列之類的案例需要一種機制，可在單一步驟中建立多個 Vm。 藉由使用 Azure Resource Manager 範本，您可以定義 Azure 解決方案的基礎結構和設定，並在一致的狀態中重複部署多個 Vm。

Azure Resource Manager 範本也提供下列優點：

- Azure Resource Manager 範本會直接從您的 GitHub 或 Azure Repos 的原始檔控制存放庫載入。
- 您的使用者可以建立環境，方法是從 Azure 入口網站挑選已設定的 Azure Resource Manager 範本，就如同使用其他 [VM 基底](devtest-lab-comparing-vm-base-image-types.md)類型一樣。
- 您可以從 Azure Resource Manager 範本，在環境中布建 Azure PaaS 資源和 IaaS Vm。
- 除了其他類型的基底建立的個別 Vm 之外，您還可以追蹤實驗室中的環境成本。 PaaS 資源會建立，並會出現在成本追蹤中。 不過，VM 自動關機不適用於 PaaS 資源。

若要深入瞭解使用 Resource Manager 範本在單一作業中部署、更新或刪除多個實驗室資源的優點，請參閱 [使用 Resource Manager 範本的優點](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)。

> [!NOTE]
> 當您使用 Resource Manager 範本作為基底來建立實驗室 Vm 時，建立多個 Vm 或單一 VM 之間會有一些差異。 如需詳細資訊，請參閱 [使用虛擬機器的 Azure Resource Manager 範本](devtest-lab-use-resource-manager-template.md)。
>

## <a name="use-devtest-labs-public-environments"></a>使用 DevTest Labs 公用環境
Azure DevTest Labs 具有 [Azure Resource Manager 範本的公用存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments) ，您可以使用這些範本來建立環境，而不需要自行連接到外部的 GitHub 來源。 此公用存放庫類似于您所建立之每個實驗室的 Azure 入口網站中可用成品的公用存放庫。 環境存放庫可讓您快速開始使用預先撰寫的環境範本，這些範本具有少數的輸入參數。 這些範本可讓您順暢地開始體驗實驗室內的 PaaS 資源。

在公用存放庫中，DevTest Labs 小組和其他人已建立和共用經常使用的範本，例如 Azure Web Apps、Service Fabric 叢集和開發 SharePoint 伺服器陣列環境。 您可以直接使用這些範本，或自訂這些範本以符合您的需求。 如需詳細資訊，請參閱[在 DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)。 建立您自己的範本之後，您可以將它們儲存在此存放庫中，以與其他人共用，或設定您自己的 Git 存放庫。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>建立您自己的範本存放庫

作為基礎結構即程式碼和設定即程式碼的其中一個最佳做法，您應該在原始檔控制中管理環境範本。 Azure DevTest Labs 遵循這種作法，並直接從您的 GitHub 或 Azure Repos 存放庫載入所有 Azure Resource Manager 的範本。 如此一來，您就可以在整個發行週期中使用 Resource Manager 範本，從測試環境到實際執行環境。

在存放庫中組織您的 Azure Resource Manager 範本時，有數個要遵循的規則：

- 您必須將主要範本檔案命名為 *azuredeploy.js*。

- 如果您想要使用參數檔中定義的參數值，參數檔必須命名為 *azuredeploy.parameters.js*。

  您可以使用 `_artifactsLocation` 和 `_artifactsLocationSasToken` 參數來建構 parametersLink URI 值，讓 DevTest Labs 自動管理巢狀範本。 如需詳細資訊，請參閱 [部署適用于測試環境的嵌套 Azure Resource Manager 範本](deploy-nested-template-environments.md)。

- 您可以定義中繼資料，在名為 *metadata.js*的檔案中指定範本顯示名稱和描述，如下所示：

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![金鑰 Azure Resource Manager 範本檔案](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>將範本存放庫新增至實驗室

建立並設定存放庫之後，您可以使用 Azure 入口網站將它新增至您的實驗室：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取您要的實驗室。
1. 在實驗室的 **[總覽** ] 窗格中，選取 [設定 **與原則**]。

   ![組態和原則](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 從 [設定 **和原則** 設定] 清單中選取 [ **存放庫**]。 系統會自動為所有實驗室產生 **公用** 成品存放庫存放庫，並連接到 [DevTest labs 公用 GitHub 存放庫](https://github.com/Azure/azure-devtestlab)。

1. 若要新增 Azure Resource Manager 範本存放庫，請選取 [ **新增**]。

   ![公用儲存機制](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 在 [ **存放庫** ] 窗格中，輸入下列資訊：

   - **名稱**：輸入要在實驗室中使用的存放庫名稱。
   - **Git 複製 url**：輸入 GitHub 或 Azure Repos 的 git HTTPS 複製 url。
   - **分支** (選擇性) ：輸入用來存取 Azure Resource Manager 範本定義的分支名稱。
   - **個人存取權杖**：輸入用來安全地存取存放庫的個人存取權杖。
     - 若要從 Azure Repos 取得權杖，請在您的設定檔下，選取 [**使用者設定**  >  **安全性**  >  **個人存取權杖**]。
     - 若要從 GitHub 取得權杖，請在您的設定檔下，選取 [**設定**  >  **開發人員設定**  >  **個人存取權杖**]。
   - **資料夾路徑**：輸入相對於您的構件定義或 Azure Resource Manager 範本定義之 GIT 複製 URI 的資料夾路徑。

1. 選取 [儲存]****。

   ![新增存放庫](./media/devtest-lab-create-environment-from-arm/repo-values.png)

一旦您將 Azure Resource Manager 範本新增至實驗室，您的實驗室使用者就可以使用範本來建立環境。

## <a name="configure-access-rights-for-lab-users"></a>設定實驗室使用者的存取權限

實驗室使用者預設具有 **讀者** 角色，所以無法變更環境資源群組中的資源。 例如，他們無法停止或啟動其資源。

若要為您的實驗室使用者授與 **參與者** 角色，讓他們可以編輯其環境中的資源，請遵循下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)] 的 [實驗室] 窗格中，選取 **[** 設定 **與原則**]，然後選取 [ **實驗室設定**]。

1. 在 [ **實驗室設定** ] 窗格中，選取 [ **參與者**]，然後選取 [ **儲存** ]，將寫入權限授與實驗室使用者。

   ![設定實驗室使用者存取權限](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

下一節將逐步說明如何從 Azure Resource Manager 範本建立環境。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>在 Azure 入口網站中從範本建立環境

一旦您將 Azure Resource Manager 範本新增至實驗室，您的實驗室使用者就可以依照下列步驟，在 Azure 入口網站中建立環境：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。

1. 從實驗室清單中，選取您要的實驗室。

1. 在實驗室的頁面上，選取 [ **新增**]。

1. [ **選擇基底** ] 窗格會顯示您可以使用的基底映射，並先列出 Azure Resource Manager 範本。 選取您要的 Azure Resource Manager 範本。

   ![選擇基底](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. 在 [ **新增** ] 窗格中，輸入要對環境使用者顯示的 **環境名稱** 值。

   Azure Resource Manager 範本會定義其餘的輸入欄位。 如果在檔案 * 上azuredeploy.parameter.js* 的範本定義了預設值，則輸入欄位會顯示這些值。

   針對類型 *安全字串*的參數，您可以使用 Azure Key Vault 中的密碼。 若要瞭解如何將秘密儲存在金鑰保存庫中，並在建立實驗室資源時使用它們，請參閱 [在 Azure Key Vault 中儲存秘密](devtest-lab-store-secrets-in-key-vault.md)。  

   ![新增窗格](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 下列參數值不會出現在輸入欄位中，即使範本指定它們也是一樣。 相反地，此表單會顯示空白的輸入欄位，實驗室使用者必須在建立環境時輸入值。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. 選取 [新增]**** 以建立環境。

   環境會立即開始布建，且狀態會顯示在 [ **我的虛擬機器** ] 清單中。 實驗室會自動建立新的資源群組，以布建 Azure Resource Manager 範本中定義的所有資源。

1. 建立環境之後，請在 [ **我的虛擬機器** ] 清單中選取環境，以開啟 [資源群組] 窗格，並流覽環境所布建的所有資源。

   ![環境資源](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   您也可以擴充環境，只查看環境所布建的 Vm 清單。

   ![我的虛擬機器清單](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 選取任何環境以查看可用的動作，例如套用成品、連接資料磁片、變更自動關機時間等等。

   ![環境動作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>使用 PowerShell 自動建立環境

使用 Azure 入口網站將單一環境新增至實驗室是可行的，但是當開發或測試案例必須建立多個環境時，自動化部署是較佳的體驗。

繼續之前，請確定您有定義要建立之資源的 Azure Resource Manager 範本。 [在 Git 存放庫中新增並設定範本](#configure-your-own-template-repositories)，然後 [將存放庫新增至實驗室](#add-template-repositories-to-the-lab)。

下列範例腳本會在您的實驗室中建立環境。 這些批註可協助您更瞭解腳本。

1. 將下列範例 PowerShell 腳本儲存至硬碟中的 *deployenv.ps1*。

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
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
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

1. 執行腳本，如下所示，在 Git 存放庫中使用您的 SubscriptionId、LabName、ResourceGroupName、Event.pushnotification.repositoryname、TemplateName (資料夾的特定值) 和 EnvironmentName。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

您也可以使用 Azure CLI 來部署具有 Resource Manager 範本的資源。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)。

> [!NOTE]
> 只有具備實驗室擁有者權限的使用者才可以使用 Azure PowerShell 從 Resource Manager 範本建立 VM。 如果您想要使用 Resource Manager 範本自動建立 VM，而且您只有使用者權限，可以使用 CLI 命令 [az lab VM create](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs 中的 Resource Manager 範本限制

在 DevTest Labs 中使用 Resource Manager 範本時，請考慮下列限制：

- 您無法從建立自 Resource Manager 範本的實驗室 Vm 建立公式或自訂映射。

- 當您部署 Resource Manager 範本時，不會評估大部分的原則。

例如，您可能有一個實驗室原則，使用者只能建立五個 Vm。 不過，使用者可以部署會建立數十個 VM 的 Resource Manager 範本。 未評估的原則包括：

  - 每位使用者的 VM 數目

  - 每位實驗室使用者的進階 VM 數目

  - 每位實驗室使用者的進階磁碟數目

## <a name="next-steps"></a>後續步驟
- 建立 VM 之後，您可以在 VM 的管理窗格上選取 **[** 連線]，以連線至 vm。
- 在實驗室的 [我的虛擬機器]**** 清單中選取環境，以檢視和管理環境中的資源。
- [從 Azure 快速入門範本資源庫探索 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)。
