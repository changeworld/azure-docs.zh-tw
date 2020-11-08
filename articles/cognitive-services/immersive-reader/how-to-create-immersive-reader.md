---
title: 建立沉浸式讀取器資源
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用自訂子域建立新的沈浸式閱讀程式資源，然後在您的 Azure 租使用者中設定 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: b012da0b2aea4a50002e9adbc0876396ddd4b5e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368724"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>建立沈浸式閱讀程式資源並設定 Azure Active Directory authentication

在本文中，我們會提供腳本來建立沈浸式閱讀程式資源，並設定 Azure Active Directory (Azure AD) 驗證。 每次建立沈浸式閱讀程式資源時（不論是使用此腳本或在入口網站中），也必須使用 Azure AD 許可權進行設定。 此腳本將協助您。

腳本的設計目的是要在一個步驟中為您建立及設定所有必要的沈浸式閱讀程式和 Azure AD 資源。 不過，您也可以只為現有的沈浸式閱讀程式資源設定 Azure AD authentication，例如，您已在 Azure 入口網站中建立一個。

對某些客戶而言，可能需要建立多個沈浸式閱讀程式資源、進行開發與生產環境的比較，或可能需要針對部署服務的多個不同區域建立。 在這些情況下，您可以返回並使用腳本多次來建立不同的沈浸式閱讀程式資源，並以 Azure AD 許可權設定這些資源。

腳本設計為有彈性。 它會先尋找您的訂用帳戶中現有的沈浸式閱讀程式和 Azure AD 資源，並在不存在的情況下建立它們。 如果這是您第一次建立沈浸式閱讀程式資源，腳本將會進行您所需的一切作業。 如果您只想要使用它來為在入口網站中建立的現有沈浸式閱讀程式資源設定 Azure AD，也會這麼做。 它也可以用來建立和設定多個沈浸式閱讀程式資源。

## <a name="set-up-powershell-environment"></a>設定 PowerShell 環境

1. 從開啟 [Azure Cloud Shell](../../cloud-shell/overview.md)開始。 確認 Cloud Shell 設定為左上角下拉式清單中的 [PowerShell] 或輸入 `pwsh` 。

1. 複製下列程式碼片段並貼到 shell 中。

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
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
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
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
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

1. 執行函數 `Create-ImmersiveReaderResource` ，並適當地提供參數。

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
      -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    | 參數 | 註解 |
    | --- | --- |
    | SubscriptionName |要用於沈浸式閱讀程式資源的 Azure 訂用帳戶名稱。 您必須擁有訂用帳戶，才能建立資源。 |
    | ResourceName |  必須是英數位元，而且可以包含 '-'，只要 '-' 不是第一個或最後一個字元。 長度不得超過63個字元。|
    | ResourceSubdomain |沈浸式閱讀程式資源需要自訂子域。 當呼叫沈浸式閱讀程式服務來啟動讀取器時，SDK 會使用子域。 子域必須是全域唯一的。 子域必須是英數位元，而且可以包含 '-'，只要 '-' 不是第一個或最後一個字元。 長度不得超過63個字元。 如果資源已經存在，則此參數是選擇性的。 |
    | ResourceSKU |選項： `S0` 。 請造訪我們的 [認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) ，以深入瞭解每個可用的 SKU。 如果資源已經存在，則此參數是選擇性的。 |
    | ResourceLocation |選項： `eastus` 、 `eastus2` 、 `southcentralus` 、 `westus` 、 `westus2` 、 `australiaeast` 、 `southeastasia` 、 `centralindia` 、 `japaneast` 、 `northeurope` `uksouth` `westeurope` 、、。 如果資源已經存在，則此參數是選擇性的。 |
    | resourceGroupName |資源會建立在訂用帳戶內的資源群組中。 提供現有資源群組的名稱。 如果資源群組不存在，則會建立具有此名稱的新資源群組。 |
    | ResourceGroupLocation |如果您的資源群組不存在，則必須提供要在其中建立群組的位置。 若要尋找位置清單，請執行 `az account list-locations` 。 在傳回的結果中，使用 *name* 屬性 (沒有空格) 。 如果您的資源群組已經存在，則此參數是選擇性的。 |
    | AADAppDisplayName |Azure Active Directory 的應用程式顯示名稱。 如果找不到現有的 Azure AD 應用程式，則會建立具有此名稱的新應用程式。 如果 Azure AD 應用程式已經存在，這個參數是選擇性的。 |
    | AADAppIdentifierUri |Azure AD 應用程式的 URI。 如果找不到現有的 Azure AD 應用程式，則會建立具有此 URI 的新應用程式。 例如 `https://immersivereaderaad-mycompany`。 |
    | AADAppClientSecret |您所建立的密碼，稍後會在取得權杖以啟動沈浸式閱讀程式時用來進行驗證。 密碼長度必須至少有16個字元、至少包含1個特殊字元，而且至少包含1個數字字元。 若要在建立此資源之後管理 Azure AD 的應用程式用戶端密碼，請流覽 https://portal.azure.com 並移至 Home-> Azure Active Directory-> 應用程式註冊-> `[AADAppDisplayName]` 的憑證與秘密] > > 的 [用戶端密碼] 區段 (如以下的 [管理您的 Azure AD 應用程式秘密] 螢幕擷取畫面所示) 。 |
    | AADAppClientSecretExpiration |您將到期的日期或日期時間 `[AADAppClientSecret]` (例如 ' 2020-12-31T11：59： 59 + 00： 00 ' 或 ' 2020-12-31 ' ) 。 |

    管理您的 Azure AD 應用程式秘密

    ![Azure 入口網站憑證和秘密分頁](./media/client-secrets-blade.png)

1. 將 JSON 輸出複製到文字檔中，以供稍後使用。 輸出應該看起來如下所示。

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
* 檢視 [Android 教學課程](./tutorial-android.md)，以查看您還可以使用適用於 Android 的 Java 或 Kotlin 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [iOS 教學課程](./tutorial-ios.md)，以查看您還可以使用適用於 iOS 的 Swift 透過沈浸式閱讀程式 SDK 執行哪些作業
* 檢視 [Python 教學課程](./tutorial-python.md)，以查看您還可以使用 Python 透過沈浸式閱讀程式 SDK 執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)