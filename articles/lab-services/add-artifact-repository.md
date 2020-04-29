---
title: 在 Azure DevTest Labs 中，將成品存放庫新增至您的實驗室 |Microsoft Docs
description: 瞭解如何在 Azure DevTest labs 中將成品存放庫新增至您的實驗室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770247"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>在 DevTest Labs 中將成品存放庫新增至您的實驗室
DevTest Labs 可讓您在建立 VM 時或在建立 VM 之後，指定要新增至 VM 的成品。 此成品可能是您想要在 VM 上安裝的工具或應用程式。 成品定義于從 GitHub 或 Azure DevOps Git 存放庫載入的 JSON 檔案中。

DevTest Labs 所維護的公用構件存放[庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)提供許多適用于 Windows 和 Linux 的常用工具。 此存放庫的連結會自動新增至您的實驗室。 您可以使用公用構件存放庫中未提供的特定工具來建立自己的成品存放庫。 若要瞭解如何建立自訂構件，請參閱[建立自訂](devtest-lab-artifact-author.md)成品。

本文提供有關如何使用 Azure 入口網站、Azure 資源管理範本和 Azure PowerShell，來新增自訂構件儲存機制的資訊。 您可以撰寫 PowerShell 或 CLI 腳本，自動將成品存放庫新增至實驗室。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件
若要將存放庫新增至實驗室，請取得存放庫中的重要資訊。 下列各節說明如何取得**GitHub**或**Azure DevOps**上主控之存放庫的必要資訊。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 移至包含構件或 Resource Manager 範本定義的 GitHub 存放庫首頁。
2. 選取 [複製或下載] ****。
3. 若要將 URL 複製到剪貼簿，請選取 [HTTPS 複製 URL]**** 按鈕。 儲存 URL 以供稍後使用。
4. 選取 GitHub 右上角的設定檔影像，然後選取 [設定]****。
5. 在左側的 [**個人設定**] 功能表中，選取 [**開發人員設定**]。
6. 選取左側功能表上的 [**個人存取權杖**]。
7. 選取 [產生新的權杖] ****。
8. 在 [新增個人存取權杖]**** 頁面上，於 [權杖描述]**** 底下輸入描述。 接受 [選取範圍]**** 底下的預設項目，然後選取 [產生權杖]****。
9. 儲存產生的權杖。 您稍後會用到該權杖。
10. 關閉 GitHub。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>取得 Azure Repos 複製 URL 和個人存取權杖
1. 移至您的小組集合首頁（例如， `https://contoso-web-team.visualstudio.com`），然後選取您的專案。
2. 在專案首頁上，選取 [程式碼] ****。
3. 若要檢視複製 URL，可在專案 [程式碼]**** 頁面上，選取 [複製]****。
4. 儲存 URL。 您稍後會用到該 URL。
5. 若要建立個人存取權杖，請選取使用者帳戶下拉式功能表中的 [我的設定檔]****。
6. 在 [設定檔資訊] 頁面上，選取 [安全性] ****。
7. 在 [**安全性 > 個人存取權杖**] 索引標籤上，選取 [ **+ 新增權杖**]。
8. 在 [**建立新的個人存取權杖**] 頁面上：
   1. 輸入標記的**名稱**。
   2. 在 [**組織**] 清單中，選取 [**所有可存取的組織**]。
   3. 在 [**到期（UTC）** ] 清單中，選取 [ **90 天**] 或自訂定義的到期時間。
   4. 選取 [範圍] 的 [**完整存取**] 選項。
   5. 選取 [建立]  。
9. 新的權杖會出現在 [個人存取權杖]**** 清單中。 選取 [複製權杖] ****，然後儲存權杖值供稍後使用。
10. 繼續 將您的實驗室連接至存放庫 一節。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節提供在 Azure 入口網站中新增成品存放庫至實驗室的步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務]****，然後從服務清單中選取 [DevTest Labs]****。
3. 從實驗室清單中選取您的實驗室。
4. 選取左側功能表上的 [設定**和原則**]。
5. 在左側功能表中，選取 [**外部資源**] 區段底下的 [**存放庫**]。
6. 選取工具列上的 [ **+ 新增**]。

    ![[新增存放庫] 按鈕](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 在 [**存放庫**] 頁面上，指定下列資訊：
   1. **名稱**。 輸入存放庫的名稱。
   2. **Git Clone URL**。 輸入您先前從 GitHub 或 Azure DevOps Services 複製的 Git HTTPS 複製 URL。
   3. **分支**。 若要取得您的定義，請輸入分支。
   4. **個人存取權杖**。 輸入您先前從 GitHub 或 Azure DevOps Services 取得的個人存取權杖。
   5. **資料夾路徑**。 輸入至少與複製 URL 相關的一個資料夾路徑，其中包含構件或 Resource Manager 範本。 指定子目錄時，請確定您有在資料夾路徑中包含斜線。

        ![存放庫區域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 選取 [儲存]  。

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
Azure 資源管理（Azure Resource Manager）範本是 JSON 檔案，可描述您想要建立的 Azure 資源。 如需這些範本的詳細資訊，請參閱[撰寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)。

本節提供使用 Azure Resource Manager 範本將成品存放庫新增至實驗室的步驟。  範本會建立實驗室（如果尚未存在）。

### <a name="template"></a>[範本]
本文中使用的範例範本會透過參數來收集下列資訊。 大部分的參數都有智慧型預設值，但有幾個必須指定的值。 您必須指定實驗室名稱、成品存放庫的 URI，以及存放庫的安全性權杖。

- 實驗室名稱。
- DevTest Labs 使用者介面（UI）中構件存放庫的顯示名稱。 預設值為： `Team Repository`。
- 存放庫的 URI （例如： `https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。
- 包含成品之存放庫中的分支。 預設值為： `master`。
- 包含構件的資料夾名稱。 預設值為： `/Artifacts`。
- 存放庫的類型。 允許的值`VsoGit`為`GitHub`或。
- 存放庫的存取權杖。

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>部署範本
有幾種方式可將範本部署至 Azure，並建立資源（如果不存在或已更新）（如果有的話）。 如需詳細資料，請參閱下文：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)
- [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)
- [使用 Resource Manager 範本與 Azure 入口網站來部署資源](../azure-resource-manager/templates/deploy-portal.md)
- [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/templates/deploy-rest.md)

讓我們繼續瞭解如何在 PowerShell 中部署範本。 用來部署範本的 Cmdlet 是內容特定的，因此會使用目前的租使用者和目前的訂用帳戶。 如有需要，請使用[set-azcoNtext](/powershell/module/az.accounts/set-azcontext)部署範本，以變更內容。

首先，使用[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)建立資源群組。 如果您想要使用的資源群組已存在，請略過此步驟。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

接下來，使用[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)建立資源群組的部署。 此 Cmdlet 會將資源變更套用至 Azure。 您可以對任何指定的資源群組進行數個資源部署。 如果您要將數次部署到相同的資源群組，請確定每個部署的名稱都是唯一的。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

New-azresourcegroupdeployment 成功執行之後，命令會輸出重要資訊，例如布建狀態（應該成功）和範本的任何輸出。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節提供範例 PowerShell 腳本，可用來將成品存放庫新增至實驗室。 如果您沒有 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0)以取得安裝的詳細指示。

### <a name="full-script"></a>完整指令碼
以下是完整的腳本，包括一些詳細訊息和批註：

**New-DevTestLabArtifactRepository. ps1**：

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>執行 PowerShell 指令碼
下列範例顯示如何執行腳本：

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>參數
本文中的範例 PowerShell 腳本會採用下列參數：

| 參數 | 描述 |
| --------- | ----------- |
| LabName | 實驗室的名稱。 |
| ArtifactRepositoryName | 新成品存放庫的名稱。 如果未指定存放庫，腳本會為其建立隨機名稱。 |
| ArtifactRepositoryDisplayName | 構件存放庫的顯示名稱。 這是顯示在 Azure 入口網站中的名稱（https://portal.azure.com)當您查看實驗室的所有成品存放庫時）。 |
| RepositoryUri | 儲存機制的 Uri。 範例： `https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。|
| RepositoryBranch | 可在其中找到成品檔案的分支。 預設為「主要」。 |
| FolderPath | 可在其下找到構件的資料夾。 預設為 '/Artifacts ' |
| PersonalAccessToken | 用於存取 GitHub 或 VSOGit 存放庫的安全性權杖。 如需取得個人存取權杖的指示，請參閱必要條件一節。 |
| SourceType | 成品為 VSOGit 或 GitHub 存放庫。 |

存放庫本身需要識別的內部名稱，這與在 Azure 入口網站中看到的顯示名稱不同。 您不會看到使用 Azure 入口網站的內部名稱，但在使用 Azure REST Api 或 Azure PowerShell 時，您會看到它。 腳本會提供名稱（如果腳本的使用者未指定的話）。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>腳本中使用的 PowerShell 命令

| PowerShell 命令 | 備忘錄 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 此命令可用來取得實驗室的詳細資料，例如其位置。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 沒有用於新增成品存放庫的特定命令。 一般的[get-azresource](/powershell/module/az.resources/new-azresource) Cmdlet 會執行作業。 此 Cmdlet 需要**ResourceId**或資源**組和** **ResourceType** ，才能知道要建立的資源類型。 此範例腳本會使用資源名稱和資源類型配對。 <br/><br/>請注意，您會在與實驗室相同的資源群組下，建立成品存放庫來源。|

腳本會將新的資源加入至目前的訂用帳戶。 請使用[set-azcoNtext](/powershell/module/az.accounts/get-azcontext)來查看此資訊。 使用[set-azcoNtext](/powershell/module/az.accounts/set-azcontext)設定目前的租使用者和訂用帳戶。

探索資源名稱和資源類型資訊的最佳方式是使用[試用產品 AZURE REST api](https://azure.github.io/projects/apis/)網站。 請查看[DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis)提供者，以查看 DevTest Labs 提供者可用的 REST api。 腳本會使用者下列資源識別碼。

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

資源類型是 URI 中的「提供者」後面列出的所有專案，但在大括弧中列出的專案除外。 資源名稱是在大括弧中看到的所有內容。 如果資源名稱預期會有一個以上的專案，請將每個專案以斜線分隔，如同我們已完成。

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>後續步驟
- [針對 Azure DevTest Labs 中的實驗室指定必要構件](devtest-lab-mandatory-artifacts.md)
- [為 DevTest Labs 虛擬機器建立自訂構件](devtest-lab-artifact-author.md)
- [針對實驗室中的構件失敗進行診斷](devtest-lab-troubleshoot-artifact-failure.md)
