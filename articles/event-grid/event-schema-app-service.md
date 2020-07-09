---
title: Azure App Service 做為事件方格來源
description: 本文說明如何使用 Azure App Service 做為事件方格事件來源。 其會提供教學課程與操作說明文章的結構描述和連結。
author: jasonfreeberg
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jafreebe
ms.openlocfilehash: 2465b2f260ed6c174b762fcf64a71100a148254d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106706"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service 做事件方格來源

本文提供 Azure App Service 事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 其也會提供快速入門和教學課程的清單，以使用 Azure App Service 做為事件來源。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure App Service 會發出下列事件類型

|    事件類型                                             |    描述                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    在備份啟動時觸發                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    在備份完成時觸發                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    在備份失敗時觸發                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    在從備份還原啟動時觸發        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    在從備份還原完成時觸發      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    在從備份還原失敗時觸發         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    在位置交換啟動時觸發                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    在位置交換完成時觸發                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    在位置交換失敗時觸發                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    在使用預覽的位置交換啟動時觸發           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    在使用預覽的位置交換取消時觸發    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    在網站重新開機時觸發                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    在網站停止時觸發                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    在網站的應用程式設定變更時觸發             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    更新 App Service 方案時觸發                 |

### <a name="the-contents-of-an-event-response"></a>事件回應的內容

觸發事件時，事件方格服務會將該事件的相關資料傳送至訂閱端點。
本節包含每個事件的資料外觀範例。 每個事件具有下列的最高層級資料：

|     屬性          |     類型     |     描述                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    主題              |    字串    |    事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。                                      |
|    subject            |    字串    |    發行者定義事件主體的路徑。                                                                                              |
|    eventType          |    字串    |    此事件來源已註冊的事件類型。                                                                                  |
|    eventTime          |    字串    |    事件產生的時間，以提供者之 UTC 時間為準。                                                                         |
|    id                 |    字串    |    事件的唯一識別碼。                                                                                                            |
|    data               |    物件 (object)    |    blob 儲存體帳戶。                                                                                                                    |
|    dataVersion        |    字串    |    資料物件的結構描述版本。 發行者會定義結構描述版本。                                                          |
|    metadataVersion    |    字串    |    事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 事件方格會提供此值。    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted、BackupOperationCompleted、BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

資料物件包含下列屬性：

|    屬性                |    類型      |    描述                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    物件 (object)    |    應用程式上的動作詳細資料                                                                                       |
|    動作                  |    字串    |    作業的動作類型                                                                                   |
|    NAME                    |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId         |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId    |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId               |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                 |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                    |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted、RestoreOperationCompleted、RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

資料物件包含下列屬性：

|    屬性                |    類型      |    描述                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    物件 (object)    |    應用程式上的動作詳細資料                                                                                       |
|    動作                  |    字串    |    作業的動作類型                                                                                   |
|    NAME                    |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId         |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId    |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId               |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                 |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                    |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted、SlotSwapCompleted、SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

資料物件包含下列屬性：

|    屬性                |    類型      |    描述                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    物件 (object)    |    應用程式上的動作詳細資料                                                                                       |
|    動作                  |    字串    |    作業的動作類型                                                                                   |
|    NAME                    |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId         |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId    |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId               |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                 |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                    |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |
|    sourceSlot              |    字串    |    交換的來源位置                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted、SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

資料物件包含下列屬性：

|    屬性                |    類型      |    描述                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    物件 (object)    |    應用程式上的動作詳細資料                                                                                       |
|    動作                  |    字串    |    作業的動作類型                                                                                   |
|    NAME                    |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId         |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId    |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId               |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                 |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                    |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted、AppUpdated.Stopped、AppUpdated.ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

資料物件具有下列屬性：

|    屬性                |    類型      |    描述                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    物件 (object)    |    應用程式上的動作詳細資料                                                                                       |
|    動作                  |    字串    |    作業的動作類型                                                                                   |
|    NAME                    |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId         |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId    |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId               |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                 |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                    |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

資料物件具有下列屬性：

|    屬性                         |    類型      |    描述                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    物件 (object)    |    App Service 方案上的動作詳細資料                                                                          |
|    stampKind                        |    字串    |    App Service 方案所在的環境種類                                                                     |
|    動作                           |    字串    |    App Service 方案上的動作類型                                                                            |
|    status                           |    字串    |    App Service 方案上的作業狀態                                                                   |
|    sku                              |    物件 (object)    |    App Service 方案的 SKU                                                                                       |
|    NAME                             |    字串    |    App Service 方案的名稱                                                                                      |
|    層                             |    字串    |    App Service 方案的層級                                                                                      |
|    大小                             |    字串    |    App Service 方案的大小                                                                                      |
|    系列                           |    字串    |    App Service 方案的系列                                                                                        |
|    Capacity                         |    字串    |    App Service 方案的容量                                                                                      |
|    動作                           |    字串    |    作業的動作類型                                                                                   |
|    NAME                             |    字串    |    具有此事件的網站名稱                                                                          |
|    clientRequestId                  |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之用戶端要求識別碼         |
|    correlationRequestId             |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之相互關聯要求識別碼    |
|    requestId                        |    字串    |    應用程式服務針對觸發此事件的網站 API 作業所產生之要求識別碼                |
|    address                          |    字串    |    這項作業的 HTTP 要求 URL                                                                                |
|    動詞命令                             |    字串    |    這項作業的 HTTP 動詞命令                                                                                       |
