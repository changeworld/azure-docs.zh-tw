---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876626"
---
範例應用程式包含兩個模擬裝置和一個 IoT Edge 閘道。 下列教學課程會示範兩種試驗和了解閘道功能的方法：

* 在 Azure VM 中建立 IoT Edge 閘道，並與模擬相機連線。
* 在真實的裝置 (例如 Intel NUC) 上建立 IoT Edge 閘道，並連接真實的相機。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用 Azure IoT Central 的影片分析應用程式範本來建立零售商店應用程式
> * 自訂應用程式設定
> * 為 IoT Edge 閘道裝置建立裝置範本
> * 將閘道裝置新增至 IoT Central 應用程式

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程系列，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。
* 如果您使用的是真實的相機，您需要連接 IoT Edge 裝置與攝影機，而且需要**即時串流通訊協定**通道。

## <a name="initial-setup"></a>初始設定

在這些教學課程中，您會更新及使用數個設定檔。 這些檔案的初始版本可在 [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 存放庫中取得。 此存放庫也包含可供您下載的便籤文字檔，可讓您用來記錄所部署服務的設定值。

在您的本機電腦上建立名為 lva-configuration 的資料夾，以儲存這些檔案的副本。 然後以滑鼠右鍵按一下下列每個連結，然後選擇 [另存新檔]，將檔案儲存至 lva-configuration 資料夾：
