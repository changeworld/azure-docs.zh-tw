---
title: 瞭解 Azure AD 架構和自訂表格達式
description: 本文描述 Azure AD 架構、布建代理程式流程的屬性，以及自訂表格達式。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299324"
---
# <a name="understand-the-azure-ad-schema"></a>瞭解 Azure AD 架構
Azure Active Directory （Azure AD）中的物件（例如任何目錄）是以程式設計的高階資料結構，代表使用者、群組和連絡人等專案。 當您在 Azure AD 中建立新的使用者或連絡人時，就是建立該物件的新實例。 這些實例可以根據其屬性加以區別。

Azure AD 中的屬性是負責將物件實例的相關資訊儲存在 Azure AD 中的元素。

Azure AD 架構會定義專案中可能會使用哪些屬性的規則、這些屬性可能具有的數值型別，以及使用者如何與這些值進行互動。 

Azure AD 有兩種類型的屬性：
- **內建屬性**：由 Azure AD 架構預先定義的屬性。 這些屬性提供不同的用法，而且可能或無法存取。
- **目錄延伸**模組：提供的屬性，讓您可以自訂 Azure AD 供您自己使用。 例如，如果您已使用特定屬性擴充內部部署 Active Directory，而且想要傳送該屬性，則可以使用所提供的其中一個自訂屬性。 

## <a name="attributes-and-expressions"></a>屬性和運算式
當物件（例如使用者）布建到 Azure AD 時，就會建立使用者物件的新實例。 此建立包含該物件的屬性，也就是所謂的屬性。 一開始，新建立的物件會將其屬性設定為由同步處理規則所決定的值。 然後透過雲端布建代理程式，將這些屬性保持在最新狀態。

![物件布建](media/concept-attributes/attribute1.png)

例如，使用者可能是行銷部門的一部分。 其 Azure AD 部門屬性一開始會在布建時建立，而值會設定為 Marketing。 六個月後，如果它們變更為 [銷售]，其內部部署 Active Directory 部門屬性會變更為 [銷售]。 這項變更會同步處理至 Azure AD，並反映在其 Azure AD 的使用者物件中。

屬性同步處理可能是直接的，其中 Azure AD 中的值會直接設定為內部部署屬性的值。 或者，程式設計運算式可能會處理同步處理。 必須進行某些邏輯或判斷以填入值時，就需要程式設計運算式。

例如，如果您有 mail 屬性 "john.smith@contoso.com"，而且需要去除 "@contoso.com" 部分，而且只傳送 "john. smith" 的值，您就會使用如下所示的內容：

`Replace([mail], "@contoso.com", , ,"", ,)`  

**範例輸入/輸出：** <br>

* **輸入** (mail)："john.smith@contoso.com"
* **輸出**： "john smith"

如需如何撰寫自訂表格達式和語法的詳細資訊，請參閱[在 Azure Active Directory 中撰寫屬性對應的運算式](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)。

下表列出常見的屬性，以及它們如何同步處理到 Azure AD。


|內部部署 Active Directory|對應類型|Azure AD|
|-----|-----|-----|
|cn|直接|commonName
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|運算是|givenName|
|objectGUID|直接|sourceAnchorBinary|  
|userprincipalName|直接|userPrincipalName|
|ProxyAdress|直接|ProxyAddress|

## <a name="view-the-schema"></a>查看架構
> [!WARNING]
> 雲端布建設定會建立服務主體。 服務主體會顯示在 Azure 入口網站中。 您不應該使用 Azure 入口網站中的服務主體體驗來修改屬性對應。  不支援這個動作。

若要查看架構並加以驗證，請遵循下列步驟。

1.  移至 [ [Graph Explorer]](https://developer.microsoft.com/graph/graph-explorer)。
1.  使用您的全域管理員帳戶登入。
1.  在左側選取 [**修改許可權**]，並確定已*同意*[**全部**]。
1.  執行查詢`https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`。 此查詢會傳回已篩選的服務主體清單。
1.  找`"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`出並記下的`"id"`值。
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
1. 將`{Service Principal id}`取代為您的值，然後執行`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`查詢。
1. 找`"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`出並記下的`"id"`值。
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
 
    範例：https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   將`{Service Principal Id}`和`{AD2ADD Provisioning Id}`取代為您的值。

1. 此查詢會傳回架構。

   ![傳回的架構](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>後續步驟

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
