---
title: 建立沉浸式讀取器資源
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何使用自訂子域創建新的沉浸式讀取器資源，然後在 Azure 租戶中配置 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330714"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>創建沉浸式讀取器資源並配置 Azure 活動目錄身份驗證

在本文中，我們將提供一個腳本，該腳本將創建沉浸式讀取器資源並配置 Azure 活動目錄 （Azure AD） 身份驗證。 每次創建沉浸式讀取器資源時，無論是使用此腳本還是在門戶中，都必須使用 Azure AD 許可權配置它。 此腳本將説明您。

該腳本旨在一步創建和配置所有必要的沉浸式讀取器和 Azure AD 資源。 但是，您也可以為現有的沉浸式讀取器資源配置 Azure AD 身份驗證（例如，如果恰好已在 Azure 門戶中創建了一個身份驗證）。

對於某些客戶，可能需要創建多個沉浸式讀取器資源，用於開發與生產，或者對於服務部署在多個不同區域。 對於這些情況，您可以返回並使用腳本多次創建不同的沉浸式讀取器資源，並使用它們配置 Azure AD 許可權。

該腳本設計為靈活。 它將首先在訂閱中查找現有的沉浸式讀取器和 Azure AD 資源，並且僅在它們不存在時才根據需要創建這些資源。 如果這是您第一次創建沉浸式閱讀器資源，腳本將執行所需的一切操作。 如果只想使用它為門戶中創建的現有沉浸式讀取器資源配置 Azure AD，它也將執行此操作。 它還可用於創建和配置多個沉浸式讀取器資源。

## <a name="set-up-powershell-environment"></a>設置 PowerShell 環境

1. 首先打開[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview)。 確保雲外殼設置為左上角下拉或鍵入`pwsh`PowerShell。

1. 複製以下程式碼片段並將其粘貼到 shell 中。

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 運行函數`Create-ImmersiveReaderResource`，根據需要提供參數。

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | 參數 | 註解 |
    | --- | --- |
    | SubscriptionName |用於沉浸式讀取器資源的 Azure 訂閱的名稱。 您必須具有訂閱才能創建資源。 |
    | ResourceName |  必須為字母數位，並且可能包含"-"，只要"-"不是第一個或最後一個字元。 長度不得超過 63 個字元。|
    | 資源子域 |沉浸式閱讀器資源需要自訂子域。 SDK 在調用沉浸式讀取器服務以啟動讀取器時使用子域。 子域必須全域唯一。 子域必須是字母數位，並且可能包含"-"，只要"-"不是第一個或最後一個字元。 長度不得超過 63 個字元。 如果資源已存在，則此參數是可選的。 |
    | 資源庫 |選項： `S0`. 請訪問我們的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)，瞭解有關每個可用的 SKU 的更多資訊。 如果資源已存在，則此參數是可選的。 |
    | ResourceLocation |選項： `eastus` `eastus2`、 `southcentralus` `westus`、 `westus2` `australiaeast`、 `southeastasia` `centralindia`、 `japaneast` `northeurope`、 `uksouth` `westeurope`、 、 、 、 、 、 、 、 、 、 如果資源已存在，則此參數是可選的。 |
    | resourceGroupName |資源在訂閱中的資源組中創建。 提供現有資源組的名稱。 如果資源組不存在，將創建具有此名稱的新資源組。 |
    | ResourceGroupLocation |如果資源組不存在，則需要提供一個位置來創建該組。 要查找位置清單，運行`az account list-locations`。 使用返回的結果*的名稱*屬性（無空格）。 如果資源組已存在，則此參數是可選的。 |
    | AADApp顯示名稱 |Azure 活動目錄應用程式顯示名稱。 如果未找到現有的 Azure AD 應用程式，將創建具有此名稱的新應用程式。 如果 Azure AD 應用程式已存在，則此參數是可選的。 |
    | AADApp識別碼 |Azure AD 應用的 URI。 如果未找到現有的 Azure AD 應用，將創建具有此 URI 的新應用。 例如： `https://immersivereaderaad-mycompany` 。 |
    | AADAppClient秘密 |您創建的密碼，稍後將用於獲取權杖以啟動沉浸式讀取器時進行身份驗證。 密碼必須至少為 16 個字元長，至少包含 1 個特殊字元，並且至少包含 1 個數字字元。 |

1. 將 JSON 輸出複製到文字檔中，供以後使用。 輸出應該看起來如下所示。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>後續步驟

* 檢視 [Node.js 快速入門](./quickstart-nodejs.md)，以查看您還可以使用 Node.js 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Python 教學課程](./tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式 SDK 執行哪些作業
* 查看[Swift 教程](./tutorial-ios-picture-immersive-reader.md)，瞭解使用 Swift 的沉浸式讀取器 SDK 還可以執行哪些操作
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)




