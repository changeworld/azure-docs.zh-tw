---
title: Azure IoT 中樞 message 擴充總覽
description: 本文說明 message 擴充，它可讓 IoT 中樞在訊息傳送至指定的端點之前，能夠以其他資訊來對訊息加上戳記。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 792486a78dfed606ce8317d9bc037221ae33d508
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767191"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>裝置到雲端 IoT 中樞訊息的訊息擴充

*Message 擴充* 是 IoT 中樞在訊息傳送至指定的端點之前，以其他資訊來 *戳記* 訊息的能力。 使用 message 擴充的其中一個原因是要包含可用於簡化下游處理的資料。 例如，使用裝置對應項標記擴充裝置遙測訊息可以減少客戶的負載，以針對此資訊進行裝置對應項 API 呼叫。

![訊息擴充流程](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Message 擴充有三個主要元素：

* 擴充名稱或索引鍵

* 值

* 應套用擴充的一或多個 [端點](iot-hub-devguide-endpoints.md) 。

索引 **鍵** 為字串。 索引鍵只能包含英數位元或這些特殊字元：連字號 (`-`) 、底線 (`_`) 和期間 (`.`) 。

此 **值** 可以是下列任何一個範例：

* 任何靜態字串。 不允許動態值，例如條件、邏輯、作業和函數。 例如，如果您開發了多個客戶所使用的 SaaS 應用程式，您可以將識別碼指派給每個客戶，並讓該識別碼可在應用程式中使用。 當應用程式執行時，IoT 中樞會使用客戶的識別碼來將裝置遙測訊息加上戳記，讓每個客戶都能以不同的方式處理訊息。

* 傳送訊息的 IoT 中樞名稱。 此值為 *$iothubname*。

* 來自裝置對應項的資訊，例如它的路徑。 範例會是 *$twin. tag* . tag.*緯度和 $twin。*

   > [!NOTE]
   > 目前，只有 $iothubname、$twin 標記、$twin 和 $twin。報告是訊息擴充支援的變數。

訊息擴充會以應用程式屬性的形式新增至傳送至所選端點 (s) 的訊息。  

## <a name="applying-enrichments"></a>套用擴充

這些訊息可以來自 [IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)所支援的任何資料來源，包括下列範例：

* 裝置遙測資料，例如溫度或壓力
* 裝置對應項變更通知--裝置對應項中的變更
* 裝置生命週期事件，例如裝置建立或刪除的時間

您可以將擴充新增至要傳送至 IoT 中樞內建端點的訊息，或是要路由傳送至自訂端點（例如 Azure Blob 儲存體、服務匯流排佇列或服務匯流排主題）的訊息。

您可以藉由選取端點作為事件方格，將擴充新增至要發佈至事件方格的訊息。 我們會根據您的事件方格訂用帳戶，在 IoT 中樞中建立裝置遙測的預設路由。 此單一路由可以處理您所有的事件方格訂用帳戶。 您可以在建立裝置遙測的事件方格訂用帳戶之後，設定事件方格端點的擴充。 如需詳細資訊，請參閱「 [Iot 中樞」和「事件方格](iot-hub-event-grid.md)」。

擴充適用于每個端點。 如果您指定要針對特定端點戳記的五個擴充，則傳送至該端點的所有訊息都會以相同的五個擴充戳記。

您可以使用下列方法來設定擴充：

| **方法** | **命令** |
| ----- | -----| 
| 入口網站 | [Azure 入口網站](https://portal.azure.com) | 請參閱[message 擴充教學](tutorial-message-enrichments.md)課程 | 
| Azure CLI   | [az iot hub message-擴充](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment) |

新增 message 擴充不會將延遲新增至訊息路由。

若要試用 message 擴充，請參閱[message 擴充教學](tutorial-message-enrichments.md)課程

## <a name="limitations"></a>限制

* 針對標準層或基本層中的中樞，您最多可以為每個 IoT 中樞新增10個擴充。 針對免費層中的 IoT 中樞，您最多可以新增2個擴充。

* 在某些情況下，如果您要在裝置對應項中套用值設定為標籤或屬性的擴充，此值將會加上字串值的戳記。 例如，如果擴充值設定 $twin 為 []，就會使用字串 "$twin. tag" 來標記訊息，而不是對應項中該欄位的值。 這會發生在下列情況：

   * 您的 IoT 中樞位於基本層。 基本層 IoT 中樞不支援裝置 twins。

   * 您的 IoT 中樞位於標準層，但傳送訊息的裝置沒有裝置對應項。

   * 您的 IoT 中樞位於標準層，但用於擴充值的裝置對應項路徑不存在。 例如，如果擴充值設定為 $twin. 標籤，而裝置對應項在標記底下沒有 location 屬性，則會以字串 "$twin. tag. location" 將訊息加上戳記。 

* 裝置對應項的更新最多可能需要五分鐘的時間，才會反映在對應的擴充值中。

* 訊息大小總計（包括擴充）不能超過 256 KB。 如果訊息大小超過 256 KB，IoT 中樞就會捨棄訊息。 您可以使用 [IoT 中樞計量](iot-hub-metrics.md) ，在訊息卸載時識別及偵測錯誤。 例如，您可以監視 d2c。無效。

* Message 擴充不會套用到數位對應項變更事件。

## <a name="pricing"></a>定價

訊息擴充可供免費使用。 目前，當您將訊息傳送至 IoT 中樞時，系統會向您收費。 即使訊息傳送至多個端點，您只需針對該訊息支付一次費用。

## <a name="next-steps"></a>後續步驟

如需將訊息路由傳送至 IoT 中樞的詳細資訊，請參閱下列文章：

* [Message 擴充教學課程](tutorial-message-enrichments.md)

* [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程： IoT 中樞路由](tutorial-routing.md)
