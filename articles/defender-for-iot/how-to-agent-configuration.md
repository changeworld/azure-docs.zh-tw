---
title: 設定安全性代理程式
description: 瞭解如何設定 Defender for IoT 安全性代理程式，以搭配 Defender for IoT 安全性服務使用。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: aa863ce556840903d16238d6afef32136ba2b80d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090840"
---
# <a name="tutorial-configure-security-agents"></a>教學課程：設定安全性代理程式

本文說明適用于 IoT 安全性代理程式的 Defender，並詳細說明如何變更和設定這些代理程式。

> [!div class="checklist"]
> * 設定安全性代理程式
> * 藉由編輯對應項屬性來變更代理程式列為
> * 探索預設設定

## <a name="agents"></a>代理程式

適用于 IoT 的 Defender 安全性代理程式會從 IoT 裝置收集資料，並執行安全性動作來減輕偵測到的弱點。 您可以使用一組可自訂的模組對應項屬性，控制安全性代理程式設定。 一般而言，這些屬性的次要更新不常發生。

適用于 IoT 的安全性代理程式對應項設定物件的 Defender 是 JSON 格式物件。 Configuration 物件是一組可控制的屬性，您可以定義這些屬性來控制代理程式的行為。

這些設定可協助您針對每個所需的案例自訂代理程式。 例如，您可以藉由設定這些屬性，自動排除某些事件，或將耗電量維持在最低層級。

使用 Defender for IoT 安全性代理程式設定 [架構](https://aka.ms/iot-security-github-module-schema) 進行變更。

## <a name="configuration-objects"></a>設定物件

適用于 IoT 安全性代理程式的每個 Defender 的相關屬性位於 **azureiotsecurity** 模組的代理程式設定物件的 [所需屬性] 區段內。

若要修改設定，請在 **azureiotsecurity** 模組對應項識別內建立和修改此物件。

如果代理程式設定物件不存在於 **azureiotsecurity** 模組對應項中，則所有的安全性代理程式屬性值都會設為預設值。

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>設定架構和驗證

請務必驗證您的代理程式設定是否符合此 [架構](https://aka.ms/iot-security-github-module-schema)。 如果設定物件不符合架構，代理程式將不會啟動。

如果代理程式正在執行，則設定物件會變更為不正確設定 (設定不符合架構) ，代理程式將會忽略不正確設定，並且會繼續使用目前的設定。

### <a name="configuration-validation"></a>設定驗證

Defender for IoT security agent 會在 **azureiotsecurity** 模組對應項身分識別的報告屬性區段內，報告其目前的設定。
代理程式會報告所有可用的屬性，如果使用者未設定屬性，則代理程式會報告預設設定。

若要驗證您的設定，請將所需區段上設定的值與報告區段中報告的值進行比較。

如果所需的屬性和回報的屬性不相符，則代理程式無法剖析設定。

針對 [架構](https://aka.ms/iot-security-github-module-schema)驗證您所需的屬性，修正錯誤，並再次設定您想要的屬性！

> [!NOTE]
> 如果代理程式無法剖析所需的設定，將會從代理程式引發設定錯誤警示。
> 比較報告和所需的區段，以瞭解是否仍然適用警示

## <a name="editing-a-property"></a>編輯屬性

所有自訂屬性都必須在 **azureiotsecurity** 模組對應項內的代理程式設定物件內部設定。
若要使用預設屬性值，請從設定物件中移除該屬性。

### <a name="setting-a-property"></a>設定屬性

1. 在 IoT 中樞內，找出並選取您想要變更的裝置。

1. 在您的裝置上按一下，然後在 **azureiotsecurity** 模組上按一下。

1. 按一下 [ **模組身分識別**對應項]。

1. 編輯您想要在安全性模組中變更的屬性。

   例如，若要將連接事件設定為高優先順序，並每隔7分鐘收集高優先順序的事件，請使用下列設定。

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. 按一下 [檔案] 。

### <a name="using-a-default-value"></a>使用預設值

若要使用預設屬性值，請從設定物件中移除該屬性。

## <a name="default-properties"></a>預設屬性

下表包含適用于 IoT 安全性代理程式之 Defender 的可控制屬性。

[GitHub](https\://aka.ms/iot-security-module-default)中的適當架構可以使用預設值。

| 名稱| 狀態 | 有效值| 預設值| 描述 |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|必要： false |有效的值： ISO 8601 格式的持續時間 |預設值： PT7M |傳送高優先順序訊息之前的最大時間間隔。|
|lowPriorityMessageFrequency |必要： false|有效的值： ISO 8601 格式的持續時間 |預設值： PT5H |傳送低優先順序訊息之前的最長時間。|
|snapshotFrequency |需要： false|有效的值： ISO 8601 格式的持續時間 |預設值 PT13H |建立裝置狀態快照集的時間間隔。|
|maxLocalCacheSizeInBytes |必要： false |有效值： |預設值：2560000，大於8192 | 代理程式的訊息快取允許的最大儲存體 (位元組) 。 在傳送訊息之前，允許將訊息儲存在裝置上的最大空間量。|
|maxMessageSizeInBytes |必要： false |有效的值：大於8192、小於262144的正數 |預設值：204800 |允許的代理程式到雲端訊息的大小上限。 這項設定會控制每個訊息中傳送的最大資料量。 |
|eventPriority $ {事件名稱} |必要： false |有效值： High、Low、Off |預設值： |每個代理程式產生事件的優先順序 |

### <a name="supported-security-events"></a>支援的安全性事件

|事件名稱| PropertyName | 預設值| 快照事件| 詳細資料狀態  |
|----------|-|---------|----|----|
|診斷事件|eventPriorityDiagnostic| 關閉| 否| 代理程式相關的診斷事件。 使用此事件以取得詳細資訊記錄。|
|設定錯誤 |eventPriorityConfigurationError |低 |否 |代理程式無法剖析設定。 針對架構驗證設定。|
|捨棄的事件統計資料 |eventPriorityDroppedEventsStatistics |低 |是|代理程式相關的事件統計資料。 |
|連線的硬體|eventPriorityConnectedHardware |低 |是 |連線到裝置的所有硬體的快照。|
|接聽連接埠|eventPriorityListeningPorts |高 |是 |裝置上所有已開啟接聽埠的快照。|
|進程建立 |eventPriorityProcessCreate |低 |否 |在裝置上審核進程建立。|
|進程終止|eventPriorityProcessTerminate |低 |否 |審核裝置上的進程終止。|
|系統資訊 |eventPrioritySystemInformation |低 |是 |系統資訊的快照集 (例如： OS 或 CPU) 。|
|本機使用者| eventPriorityLocalUsers |高 |是|系統內已註冊之本機使用者的快照。 |
|登入|  eventPriorityLogin |高|否|將登入事件 (本機和遠端登入) 。|
|連接建立 |eventPriorityConnectionCreate|低|否|審核在裝置上建立的 TCP 連線。 |
|防火牆設定| eventPriorityFirewallConfiguration|低|是| (防火牆規則) 的裝置防火牆設定快照集。 |
|作業系統基準| eventPriorityOSBaseline| 低|是|裝置作業系統基準檢查的快照。|
|

## <a name="next-steps"></a>後續步驟

- [瞭解 Defender for IoT 建議](concept-recommendations.md)
- [探索適用于 IoT 的 Defender 警示](concept-security-alerts.md)
- [存取未經處理的安全性資料](how-to-security-data-access.md)
