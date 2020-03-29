---
title: 使用 C 將 Raspberry Pi 佈建到遠端監視 - Azure | Microsoft Docs
description: 描述如何使用以 C 編寫的應用程式，將 Raspberry Pi 裝置連線到遠端監視解決方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454489"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>將 Raspberry Pi 裝置連線到遠端監視解決方案加速器 (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教學課程示範如何將真實裝置連線到遠端監視解決方案加速器。 如同大部分在受條件約束裝置上執行的內嵌應用程式，Raspberry Pi 裝置應用程式的用戶端程式碼是以 C 撰寫的。在此教學課程中，您要在執行 Raspbian OS 的 Raspberry Pi 上建置應用程式。

如果您偏好模擬裝置，請參閱[建立及測試新模擬裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)。

### <a name="required-hardware"></a>必要的硬體

一部桌上型電腦，可讓您從遠端連線到 Raspberry Pi 上的命令列。

[適用於 Raspberry Pi 3 的 Microsoft IoT 入門套件](https://azure.microsoft.com/develop/iot/starter-kits/)或對等的元件。 本教學課程會使用套件中的下列項目：

- Raspberry Pi 3
- MicroSD 卡 (具有 NOOBS)
- USB Mini 連接線
- 乙太網路連接線

### <a name="required-desktop-software"></a>必要的桌面軟體

您需要透過桌上型電腦上的 SSH 用戶端，才能從遠端存取 Raspberry Pi 上的命令列。

- Windows 不包含 SSH 用戶端。 我們建議使用 [PuTTY](https://www.putty.org/)。
- 大部分的 Linux 散發套件和 Mac OS 都包含命令列 SSH 公用程式。 如需詳細資訊，請參閱[使用 Linux 或 Mac OS 的 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)。

### <a name="required-raspberry-pi-software"></a>必要的 Raspberry Pi 軟體

本文假設您已將最新版的 [Raspbian OS 安裝在 Raspberry Pi 上](https://www.raspberrypi.org/learning/software-guide/quickstart/)。

下列步驟說明如何準備 Raspberry Pi，以建置連線到解決方案加速器的 C 應用程式：

1. 使用 **ssh** 連線至您的 Raspberry Pi。 如需詳細資訊，請參閱 [Raspberry Pi 網站](https://www.raspberrypi.org/)上的 [SSH (安全殼層)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)。

1. 若要更新 Raspberry Pi，請使用下列命令︰

    ```sh
    sudo apt-get update
    ```

1. 若要完成本操作指南中的步驟，請依照[設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) \(英文\) 中的步驟，將必要的開發工具和程式庫新增至 Raspberry Pi。

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
