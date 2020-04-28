---
title: Azure IoT 中樞 message 擴充的總覽
description: 本文說明 message 擴充，這可讓 IoT 中樞在訊息傳送至指定的端點之前，先以其他資訊對訊息進行戳記的功能。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75429111"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>裝置到雲端 IoT 中樞訊息的訊息擴充

*Message 擴充*是 IoT 中樞在訊息傳送至指定的端點之前，以其他資訊來*戳記*訊息的能力。 使用 message 擴充的其中一個原因是要包含可用於簡化下游處理的資料。 例如，使用裝置對應項標記來擴充裝置遙測訊息，可以降低客戶的負載，讓裝置對應項 API 呼叫此資訊。

![訊息擴充流程](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Message 擴充有三個主要元素：

* 擴充名稱或金鑰

* 值

* 應套用擴充的一或多個[端點](iot-hub-devguide-endpoints.md)。

索引**鍵**為字串。 金鑰只能包含英數位元或下列特殊字元：連字號（`-`）、底線（`_`）和句號（`.`）。

此**值**可以是下列任何一個範例：

* 任何靜態字串。 不允許動態值，例如條件、邏輯、作業和函數。 例如，如果您開發數個客戶所使用的 SaaS 應用程式，您可以將識別碼指派給每個客戶，並讓該識別碼可用於應用程式中。 當應用程式執行時，IoT 中樞會使用客戶的識別碼來戳記裝置遙測訊息，讓每個客戶能夠以不同方式處理訊息。

* 傳送訊息的 IoT 中樞名稱。 這個值是 *$iothubname*。

* 來自裝置對應項的資訊，例如其路徑。 範例會 *$twin. tags. 欄位*和 *$twin. 標記. 緯度*。

   > [!NOTE]
   > 此時，只會 $iothubname、$twin 標記、$twin 的屬性和 $twin。報告為 message 擴充所支援的變數。

訊息擴充會當做應用程式屬性新增至傳送至所選端點的訊息。  

## <a name="applying-enrichments"></a>套用擴充

訊息可以來自[IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)所支援的任何資料來源，包括下列範例：

* 裝置遙測資料，例如溫度或壓力
* 裝置對應項變更通知--裝置對應項中的變更
* 裝置生命週期事件，例如建立或刪除裝置時

您可以將擴充新增至要傳送至 IoT 中樞內建端點的訊息，或將訊息路由至自訂端點（例如 Azure Blob 儲存體、服務匯流排佇列或服務匯流排主題）。

您可以藉由選取端點作為事件方格，將擴充新增至要發佈至事件方格的訊息。 我們會根據您的「事件方格」訂用帳戶，在裝置遙測 IoT 中樞中建立預設路由。 這個單一路由可以處理您的所有事件方格訂用帳戶。 您可以在建立裝置遙測的事件方格訂用帳戶之後，設定事件方格端點的擴充。 如需詳細資訊，請參閱[Iot 中樞和事件方格](iot-hub-event-grid.md)。

擴充適用于每個端點。 如果您指定要為特定端點戳記的五個擴充，則所有傳送至該端點的訊息都會以相同的五個擴充戳記。

您可以使用下列方法來設定擴充：

| **方法** | **命令** |
| ----- | -----| 
| 入口網站 | [Azure 入口網站](https://portal.azure.com) | 請參閱[message 擴充教學](tutorial-message-enrichments.md)課程 | 
| Azure CLI   | [az iot hub message-擴充](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

新增 message 擴充並不會將延遲新增至訊息路由。

若要試用 message 擴充，請參閱[message 擴充教學](tutorial-message-enrichments.md)課程

## <a name="limitations"></a>限制

* 在標準或基本層中，您可以為這些中樞的每個 IoT 中樞新增最多10個擴充。 針對免費層中的 IoT 中樞，您最多可以新增2個擴充。

* 在某些情況下，如果您要套用的擴充值設定為裝置對應項中的標籤或屬性，則此值會被標記為字串值。 例如，如果 [擴充] 值設定為 [$twin]，則會以字串 "$twin 標記，而非對應項中該欄位的值來標記訊息。 這會發生在下列情況：

   * 您的 IoT 中樞是在基本層中。 基本層 IoT 中樞不支援裝置 twins。

   * 您的 IoT 中樞是在標準層中，但傳送訊息的裝置沒有裝置對應項。

   * 您的 IoT 中樞在標準層中，但擴充的值所使用的裝置對應項路徑不存在。 例如，如果 [擴充] 值設定為 [$twin 標籤]，而 [裝置對應項] 在 [標籤] 底下沒有 [位置] 屬性，則會以字串「$twin 標記」來標記訊息。 

* 裝置對應項的更新最多可能需要五分鐘的時間，才會反映在對應的擴充值中。

* 訊息大小總計（包括擴充）不能超過 256 KB。 如果訊息大小超過 256 KB，IoT 中樞將會捨棄訊息。 當訊息卸載時，您可以使用[IoT 中樞度量](iot-hub-metrics.md)來識別和偵測錯誤。 例如，您可以監視 d2c 無效。

* Message 擴充不適用於數位對應項變更事件（ [IoT 隨插即用公開預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分）。

## <a name="pricing"></a>定價

Message 擴充可免費使用。 目前，當您將訊息傳送至 IoT 中樞時，會向您收取費用。 即使訊息傳送至多個端點，您只需要針對該訊息收取一次。

## <a name="next-steps"></a>後續步驟

如需將訊息路由傳送至 IoT 中樞的詳細資訊，請參閱這些文章：

* [Message 擴充教學課程](tutorial-message-enrichments.md)

* [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程： IoT 中樞路由](tutorial-routing.md)
