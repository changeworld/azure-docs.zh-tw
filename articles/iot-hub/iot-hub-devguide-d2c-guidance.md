---
title: Azure IoT 中樞裝置到雲端選項 | Microsoft Docs
description: 開發人員指南 - 針對雲端到裝置通訊，提供裝置到雲端訊息、報告屬性或檔案上傳的使用時機指引。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 02dc1b55d85b7137a5c1f57999cc3b7e9b1efe29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591331"
---
# <a name="device-to-cloud-communications-guidance"></a>Device-to-cloud communications guidance

將資訊從裝置應用程式傳送到解決方案後端時，IoT 中樞會公開三個選項︰

* [裝置到雲端訊息](iot-hub-devguide-messages-d2c.md)，適用於時間序列遙測和警示。

* [裝置對應項的回報屬性](iot-hub-devguide-device-twins.md)，適用於回報裝置狀態資訊，例如可用的功能、條件及長時間執行之工作流程的狀態。 例如，組態和軟體更新。

* [檔案上傳](iot-hub-devguide-file-upload.md)，適用於間歇性連線裝置所上傳或為了節省頻寬而壓縮的媒體檔案和大型遙測批次。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

以下是各種裝置到雲端通訊選項的詳細比較。

|  | 裝置到雲端的訊息 | 裝置對應項的回報屬性 | 檔案上傳 |
| ---- | ------- | ---------- | ---- |
| 狀況 | 遙測時間序列和警示。 例如，每隔 5 分鐘傳送一次的 256 KB 感應器資料批次。 | 可用的功能與條件。 例如，目前裝置連線能力模式，例如行動電話或 WiFi。 同步處理長時間執行的工作流程，例如組態與軟體更新。 | 媒體檔案。 大型 (通常已壓縮的) 遙測批次。 |
| 儲存和擷取 | 由 IoT 中樞暫時儲存 (最多 7 天)。 僅限循序讀取。 | 由裝置對應項中的 IoT 中樞儲存。 使用 [IoT 中樞查詢語言](iot-hub-devguide-query-language.md)擷取。 | 儲存在使用者提供的 Azure 儲存體帳戶中。 |
| 大小 | 最多 256 KB 的訊息。 | 最大報告的屬性大小為 32 KB。 | Azure Blob 儲存體所支援的檔案大小上限。 |
| 頻率 | 高。 如需詳細資訊，請參閱 [IoT 中樞限制](iot-hub-devguide-quotas-throttling.md)。 | 中。 如需詳細資訊，請參閱 [IoT 中樞限制](iot-hub-devguide-quotas-throttling.md)。 | 低。 如需詳細資訊，請參閱 [IoT 中樞限制](iot-hub-devguide-quotas-throttling.md)。 |
| 通訊協定 | 適用於所有通訊協定。 | 可使用 MQTT 或 AMQP。 | 使用任何通訊協定時都可用，但裝置上必須是 HTTPS。 |

應用程式可能需要以遙測時間序列或警示形式來傳送資訊，並在裝置對應項中提供此資訊。 在此案例中，您可以選擇下列其中一個選項：

* 裝置應用程式可傳送裝置到雲端訊息及回報屬性變更。
* 解決方案後端可以在收到訊息時將資訊儲存在裝置對應項的標籤中。

由於裝置到雲端訊息允許高於裝置對應項更新的輸送量，所以有時希望避免針對每則裝置到雲端訊息更新裝置對應項。
