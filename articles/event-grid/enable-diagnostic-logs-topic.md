---
title: Azure Event Grid-啟用主題或網域的診斷記錄
description: 本文提供逐步指示，說明如何啟用 Azure 事件方格主題的診斷記錄。
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 2d76d3ededd6d241197b26ac357c3b5406f43f02
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297516"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>啟用 Azure 事件方格主題或網域的診斷記錄
診斷設定可讓事件方格使用者在儲存體帳戶、事件中樞或 Log Analytics 工作區中，捕獲和查看 **發佈和傳遞失敗** 記錄。 本文提供在事件方格主題上啟用這些設定的逐步指示。

## <a name="prerequisites"></a>Prerequisites

- 已布建的事件方格主題
- 用於捕獲診斷記錄的已布建目的地。 它可以是與事件方格主題位於相同位置的下列其中一個目的地：
    - Azure 儲存體帳戶
    - 事件中樞
    - Log Analytics 工作區

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>啟用自訂主題的診斷記錄

> [!NOTE]
> 下列程式提供啟用主題診斷記錄的逐步指示。 啟用網域診斷記錄的步驟非常類似。 在步驟2中，流覽至 Azure 入口網站中的事件方格 **網域** 。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至您要啟用診斷記錄設定的事件方格主題。 
    1. 在頂端的搜尋列中，搜尋 **事件方格主題**。 
    
        ![搜尋自訂主題](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. 從清單中選取您要設定診斷設定的 **主題** 。 
1. 在左側功能表中，選取 [**監視**] 底下的 [**診斷設定**]。
1. 在 [ **診斷設定** ] 頁面上，選取 [ **加入新的診斷設定**]。 
    
    ![[新增診斷設定] 按鈕](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 指定診斷設定的 **名稱** 。 
6. 在 [**記錄**] 區段中，選取 [ **DeliveryFailures** ] 和 [ **PublishFailures** ] 選項。 
    ![選取失敗](./media/enable-diagnostic-logs-topic/log-failures.png)
7. 啟用記錄的一或多個 capture 目的地，然後選取先前建立的捕獲資源來設定它們。 
    - 如果您選取 [封存 **至儲存體帳戶**]，請選取 [ **儲存體帳戶]-[設定**]，然後選取 Azure 訂用帳戶中的儲存體帳戶。 

        ![顯示 [診斷設定] 頁面的螢幕擷取畫面，其中已核取 [封存至 Azure 儲存體帳戶]，並已選取儲存體帳戶。](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果您選取 [ **串流至事件中樞**]，請選取 [ **事件中樞-設定**]，然後選取 [事件中樞命名空間]、[事件中樞] 和 [存取原則]。 
        ![螢幕擷取畫面，顯示已核取 [串流至事件中樞] 的 [診斷設定] 頁面。](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果您選取 [ **傳送至 Log analytics**]，請選取 log analytics 工作區。
        ![螢幕擷取畫面，顯示已核取 [傳送至 Log Analytics] 的 [診斷設定] 頁面。](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. 選取 [儲存]。 然後，選取右上角的 [ **X** ] 關閉頁面。 
9. 現在，回到 [ **診斷設定** ] 頁面，確認您在 [ **診斷設定** ] 資料表中看到新專案。 
    ![顯示 [診斷設定] 頁面的螢幕擷取畫面，其中包含在 [診斷設定] 資料表中反白顯示的新專案。](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     您也可以啟用主題的所有計量收集。 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>啟用系統主題的診斷記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 流覽至您要啟用診斷記錄設定的事件方格主題。 
    1. 在頂端的搜尋列中，搜尋 **事件方格系統主題**。 
    
        ![搜尋系統主題](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 選取您要設定診斷設定的 **系統主題** 。 
    
        ![選取系統主題](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 在左側功能表中，選取 [**監視**] 底下的 [**診斷設定**]，然後選取 [**新增診斷設定**]。 

    ![新增診斷設定-按鈕](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 指定診斷設定的 **名稱** 。 
7. 在 [**記錄**] 區段中選取**DeliveryFailures** 。 
    ![選取傳遞失敗](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. 啟用記錄的一或多個 capture 目的地，然後選取先前建立的捕獲資源來設定它們。 
    - 如果您選取 [ **傳送至 Log analytics**]，請選取 log analytics 工作區。
        ![傳送至 Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - 如果您選取 [封存 **至儲存體帳戶**]，請選取 [ **儲存體帳戶]-[設定**]，然後選取 Azure 訂用帳戶中的儲存體帳戶。 

        ![封存至 Azure 儲存體帳戶](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - 如果您選取 [ **串流至事件中樞**]，請選取 [ **事件中樞-設定**]，然後選取 [事件中樞命名空間]、[事件中樞] 和 [存取原則]。 
        ![串流至事件中樞](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. 選取 [儲存]。 然後，選取右上角的 [ **X** ] 關閉頁面。 
9. 現在，回到 [ **診斷設定** ] 頁面，確認您在 [ **診斷設定** ] 資料表中看到新專案。 
    ![清單中的診斷設定](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     您也可以啟用系統主題的所有 **計量** 收集。

    ![系統主題-啟用所有計量](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>查看 Azure 儲存體中的診斷記錄 

1. 一旦您將儲存體帳戶啟用為「捕獲目的地」，而「事件方格」開始發出診斷記錄，您應該會在儲存體帳戶中看到名為 **insights-logs-deliveryfailures** 和 **insights-logs-publishfailures** 的新容器。 

    ![儲存體-診斷記錄的容器](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 當您流覽其中一個容器時，您最後會得到 JSON 格式的 blob。 檔案包含傳遞失敗或發行失敗的記錄專案。 導覽路徑表示事件方格主題的 **ResourceId** ，以及發出記錄專案時的時間戳記 (分鐘層級) 。 Blob/JSON 檔案（可供下載）最後會遵循下一節所述的架構。 

    [![儲存體 ](./media/enable-diagnostic-logs-topic/select-json.png) 中的 JSON 檔案](./media/enable-diagnostic-logs-topic/select-json.png)
3. 您應該會在 JSON 檔案中看到類似于下列範例的內容： 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>後續步驟
如需有關主題或網域之診斷記錄的記錄架構和其他概念資訊，請參閱 [診斷記錄](diagnostic-logs.md)。
