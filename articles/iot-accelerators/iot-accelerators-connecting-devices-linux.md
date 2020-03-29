---
title: 以 C 將 Linux 裝置佈建到遠端監視 - Azure | Microsoft Docs
description: 描述如何使用以 Linux 上執行的 C 所編寫的應用程式，將裝置連線到遠端監視解決方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454485"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>將裝置連線到遠端監視解決方案加速器 (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教學課程示範如何將真實裝置連線到遠端監視解決方案加速器。

如同大部分在受條件約束裝置上執行的內嵌應用程式，裝置應用程式的用戶端程式碼是以 C 撰寫的。在此教學課程中，您要在執行 Ubuntu (Linux) 的電腦上建置應用程式。

如果您偏好模擬裝置，請參閱[建立及測試新模擬裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成此操作指南中的步驟，您需要執行 Ubuntu 版本 15.04 或更新版本的裝置。 繼續之前，請先[設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) \(英文\)。

## <a name="view-the-code"></a>檢視程式碼

您可以在 Azure IoT C SDK GitHub 存放庫中取得本指南中所使用的[範例程式碼](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)。

### <a name="download-the-source-code-and-prepare-the-project"></a>下載原始程式碼並準備專案

若要準備專案，請從 GitHub 複製或下載[Azure IoT C SDK 存放庫](https://github.com/Azure/azure-iot-sdk-c)。

範例位於 **samples/solutions/remote_monitoring_client** 資料夾中。

請在文字編輯器中，開啟 **samples/solutions/remote_monitoring_client** 資料夾中的 **remote_monitoring.c** 檔案。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>建置並執行應用程式

下列步驟說明如何使用 *CMake* 來建置用戶端應用程式。 在 SDK 的建置過程中會一併建置遠端監視用戶端應用程式。

1. 編輯 **remote_monitoring.c** 檔案，將 `<connectionstring>` 取代為您在此操作指南一開始將裝置新增到解決方案加速器時所記下的裝置連接字串。

1. 瀏覽至 [Azure IoT C SDK 存放庫](https://github.com/Azure/azure-iot-sdk-c)之複製複本的根目錄，然後執行下列命令來建置用戶端應用程式：

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 執行用戶端應用程式，並將遙測傳送至 IoT 中樞：

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    主控台會將訊息顯示為：

    - 應用程式會將範例遙測傳送到解決方案加速器。
    - 回應從解決方案儀表板叫用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
