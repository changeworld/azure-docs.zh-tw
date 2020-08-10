---
title: 如何部署影片分析-物件和動作偵測 Azure IoT Central 應用程式範本
description: 本指南摘要說明使用影片分析-物件和動作偵測應用程式範本來部署 Azure IoT Central 應用程式的步驟。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d2ab93708950743eafdb6cf733273b602cfb5a68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038169"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>如何使用影片分析來部署 IoT Central 應用程式-物件和動作偵測應用程式範本

如需重要的*影片分析-物件和動作偵測*應用程式元件的總覽，請參閱[物件和動作偵測影片分析應用程式架構](architecture-video-analytics.md)。

## <a name="deploy-the-application"></a>部署應用程式

請完成下列步驟，使用影片分析應用程式範本來部署 IoT Central 應用程式：

1. 完成[在 Azure 中建立影片分析應用程式 IoT Central](tutorial-video-analytics-create-app.md)教學課程，以：
    - 建立 Azure 媒體服務帳戶。
    - 從 [影片分析-物件和動作偵測] 應用程式範本建立 IoT Central 應用程式。
    - 在 IoT Central 應用程式中設定閘道裝置。 閘道可讓相機裝置連接到應用程式。

1. 完成[建立影片分析 (LINUX VM 的 IoT Edge 實例) ](tutorial-video-analytics-iot-edge-vm.md)教學課程，以：
    - 建立已安裝 Azure IoT Edge 執行時間的 Azure VM。-準備 IoT Edge 安裝以裝載影片分析模組。
    - 將 IoT Edge 裝置連線到您的 IoT Central 應用程式。

1. 完成[監視並管理影片分析應用程式](tutorial-video-analytics-manage.md)教學課程，以：
    - 將物件和動作偵測攝影機新增至 IoT Central 應用程式中的閘道。
    - 開始相機處理。
    - 安裝本機媒體播放機以在 AMS 中觀看已捕獲的影片。
    - 觀看已捕獲的影片，顯示偵測到的物件。
    - 整理。

## <a name="next-steps"></a>後續步驟

現在您已大致瞭解部署和使用影片分析應用程式範本的步驟，請參閱[在 Azure IoT Central 中建立影片分析 n 應用程式](tutorial-video-analytics-create-app.md)以開始使用。
