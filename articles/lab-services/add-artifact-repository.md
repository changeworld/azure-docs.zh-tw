---
title: 在 Azure 開發人員測試實驗室中向實驗室添加專案存儲庫 |微軟文件
description: 瞭解如何在 Azure DevTest 實驗室中向實驗室添加專案儲存庫。
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770247"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>在 DevTest 實驗室中向實驗室新增專案儲存庫
DevTest Labs 允許您指定在創建 VM 時或創建 VM 後要添加到 VM 的專案。 此專案可以是要在 VM 上安裝的工具或應用程式。 專案在從 GitHub 或 Azure DevOps Git 儲存庫載入的 JSON 檔中定義。

由 DevTest Labs 維護[的公共工件儲存庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)為 Windows 和 Linux 提供了許多常用工具。 指向此存儲庫的連結將自動添加到您的實驗室。 您可以使用公共專案儲存庫中不可用的特定工具創建自己的專案儲存庫。 要瞭解如何建立自訂項目,請參閱[建立自訂項目](devtest-lab-artifact-author.md)。

本文提供有關如何使用 Azure 門戶、Azure 資源管理範本和 Azure PowerShell 添加自定義專案存儲庫的資訊。 您可以透過編寫 PowerShell 或 CLI 文稿自動將專案儲存庫添加到實驗室。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites
若要將存放庫新增至實驗室，請取得存放庫中的重要資訊。 以下各節介紹如何獲取託管在**GitHub**或**Azure DevOps**上存儲的儲存庫所需的資訊。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>取得 GitHub 儲存機制複製 URL 和個人存取權杖

1. 移至包含構件或 Resource Manager 範本定義的 GitHub 存放庫首頁。
2. 選取 [複製或下載] ****。
3. 若要將 URL 複製到剪貼簿，請選取 [HTTPS 複製 URL]**** 按鈕。 儲存 URL 以供稍後使用。
4. 選取 GitHub 右上角的設定檔影像，然後選取 [設定]****。
5. 在左側的 **「個人設置」** 選單中,選擇 **「開發人員設置**」。
6. 選擇左方選單上**的個人存取權杖**。
7. 選取 [產生新的權杖] ****。
8. 在 [新增個人存取權杖]**** 頁面上，於 [權杖描述]**** 底下輸入描述。 接受 [選取範圍]**** 底下的預設項目，然後選取 [產生權杖]****。
9. 儲存產生的權杖。 您稍後會用到該權杖。
10. 關閉 GitHub。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>取得 Azure Repos 複製 URL 和個人存取權杖
1. 轉到團隊集合的主頁(例如`https://contoso-web-team.visualstudio.com`),然後選擇專案。
2. 在專案首頁上，選取 [程式碼] ****。
3. 若要檢視複製 URL，可在專案 [程式碼]**** 頁面上，選取 [複製]****。
4. 儲存 URL。 您稍後會用到該 URL。
5. 若要建立個人存取權杖，請選取使用者帳戶下拉式功能表中的 [我的設定檔]****。
6. 在 [設定檔資訊] 頁面上，選取 [安全性] ****。
7. 在 **"安全>個人存取權杖"** 選項卡上,選擇 **'新建權杖**"。
8. 在 **'建立新的個人存取權杖'** 頁上:
   1. 輸入權杖**的名稱**。
   2. 在 **「組織」** 清單中,選擇 **「所有可存取的組織**」。
   3. 在**過期 (UTC)** 清單中,選擇**90 天**或自定義的過期期限。
   4. 選擇「範圍 **」的「完全存取**」選項。
   5. 選取 [建立]  。
9. 新的權杖會出現在 [個人存取權杖]**** 清單中。 選取 [複製權杖] ****，然後儲存權杖值供稍後使用。
10. 繼續 將您的實驗室連接至存放庫 一節。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
本節提供向 Azure 門戶中的實驗室添加專案存儲庫的步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務]****，然後從服務清單中選取 [DevTest Labs]****。
3. 從實驗室清單中選取您的實驗室。
4. 選擇左側功能表上的 **「配置」 和「策略**」 。
5. 在左方選單**的外部資源**的部份下選擇**儲存函式庫**。
6. 選擇 **= 在**工具列上添加。

    ![[新增存放庫] 按鈕](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 儲存**函式庫**頁面上,指定以下資訊:
   1. [名稱]****。 輸入存放庫的名稱。
   2. **Git Clone URL**。 輸入您先前從 GitHub 或 Azure DevOps Services 複製的 Git HTTPS 複製 URL。
   3. **分支**。 若要取得您的定義，請輸入分支。
   4. **個人存取權杖**。 輸入您先前從 GitHub 或 Azure DevOps Services 取得的個人存取權杖。
   5. **資料夾路徑**。 輸入至少與複製 URL 相關的一個資料夾路徑，其中包含構件或 Resource Manager 範本。 指定子目錄時，請確定您有在資料夾路徑中包含斜線。

        ![存放庫區域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 選取 [儲存]  。

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本
Azure 資源管理(Azure 資源管理員)範本是 JSON 檔,用於描述要建立的 Azure 中的資源。 有關這些範本的詳細資訊,請參閱[創作 Azure 資源管理員樣本](../azure-resource-manager/templates/template-syntax.md)。

本節提供了使用 Azure 資源管理器範本將專案存儲庫添加到實驗室的步驟。  如果實驗室不存在,則範本將創建該實驗室。

### <a name="template"></a>[範本]
本文中使用的示例範本通過參數收集以下資訊。 大多數參數確實具有智慧預設值,但必須指定一些值。 您必須指定實驗室名稱、專案存儲庫的 URI 以及儲存庫的安全權杖。

- 實驗室名稱。
- 在 DevTest Labs 使用者介面 (UI) 中顯示專案儲存庫的名稱。 預設值為: `Team Repository`。
- URI 到儲存函式庫(`https://github.com/<myteam>/<nameofrepo>.git`例如`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`: 或 )
- 包含工件的存儲庫中的分支。 預設值為: `master`。
- 包含工件的資料夾的名稱。 預設值為: `/Artifacts`。
- 存放庫的類型。 允許的值為`VsoGit``GitHub`或 。
- 存取存儲庫的權杖。

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
有幾種方法可以將範本部署到 Azure 並建立資源(如果不存在或更新)(如果確實存在)。 如需詳細資料，請參閱下文：

- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)
- [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)
- [使用 Resource Manager 範本與 Azure 入口網站來部署資源](../azure-resource-manager/templates/deploy-portal.md)
- [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/templates/deploy-rest.md)

讓我們繼續瞭解如何在 PowerShell 中部署範本。 用於部署範本的 Cmdlet 特定於上下文,因此使用當前租戶和當前訂閱。 如果需要,在部署範本之前使用[Set-AzContext](/powershell/module/az.accounts/set-azcontext)來更改上下文。

首先,使用[新阿茲資源組](/powershell/module/az.resources/new-azresourcegroup)創建資源組。 如果要使用的資源組已存在,請跳過此步驟。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

接下來,使用[New-AzResourceGroup 部署](/powershell/module/az.resources/new-azresourcegroupdeployment)創建資源組部署。 此 cmdlet 將資源更改應用於 Azure。 可以對任何給定的資源組進行多次資源部署。 如果要多次部署到同一資源組,請確保每個部署的名稱是唯一的。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

在 New-AzResourceGroup 部署成功執行後,命令將輸出重要資訊,如預配狀態(應成功)和範本的任何輸出。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節為您提供了可用於將專案存儲庫添加到實驗室的示例 PowerShell 腳本。 如果沒有 Azure PowerShell,請參閱[如何安裝和配置 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0)以獲取安裝它的詳細說明。

### <a name="full-script"></a>完整指令碼
下面是完整的腳本,包括一些詳細的消息和註釋:

**新開發實驗室資料庫.ps1**:

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
下面的範例簡報如何執行文稿:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>參數
本文中的範例 PowerShell 文稿採用以下參數:

| 參數 | 描述 |
| --------- | ----------- |
| 實驗室名稱 | 實驗室的名稱。 |
| 專案儲存庫名稱 | 新專案存儲庫的名稱。 如果未指定,文稿將創建儲存庫的隨機名稱。 |
| 專案儲存函式庫顯示名稱 | 顯示專案存儲庫的名稱。 這是顯示在 Azure 門戶中的https://portal.azure.com)名稱( 查看實驗室的所有專案儲存庫時)。 |
| 儲存庫 | Uri 到存儲庫。 範例:`https://github.com/<myteam>/<nameofrepo>.git``"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`或 。|
| 儲存庫分支 | 可以在其中找到專案檔。 默認值為"主"。 |
| FolderPath | 可在其下找到專案。 默認值為"/專案" |
| 個人存取權杖 | 用於存取 GitHub 或 VSOGit 儲存庫的安全權杖。 有關獲取個人訪問權杖的說明,請參閱先決條件部分 |
| SourceType | 專案是 VSOGit 還是 GitHub 儲存庫。 |

存儲庫本身需要一個內部名稱進行標識,這與 Azure 門戶中看到的顯示名稱不同。 您不會使用 Azure 門戶看到內部名稱,但在使用 Azure REST API 或 Azure PowerShell 時可以看到它。 如果腳本的使用者未指定名稱,則該腳本提供名稱。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>文稿中使用的 PowerShell 命令

| PowerShell 命令 | 注意 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 此命令用於獲取有關實驗室的詳細資訊,例如其位置。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 沒有用於添加工件存儲庫的特定命令。 通用[的 New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet 執行該作業。 此 cmdlet 需要**ResourceId**或**資源名稱**和資源**類型**對來瞭解要建立的資源類型。 此示例腳本使用資源名稱和資源類型對。 <br/><br/>請注意,您正在與實驗室相同的位置和下同一資源組下創建專案存儲庫源。|

該腳本向當前訂閱添加新資源。 使用[取得-AzContext](/powershell/module/az.accounts/get-azcontext)查看此資訊。 使用[Set-AzContext](/powershell/module/az.accounts/set-azcontext)設置當前租戶和訂閱。

發現資源名稱和資源類型資訊的最佳方法是使用[測試驅動器 Azure REST API](https://azure.github.io/projects/apis/)網站。 查看[DevTest 實驗室 – 2016-05-15](https://aka.ms/dtlrestapis)提供者,查看為 DevTest Labs 提供程式提供的 REST API。 腳本使用者以下資源 ID。

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

資源類型是URI中的"提供程式"之後列出的所有內容,但捲曲括弧中列出的項除外。 資源名稱是捲曲括弧中看到的所有內容。 如果資源名稱需要多個項,則像我們所做的那樣,用斜杠分隔每個專案。

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>後續步驟
- [針對 Azure DevTest Labs 中的實驗室指定必要構件](devtest-lab-mandatory-artifacts.md)
- [為 DevTest Labs 虛擬機器建立自訂構件](devtest-lab-artifact-author.md)
- [針對實驗室中的構件失敗進行診斷](devtest-lab-troubleshoot-artifact-failure.md)
