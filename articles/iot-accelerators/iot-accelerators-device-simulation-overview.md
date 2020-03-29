---
title: 裝置模擬概觀 - Azure | Microsoft Docs
description: 「裝置模擬」解決方案加速器及其功能的描述。
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464881"
---
# <a name="device-simulation-solution-accelerator-overview"></a>裝置模擬解決方案加速器概觀

在雲端型 IoT 解決方案中，您的裝置會連線到雲端端點來傳送遙測資料，例如溫度、位置及狀態。 您的解決方案會取用這些資料，讓您能夠採取動作或從中獲得見解。

開發 IoT 解決方案時，實驗和測試是該程序的必要部分。 模擬是這整個程序中的重要工具。 藉由「裝置模擬」，您可以：

* 透過以動態方式調整模擬的裝置行為，來快速啟動並執行原型。 此程序可讓您在投資昂貴的硬體前，先實證構想的可行性。 您可以透過 Web UI 建立自訂裝置，在幾秒內產生原型裝置。
* 藉由模擬實際的裝置行為，驗證解決方案從裝置到解決方案都能如預期般運作。 您可以使用 JavaScript 來撰寫複雜的裝置行為指令碼，以產生逼真的模擬遙測資料。
* 藉由模擬正常、尖峰及超出尖峰負載的狀況，為您的解決方案進行規模測試。 規模測試也可讓您調整出執行解決方案所需的 Azure 資源正確大小。

![範例無人機模擬](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

您可以利用「裝置模擬」來定義裝置模型，以模擬您的實際裝置。 此模型包含訊息格式、對應項屬性及方法。 您也可以使用 JavaScript 來模擬複雜的裝置行為。

您可以針對一到數千個連線至任何 IoT 中樞的裝置執行模擬。 為了協助測試，您可以視需要搭配「裝置模擬」部署一個 IoT 中樞作為獨立環境。

「裝置模擬」是免費功能。 不過，「裝置模擬」會部署至您的雲端 Azure 訂用帳戶中，因此會取用 Azure 資源。 如果「裝置模擬」不符合您的需求，[GitHub 上也提供原始程式碼](https://github.com/Azure/device-simulation-dotnet)，可供您複製和修改。

## <a name="sample-simulations"></a>範例模擬

當您部署「裝置模擬」時，會取得一些範例模擬和範例裝置。 您可以利用這些範例來了解如何使用「裝置模擬」。 若要開始進行，請執行一個[模擬 10 輛卡車的範例模擬](quickstart-device-simulation-deploy.md)。 您也可以[使用所提供眾多範例裝置的其中一個來建立自己的模擬](iot-accelerators-device-simulation-create-simulation.md)。

![模擬組態](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>自訂模擬的裝置

您可以使用「裝置模擬」來[建立自訂裝置模型](iot-accelerators-device-simulation-create-custom-device.md)，以在您的模擬中使用。 例如，您可以定義一個會傳送溫度和濕度遙測資料的新冰箱裝置模型。 自訂的模擬裝置非常適用於具有隨機、遞增或遞減遙測值的簡單裝置行為。

![建立裝置型號](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>進階模擬裝置

當您需要對裝置所傳送的遙測值有更多掌控時，可以使用進階裝置模型。 進階裝置模型會啟用 JavaScript 支援以操作所傳送的遙測值。 例如，您可以模擬艷陽天下某部停放車輛的內部溫度 - 當外部溫度上升時，內部溫度會以指數方式增加。

進階裝置模型可讓您[建立及上傳自己的裝置模型](iot-accelerators-device-simulation-advanced-device.md)，這些裝置模型會由 JSON 裝置定義檔和相對應的 JavaScript 檔案所組成。

進階裝置模型可讓您：

* 指定從裝置傳送的訊息格式及遙測類型。
* 使用自訂指令碼處理來產生隨時間維護裝置狀態的遙測值。
* 使用自訂指令碼處理來指定模擬裝置回應方法的方式。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解「裝置模擬」解決方案加速器及其功能。 若要開始使用解決方案加速器，請繼續進行快速入門：

> [!div class="nextstepaction"]
> [在 Azure 中部署和執行 IoT 裝置模擬](quickstart-device-simulation-deploy.md)
