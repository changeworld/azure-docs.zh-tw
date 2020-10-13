---
title: 如何部署影片分析-物件和動作偵測 Azure IoT Central 應用程式範本
description: 本指南摘要說明使用影片分析-物件和動作偵測應用程式範本部署 Azure IoT Central 應用程式的步驟。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873331"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>如何使用影片分析部署 IoT Central 應用程式-物件和動作偵測應用程式範本

如需重要的 *影片分析-物件和動作偵測* 應用程式元件的總覽，請參閱 [物件和動作偵測影片分析應用程式架構](architecture-video-analytics.md)。

下列影片將逐步解說如何使用 _影片分析-物件和動作偵測應用程式範本_ 來部署 IoT Central 方案：

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>部署應用程式

請完成下列步驟，使用影片分析應用程式範本部署 IoT Central 應用程式：

1. 完成 [Azure IoT Central (YOLO v3 中的建立影片分析應用程式) ](tutorial-video-analytics-create-app-yolo-v3.md) 或在 [Azure IoT Central 中建立影片分析 (OpenVINO &trade;) ](tutorial-video-analytics-create-app-openvino.md) 教學課程，以：
    - 建立 Azure 媒體服務帳戶。
    - 從影片分析-物件和動作偵測應用程式範本，建立 IoT Central 應用程式。
    - 在 IoT Central 應用程式中設定閘道裝置。 閘道可讓相機裝置連接到應用程式。

1. 完成 [建立適用于影片分析 (LINUX VM 的 IoT Edge 實例) ](tutorial-video-analytics-iot-edge-vm.md) 或教學課程 [：建立影片分析的 IoT Edge 實例 (Intel NUC) ](tutorial-video-analytics-iot-edge-nuc.md) 教學課程，以：
    - 建立已安裝 Azure IoT Edge runtime 的 Azure VM。-準備用來裝載影片分析模組的 IoT Edge 安裝。
    - 將 IoT Edge 裝置連線到您的 IoT Central 應用程式。

1. 完成 [監視和管理影片分析應用程式](tutorial-video-analytics-manage.md) 教學課程，以：
    - 將物件和動作偵測攝影機新增至 IoT Central 應用程式中的閘道。
    - 開始相機處理。
    - 安裝本機媒體播放機來觀看 AMS 中的已捕獲影片。
    - 查看顯示偵測到之物件的已捕獲影片。
    - 整理。

## <a name="next-steps"></a>後續步驟

現在您已大致瞭解部署和使用影片分析應用程式範本的步驟，請參閱 [Azure IoT Central (YOLO v3 中的建立影片分析應用程式) ](tutorial-video-analytics-create-app-yolo-v3.md) 或 [在 Azure IoT Central (OpenVINO &trade;) 中建立影片分析 ](tutorial-video-analytics-create-app-openvino.md) 以開始使用。
