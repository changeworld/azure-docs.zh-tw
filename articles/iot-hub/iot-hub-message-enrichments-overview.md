---
title: Azure IoT 中心消息擴充概述
description: 本文顯示消息擴充，使 IoT 中心能夠在消息發送到指定的終結點之前用附加資訊標記消息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429111"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>設備到雲 IoT 中心消息的消息擴充

*消息擴充*是 IoT 中心在消息發送到指定終結點之前使用附加資訊*標記*消息的能力。 使用消息擴充的一個原因是包含可用於簡化下游處理的資料。 例如，使用設備孿生標記豐富設備遙測消息可以減少客戶對此資訊進行設備孿生 API 呼叫的負載。

![消息擴充流](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

消息擴充有三個關鍵元素：

* 擴充名稱或金鑰

* 值

* 應用濃縮的一個或多個[終結點](iot-hub-devguide-endpoints.md)。

**鍵**是字串。 鍵只能包含字母數位字元或這些特殊字元：連字號 （`-`）、下劃`_`線 （`.`） 和句點 （ 。

**該值**可以是以下任何示例：

* 任何靜態字串。 不允許動態值（如條件、邏輯、操作和函數）。 例如，如果您開發由多個客戶使用的 SaaS 應用程式，則可以為每個客戶分配一個識別碼，並使該識別碼在應用程式中可用。 應用程式運行時，IoT 中心會用客戶的識別碼標記設備遙測消息，從而可以針對每個客戶以不同的方式處理消息。

* 發送消息的 IoT 中心的名稱。 此值 *$iothubname。*

* 來自設備孿生的資訊，如其路徑。 例如 *$twin.tags.field*和 *$twin.tags.緯度*。

   > [!NOTE]
   > 此時，僅支援$iothubname、$twin.tag、$twin.屬性.希望和$twin.屬性.報告用於消息擴充的變數。

消息擴充將作為應用程式屬性添加到發送到所選終結點的消息。  

## <a name="applying-enrichments"></a>應用濃縮

消息可能來自[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)支援的任何資料來源，包括以下示例：

* 設備遙測，如溫度或壓力
* 設備孿生更改通知 -- 設備孿生中的更改
* 設備生命週期事件，例如創建或刪除設備時

您可以將豐富添加到 IoT 中心的內置終結點的消息，或路由到自訂終結點（如 Azure Blob 存儲、服務匯流排佇列或服務匯流排主題）的消息。

您可以通過選擇終結點作為事件網格，向發佈到事件網格的消息添加擴充。 我們根據您的事件網格訂閱在 IoT 中心中創建設備遙測的預設路由。 此單一路由可以處理所有事件網格訂閱。 在創建事件網格訂閱到設備遙測後，可以為事件網格終結點配置擴充。 有關詳細資訊，請參閱[Iot 中心和事件網格](iot-hub-event-grid.md)。

每個終結點都應用擴充。 如果指定要為特定終結點加印的五個富集，則所有到該終結點的消息都用相同的五個富集標記。

可以使用以下方法配置擴充：

| **方法** | **命令** |
| ----- | -----| 
| 入口網站 | [Azure 門戶](https://portal.azure.com) | 請參閱[消息擴充教程](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot 中心消息擴充](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

添加消息擴充不會為消息路由增加延遲。

要嘗試消息擴充，請參閱[消息擴充教程](tutorial-message-enrichments.md)

## <a name="limitations"></a>限制

* 對於標準層或基本層中的這些中心，每個 IoT 中心最多可以添加 10 個擴充。 對於免費套餐中的 IoT 中心，您最多可以添加 2 個擴充。

* 在某些情況下，如果將具有值集于設備孿生中的標記或屬性的富集，則該值將標記為字串值。 例如，如果將富集值設置為 $twin.tag.field，則消息將加蓋字串"$twin.tag.field"，而不是孿生中該欄位的值。 在以下情況下，會發生這種情況：

   * IoT 中心位於基本層中。 基本層 IoT 中心不支援設備孿生。

   * IoT 中心位於標準層中，但發送消息的設備沒有設備孿生。

   * IoT 中心位於標準層中，但用於富集值的設備孿生路徑不存在。 例如，如果擴充值設置為 $twin.tag.location，並且設備孿生在標記下沒有位置屬性，則消息將加蓋字串"$twin.tag.location"。 

* 對設備孿生的更新最多可能需要五分鐘才能反映在相應的擴充值中。

* 總消息大小（包括擴充）不能超過 256 KB。 如果消息大小超過 256 KB，IoT 中心將刪除該消息。 您可以使用[IoT 中心指標](iot-hub-metrics.md)在刪除消息時識別和調試錯誤。 例如，您可以監視 d2c.遙測.出口。

* 消息豐富不適用於數位孿生變更事件[（IoT 隨插即用公共預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分）。

## <a name="pricing"></a>定價

消息豐富不收取額外費用。 目前，當您向 IoT 中心發送消息時，您需要付費。 即使消息轉到多個終結點，也只向該郵件收取一次費用。

## <a name="next-steps"></a>後續步驟

有關將消息路由到 IoT 中心的詳細資訊，請查看這些文章：

* [消息豐富教程](tutorial-message-enrichments.md)

* [使用 IoT 中心消息路由將設備到雲的消息發送到不同的終結點](iot-hub-devguide-messages-d2c.md)

* [教程：IoT 中心路由](tutorial-routing.md)
