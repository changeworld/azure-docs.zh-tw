---
title: 以 C 將 Windows 裝置佈建到遠端監視 - Azure | Microsoft Docs
description: 描述如何使用以 Windows 上執行的 C 所編寫的應用程式，將裝置連線到遠端監視解決方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "61450208"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>將裝置連線到遠端監視解決方案加速器 (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教學課程示範如何將真實裝置連線到遠端監視解決方案加速器。

如同大部分在受條件約束裝置上執行的內嵌應用程式，裝置應用程式的用戶端程式碼是以 C 撰寫的。在此教學課程中，您要在執行 Windows 的電腦上建置裝置用戶端應用程式。

如果您偏好模擬裝置，請參閱[建立及測試新模擬裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)。

## <a name="prerequisites"></a>必要條件

若要完成本操作指南中的步驟，請遵循[設定 Windows 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)中的步驟，將必要的開發工具和程式庫新增至 Windows 電腦。

## <a name="view-the-code"></a>檢視程式碼

您可以在 Azure IoT C SDK GitHub 存放庫中取得本指南中所使用的[範例程式碼](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)。

### <a name="download-the-source-code-and-prepare-the-project"></a>下載原始程式碼並準備專案

若要準備專案，請從 GitHub [複製 Azure IoT C SDK 存放庫](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) \(英文\)。

範例位於 **samples/solutions/remote_monitoring_client** 資料夾中。

請在文字編輯器中，開啟 **samples/solutions/remote_monitoring_client** 資料夾中的 **remote_monitoring.c** 檔案。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 編輯 **remote_monitoring.c** 檔案，將 `<connectionstring>` 取代為您在此操作指南一開始將裝置新增到解決方案加速器時所記下的裝置連接字串。

1. 請遵循[在 Windows 中建置 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) 中的步驟，來建置 SDK 和遠端監視用戶端應用程式。

1. 在用來建置解決方案的命令提示字元中，執行：

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    主控台會將訊息顯示為：

    - 應用程式會將範例遙測傳送到解決方案加速器。
    - 回應從解決方案儀表板叫用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
