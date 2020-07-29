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
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 1b51e2338dabd34ec69d311a9eb1a5c4a644f628
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322749"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>將 IoT 裝置連接到 Azure：IoT 中樞和事件中樞

Azure 提供專為不同連線和通訊類型所開發的服務，協助您連接資料與雲端的強大功能。 Azure IoT 中樞與 Azure 事件中樞都是雲端服務，可以內嵌大量資料並處理或儲存那些資料以取得商業見解。 這兩個服務的相似之處在於其都支援以低延遲與高可靠性來內嵌資料，但兩者是針對不同目的而設計的。 IoT 中樞的開發目的是要解決將 IoT 裝置連線到 Azure 雲端的獨特需求，同時事件中樞是針對大型資料串流所設計。 Microsoft 建議使用 Azure IoT 中樞將 IoT 裝置連線到 Azure

Azure IoT 中樞是雲端閘道，可連接 IoT 裝置來收集資料，並推動商業見解和自動化。 此外，IoT 中樞的功能還能讓裝置與後端系統之間的關係更豐富。 雙向通訊功能表示，當您收到來自裝置的資料時，您也可以將命令和原則傳送回裝置。 例如，使用雲端到裝置訊息來更新屬性或叫用裝置管理動作。 雲端到裝置的通訊也可讓您使用 Azure IoT Edge 將雲端智慧傳送到邊緣裝置。 IoT 中樞提供的獨特裝置層級身分識別，有助於進一步保護 IoT 解決方案，預防潛在攻擊。 

[Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)是 Azure 的巨量資料串流服務。 它是專為高輸送量資料串流情節所設計的服務，允許客戶每天傳送數十億筆要求。 事件中樞使用分割取用者模型來相應放大您的串流，並且已融入 Azure 的巨量資料和分析服務當中，如 Databricks、串流分析、ADLS 及 HDInsight。 該服務提供像事件中樞擷取和自動擴充之類的功能，能支援您的巨量資料應用程式和解決方案。 此外，IoT 中樞會使用事件中樞作為其遙測流程路徑，因此您的 IoT 解決方案也會從事件中樞的強大功能中獲益。

總而言之，這兩種解決方案都是針對大規模的資料內嵌所設計。 只有 IoT 中樞提供豐富的 IoT 特定功能，其設計是為了讓您將 IoT 裝置連線到 Azure 雲端的商業價值最大化。  如果您剛踏上 IoT 之旅，入手 IoT 中樞來支援資料擷取情節，將能確保當您需要 IoT 功能來滿足商業和技術需求時，可立即取用完備的 IoT 功能。

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
