---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9a20dca71727e83db98c4c97567949bd127fc7fb
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111242"
---
[訊息路由](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)可讓您將遙測資料從 IoT 裝置傳送至內建事件中樞相容端點或自訂端點 (例如 Blob 儲存體、服務匯流排佇列、服務匯流排主題和事件中樞)。 若要設定自訂的訊息路由，您可以建立[路由查詢](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)來自訂符合特定條件的路由。 設定好之後，IoT 中樞就會自動將內送資料路由至端點。 如果訊息不符合任何已定義的路由查詢，系統就會將其路由傳送至預設端點。

在這個有兩個部分的教學課程中，您將了解如何透過 IoT 中樞設定和使用這些自訂的路由查詢。 您會將訊息從 IoT 裝置路由至多個端點的其中一個，包括 Blob 儲存體和服務匯流排佇列。 邏輯應用程式會挑選傳送至服務匯流排佇列的訊息，並透過電子郵件傳送。 未定義自訂訊息路由的訊息會傳送到預設端點，然後由 Azure 串流分析挑選並在 Power BI 視覺效果中檢視。

若要完成本教學課程的第 1 和第 2 部分，請執行下列工作：

**第一部分：建立資源、設定訊息路由**
> [!div class="checklist"]
> * 建立資源 - IoT 中樞、儲存體帳戶、服務匯流排佇列和模擬裝置。 此作業可使用 Azure 入口網站、Azure Resource Manager 範本、Azure CLI 或 Azure PowerShell 來完成。
> * 在 IoT 中樞內設定儲存體帳戶和服務匯流排佇列的端點及訊息路由。

**第二部分：將訊息傳送至中樞、檢視路由的結果**
> [!div class="checklist"]
> * 建立邏輯應用程式，使其在訊息新增至服務匯流排佇列時觸發並傳送電子郵件。
> * 下載並執行模擬 IoT 裝置的應用程式，針對不同路由選項將訊息傳送至中樞。
> * 為傳送至預設端點的資料建立 Power BI 視覺效果。
> * 檢視結果...
> * ...在服務匯流排佇列和電子郵件中。
> * ...在儲存體帳戶中。
> * ...在 Power BI 視覺效果中。

## <a name="prerequisites"></a>Prerequisites

* 針對本教學課程的第 1 部分：
  - 您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 針對本教學課程的第 2 部分：
  - 您必須已完成本教學課程的第 1 部分，且有仍然可用的資源。
  - 安裝 [Visual Studio](https://www.visualstudio.com/)。
  - 可存取 PowerBI 帳戶以分析預設端點的串流分析。 ([免費試用 Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)。)
  - 具有用來傳送通知電子郵件的 Office 365 帳戶。
  - 請確定您的防火牆已開啟連接埠 8883。 本教學課程中的範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
