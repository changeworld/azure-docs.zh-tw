---
title: 比較 Azure IoT 中樞與 Azure 事件中樞 | Microsoft Docs
description: IoT 中樞和事件中樞 Azure 服務的比較，重點在於功能差異和使用案例。 比較的項目包括支援的通訊協定、裝置管理、監視及檔案上傳。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733444"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>將 IoT 裝置連接到 Azure：IoT 中樞和事件中樞

Azure 提供專為不同連線和通訊類型所開發的服務，協助您連接資料與雲端的強大功能。 「Azure IoT 中樞」和「Azure 事件中樞」都是雲端服務，可擷取大量資料並處理或儲存該資料來獲得業務見解。 這兩種服務的共通點在於支援低延遲時間和高可靠性的資料擷取，不過它們是為不同目的而設計的服務。 開發 IoT 中心是為了滿足將 IoT 設備連接到 Azure 雲端的獨特要求,而事件中心是為大數據流設計的。 Microsoft 建議使用 Azure IoT 中心將 IoT 裝置連接到 Azure

Azure IoT 中心是連接IoT設備的雲閘道,用於收集數據並推動業務洞察和自動化。 此外，IoT 中樞的功能還能讓裝置與後端系統之間的關係更豐富。 雙向通訊功能意味著,當您從設備接收數據時,您還可以將命令和策略發送回設備。 例如,使用雲端到設備訊息更新屬性或調用設備管理操作。 雲到設備通信還使您能夠使用 Azure IoT Edge 將雲端智慧發送到邊緣設備。 IoT 中樞提供的獨特裝置層級身分識別，有助於進一步保護 IoT 解決方案，預防潛在攻擊。 

[Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)是 Azure 的巨量資料串流服務。 它是專為高輸送量資料串流情節所設計的服務，允許客戶每天傳送數十億筆要求。 事件中樞使用分割取用者模型來相應放大您的串流，並且已融入 Azure 的巨量資料和分析服務當中，如 Databricks、串流分析、ADLS 及 HDInsight。 該服務提供像事件中樞擷取和自動擴充之類的功能，能支援您的巨量資料應用程式和解決方案。 此外,IoT 中心使用事件中心作為其遙測流路徑,因此IoT解決方案還受益於事件中心的強大功能。

總之,這兩種解決方案都是為大規模數據引入而設計的。 只有 IoT 中心提供豐富的 IoT 特定功能,旨在為您最大化將 IoT 設備連接到 Azure 雲端的業務價值。  如果您剛踏上 IoT 之旅，入手 IoT 中樞來支援資料擷取情節，將能確保當您需要 IoT 功能來滿足商業和技術需求時，可立即取用完備的 IoT 功能。

下表提供有關針對 IoT 功能評估這兩項服務時，「IoT 中樞」的兩個層級如何與「事件中樞」做比較的詳細資料。 如需有關標準和基本 IoT 中樞層的詳細資訊，請參閱[如何選擇適合的 IoT 中樞層](iot-hub-scaling.md)。

| IoT 功能 | IoT 中樞標準層 | IoT 中樞基本層 | 事件中樞 |
| --- | --- | --- | --- |
| 裝置到雲端傳訊 | ![勾選][checkmark] | ![勾選][checkmark] | ![勾選][checkmark] |
| 通訊協定：HTTPS、AMQP、透過 webSocket 的 AMQP | ![勾選][checkmark] | ![勾選][checkmark] | ![勾選][checkmark] |
| 通訊協定：MQTT、透過 webSocket 的 MQTT | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 每部裝置身分識別 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 從裝置上傳檔案 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 裝置佈建服務 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 雲端到裝置傳訊 | ![勾選][checkmark] |  |  |
| 裝置對應項和裝置管理 | ![勾選][checkmark] |  |  |
| 裝置串流 (預覽) | ![勾選][checkmark] |  |  |
| IoT Edge | ![勾選][checkmark] |  |  |

即使唯一的使用案例是裝置到雲端資料擷取，仍強烈建議您使用「IoT 中樞」，因為它可提供專為 IoT 裝置連線設計的服務。 

### <a name="next-steps"></a>後續步驟

若要進一步探索「IoT 中樞」的功能，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
