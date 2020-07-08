---
title: 建立沉浸式讀取器資源
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用自訂子域建立新的沉浸式讀取器資源，然後在您的 Azure 租使用者中設定 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 972eb3f9983004ec7dbb3cb0df7bb3c59bdc9122
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042009"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>建立沉浸式讀取器資源並設定 Azure Active Directory 驗證

在本文中，我們提供的腳本會建立沉浸式讀取器資源，並設定 Azure Active Directory （Azure AD）驗證。 每次建立沉浸式讀取器資源時，不論是使用此腳本或在入口網站中，也必須以 Azure AD 許可權來設定它。 此腳本將協助您進行此程式。

此腳本的設計目的是要在一個步驟中，為您建立並設定所有必要的沉浸式讀取器和 Azure AD 資源。 不過，您也可以只針對現有的沉浸式讀取器資源設定 Azure AD 驗證，例如，您在 Azure 入口網站中已建立一個。

對於某些客戶，可能需要為開發與生產環境，或可能針對您的服務部署所在的多個不同區域建立多個沉浸式讀取器資源。 在這些情況下，您可以返回並使用腳本多次來建立不同的沉浸式讀取器資源，並將其設定為 Azure AD 的許可權。

腳本是設計成有彈性的。 它會先尋找您訂用帳戶中現有的沉浸式讀取器和 Azure AD 資源，並在必要時才建立它們（如果尚未存在的話）。 如果這是您第一次建立沉浸式讀取器資源，腳本會執行您所需的一切。 如果您只想要使用它來設定在入口網站中建立的現有沉浸式讀取器資源 Azure AD，它也會這麼做。 它也可以用來建立和設定多個沉浸式讀取器資源。

## <a name="set-up-powershell-environment"></a>設定 PowerShell 環境

1. 從開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)開始。 確定在左上角的下拉式清單中，或輸入，Cloud Shell 設定為 [PowerShell] `pwsh` 。

1. 將下列程式碼片段複製並貼到 shell 中。

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

1. 執行函式 `Create-ImmersiveReaderResource` ，並視需要提供參數。

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
    | SubscriptionName |要用於您的沉浸式讀取器資源的 Azure 訂用帳戶名稱。 您必須擁有訂用帳戶，才能建立資源。 |
    | ResourceName |  必須是英數位元，而且可以包含 '-'，前提是 '-' 不是第一個或最後一個字元。 長度不得超過63個字元。|
    | ResourceSubdomain |您的沉浸式讀取器資源需要自訂子域。 當呼叫沉浸式讀取器服務來啟動讀取器時，SDK 會使用子域。 子域必須是全域唯一的。 子域必須是英數位元，而且可以包含 '-'，前提是 '-' 不是第一個或最後一個字元。 長度不得超過63個字元。 如果資源已經存在，此參數是選擇性的。 |
    | ResourceSKU |選項： `S0` 。 請流覽我們的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)，以深入瞭解每個可用的 SKU。 如果資源已經存在，此參數是選擇性的。 |
    | ResourceLocation |選項： `eastus` 、 `eastus2` 、 `southcentralus` 、 `westus` 、 `westus2` 、 `australiaeast` `southeastasia` `centralindia` `japaneast` `northeurope` `uksouth` `westeurope` 、、、、、、。 如果資源已經存在，此參數是選擇性的。 |
    | resourceGroupName |資源會建立在訂用帳戶內的資源群組中。 提供現有資源群組的名稱。 如果資源群組不存在，則會建立一個具有這個名稱的新群組。 |
    | ResourceGroupLocation |如果您的資源群組不存在，您必須提供要在其中建立群組的位置。 若要尋找位置的清單，請執行 `az account list-locations` 。 在傳回的結果中使用*name*屬性（不含空格）。 如果您的資源群組已經存在，此參數是選擇性的。 |
    | AADAppDisplayName |Azure Active Directory 應用程式顯示名稱。 如果找不到現有的 Azure AD 應用程式，則會建立一個具有這個名稱的新的。 如果 Azure AD 應用程式已存在，則此參數為選擇性。 |
    | AADAppIdentifierUri |Azure AD 應用程式的 URI。 如果找不到現有的 Azure AD 應用程式，將會建立一個具有此 URI 的新應用程式。 例如： `https://immersivereaderaad-mycompany` 。 |
    | AADAppClientSecret |您所建立的密碼，會在您取得權杖以啟動沉浸式讀取器時用來進行驗證。 密碼長度至少必須有16個字元，至少包含1個特殊字元，且至少包含1個數字字元。 |

1. 將 JSON 輸出複製到文字檔，以供稍後使用。 輸出應該看起來如下所示。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>後續步驟

* 檢視 [Node.js 快速入門](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)，以查看您還可以使用 Node.js 透過沈浸式閱讀程式 SDK 執行哪些作業
* 觀看[android 教學](./tutorial-android.md)課程，以瞭解如何使用適用于 Android 的 JAVA 或 Kotlin 的沉浸式讀取器 SDK 來執行其他工作
* 觀看[ios 教學](./tutorial-ios.md)課程，以瞭解如何使用適用于 IOS 的 Swift 的沉浸式讀取器 SDK 來執行其他工作
* 檢視 [Python 教學課程](./tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式 SDK 執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)




