---
title: 如何使用 Azure IoT 中樞裝置佈建服務的自動佈建向 IoT 中樞註冊 MXChip IoT DevKit | Microsoft Docs
description: 如何使用 Azure IoT 中心設備佈建服務 （DPS） 自動預配將 MXChip IoT 開發人員工具組註冊到 IoT 中心。
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975647"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>使用 Azure IoT 中樞裝置佈建服務的自動佈建向 IoT 中樞註冊 MXChip IoT DevKit

本文說明如何使用 Azure IoT 中樞裝置佈建服務的[自動佈建](concepts-auto-provisioning.md)，向 Azure IoT 中樞註冊 MXChip IoT DevKit。 在本教學課程中，您會了解如何：

* 在裝置上設定裝置佈建服務的全域端點。
* 使用唯一裝置祕密 (UDS) 來產生 X.509 憑證。
* 註冊個別裝置。
* 確認裝置已註冊。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是全方位 Arduino 相容板，具有豐富的週邊設備和感應器。 您可以使用 Visual Studio Code 中的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 或 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 擴充套件來對它進行開發。 DevKit 隨附不斷增長的[專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，以引導您設定運用 Azure 服務的物聯網 (IoT) 解決方案原型。

## <a name="before-you-begin"></a>開始之前

若要完成本教學課程中的步驟，請先執行下列工作：

* 按照[雲中連接 IoT DevKit AZ3166](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)中的"準備開發環境"部分步驟配置 DevKit 的 Wi-Fi 並準備開發環境。
* 利用[更新 DevKit 韌體](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)教學課程，升級至最新的韌體 (1.3.0 或更新版本)。
* 按照使用[Azure 門戶設置 IoT 中心設備預配服務](/azure/iot-dps/quick-setup-auto-provision)中的步驟創建並將 IoT 中心與設備預配服務實例連結。

## <a name="open-sample-project"></a>開啟範例專案

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

1. 按一下`F1`以打開命令調色板，鍵入並選擇**Azure IoT 設備工作臺：打開示例..."** 然後選擇**IoT 開發人員工具組**作為板。

1. 在 [IoT Workbench 範例] 頁面上，尋找 [使用 DPS 進行裝置註冊]****，然後按一下 [開啟範例]****。 然後選取預設路徑來下載範例程式碼。
    ![開啟範例](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>將唯一裝置祕密儲存在裝置安全性儲存體上

可以根據裝置的[證明機制](concepts-security.md#attestation-mechanism)，在裝置上設定自動佈建。 MXChip IoT DevKit 會使用來自 [Trusted Computing Group](https://trustedcomputinggroup.org) 的[裝置身分識別組成引擎 (Device Identity Composition Engine)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)。 儲存在 DevKit 上 STSAFE 安全晶片 ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/) \(英文\)) 中的**唯一裝置祕密** (UDS) 會用來產生裝置的唯一 [X.509 憑證](concepts-security.md#x509-certificates)。 此憑證稍後會用於裝置佈建服務中的註冊程序，以及在執行階段的註冊期間使用。

典型的 UDS 是一個 64 個字元的字串，如以下範例所示：

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

將 UDS 儲存於 DevKit 上：

1. 在 VS Code 中，按一下狀態列以選取適用於 DevKit 的 COM 連接埠。
  ![選取 COM 連接埠](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. 在 DevKit 上，按住**按鈕 A**，按下並釋放**重置**按鈕，然後釋放**按鈕 A**。您的 DevKit 進入配置模式。

1. 按一下`F1`以打開命令調色板，鍵入並選擇**Azure IoT 設備工作臺：配置設備設置... >配置唯一設備字串 （UDS）。**
  ![設定 UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. 請記下所產生的 UDS 字串。 您將需要它來產生 X.509 憑證。 然後按 `Enter`。
  ![複製 UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. 確認已在 STSAFE 上成功設定 UDS 的通知。
  ![設定 UD 成功](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> 或者，您也可以使用 Putty 之類的公用程式，透過序列埠來設定 UDS。 請遵循[使用設定模式](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) \(英文\) 來執行此動作。

## <a name="update-the-global-device-endpoint-and-id-scope"></a>更新全域裝置端點和識別碼範圍

在裝置程式碼中，您需要指定[裝置佈建端點](/azure/iot-dps/concepts-service#device-provisioning-endpoint)和識別碼範圍，以確保租用戶隔離。

1. 在 Azure 門戶中，選擇設備預配服務的 **"概述"** 窗格，然後記下**全域設備終結點**和**ID 範圍**值。
  ![裝置佈建服務全域端點和識別碼範圍](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. 開啟 **DevKitDPS.ino**。 尋找 `[Global Device Endpoint]` 和 `[ID Scope]` 並將其替換為您剛才記下的值。
  ![裝置佈建服務端點](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. 在程式碼中填入 `registrationId` 變數。 僅允許最多 128 個字元的英數字元、小寫字母和連字號組合。 也請記下此值。
  ![註冊識別碼](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. 按一下`F1`，鍵入並選擇**Azure IoT 設備工作臺：上載設備代碼**。 它會開始編譯程式碼並上傳到 DevKit。
  ![裝置上傳](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>產生 X.509 憑證

此範例所使用的[證明機制](/azure/iot-dps/concepts-device#attestation-mechanism)是 X.509 憑證。 您需要使用公用程式來產生它。

1. 在 VS Code 中，按一下 `F1`，輸入並選取 [開啟新的終端機]**** 以開啟終端機視窗。

1. 在 `tool` 資料夾中執行 `dps_cert_gen.exe`。

1. 將已編譯的二進位檔位置指定為 `..\.build\DevKitDPS`。 接著貼上您剛記下的 **UDS** 和 **registrationId**。 
  ![產生 X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. `.pem` X.509 憑證會在相同資料夾中產生。
  ![X.509 檔案](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>建立裝置註冊項目

1. 在 Azure 入口網站中，開啟您的裝置佈建服務、瀏覽至 [管理註冊] 區段，然後按一下 [新增個別註冊]****。
  ![新增個別註冊](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. 按一下 [主要憑證 .pem 或 .cer 檔案]**** 旁的檔案圖示，以上傳已產生的 `.pem` 檔案。
  ![上傳 .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>確認 DevKit 已向 Azure IoT 中樞註冊

按下 DevKit 上的 [Reset] \(重設\)**** 按鈕。 您應該會在 DevKit 畫面上看到 [DPS 已連線！]**** 。 當裝置重新啟動之後，會發生下列動作：

1. 裝置會向裝置佈建服務傳送註冊要求。
1. 裝置佈建服務會回傳註冊挑戰供您的裝置回應。
1. 註冊成功後，裝置佈建服務會對裝置回傳 IoT 中樞 URI、裝置識別碼和加密金鑰。
1. 裝置上的 IoT 中樞用戶端應用程式會連線到您的中樞。
1. 成功連線至中樞後，您會看到裝置出現在 IoT 中樞的 Device Explorer 中。
  ![裝置已註冊](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>問題與意見反應

如果您遇到問題，請參閱 IoT DevKit [常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或透過下列管道取得支援：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您會學習如何使用裝置身分識別組合引擎將裝置安全地註冊到裝置佈建服務，讓裝置可以自動向 Azure IoT 中樞註冊。 

總而言之，您學會如何：

> [!div class="checklist"]
> * 在裝置上設定裝置佈建服務的全域端點。
> * 使用唯一裝置祕密來產生 X.509 憑證。
> * 註冊個別裝置。
> * 確認裝置已註冊。

深入了解如何[建立及佈建模擬裝置](./quick-create-simulated-device.md)。

