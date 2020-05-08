---
title: Marketplace 計量服務驗證策略 |Azure Marketplace
description: Azure Marketplace 中支援的計量服務驗證策略。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736197"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace 計量服務驗證策略

Marketplace 計量服務支援兩種驗證策略：

* [Azure AD 安全性權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

我們將說明何時及如何使用不同的驗證策略，以安全地使用 Marketplace 計量服務提交自訂計量。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全性權杖

適用的供應專案類型為 SaaS 和具有受控應用程式方案類型的 Azure 應用程式。  

使用預先定義的固定應用程式識別碼來進行驗證，以提交自訂計量。

針對 SaaS 供應專案，Azure AD 是唯一可用的選項。

針對具有受控應用程式方案的 Azure 應用程式，您應該考慮在下列情況下使用此策略：

* 您已經有一種機制可以與後端服務通訊，而您想要擴充此機制，從中央服務發出自訂計量。
* 您有複雜的自訂計量邏輯。  在中央位置（而不是受控應用程式資源）中執行此邏輯。

註冊應用程式之後，您可以透過程式設計方式要求 Azure AD 的安全性權杖。 發行者應使用此權杖，並提出要求來解決此問題。

如需這些權杖的詳細資訊，請參閱[Azure Active Directory 存取權杖](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>取得以 Azure AD 應用程式為基礎的權杖

#### <a name="http-method"></a>HTTP 方法

**發佈**

#### <a name="request-url"></a>*要求 URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 參數*

|  **參數名稱** |  **必要**  |  **說明**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 已註冊 Azure AD 應用程式的租使用者識別碼。   |
| | | |

#### <a name="request-header"></a>*要求標頭*

|  **標頭名稱**    |  **必要**  |  **說明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 與要求相關聯的內容類型。 預設值為 `application/x-www-form-urlencoded`。  |
| | | |

#### <a name="request-body"></a>*要求本文*

|  **屬性名稱**  |  **必要**  |  **說明**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 授與類型。 預設值為 `client_credentials`。 |
|  `Client_id`        |   True         | 與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。|
|  `client_secret`    |   True         | 與 Azure AD 應用程式相關聯的密碼。  |
|  `Resource`         |   True         | 要求權杖的目標資源。 預設值為 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`。  |
| | | |

#### <a name="response"></a>*回應*

|  **名稱**    |  **型別**  |  **說明**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | 要求成功。  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

權杖回應範例：

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>使用 Azure 受控識別權杖

適用的供應專案類型是具有受控應用程式方案類型的 Azure 應用程式。

使用此方法可讓已部署的資源身分識別進行驗證，以傳送自訂計量使用事件。  您可以內嵌程式碼，以在部署界限內發出使用方式。

>[!Note]
>發行者應確保發出使用量的資源已鎖定，因此不會遭到篡改。

您的受控應用程式可以包含不同類型的資源，從虛擬機器到 Azure Functions。  如需有關如何使用不同服務的受控識別進行驗證的詳細資訊，請參閱[如何使用適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)識別。

例如，遵循下列步驟，使用 Windows VM 進行驗證。

1. 請確定已使用其中一種方法來設定受控識別：
    * [Azure 入口網站 UI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Azure Resource Manager 範本](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure SDK](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. 取得 Marketplace 計量服務應用程式識別碼（`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`）的存取權杖使用系統身分識別，透過 RDP 連線至 VM，開啟 PowerShell 主控台並執行下列命令

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 從目前的資源群組 ' ManagedBy ' 屬性取得受管理的應用程式識別碼

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace 計量服務需要報告的`resourceID`使用方式，以及`resourceUsageId`受控應用程式。

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. 使用[Marketplace 計量服務 API](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847)來發出使用量。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 應用程式供應項目](./create-new-azure-apps-offer.md)