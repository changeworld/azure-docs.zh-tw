---
title: 了解 Azure IoT 中樞的雲端到裝置傳訊 | Microsoft Docs
description: 本開發人員指南討論了如何使用雲到設備消息傳遞與IoT中心。 它包括有關消息生命週期和配置選項的資訊。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730003"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>從 IoT 中心傳送到裝置訊息

要從解決方案後端向裝置應用發送單向通知,請從IoT中心向設備發送雲到設備的消息。 有關 Azure IoT 中心支援的其他雲端到裝置選項的討論,請參閱[雲到裝置通訊指南](iot-hub-devguide-c2d-guidance.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

通過面向服務的終結點 */消息/設備綁定*,發送雲到設備的消息。 然後,設備通過特定於設備的終結點 */設備/[設備Id]/消息/設備綁定*接收消息。

要將每個雲端到裝置訊息定位到單裝置,IoT 中心將 「**到內容**」集到 */裝置/[設備 Id]/訊息/裝置繫結 。*

每個設備佇列最多包含 50 個雲端到設備消息。 嘗試向同一設備發送更多消息會導致錯誤。

## <a name="the-cloud-to-device-message-life-cycle"></a>雲到裝置訊息生命週期

為了保證至少一次郵件傳遞,IoT 中心將雲到設備的消息保留到每個設備佇列中。 對於 IoT 中要從佇列中移除訊息,裝置必須顯式確認*完成*。 此方法保證連線失敗和裝置故障能夠恢復。

生命循環狀態圖顯示在下圖中:

![雲到裝置訊息生命週期](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

當 IoT 中心服務向設備發送消息時,服務將消息狀態設置為 *「排隊*」。 當裝置想要*接收*消息時,IoT 中心通過將狀態設置為 *「不可見*」*來鎖定*消息。 此狀態允許設備上的其他線程開始接收其他消息。 當設備線程完成消息的處理時,它會通過*完成*消息通知IoT中心。 然後,IoT 中心將狀態設置為 *"已完成*」。。

裝置也可以︰

* *拒絕*該消息,這將導致 IoT 中心將其設置為*死字母*狀態。 通過消息佇列遙測傳輸 (MQTT) 協定連接的設備不能拒絕雲到設備的消息。

* *放棄*該消息,這將導致 IoT 中心將消息放回佇列中,狀態設置為 *"排隊*"。 通過 MQTT 協定連接的設備不能放棄雲到設備的消息。

線程在未通知 IoT 中心的情況下可能無法處理消息。 在這種情況下,消息在*可見性*超時(或*鎖定*超時)后自動從 *「不可見*」狀態轉換回*排隊*狀態。 此超時的值為一分鐘,無法更改。

IoT 中心**上的最大傳遞計數**屬性確定消息在 *「排隊」和「**不可見」* 狀態之間轉換的最大次數。 轉換次數後, IoT 中心將訊息的狀態設定為*死字母*。 同樣,IoT 中心將訊息的狀態設定為過期時間後*已寫死郵件*。 有關詳細資訊,請參閱[存留時間](#message-expiration-time-to-live)。

[如何使用 IoT Hub 發送雲到設備的消息](iot-hub-csharp-csharp-c2d.md),介紹如何從雲發送雲到設備的消息並在設備上接收它們。

當消息丟失不會影響應用程式邏輯時,設備通常會完成雲到設備消息。 例如,當設備在本地保留消息內容或成功執行操作時,可能會顯示此示例。 該消息還可能攜帶瞬態資訊,其丟失不會影響應用程式的功能。 對於長時間執行的工作，您有時可以：

* 在設備將任務描述保留到本地存儲中後,完成雲到設備消息。

* 在作業進度的各個階段，利用一或多個裝置到雲端訊息來通知解決方案後端。

## <a name="message-expiration-time-to-live"></a>訊息到期 (存留時間)

每個雲端到裝置的訊息都有到期時間。 此時間由以下任一設定:

* 服務中的**過期TimeUtc**屬性
* IoT 中心,透過使用指定為 IoT 中心屬性的預設*時間來生存*

請參閱[雲端到裝置的設定選項](#cloud-to-device-configuration-options)。

利用消息過期和避免向斷開連接的設備發送消息的常見方法是將*短時間設置為即時*值。 此方法與維護設備連接狀態的結果相同,但效率更高。 要求訊息確認時,IoT 中心會通知您哪些設備:

* 能夠接收訊息。
* 未上線或已失敗。

## <a name="message-feedback"></a>訊息意見反應

當您發送雲到設備消息時,服務可以請求傳遞有關該郵件最終狀態的每封郵件反饋。 透過在送出到以下四個值之一的雲端到裝置訊息中設定**iothub-ack**應用程式屬性執行此操作:

| Ack 屬性值 | 行為 |
| ------------ | -------- |
| 無     | IoT 中心不生成反饋消息(預設行為)。 |
| 正面 | 如果雲到設備消息達到 *「已完成」* 狀態,IoT 中心將生成反饋消息。 |
| 負面 | 如果雲到設備消息達到*死信*狀態,IoT 中心將生成反饋消息。 |
| 完整     | 無論哪種情況,IoT 中心都會生成反饋消息。 |

如果**Ack**值*已滿*,並且您沒有收到反饋消息,則表示反饋消息已過期。 服務無法得知原始訊息發生了什麼事。 實際上，服務應該確保它可以在回饋到期之前對其進行處理。 最長過期時間是兩天,如果發生故障,則需要時間讓服務再次運行。

如[端點](iot-hub-devguide-endpoints.md)所述,IoT 中心通過面向服務的終結點 */消息/服務綁定/反饋*作為消息提供反饋。 接收意見反應的語意與雲端到裝置訊息的接收語意相同。 可能的話，訊息意見反應會放入單一訊息中，其格式如下：

| 屬性     | 描述 |
| ------------ | ----------- |
| EnqueuedTime | 指示中心何時收到回饋訊息的時間戳 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

主體是記錄的 JSON 序列化陣列，而每筆記錄都具有下列屬性︰

| 屬性           | 描述 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 指示訊息結果發生的時間戳(例如,中心收到反饋消息或原始消息過期) |
| OriginalMessageId  | 此回饋資訊相關的雲到裝置訊息的*MessageId* |
| StatusCode         | 所需的字串,用於 IoT 中心產生的回饋訊息: <br/> 「成功」  <br/> *已過期* <br/> *超過交貨數量* <br/> *已拒絕* <br/> *清除* |
| 描述        | *狀態代碼*的字串值 |
| deviceId           | 此回饋相關的雲端到裝置訊息的目標裝置的*DeviceId* |
| DeviceGenerationId | 此回饋相關的雲端到裝置訊息的目標裝置的*DeviceGenerationId* |

對於雲端到裝置訊息與其回饋與原始訊息相關聯,服務必須指定*MessageId*。

回饋訊息的正文顯示在以下代碼中:

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

**已移除裝置的暫停回饋**

刪除設備時,也刪除任何掛起的反饋。 設備反饋分批發送。 如果在設備確認收到消息和準備下一個反饋批次之間的狹窄視窗中刪除設備(通常小於1秒),則不會發生反饋。

您可以通過等待一段時間等待待處理的反饋到達,然後再刪除設備來解決此行為。 一旦設備被刪除,應假定相關消息反饋丟失。

## <a name="cloud-to-device-configuration-options"></a>雲端到裝置組態選項

每個 IoT 中樞都會針對雲端到裝置傳訊公開下列設定選項：

| 屬性                  | 描述 | 範圍和預設值 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 雲到裝置訊息的預設 TTL | ISO_8601間隔可達2天(至少1分鐘);預設值: 1 小時 |
| maxDeliveryCount          | 雲到裝置每個裝置佇列的最大交付計數 | 1 到 100;預設值: 10 |
| feedback.ttlAsIso8601     | 保留服務繫結回饋訊息 | ISO_8601間隔可達2天(至少1分鐘);預設值: 1 小時 |
| feedback.maxDeliveryCount | 回饋佇列的最大交付計數 | 1 到 100;預設值: 10 |
| 回饋.鎖持續時間Iso8601 | 回饋佇列的最大交付計數 | ISO_8601間隔從 5 秒到 300 秒(至少 5 秒);默認值:60 秒。 |

您可以透過以下方式的設定設定選項:

* **Azure 門戶**:在 IoT 中心的 **「設定」** 下,選擇**內建終結點**並將**雲端擴展到裝置訊息傳送**。 (設置**反饋.maxDeliveryCount**和**反饋. lockDurationAsIso8601**屬性當前不支援 Azure 門戶中。

    ![為門戶中的雲到裝置訊息設定選項](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: 使用[az iot 中心更新](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)指令:

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

有關可用於接收雲到裝置訊息的 SDK 的資訊,請參閱[Azure IoT SDK](iot-hub-devguide-sdks.md)。

若要嘗試接收雲端到裝置訊息，請參閱[傳送雲端到裝置](iot-hub-csharp-csharp-c2d.md)教學課程。
