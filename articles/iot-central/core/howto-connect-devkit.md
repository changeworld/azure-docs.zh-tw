---
title: 將 DevKit 裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 作為設備開發人員,瞭解如何使用 IoT 隨插即用(預覽)將 MXChip IoT DevKit 裝置連接到 Azure IoT 中央應用程式。
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756802"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式

*本文適用於設備開發人員。*

本文介紹如何將 MXChip IoT DevKit (DevKit) 設備連接到 Azure IoT 中央應用程式。 設備使用 DevKit 裝置的經認證的 IoT 隨插即用(預覽)型號來配置其與 IoT 中心的連接。

在本「如何」一文中,您:

- 從 IoT 中央應用程式獲取連接詳細資訊。
- 準備設備並將其連接到IoT中心應用程式。
- 查看 IoT 中心中的設備的遙測和屬性。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您需要下列資源：

- [DevKit 裝置](https://aka.ms/iot-devkit-purchase)。
- IoT 中心應用程式。 您可以按照[創建 IoT 中央應用程式](./quick-deploy-iot-central.md)中的步驟操作。

## <a name="get-device-connection-details"></a>取得裝置連線詳細資訊

1. 在 Azure IoT 中心應用程式中,選擇 **「設備範本」** 選項卡並選擇 **「新建**」。 在「**使用預先設定的裝置樣本**」 一個項目中, 選擇**MXChip IoT 開發人員工具套件**。

    ![MXChip IoT 開發工具套件的裝置樣本](media/howto-connect-devkit/device-template.png)

1. 選擇 **「下一步」:自訂**,然後**建立**。

1. 選擇**裝置'** 選項卡。在裝置清單中,選擇**MXChip IoT DevKit,** 然後選擇 **「新建」** 以從範本創建新裝置。

    ![新裝置](media/howto-connect-devkit/new-device.png)

1. 在彈出視窗中,將**裝置**ID`SampleDevKit`輸入為 **,裝置名稱**為`MXChip IoT DevKit - Sample`。 確保 **「類比」** 選項已關閉。 然後選取 [建立]  。

    ![裝置識別碼與名稱](media/howto-connect-devkit/device-id-name.png)

1. 選擇您創建的設備,然後選擇 **「連接**」。 將**ID 範圍**、裝置**代碼**與**主鍵**。 在這篇"如何"一文的後面部分,您需要這些值。

    ![裝置連線資訊](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>準備裝置

1. 從 GitHub 下載 DevKit 裝置的最新[預置的 Azure IoT 中央即插即用(預覽)韌體](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin)。

1. 使用 USB 纜線將 DevKit 裝置連接到開發電腦。 在 Windows 中，檔案總管視窗會在對應到 DevKit 裝置上儲存體的磁碟機上開啟。 例如，此磁碟機可能會稱為 **AZ3166 (D:)**。

1. 將**iotc_devkit.bin**檔拖到驅動器視窗中。 複製完成時，使用新的韌體重新啟動裝置。

    > [!NOTE]
    > 如果您在螢幕上看到錯誤(如**無 Wi-Fi),** 這是因為 DevKit 尚未連接到 WiFi。

1. 在 DevKit 上,按住**按鍵 B**,按下並釋放 **「重置」** 按鈕,然後釋放**按鈕 B**。設備現在處於接入點模式。 要確認,螢幕將顯示"IoT DevKit - AP"和配置門戶 IP 位址。

1. 在電腦或平板電腦上,連接到設備螢幕上顯示的 WiFi 網路名稱。 WiFi 網路以**AZ 開頭,** 後跟 MAC 位址。 當您連接到此網路時,您沒有網路接入。 此狀態是預期的,並且僅在配置設備時連接到此網路很短的時間。

1. 開啟網頁瀏覽器並瀏[http://192.168.0.1/](http://192.168.0.1/)覽到 。 以下網頁隨即顯示：

    ![設定 UI](media/howto-connect-devkit/config-ui.png)

    在網頁上,輸入:

    - WiFi 網路 (SSID) 的名稱。
    - 您的 WiFi 網路密碼。
    - 連線詳細資訊:輸入您以前記下**的裝置 ID、ID****範圍**與**SAS 主金鑰**。

    > [!NOTE]
    > 目前,IoT DevKit 只能連接到 2.4 GHz Wi-Fi,由於硬體限制,不支援 5 GHz。

1. 選擇 **「設定裝置**」,DevKit 裝置重新啟動並執行應用程式:

    ![重新啟動 UI](media/howto-connect-devkit/reboot-ui.png)

    DevKit 螢幕顯示應用程式正在執行的確認:

    ![執行的 DevKit](media/howto-connect-devkit/devkit-running.png)

DevKit 首先在IoT中央應用程式中註冊新設備,然後開始發送數據。

## <a name="view-the-telemetry"></a>檢視遙測資料

在此步驟中,您可以在 Azure IoT 中央應用程式中查看遙測數據。

在 IoT 集中應用程式中,選擇 **「裝置」** 選項卡,選擇您添加的裝置。 在 **「概述」** 選項卡中,您可以看到來自 DevKit 裝置的遙測資料:

![IoT 中央設備概述](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>檢閱程式碼

要檢視碼或修改與編譯碼,請轉到[程式碼範例](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)。

## <a name="next-steps"></a>後續步驟

如果您是設備開發人員,建議的後續步驟是:

- 閱讀[有關 Azure IoT 中心中的裝置連線](./concepts-get-connected.md)
- 瞭解如何使用[Azure CLI 監視裝置連線](./howto-monitor-devices-azure-cli.md)
