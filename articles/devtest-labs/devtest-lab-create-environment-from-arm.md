---
title: 使用範本建立多 VM 環境和 PaaS 資源
description: 了解如何從 Azure Resource Manager 範本在 Azure DevTest Labs 中建立多個 VM 環境和 PaaS 資源
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182403"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源

Azure DevTest Labs 環境可讓使用者以一致的方式，在實驗室的範圍內輕鬆部署複雜的基礎結構。 您可以使用[Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)，在 DevTest Labs 中建立具有資源集的環境。 這些環境可以包含 Resource Manager 範本可以建立的任何 Azure 資源。

您可以使用[Azure 入口網站](https://portal.azure.com)，輕鬆地[一次將一部虛擬機器 (VM) 新增](devtest-lab-add-vm.md)至實驗室。 不過，多層式 web 應用程式或 SharePoint 伺服器陣列之類的案例，需要在單一步驟中建立多個 Vm 的機制。 藉由使用 Azure Resource Manager 範本，您可以定義 Azure 解決方案的基礎結構和設定，並在一致的狀態中重複部署多個 Vm。

Azure Resource Manager 範本也提供下列優點：

- 系統會直接從您的 GitHub 或 Azure Repos 原始檔控制存放庫載入 Azure Resource Manager 範本。
- 您的使用者可以從 Azure 入口網站挑選已設定的 Azure Resource Manager 範本來建立環境，就像使用其他類型的[VM 基底](devtest-lab-comparing-vm-base-image-types.md)一樣。
- 您可以從 Azure Resource Manager 範本，在環境中布建 Azure PaaS 資源和 IaaS Vm。
- 除了其他類型的基底所建立的個別 Vm 之外，您還可以追蹤實驗室中環境的成本。 PaaS 資源隨即建立，並會顯示在成本追蹤中。 不過，VM 自動關機不適用於 PaaS 資源。

若要深入瞭解使用 Resource Manager 範本在單一作業中部署、更新或刪除許多實驗室資源的優點，請參閱[使用 Resource Manager 範本的優點](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)。

> [!NOTE]
> 當您使用 Resource Manager 範本做為建立實驗室 Vm 的基礎時，建立多個 Vm 或單一 VM 之間有一些差異。 如需詳細資訊，請參閱[使用虛擬機器的 Azure Resource Manager 範本](devtest-lab-use-resource-manager-template.md)。
>

## <a name="use-devtest-labs-public-environments"></a>使用 DevTest Labs 公用環境
Azure DevTest Labs 具有[Azure Resource Manager 範本的公用存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Environments)，您可以用來建立環境，而不需要自行連接至外部 GitHub 來源。 這個公用存放庫類似于您所建立之每個實驗室的 Azure 入口網站中提供的公用存放庫。 環境存放庫可讓您快速開始使用預先撰寫的環境範本，其中有幾個輸入參數。 這些範本可為您提供實驗室內 PaaS 資源的順暢入門體驗。

在公用存放庫中，DevTest Labs 小組和其他人已建立並共用常用的範本，例如 Azure Web Apps、Service Fabric 叢集，以及開發 SharePoint 伺服器陣列環境。 您可以直接使用這些範本，或自訂它們以符合您的需求。 如需詳細資訊，請參閱[在 DevTest Labs 中設定及使用公用環境](devtest-lab-configure-use-public-environments.md)。 建立自己的範本之後，您可以將它們儲存在此存放庫中，以便與其他人共用，或設定您自己的 Git 存放庫。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>建立您自己的範本存放庫

作為基礎結構即程式碼和設定即程式碼的其中一個最佳作法，您應該在原始檔控制中管理環境範本。 Azure DevTest Labs 遵循這種作法，並直接從您的 GitHub 或 Azure Repos 存放庫載入所有 Azure Resource Manager 範本。 因此，您可以在整個發行週期中使用 Resource Manager 的範本，從測試環境到生產環境。

在存放庫中組織 Azure Resource Manager 範本時，有幾個要遵循的規則：

- 您必須將主要範本檔案命名為*azuredeploy.js在上*。

- 如果您想要使用參數檔中定義的參數值，參數檔案必須命名為*azuredeploy.parameters.js在上*。

  您可以使用 `_artifactsLocation` 和 `_artifactsLocationSasToken` 參數來建構 parametersLink URI 值，讓 DevTest Labs 自動管理巢狀範本。 如需詳細資訊，請參閱[部署適用于測試環境的嵌套 Azure Resource Manager 範本](deploy-nested-template-environments.md)。

- 您可以定義中繼資料，在名為*metadata.js*的檔案中指定範本顯示名稱和描述，如下所示：

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![金鑰 Azure Resource Manager 範本檔案](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>將範本存放庫新增至實驗室

建立並設定您的存放庫之後，您可以使用 Azure 入口網站將它新增至您的實驗室：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取您想要的實驗室。
1. 在實驗室的 **[總覽**] 窗格中，選取 [設定**和原則**]。

   ![組態和原則](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 從 [設定**和原則**設定] 清單中，選取 [**存放庫**]。 **公用**構件存放庫會針對所有實驗室自動產生，並連接到[DevTest labs 公用 GitHub 存放庫](https://github.com/Azure/azure-devtestlab)。

1. 若要新增您的 Azure Resource Manager 範本存放庫，請選取 [**新增**]。

   ![公用儲存機制](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 在 [**存放庫**] 窗格中，輸入下列資訊：

   - **名稱**：輸入要在實驗室中使用的存放庫名稱。
   - **Git 複製 url**：輸入 GitHub 或 Azure Repos 的 git HTTPS 複製 url。
   - **分支** (選擇性) ：輸入分支名稱以存取您 Azure Resource Manager 的範本定義。
   - **個人存取權杖**：輸入用來安全存取存放庫的個人存取權杖。
     - 若要從 Azure Repos 取得您的權杖，請在設定檔中選取 [**使用者設定**] [安全性] [  >  **Security**  >  **個人存取權杖**]。
     - 若要從 GitHub 取得您的權杖，請選取 [**設定**] [  >  **開發人員設定**] [  >  **個人存取權杖**]。
   - **資料夾路徑**：針對您的成品定義或 Azure Resource Manager 範本定義，輸入相對於您的 GIT 複製 URI 的資料夾路徑。

1. 選取 [儲存]。

   ![加入新的存放庫](./media/devtest-lab-create-environment-from-arm/repo-values.png)

將 Azure Resource Manager 範本新增至實驗室之後，您的實驗室使用者就可以使用範本來建立環境。

## <a name="configure-access-rights-for-lab-users"></a>設定實驗室使用者的存取權限

實驗室使用者預設具有 [**讀者**] 角色，因此無法變更環境資源群組中的資源。 例如，他們無法停止或啟動其資源。

若要授與您的實驗室使用者**參與者**角色，讓他們能夠編輯其環境中的資源，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)的實驗室的 **[總覽**] 窗格中，選取 [設定**和原則**]，然後選取 [**實驗室設定**]。

1. 在 [**實驗室設定**] 窗格中，選取 [**參與者**]，然後選取 [**儲存**]，將寫入權限授與實驗室使用者。

   ![設定實驗室使用者存取權限](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

下一節將逐步解說如何從 Azure Resource Manager 範本建立環境。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>從 Azure 入口網站中的範本建立環境

將 Azure Resource Manager 範本新增至實驗室之後，您的實驗室使用者就可以依照下列步驟，在 Azure 入口網站中建立環境：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。

1. 從實驗室清單中，選取您想要的實驗室。

1. 在實驗室的頁面上，選取 [**新增**]。

1. [**選擇基底**] 窗格會顯示您可以使用的基底映射，並先列出 Azure Resource Manager 範本。 選取您想要的 Azure Resource Manager 範本。

   ![選擇基底](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. 在 [**新增**] 窗格中，輸入要對環境使用者顯示的**環境名稱**值。

   Azure Resource Manager 範本會定義其餘的輸入欄位。 如果在檔案*上azuredeploy.parameter.js*範本定義預設值，則輸入欄位會顯示這些值。

   針對*安全字串*類型的參數，您可以使用來自 Azure Key Vault 的秘密。 若要瞭解如何將秘密儲存在金鑰保存庫中，並在建立實驗室資源時使用它們，請參閱[在 Azure Key Vault 中儲存秘密](devtest-lab-store-secrets-in-key-vault.md)。  

   ![新增窗格](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 下列參數值不會出現在輸入欄位中，即使範本指定它們也一樣。 相反地，表單會顯示空白的輸入欄位，實驗室使用者在建立環境時必須輸入值。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. 選取 [新增]**** 以建立環境。

   環境會立即開始布建，且狀態會顯示在 [**我的虛擬機器**] 清單中。 實驗室會自動建立新的資源群組，以布建 Azure Resource Manager 範本中定義的所有資源。

1. 建立環境之後，請在 [**我的虛擬機器**] 清單中選取環境，以開啟 [資源群組] 窗格，並流覽環境所布建的所有資源。

   ![環境資源](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   您也可以展開環境，只查看已布建環境的 Vm 清單。

   ![我的虛擬機器清單](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 選取任何環境以查看可用的動作，例如套用成品、連接資料磁片、變更自動關機時間等等。

   ![環境動作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>使用 PowerShell 自動建立環境

您可以使用 Azure 入口網站將單一環境新增至實驗室，但當開發或測試案例必須建立多個環境時，自動化部署會是更好的體驗。

在繼續之前，請確定您有一個 Azure Resource Manager 範本，可定義要建立的資源。 [在 Git 存放庫中新增及設定範本](#configure-your-own-template-repositories)，並[將存放庫新增至實驗室](#add-template-repositories-to-the-lab)。

下列範例腳本會在您的實驗室中建立環境。 這些批註可協助您更清楚地瞭解腳本。

1. 將下列範例 PowerShell 腳本儲存至您的硬碟，如*deployenv.ps1*。

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

1. 執行腳本，如下所示，使用您在 Git 存放庫中的 SubscriptionId、LabName、ResourceGroupName、Event.pushnotification.repositoryname、TemplateName (資料夾的特定值) 和 EnvironmentName。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

您也可以使用 Azure CLI 來部署具有 Resource Manager 範本的資源。 如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)。

> [!NOTE]
> 只有具備實驗室擁有者權限的使用者才可以使用 Azure PowerShell 從 Resource Manager 範本建立 VM。 如果您想要使用 Resource Manager 範本自動建立 VM，而且只有使用者權限，您可以使用 CLI 命令[az lab VM create](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs 中 Resource Manager 範本限制

在 DevTest Labs 中使用 Resource Manager 範本時，請考慮下列限制：

- 您無法從從 Resource Manager 範本建立的實驗室 Vm 建立公式或自訂映射。

- 當您部署 Resource Manager 範本時，不會評估大部分的原則。

例如，您可能有一個實驗室原則，使用者只能建立五個 Vm。 不過，使用者可以部署會建立數十個 VM 的 Resource Manager 範本。 未評估的原則包括：

  - 每位使用者的 VM 數目

  - 每位實驗室使用者的進階 VM 數目

  - 每位實驗室使用者的進階磁碟數目

## <a name="next-steps"></a>後續步驟
- 建立 VM 之後，您可以在 VM 的管理窗格上選取 **[** 連線] 來連線至 vm。
- 在實驗室的 [我的虛擬機器]**** 清單中選取環境，以檢視和管理環境中的資源。
- 流覽[Azure 快速入門範本資源庫中的 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)。
