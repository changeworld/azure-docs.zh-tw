---
title: 將 DevKit 裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 作為設備開發人員，瞭解如何使用 IoT 隨插即用（預覽）將 MXChip IoT DevKit 設備連接到 Azure IoT 中央應用程式。
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158539"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式

本文介紹如何將 MXChip IoT DevKit （DevKit） 設備連接到 Azure IoT 中央應用程式。 設備使用 DevKit 設備的經認證的 IoT 隨插即用（預覽）型號來配置其與 IoT 中心的連接。

在本"如何"一文中，您：

- 從 IoT 中央應用程式獲取連接詳細資訊。
- 準備設備並將其連接到 IoT 中心應用程式。
- 查看 IoT 中心中的設備的遙測和屬性。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您需要下列資源：

- [DevKit 設備](https://aka.ms/iot-devkit-purchase)。
- IoT 中心應用程式。 您可以按照[創建 IoT 中央應用程式](./quick-deploy-iot-central.md)中的步驟操作。

## <a name="get-device-connection-details"></a>獲取設備連接詳細資訊

1. 在 Azure IoT 中心應用程式中，選擇 **"設備範本"** 選項卡並選擇 **"新建**"。 在"**使用預配置的設備範本**"一節中，選擇**MXChip IoT 開發人員工具組**。

    ![MXChip IoT 開發工具組的設備範本](media/howto-connect-devkit/device-template.png)

1. 選擇 **"下一步"：自訂**，然後**創建**。

1. 選擇 **"設備"** 選項卡。在設備清單中，選擇**MXChip IoT DevKit，** 然後選擇 **"新建"** 以從範本創建新設備。

    ![新設備](media/howto-connect-devkit/new-device.png)

1. 在快顯視窗中，將**裝置識別碼**輸入`SampleDevKit`為 **，設備名稱**為`MXChip IoT DevKit - Sample`。 確保 **"類比"** 選項已關閉。 然後選擇 **"創建**"。

    ![裝置識別碼 和名稱](media/howto-connect-devkit/device-id-name.png)

1. 選擇您創建的設備，然後選擇 **"連接**"。 記下**ID 範圍**、設備**ID**和**主鍵**。 在這篇"如何"一文的後面部分，您需要這些值。

    ![設備連接資訊](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>準備設備

1. 從 GitHub 下載 DevKit 設備的最新[預構建的 Azure IoT 中央隨插即用（預覽）固件](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin)。

1. 使用 USB 纜線將 DevKit 裝置連接到開發電腦。 在 Windows 中，檔案總管視窗會在對應到 DevKit 裝置上儲存體的磁碟機上開啟。 例如，此磁碟機可能會稱為 **AZ3166 (D:)**。

1. 將**iotc_devkit.bin**檔拖到磁碟機視窗中。 複製完成時，使用新的韌體重新啟動裝置。

    > [!NOTE]
    > 如果您在螢幕上看到錯誤（如**無 Wi-Fi），** 這是因為 DevKit 尚未連接到 WiFi。

1. 在 DevKit 上，按住**按鈕 B**，按下並釋放 **"重置"** 按鈕，然後釋放**按鈕 B**。設備現在處於存取點模式。 要確認，螢幕將顯示"IoT DevKit - AP"和配置門戶 IP 位址。

1. 在電腦或平板電腦上，連接到設備螢幕上顯示的 WiFi 網路名稱。 WiFi 網路以**AZ 開頭，** 後跟 MAC 位址。 當您連接到此網路時，您沒有互聯網接入。 此狀態是預期的，並且僅在配置設備時連接到此網路很短的時間。

1. 打開 Web 瀏覽器並導航[http://192.168.0.1/](http://192.168.0.1/)到 。 以下網頁隨即顯示：

    ![配置 UI](media/howto-connect-devkit/config-ui.png)

    在網頁上，輸入：

    - WiFi 網路 （SSID） 的名稱。
    - 您的 WiFi 網路密碼。
    - 連接詳細資訊：輸入您以前記下**的裝置識別碼、ID****範圍**和**SAS 主金鑰**。

    > [!NOTE]
    > 目前，IoT DevKit 只能連接到 2.4 GHz Wi-Fi，由於硬體限制，不支援 5 GHz。

1. 選擇 **"配置設備**"，DevKit 設備重新開機並運行應用程式：

    ![重新開機 UI](media/howto-connect-devkit/reboot-ui.png)

    DevKit 螢幕顯示應用程式正在運行的確認：

    ![正在運行的 DevKit](media/howto-connect-devkit/devkit-running.png)

DevKit 首先在 IoT 中央應用程式中註冊新設備，然後開始發送資料。

## <a name="view-the-telemetry"></a>檢視遙測資料

在此步驟中，您可以在 Azure IoT 中央應用程式中查看遙測資料。

在 IoT 集中應用程式中，選擇 **"設備"** 選項卡，選擇您添加的設備。 在 **"概述"** 選項卡中，您可以看到來自 DevKit 設備的遙測資料：

![IoT 中央設備概述](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>檢閱程式碼

要查看代碼或修改和編譯代碼，請轉到[代碼示例](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何將 DevKit 設備連接到 Azure IoT 中央應用程式，建議的下一步是瞭解如何為自己的 IoT 設備[設置自訂設備範本](./howto-set-up-template.md)。
