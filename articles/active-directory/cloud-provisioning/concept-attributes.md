---
title: 瞭解 Azure AD 架構和自訂表格達式
description: 本文介紹 Azure AD 架構、預配代理流的屬性和自訂表格達式。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299324"
---
# <a name="understand-the-azure-ad-schema"></a>瞭解 Azure AD 架構
與任何目錄一樣，Azure 活動目錄 （Azure AD） 中的物件是一個程式設計性的高級資料構造，表示使用者、組和連絡人等內容。 在 Azure AD 中創建新使用者或連絡人時，將創建該物件的新實例。 這些實例可以根據其屬性進行區分。

Azure AD 中的屬性是負責存儲有關 Azure AD 中物件實例的資訊的元素。

Azure AD 架構定義可在條目中使用屬性的規則、這些屬性可能具有的數值型別以及使用者如何與這些值交互。 

Azure AD 有兩種類型的屬性：
- **內置屬性**：由 Azure AD 架構預定義的屬性。 這些屬性提供不同的用途，並且可能訪問也可能無法訪問。
- **目錄擴展**：提供的屬性，以便您可以自訂 Azure AD 供自己使用。 例如，如果您已使用特定屬性擴展本地活動目錄，並希望流該屬性，則可以使用提供的自訂屬性之一。 

## <a name="attributes-and-expressions"></a>屬性和運算式
當將使用者等物件預配到 Azure AD 時，將創建使用者物件的新實例。 此創建包括該物件的屬性，該屬性也稱為屬性。 最初，新創建的物件將其屬性設置為由同步規則確定的值。 然後，這些屬性通過雲預配代理保持最新。

![物件預配](media/concept-attributes/attribute1.png)

例如，使用者可能是市場行銷部門的一部分。 其 Azure AD 部門屬性最初在預配時創建，該值設置為市場行銷。 六個月後，如果他們更改為"銷售"，則其本地活動目錄部門屬性將更改為"銷售"。 此更改同步到 Azure AD，並反映在其 Azure AD 使用者物件中。

屬性同步可能是直接的，其中 Azure AD 中的值直接設置為本地屬性的值。 或者，程式設計運算式可能處理同步。 在必須確定某些邏輯或確定以填充值的情況下，需要程式設計運算式。

例如，如果您有郵件屬性""，john.smith@contoso.com並且需要剝離""@contoso.com部分，並且只流值"john.smith"，則可以使用類似這樣的內容：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**範例輸入/輸出：** <br>

* **輸入** (mail)："john.smith@contoso.com"
* **輸出**：" 約翰.史密斯"

有關如何編寫自訂表格達式和語法的詳細資訊，請參閱[在 Azure 活動目錄中編寫屬性對應的運算式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)。

下表列出了常見屬性及其如何同步到 Azure AD。


|內部部署 Active Directory|映射類型|Azure AD|
|-----|-----|-----|
|cn|直接|通用名稱
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|運算是|givenName|
|objectGUID|直接|源錨二進位|  
|userprincipalName|直接|userPrincipalName|
|代理阿萊斯|直接|ProxyAddress|

## <a name="view-the-schema"></a>查看架構
> [!WARNING]
> 雲預配配置創建服務主體。 服務主體在 Azure 門戶中可見。 不應使用 Azure 門戶中的服務主體體驗修改屬性對應。  不支援這個動作。

要查看架構並驗證它，請按照以下步驟操作。

1.  轉到[圖形資源管理器](https://developer.microsoft.com/graph/graph-explorer)。
1.  使用全域管理員帳戶登錄。
1.  在左側，選擇**修改許可權**並確保**目錄.ReadWrite.所有內容***都已同意*。
1.  執行查詢`https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`。 此查詢返回經過篩選的服務主體清單。
1.  查找`"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`並記下 的值`"id"`。
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. 替換為`{Service Principal id}`值，然後執行查詢`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`。
1. 查找`"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`並記下 的值`"id"`。
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. 現在執行查詢`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`。
 
    範例： https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   替換`{Service Principal Id}`和`{AD2ADD Provisioning Id}`用您的值。

1. 此查詢返回架構。

   ![返回的架構](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>後續步驟

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
