---
title: 設定 IoT 代理程式的 Azure 資訊安全中心 |Microsoft Docs
description: 瞭解如何設定 IoT 安全性代理程式的 Azure 資訊安全中心，以便與 IoT 安全性服務的 Azure 資訊安全中心搭配使用。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 70396cdcaf8b6e2ac66619290eea35a7b260cd9a
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461244"
---
# <a name="tutorial-configure-security-agents"></a>教學課程：設定安全性代理程式

本文說明 IoT 安全性代理程式的 Azure 資訊安全中心，以及如何變更和設定它們的詳細資訊。 

> [!div class="checklist"]
> * 設定安全性代理程式
> * 藉由編輯對應項屬性來變更代理程式列為
> * 探索預設設定

## <a name="agents"></a>代理程式

Azure 資訊安全中心 IoT 安全性代理程式會從 IoT 裝置收集資料，並執行安全性動作來減輕偵測到的弱點。 安全性代理程式設定可使用一組您可以自訂的模組對應項屬性進行控制。 一般而言，這些屬性的次要更新不常發生。  

IoT 安全性代理程式對應項設定物件的 Azure 資訊安全中心是 JSON 格式物件。 設定物件是一組可控制的屬性，您可以加以定義來控制代理程式的行為。 

這些設定可協助您針對所需的每個案例自訂代理程式。 例如，您可以藉由設定這些屬性，自動排除某些事件，或將耗電量保持在最小層級。  

使用 IoT 安全性代理程式設定[架構](https://aka.ms/iot-security-github-module-schema)的 Azure 資訊安全中心進行變更。  

## <a name="configuration-objects"></a>設定物件 

與 IoT 安全性代理程式的每個 Azure 資訊安全中心相關的屬性位於**azureiotsecurity**模組的 [所需屬性] 區段內的 [代理程式設定] 物件中。 

若要修改設定，請在**azureiotsecurity**模組對應項識別內建立並修改此物件。 

如果代理程式設定物件不存在**azureiotsecurity**模組對應項中，則所有安全性代理程式屬性值都會設定為 [預設]。 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>設定架構和驗證 

請務必根據此[架構](https://aka.ms/iot-security-github-module-schema)來驗證您的代理程式設定。 如果設定物件不符合架構，代理程式將不會啟動。

 
如果代理程式正在執行，則設定物件會變更為不正確設定（設定不符合架構），代理程式將會忽略不正確設定，而且會繼續使用目前的設定。 

### <a name="configuration-validation"></a>設定驗證

IoT 安全性代理程式的 Azure 資訊安全中心會在**azureiotsecurity**模組對應項身分識別的 [報告屬性] 區段內報告其目前的設定。
代理程式會報告所有可用的屬性，如果使用者未設定屬性，代理程式就會報告預設設定。

若要驗證您的設定，請將所需區段上的值與報告區段中所報告的值進行比較。

如果所需的和報告屬性不相符，代理程式就無法剖析設定。

請針對[架構](https://aka.ms/iot-security-github-module-schema)驗證您所需的屬性、修正錯誤，然後再次設定您想要的屬性！

> [!NOTE]
> 系統會從代理程式引發設定錯誤警示，以防代理程式無法剖析所需的設定。
> 比較所回報和所需的區段，以瞭解警示是否仍適用

## <a name="editing-a-property"></a>編輯屬性 

所有自訂屬性都必須在**azureiotsecurity**模組對應項內的代理程式設定物件內設定。
若要使用預設屬性值，請從 configuration 物件中移除屬性。

### <a name="setting-a-property"></a>設定屬性

1. 在您的 IoT 中樞中，找出並選取您想要變更的裝置。

1. 按一下您的裝置，然後在 [ **azureiotsecurity**模組] 上。

1. 按一下 [**模組身分識別**對應項]。

1. 在 [安全性] 模組中編輯您想要變更的屬性。
   
   例如，若要將線上活動設定為高優先順序，並每7分鐘收集高優先順序事件，請使用下列設定。
   
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

1. 按一下 [檔案]。

### <a name="using-a-default-value"></a>使用預設值

若要使用預設屬性值，請從 configuration 物件中移除屬性。

## <a name="default-properties"></a>預設屬性 

下表包含適用于 IoT 安全性代理程式之 Azure 資訊安全中心的可控制屬性。

[GitHub](https\://aka.ms/iot-security-module-default)中的適當架構提供預設值。

| 名稱| 狀態 | 有效值| 預設值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|必要： false |有效值： ISO 8601 格式的持續時間 |預設值： PT7M |傳送高優先順序訊息之前的最大時間間隔。|
|lowPriorityMessageFrequency |必要： false|有效值： ISO 8601 格式的持續時間 |預設值： PT5H |傳送低優先順序訊息之前的最長時間。| 
|snapshotFrequency |需要： false|有效值： ISO 8601 格式的持續時間 |預設值 PT13H |建立裝置狀態快照的時間間隔。| 
|maxLocalCacheSizeInBytes |必要： false |有效值： |預設值：2560000，大於8192 | 代理程式的訊息快取允許的最大儲存空間（以位元組為單位）。 在傳送訊息之前，允許在裝置上儲存訊息的最大空間量。| 
|maxMessageSizeInBytes |必要： false |有效值：大於8192、小於262144的正數 |預設值：204800 |代理程式至雲端訊息的允許大小上限。 此設定會控制每個訊息中傳送的最大資料量。 |
|eventPriority $ {事件名稱} |必要： false |有效值： [高]、[低]、[關閉] |預設值： |每個代理程式產生的事件的優先順序 | 

### <a name="supported-security-events"></a>支援的安全性事件

|事件名稱| PropertyName | 預設值| 快照事件| 詳細資料狀態  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|診斷事件|eventPriorityDiagnostic| 關閉| False| 代理程式相關的診斷事件。 請使用此事件來進行詳細資訊記錄。| 
|組態錯誤 |eventPriorityConfigurationError |低 |False |代理程式無法剖析設定。 針對架構驗證設定。| 
|已卸載事件統計資料 |eventPriorityDroppedEventsStatistics |低 |True|代理程式相關事件統計資料。 |
|連線的硬體|eventPriorityConnectedHardware |低 |True |所有連線到裝置的硬體快照集。|
|接聽連接埠|eventPriorityListeningPorts |高 |True |裝置上所有已開啟接聽埠的快照集。|
|進程建立 |eventPriorityProcessCreate |低 |False |審核裝置上的進程建立。|
|進程終止|eventPriorityProcessTerminate |低 |False |審核裝置上的處理終止。| 
|系統資訊 |eventPrioritySystemInformation |低 |True |系統資訊的快照（例如： OS 或 CPU）。| 
|本機使用者| eventPriorityLocalUsers |高 |True|系統內已註冊之本機使用者的快照集。 |
|登入|  eventPriorityLogin |高|False|對裝置（本機和遠端登入）進行登入事件的審核。|
|連接建立 |eventPriorityConnectionCreate|低|False|審核從裝置建立的 TCP 連線。 |
|防火牆組態| eventPriorityFirewallConfiguration|低|True|裝置防火牆設定（防火牆規則）的快照集。 |
|OS 基準| eventPriorityOSBaseline| 低|True|裝置作業系統基準檢查的快照。|
|
 

## <a name="next-steps"></a>後續步驟

- [瞭解 IoT 建議的 Azure 資訊安全中心](concept-recommendations.md)
- [探索 IoT 警示 Azure 資訊安全中心](concept-security-alerts.md)
- [存取原始安全性資料](how-to-security-data-access.md)
