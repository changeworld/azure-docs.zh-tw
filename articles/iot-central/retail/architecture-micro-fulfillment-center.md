---
title: Azure IoT Central 微型物流中心 | Microsoft Docs
description: 了解如何使用 IoT Central 中的微型物流中心應用程式範本來建置微型物流中心應用程式
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 9a0d91677770e89f3e249fc699057332cccaa323
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347467"
---
# <a name="micro-fulfillment-center-architecture"></a>微型物流中心架構

微型物流中心解決方案可讓您以數位方式連線、監視及管理全自動化物流中心的各種層面，以避免停止運作狀況，同時提高安全性和整體效率，從而降低成本。 您可以利用 IoT Central 內的其中一個應用程式範本及下列架構作為指引，來建置這些解決方案。

![Azure IoT Central 商店分析](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. 將遙測資料傳送至閘道裝置的一組 IoT 感應器
2. 將遙測資料和彙總的見解傳送至 IoT Central 的閘道裝置
3. 連續資料匯出至所需的 Azure 服務以進行操作
4. 資料可利用所需格式進行結構化，並傳送至儲存體服務
5. 商務應用程式可以查詢資料，並產生支援零售營運的見解
 
讓我們看看在微型物流中心解決方案中常會用到的主要元件。

## <a name="robotic-carriers"></a>機器人載具

微型物流中心解決方案可能有大量機器人載具會產生類型不同的遙測訊號。 這些訊號可以由閘道裝置內嵌、彙總，然後傳送至 IoT Central，如架構圖左側所反映。  

## <a name="condition-monitoring-sensors"></a>狀況監視感應器

IoT 解決方案一開始會以一組感應器從您的物流中心內擷取有意義的訊號。 這會透過上述架構圖最左邊的不同類型感應器來反映。

## <a name="gateway-devices"></a>閘道裝置

許多 IoT 感應器都可將原始信號直接饋送到雲端或位於其附近的閘道裝置。 閘道裝置會在將摘要見解傳送到 IoT Central 應用程式之前，先在邊緣執行資料彙總。 閘道裝置也會負責將命令和控制作業轉送到感應器裝置 (如果適用)。 

## <a name="iot-central-application"></a>IoT Central 應用程式

Azure IoT Central 應用程式會內嵌來自物流中心環境內的不同類型 IoT 感應器、機器人及閘道裝置的資料，並產生一組有意義的見解。

Azure IoT Central 也為店員提供量身打造的體驗，讓他們能夠從遠端監視和管理基礎結構裝置。

## <a name="data-transform"></a>資料轉換
解決方案內的 Azure IoT Central 應用程式可設定為將原始或彙總的見解匯出至一組 Azure PaaS (平台即服務) 服務，這些服務可以執行資料操作，並且在將這些見解放入企業應用程式之前加以擴充。 

## <a name="business-application"></a>商務應用程式
IoT 資料可用來為零售環境內部署的不同類型商務應用程式提供強大的功能。 物流中心經理或員工成員可以利用這些應用程式來將商業見解視覺化，並即時採取有意義的動作。 若要了解如何為零售小組建置即時 Power BI 儀表板，請遵循此[教學課程](./tutorial-in-store-analytics-create-app.md)。

## <a name="next-steps"></a>後續步驟
* 開始使用[微型物流中心](https://aka.ms/checkouttemplate)應用程式範本。 
* 請參閱[教學課程](https://aka.ms/mfc-tutorial)，以逐步了解如何利用微型物流中心應用程式範本來建置解決方案。
