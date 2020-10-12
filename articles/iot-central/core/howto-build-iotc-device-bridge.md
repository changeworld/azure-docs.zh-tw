---
title: 建置 Azure IoT Central 裝置橋接器 | Microsoft Docs
description: 建置用來將其他 IoT 雲端 (Sigfox、粒子、物聯網等) 連線至 IoT Central 應用程式的 IoT Central 裝置橋接器。
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80158413"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>建置用來將其他 IoT 雲端連線至 IoT Central 的 IoT Central 裝置橋接器

*此主題適用於系統管理員。*

IoT Central 裝置橋接器是一項開放原始碼解決方案，可將您的 Sigfox、粒子、物聯網和其他雲端連線至 IoT Central 應用程式。 無論您使用的資產追蹤裝置是連線到 Sigfox 的低功率廣域網路，或是在裝置雲端上使用空氣品質監視裝置，或使用 TTN 上的上料濕度監視裝置，您都可以使用 IoT Central 裝置橋接器直接利用 IoT Central 的功能。 裝置橋接器可藉由將您的裝置傳送到其他雲端的資料轉送至 IoT Central 應用程式，來連線其他 IoT 雲端與 IoT Central。 在您的 IoT Central 應用程式中，您可以建置規則並對該資料執行分析、在 Microsoft Flow 和 Azure 邏輯應用程式中建立工作流程、匯出該資料，以及其他工作。 從 GitHub 取得[IoT Central 裝置橋接器](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="what-is-it-and-how-does-it-work"></a>這是什麼？如何運作？
IoT Central 裝置橋接器是 GitHub 中的開放原始碼解決方案。 您可以使用 [部署至 Azure] 按鈕，將包含數個 Azure 資源的自訂 Azure Resource Manager 範本部署到您的 Azure 訂用帳戶。 這些資源包括：
-    Azure 函式應用程式
-    Azure 儲存體帳戶
-    取用方案
-    Azure 金鑰保存庫

函數應用程式是裝置橋接器的重要部分。 它可透過簡單的 Webhook 整合，從其他 IoT 平台或任何自訂平台接收 HTTP POST 要求。 我們提供了範例來說明如何連線至 Sigfox、粒子和 TTN 雲端。 如果您的平台可將 HTTP POST 要求傳送至函式應用程式，您即可輕鬆地擴充此解決方案以連線至您的自訂 IoT 雲端。
函式應用程式可將資料轉換成 IoT Central 可接受的格式，並透過 DPS API 加以轉送。

![Azure 函式螢幕擷取畫面](media/howto-build-iotc-device-bridge/azfunctions.png)

如果您的 IoT Central 應用程式可透過轉送訊息中的裝置識別碼來辨識裝置，即會出現該裝置的新量測。 如果您的 IoT Central 應用程式從未看到裝置識別碼，您的函式應用程式將會嘗試使用該裝置識別碼來註冊新裝置，而且在您的 IoT Central 應用程式中會顯示為「未關聯的裝置」。 

## <a name="how-do-i-set-it-up"></a>如何加以設定？
相關指示詳列在 GitHub 存放庫內的 README 檔案中。 

## <a name="pricing"></a>定價
Azure 資源會裝載在您的 Azure 訂用帳戶中。 您可以從 [README 檔案](https://aka.ms/iotcentralgithubdevicebridge)深入了解定價。

## <a name="next-steps"></a>後續步驟
您現在已了解如何建置 IoT Central 裝置橋接器，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的裝置](howto-manage-devices.md)
