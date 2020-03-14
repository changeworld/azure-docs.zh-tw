---
title: 了解 Azure IoT 中樞的雲端到裝置傳訊 | Microsoft Docs
description: 本開發人員指南討論如何搭配 IoT 中樞使用雲端到裝置訊息。 其中包含訊息生命週期和設定選項的相關資訊。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3a7254cc9de89a297811792b4dd64b4b669ba8e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271235"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>從 IoT 中樞傳送雲端到裝置訊息

若要從您的解決方案後端將單向通知傳送至裝置應用程式，請將雲端到裝置訊息從 IoT 中樞傳送至裝置。 如需 Azure IoT 中樞支援之其他雲端到裝置選項的討論，請參閱[雲端到裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

您可以透過面向服務的端點（ */messages/devicebound*）傳送雲端到裝置訊息。 然後，裝置會透過裝置特定的端點（ */devices/{deviceId}/messages/devicebound*）來接收訊息。

若要在單一裝置上將每個雲端到裝置訊息設為目標，您的 IoT 中樞會將**to**屬性設定為 */devices/{deviceId}/messages/devicebound*。

每個裝置佇列最多可保存50個雲端到裝置訊息。 若要嘗試傳送更多訊息到相同的裝置，會導致錯誤。

## <a name="the-cloud-to-device-message-life-cycle"></a>雲端到裝置訊息生命週期

為了保證至少一次訊息傳遞，IoT 中樞會將雲端到裝置的訊息保存在每個裝置的佇列中。 若要讓 IoT 中樞從佇列中移除訊息，裝置必須明確地確認*完成*。 此方法保證連線失敗和裝置故障能夠恢復。

生命週期狀態圖表會顯示在下圖中：

![雲端到裝置訊息生命週期](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

當 IoT 中樞服務將訊息傳送至裝置時，服務會將訊息狀態設定為已加入*佇列*。 當裝置想要接收訊息時，IoT 中樞*會*藉由將狀態設定為 [*不可見*] 來*鎖定*訊息。 此狀態可讓裝置上的其他執行緒開始接收其他訊息。 當裝置執行緒完成訊息的處理時，它會藉由*完成*訊息來通知 IoT 中樞。 IoT 中樞接著會將狀態設定為 [*已完成*]。

裝置也可以︰

* *拒絕*訊息，這會導致 IoT 中樞將其設定為*死信*狀態。 透過訊息佇列遙測傳輸（MQTT）通訊協定連接的裝置無法拒絕雲端到裝置訊息。

* *放棄*訊息，這會導致 IoT 中樞將訊息放回佇列中，並將狀態*設定為 [已排入佇列]* 。 透過 MQTT 通訊協定連線的裝置無法放棄雲端到裝置的訊息。

執行緒可能無法處理訊息，而不會通知 IoT 中樞。 在此情況下，訊息會在*可見度*超時（或*鎖定*超時）後，自動從*不可見*狀態轉換回已加入*佇列*的狀態。 這個超時的值是一分鐘，而且無法變更。

IoT 中樞上的**最大傳遞計數**屬性會決定訊息可以在已加入*佇列*與*不可見*狀態之間轉換的次數上限。 在該轉換次數之後，IoT 中樞會將訊息的狀態設定為無效*字母*。 同樣地，IoT 中樞會在其到期時間之後，將訊息的狀態設定為*失效*。 如需詳細資訊，請參閱生存[時間](#message-expiration-time-to-live)。

[如何使用 IoT 中樞傳送雲端到裝置訊息](iot-hub-csharp-csharp-c2d.md)一文說明如何從雲端傳送雲端到裝置訊息，並在裝置上接收它們。

當遺失訊息不會影響應用程式邏輯時，裝置通常會完成雲端到裝置訊息。 其中一個範例可能是裝置已在本機保存訊息內容，或已成功執行作業。 此訊息也會攜帶暫時性資訊，而其遺失不會影響應用程式的功能。 對於長時間執行的工作，您有時可以：

* 在裝置將工作描述保存于本機儲存體之後，完成雲端到裝置訊息。

* 在作業進度的各個階段，利用一或多個裝置到雲端訊息來通知解決方案後端。

## <a name="message-expiration-time-to-live"></a>訊息到期 (存留時間)

每個雲端到裝置的訊息都有到期時間。 這段時間是由下列其中一項設定：

* 服務中的**ExpiryTimeUtc**屬性
* IoT 中樞，方法是使用指定為 IoT 中樞屬性的預設生存*時間*

請參閱 [雲端到裝置的設定選項](#cloud-to-device-configuration-options)。

利用訊息到期和避免將訊息傳送至已中斷連線之裝置的常見方式，是設定短*時間的即時*值。 這種方法可達到與維護裝置線上狀態相同的結果，但更有效率。 當您要求訊息通知時，IoT 中樞會通知您哪些裝置為：

* 能夠接收訊息。
* 未上線或已失敗。

## <a name="message-feedback"></a>訊息意見反應

當您傳送雲端到裝置訊息時，服務可以要求傳遞有關該訊息最終狀態的每一訊息意見反應。 若要這麼做，您可以在傳送至下列四個值之一的雲端到裝置訊息中，設定**iothub-ack**應用程式屬性：

| Ack 屬性值 | 行為 |
| ------------ | -------- |
| 無     | IoT 中樞不會產生意見反應訊息（預設行為）。 |
| 正面 | 如果雲端到裝置訊息達到*已完成*狀態，IoT 中樞就會產生意見反應訊息。 |
| 負面 | 如果雲端到裝置訊息達到*死信*狀態，IoT 中樞就會產生意見反應訊息。 |
| 完整     | 在任一情況下，IoT 中樞都會產生意見反應訊息。 |

如果**Ack**值已*滿*，而且您沒有收到意見反應訊息，這表示意見反應訊息已過期。 服務無法得知原始訊息發生了什麼事。 實際上，服務應該確保它可以在回饋到期之前對其進行處理。 最長到期時間是兩天，這會在發生失敗時，讓服務再次執行。

如[端點](iot-hub-devguide-endpoints.md)中所述，IoT 中樞會透過服務面向端點 */messages/servicebound/feedback 利用*來傳遞意見反應，做為訊息。 接收意見反應的語意與雲端到裝置訊息的接收語意相同。 可能的話，訊息意見反應會放入單一訊息中，其格式如下：

| 屬性     | 描述 |
| ------------ | ----------- |
| EnqueuedTime | 時間戳記，指出中樞收到意見反應訊息的時間 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性           | 描述 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 指出何時發生訊息結果的時間戳記（例如，中樞收到意見反應訊息或原始郵件已過期） |
| OriginalMessageId  | 此意見反應資訊相關之雲端到裝置訊息的*MessageId* |
| StatusCode         | 必要的字串，用於 IoT 中樞所產生的意見反應訊息： <br/> 「成功」 <br/> *過時* <br/> *DeliveryCountExceeded* <br/> *退回* <br/> *按照* |
| 描述        | *StatusCode*的字串值 |
| deviceId           | 此意見反應相關之雲端到裝置訊息的目標裝置的*DeviceId* |
| DeviceGenerationId | 此意見反應相關之雲端到裝置訊息的目標裝置*DeviceGenerationId* |

針對雲端到裝置訊息，若要將其意見反應與原始訊息相互關聯，服務必須指定*MessageId*。

意見反應訊息的主體會顯示在下列程式碼中：

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**已刪除裝置的暫止意見反應**

刪除裝置時，也會一併刪除所有擱置中的意見反應。 裝置意見反應會以批次方式傳送。 如果裝置在縮小視窗中刪除（通常小於1秒），則在裝置確認收到訊息時，以及當下一個意見反應批次準備好時，將不會產生意見反應。

您可以在刪除您的裝置之前，先等候一段時間讓暫止的意見反應抵達，以解決此行為。 刪除裝置之後，應假設相關的訊息意見反應遺失。

## <a name="cloud-to-device-configuration-options"></a>雲端到裝置組態選項

每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項：

| 屬性                  | 描述 | 範圍和預設值 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 雲端到裝置訊息的預設 TTL | 最多2天的 ISO_8601 間隔（最少1分鐘）;預設值：1小時 |
| maxDeliveryCount          | 雲端到裝置的每個裝置佇列的最大傳遞計數 | 1到 100;預設值：10 |
| feedback.ttlAsIso8601     | 服務系結意見反應訊息的保留期 | 最多2天的 ISO_8601 間隔（最少1分鐘）;預設值：1小時 |
| feedback.maxDeliveryCount | 意見反應佇列的最大傳遞計數 | 1到 100;預設值：10 |
| 意見反應。 lockDurationAsIso8601 | 意見反應佇列的最大傳遞計數 | ISO_8601 間隔為5到300秒（最少5秒）;預設值：60秒。 |

您可以透過下列其中一種方式來設定設定選項：

* **Azure 入口網站**：在 IoT 中樞的 [**設定**] 底下，選取 [**內建端點**]，並**將 [雲端到裝置訊息**] 展開。 （目前不支援在 Azure 入口網站中設定**maxDeliveryCount**和**意見 lockDurationAsIso8601**屬性。）

    ![在入口網站中設定雲端到裝置訊息的設定選項](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**：使用[az iot hub update](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)命令：

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>後續步驟

如需您可以用來接收雲端到裝置訊息之 Sdk 的詳細資訊，請參閱[Azure IoT sdk](iot-hub-devguide-sdks.md)。

若要嘗試接收雲端到裝置訊息，請參閱[傳送雲端到裝置](iot-hub-csharp-csharp-c2d.md)教學課程。
