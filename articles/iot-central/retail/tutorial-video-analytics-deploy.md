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
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719656"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>如何使用影片分析部署 IoT Central 應用程式-物件和動作偵測應用程式範本

如需重要的 *影片分析-物件和動作偵測* 應用程式元件的總覽，請參閱 [物件和動作偵測影片分析應用程式架構](architecture-video-analytics.md)。

## <a name="deploy-the-application"></a>部署應用程式

請完成下列步驟，使用影片分析應用程式範本部署 IoT Central 應用程式：

1. 完成在 [Azure IoT Central 教學課程中建立影片分析應用程式](tutorial-video-analytics-create-app.md) ，以：
    - 建立 Azure 媒體服務帳戶。
    - 從影片分析-物件和動作偵測應用程式範本，建立 IoT Central 應用程式。
    - 在 IoT Central 應用程式中設定閘道裝置。 閘道可讓相機裝置連接到應用程式。

1. 完成 [建立影片分析 (LINUX VM 的 IoT Edge 實例) ](tutorial-video-analytics-iot-edge-vm.md) 教學課程，以：
    - 建立已安裝 Azure IoT Edge runtime 的 Azure VM。-準備用來裝載影片分析模組的 IoT Edge 安裝。
    - 將 IoT Edge 裝置連線到您的 IoT Central 應用程式。

1. 完成 [監視和管理影片分析應用程式](tutorial-video-analytics-manage.md) 教學課程，以：
    - 將物件和動作偵測攝影機新增至 IoT Central 應用程式中的閘道。
    - 開始相機處理。
    - 安裝本機媒體播放機來觀看 AMS 中的已捕獲影片。
    - 查看顯示偵測到之物件的已捕獲影片。
    - 整理。

## <a name="next-steps"></a>接下來的步驟

現在您已大致瞭解部署和使用影片分析應用程式範本的步驟，請參閱 [在 Azure IoT Central 中建立影片分析應用程式](tutorial-video-analytics-create-app.md) 以開始使用。
