---
title: 使用 MS 圖形 API 進行雲端同步的輸入同步處理
description: 本主題說明如何只使用圖形 API 來啟用輸入同步處理
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e65569cadd8f778a94f93aa22dd3924c52ff12f8
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613383"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>使用 MS 圖形 API 進行雲端同步的輸入同步處理

下列檔說明如何只使用 MSGraph Api 從頭開始複寫同步處理設定檔。  
如何執行這項作業的結構包括下列步驟。  這些包括：

- [基本設定](#basic-setup)
- [建立服務主體](#create-service-principals)
- [建立同步處理作業](#create-sync-job)
- [更新目標網域](#update-targeted-domain)
- [啟用同步處理密碼雜湊](#enable-sync-password-hashes-on-configuration-blade)
- [開始同步處理作業](#start-sync-job)
- [審核狀態](#review-status)

使用這些 [適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組](https://docs.microsoft.com/powershell/module/msonline/) 命令來啟用生產環境租使用者的同步處理，這是能夠呼叫該租使用者之管理 Web 服務的先決條件。

## <a name="basic-setup"></a>基本設定

### <a name="enable-tenant-flags"></a>啟用租使用者旗標

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
這兩個命令的第一個命令需要 Azure Active Directory 認證。 這些 commandlet 會隱含地識別租使用者，並加以啟用以進行同步處理。

## <a name="create-service-principals"></a>建立服務主體
接下來，我們需要建立 [AD2AAD 應用程式/服務主體](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

您必須使用此應用程式識別碼 1a4721b3-e57f-4451-ae87-ef078703ec94。 如果在入口網站中使用，則 displayName 是 AD 網域 url (例如，contoso.com) ，但可能會將它命名為其他內容。

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>建立同步處理作業
上述命令的輸出將會傳回所建立之服務主體的 objectId。 在此範例中，objectId 是614ac0e9-a59b-481f-bd8f-79a73d167e1c。  使用 Microsoft Graph 將 synchronizationJob 新增至該服務主體。  

您可以在 [這裡](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)找到建立同步作業的檔。

如果您未記錄上述識別碼，您可以執行下列 MS Graph 呼叫來尋找服務主體。 您將需要目錄。請參閱，以進行該呼叫的擁有權限：
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

然後在輸出中尋找您的應用程式名稱。

執行下列兩個命令來建立兩個作業：一個用於布建使用者/群組，另一個用於密碼雜湊同步處理。 這兩次是相同的要求，但使用不同的範本識別碼。


呼叫下列兩個要求：

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

如果您想要建立兩者，您將需要兩個呼叫。

布建) 的範例傳回值 (：

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>更新目標網域
針對此租使用者，服務主體的物件識別碼和應用程式識別碼如下所示：

ObjectId： 8895955e-2e6c-4d79-8943-4d72ca36878f AppId： 00000014-0000-0000-c000-000000000000 DisplayName： testApp

我們需要更新此設定的目標網域，因此請更新此網域的密碼。

請確定您使用的功能變數名稱與您為內部內部部署網域控制站設定的 url 相同

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 根據您嘗試執行的動作，在下列值陣列中新增下列索引鍵/值組：
 - 同時啟用 PHS 和 sync 租使用者旗標 {key： "AppKey"，value： "{" appKeyScenario "：" AD2AADPasswordHash "}"}
 
 - 僅啟用同步租使用者旗標 (不要開啟 PHS) {key： "AppKey"，value： "{" appKeyScenario "：" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

預期的回應為 .。。 HTTP 204/沒有內容

在這裡，反白顯示的 "Domain" 值是要將專案布建至 Azure Active Directory 的內部部署 Active Directory 網域的名稱。

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>在設定分頁上啟用同步處理密碼雜湊

 本節將討論如何同步處理特定設定的密碼雜湊。 這與啟用租使用者層級功能旗標的 AppKey 秘密不同，這僅適用于單一網域/設定。您必須將應用程式金鑰設定為 PHS，才能讓它端對端運作。

1. 抓取架構 (警告這相當大)  
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. 採用這個 CredentialData 屬性對應：
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. 以架構中的下列名稱尋找下列物件對應
 - 布建 Active Directory 使用者
 - 布建 Active Directory inetOrgPersons

 物件對應是在架構中。 synchronizationRules [0]. objectMappings (現在您可以假設只有1個同步處理規則) 

4. 從步驟 (2) 取得 CredentialData 對應，並將其插入步驟 (3 中的物件對應) 

 您的物件對應看起來像這樣：
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 將 [ **建立 AD2AADProvisioning] 和 [AD2AADPasswordHash 作業** ] 步驟中的對應複製/貼到 attributeMappings 陣列中。 

 此陣列中的元素順序並不重要 (後端會對您的) 進行排序。 如果已經存在於陣列中，請小心加入此屬性對應 (例如，如果 attributeMappings 中已經有 targetAttributeName) CredentialData 的專案，您可能會收到衝突錯誤，或者預先存在的和新的對應可能會結合 (通常不需要的結果) 。 後端不會為您重複執行。 

 請記得為使用者和 inetOrgpersons

5. 儲存您所建立的架構 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 在要求主體中新增架構。 

## <a name="start-sync-job"></a>開始同步處理作業
您可以透過下列命令再次取回作業：

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

您可以在 [這裡](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http)找到可供取得作業的檔。 
 
若要啟動作業，請發出此要求，並使用第一個步驟中所建立之服務主體的 objectId，以及從建立作業的要求傳回的工作識別碼。

您可以在 [這裡](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)找到如何啟動作業的檔。 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

預期的回應為 .。。 HTTP 204/沒有內容。

控制作業的其他命令記載于 [此處](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)。
 
若要重新開機作業，其中一個工作會使用 .。。

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>審核狀態
取得您的工作狀態 .。。

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

在傳回物件的 [狀態] 區段中，查看相關的詳細資料

## <a name="next-steps"></a>後續步驟 

- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
- [轉換](how-to-transformation.md)
- [Azure AD 同步處理 API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
