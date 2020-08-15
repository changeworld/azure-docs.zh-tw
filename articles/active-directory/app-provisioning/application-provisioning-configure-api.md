---
title: 使用 Microsoft Graph Api 來設定布建 Azure Active Directory |Microsoft Docs
description: 需要為應用程式的多個實例設定布建嗎？ 瞭解如何使用 Microsoft Graph Api 來自動化自動布建的設定，以節省時間。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 17660df34c8039ae96440c417aef051d51a5c91c
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234742"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>使用 Microsoft Graph Api 設定布建

Azure 入口網站是一次為個別應用程式設定布建的便利方式。 但是，如果您要建立應用程式的數個（甚至上百個）實例，使用 Microsoft Graph Api 來自動化建立和設定應用程式會變得更容易。 本文概述如何透過 Api 將布建設定自動化。 這個方法通常用於 [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)之類的應用程式。

**使用 Microsoft Graph Api 將布建設定自動化的步驟總覽**


|步驟  |詳細資料  |
|---------|---------|
|[步驟1。建立資源庫應用程式](#step-1-create-the-gallery-application)     |登入 API 用戶端 <br> 取出資源庫應用程式範本 <br> 建立資源庫應用程式         |
|[步驟2。建立以範本為基礎的布建作業](#step-2-create-the-provisioning-job-based-on-the-template)     |取得布建連接器的範本 <br> 建立布建作業         |
|[步驟3。授權存取](#step-3-authorize-access)     |測試應用程式的連接 <br> 儲存認證         |
|[步驟4。開始布建作業](#step-4-start-the-provisioning-job)     |啟動工作         |
|[步驟5。監視布建](#step-5-monitor-provisioning)     |檢查布建作業的狀態 <br> 取出布建記錄         |

> [!NOTE]
> 為了方便閱讀，此文章中顯示的回應物件可能會縮短。 所有屬性都會從實際的呼叫傳回。

## <a name="step-1-create-the-gallery-application"></a>步驟 1:建立資源庫應用程式

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>登入 Microsoft Graph 總管 (建議)、Postman，或您使用的任何其他 API 用戶端

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer) \(英文\)
1. 選取 [使用 Microsoft 登入] 按鈕，然後使用 Azure AD 全域管理員或應用程式管理員認證登入。

    ![Graph 登入](./media/application-provisioning-configure-api/wd_export_02.png)

1. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

### <a name="retrieve-the-gallery-application-template-identifier"></a>擷取資源庫應用程式範本識別碼
Azure AD 應用程式資源庫中的應用程式都有一個[應用程式範本](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta) \(英文\)，可描述該應用程式的中繼資料。 使用此範本，您可以在租用戶中建立應用程式和服務主體的執行個體以進行管理。

#### <a name="request"></a>*要求*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*回應*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>建立資源庫應用程式

在最後一個步驟中，使用為您的應用程式抓取的範本識別碼，以在您的租使用者中建立應用程式和服務主體的 [實例](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta) 。

#### <a name="request"></a>*要求*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*回應*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>步驟2：建立以範本為基礎的布建作業

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>取得布建連接器的範本

資源庫中啟用布建的應用程式具有可簡化設定的範本。 使用下列要求來抓取布建設定的 [範本](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta)。 請注意，您將需要提供識別碼。 此識別碼是指先前的資源，在此案例中為 ServicePrincipal。 

#### <a name="request"></a>*要求*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>建立布建作業
若要啟用布建，您需要先 [建立作業](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)。 請使用下方的要求來建立布建作業。 指定要用於作業的範本時，請使用上一個步驟中的 templateId。

#### <a name="request"></a>*要求*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>步驟3：授權存取

### <a name="test-the-connection-to-the-application"></a>測試應用程式的連接

測試與協力廠商應用程式的連接。 下列範例適用于需要 clientSecret 和 secretToken 的應用程式。 每個應用程式都有其需求。 應用程式通常會使用 BaseAddress 來取代 ClientSecret。 若要判斷您的應用程式需要哪些認證，請流覽至應用程式的 [布建設定] 頁面，然後在開發人員模式中按一下 [測試連線] 網路流量會顯示認證所使用的參數。 您可以在 [這裡](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta)找到完整的認證清單。 

#### <a name="request"></a>*要求*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>儲存您的認證

設定布建需要在 Azure AD 和應用程式之間建立信任。 授權協力廠商應用程式的存取權。 下列範例適用于需要 clientSecret 和 secretToken 的應用程式。 每個應用程式都有其需求。 若要查看可用的選項，請參閱 [API 檔](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta) 。 

#### <a name="request"></a>*要求*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>步驟4：啟動布建作業
現在已設定布建作業，請使用下列命令來 [啟動作業](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)。 


#### <a name="request"></a>*要求*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>步驟5：監視布建

### <a name="monitor-the-provisioning-job-status"></a>監視布建作業狀態

布建作業現在正在執行，請使用下列命令來追蹤目前布建週期的進度以及統計資料的日期，例如目標系統中已建立的使用者和群組數目。 

#### <a name="request"></a>*要求*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>使用布建記錄監視布建事件
除了監視布建作業的狀態以外，您還可以使用布建 [記錄](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta) 來查詢發生 (的所有事件，例如，查詢特定使用者，並判斷是否已成功將其布建) 。

#### <a name="request"></a>*要求*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*回應*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>相關文章

- [查看同步處理 Microsoft Graph 檔](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [整合自訂 SCIM 應用程式與 Azure AD](use-scim-to-provision-users-and-groups.md)