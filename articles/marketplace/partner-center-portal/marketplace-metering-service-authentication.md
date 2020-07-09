---
title: Marketplace 計量服務驗證策略 | Azure Marketplace
description: Azure Marketplace 支援的計量服務驗證策略。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: e24dcdc2de94fe73f6bb83dcc03bbd1139503784
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120750"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace 計量服務驗證策略

Marketplace 計量服務支援兩種驗證策略：

* [Azure AD 安全性權杖](../../active-directory/develop/access-tokens.md)
* [受控識別](../../active-directory/managed-identities-azure-resources/overview.md) 

本文會說明應在何時如何使用不同的驗證策略，以使用 Marketplace 計量服務安全地提交自訂計量。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全性權杖

適用的供應專案類型為 transactable SaaS 和具備受控應用程式方案類型的 Azure 應用程式。  

使用預先定義的固定 Azure AD 應用程式識別碼來提交自訂計量以進行驗證。

針對 SaaS 供應專案，這是唯一可用的選項。 如[註冊 saas 應用程式](./pc-saas-registration.md)中所述，這是發佈任何 saas 供應專案的必要步驟。

若為具有受控應用程式方案的 Azure 應用程式，建議於下列情況考慮使用此策略：

* 您已經擁有能夠與後端服務通訊的機制，而且想要擴充此機制以從中央服務發出自訂計量。
* 您有複雜的自訂計量邏輯。  請在中央位置 (而非受控應用程式資源) 執行此邏輯。

註冊應用程式之後，您可以透過程式設計方式來要求 Azure AD 安全性權杖。 發行者需使用此權杖並提出解析要求。

如需這類權杖的詳細資訊，請參閱 [Azure Active Directory 存取權杖](../../active-directory/develop/access-tokens.md)。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>取得以 Azure AD 應用程式為基礎的權杖

#### <a name="http-method"></a>HTTP 方法

**POST**

#### <a name="request-url"></a>*Request URL (要求 URL)*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI 參數*

|  **參數名稱** |  **必要**  |  **描述**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | 已註冊的 Azure AD 應用程式租用戶識別碼。   |
| | | |

#### <a name="request-header"></a>*要求標頭*

|  **標頭名稱**    |  **必要**  |  **描述**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | 與要求相關聯的內容類型。 預設值是 `application/x-www-form-urlencoded`。  |
| | | |

#### <a name="request-body"></a>*要求本文*

|  **屬性名稱**  |  **必要**  |  **描述**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | 授與類型。 使用 `client_credentials`。 |
|  `Client_id`        |   True         | 與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。|
|  `client_secret`    |   True         | 與 Azure AD 應用程式相關聯的密碼。  |
|  `Resource`         |   True         | 要求權杖的目標資源。 使用 `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`。 |
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

適用的供應項目類型為具有受控應用程式方案類型的 Azure 應用程式。

使用此方法可讓已部署的資源識別進行驗證，以傳送自訂計量使用事件。  您可以內嵌程式碼，以發出部署界限內的使用量。

>[!Note]
>發行者應確保發出使用量的資源已鎖定，不會遭到篡改。

您的受控應用程式可以含有從虛擬機器到 Azure Functions 等不同類型的資源。  如需有關如何使用不同服務的受控識別進行驗證的詳細資訊，請參閱[如何使用適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources)識別）。

舉例來說，請按照下列步驟以使用 Windows VM 進行驗證。

1. 請務必使用下列其中一種方法來設定受控識別：
    * [Azure 入口網站 UI](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager 範本](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [REST](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity)）
    * [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. 使用系統身分識別取得 Marketplace 計量服務應用程式識別碼的存取權杖 (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`)、透過 RDP 連接到 VM、開啟 PowerShell 主控台，然後執行下列命令

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. 從目前的資源群組 'ManagedBy' 屬性取得受控應用程式識別碼

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace 計量服務需要報告 `resourceID` 的使用量，若為受控應用程式，還需要報告 `resourceUsageId` 的使用量。

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. 使用 [Marketplace 計量服務 API](./marketplace-metering-service-apis.md) 來發出使用量。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 應用程式供應項目](./create-new-azure-apps-offer.md)
* [建立 transactable SaaS 供應專案](./offer-creation-checklist.md)