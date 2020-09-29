---
title: 註冊新的 Azure IoT Edge 裝置 | Microsoft Docs
description: 使用 Azure CLI 的 IoT 擴充功能來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450140"
---
# <a name="register-an-azure-iot-edge-device"></a>註冊 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能搭配 Azure IoT Edge 使用。 註冊裝置之後，即可擷取連接字串以設定用於 IoT Edge 工作負載的裝置。

您可選擇使用下列其中一種工具來進行註冊：

* 如果偏好使用圖形化使用者介面來建立、檢視和管理 Azure 資源，請在 [Azure 入口網站中註冊裝置](#register-in-the-azure-portal)。
* 如果偏好在開發 IoT 解決方案的相同位置管理 Azure IoT 資源，請[使用 Visual Studio Code 註冊裝置](#register-with-visual-studio-code)。
* 如果偏好使用命令列工具來管理 Azure 資源，或打算自動化工作，請[使用 Azure CLI 註冊裝置](#register-with-the-azure-cli)。

## <a name="register-in-the-azure-portal"></a>在 Azure 入口網站中註冊

您可在 Azure 入口網站中執行所有註冊工作。

### <a name="prerequisites-for-the-azure-portal"></a>Azure 入口網站的先決條件

Azure 訂閱的免費或標準 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 入口網站中建立 IoT Edge 裝置

在 Azure 入口網站的 IoT 中樞中，您會在未啟用 Edge 的 IOT 裝置上分別建立與管理 IoT Edge 裝置。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
2. 在左側窗格中，從功能表中選取 [IoT Edge]。
3. 選取 [新增 IoT Edge 裝置]。
4. 提供描述性裝置識別碼。 使用預設設定自動產生驗證金鑰，將新裝置連線到中樞。
5. 選取 [儲存]。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 入口網站中檢視 IoT Edge 裝置

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>在 Azure 入口網站中擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 在入口網站的 [IoT Edge] 頁面中，按一下 IoT Edge 裝置清單中的裝置識別碼。
2. 複製 [主要連接字串] 或 [次要連接字串] 的值。

## <a name="register-with-visual-studio-code"></a>使用 Visual Studio Code 註冊

目前有多種方式可以在 VS Code 中執行大部分的操作。 本文使用 Explorer，但您也可使用 [命令選擇區] 來執行步驟。

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可使用適用於 Visual Studio Code 的 Azure IoT 延伸模組，以利用 IoT 中樞來執行各種操作。 為了讓這些操作發揮作用，您必須登入 Azure 帳戶並選取 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。
1. 在 Explorer 底部，展開 [Azure IoT 中樞] 區段。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device/azure-iot-hub-devices.png)

1. 按一下 [Azure IoT 中樞] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選擇 [選取 IoT 中樞]。
1. 如未登入 Azure 帳戶，請遵循提示登入。
1. 選取 Azure 訂用帳戶。
1. 選取您的 IoT 中樞。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用 Visual Studio Code 建立 IoT Edge 裝置

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。
1. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選取 [建立 IoT Edge 裝置]。
1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 檢視 IoT Edge 裝置

所有連線到 IoT 中樞的裝置都會列在 Visual Studio Code 總管其 [Azure IoT 中樞] 區段中。 IoT Edge 裝置與非 Edge 裝置使用不同的圖示區別，且 **$edgeAgent** 和 **$edgeHub** 模組已部署至每個 IoT Edge 裝置。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>使用 Visual Studio Code 擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 以滑鼠右鍵按一下 [Azure IoT 中樞] 區段中的裝置識別碼。
1. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。

## <a name="register-with-the-azure-cli"></a>使用 Azure CLI 註冊

[Azure CLI](/cli/azure) 是開放原始碼的跨平台命令列工具，用於管理 IoT Edge 等 Azure 資源。 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 的功能。

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本至少必須是 2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>使用 Azure CLI 建立 IoT Edge 裝置

使用 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令，在 IoT 中樞中建立新的裝置身分識別。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* **device-id**：提供 IoT 中樞內唯一的描述性名稱。
* **hub-name**：提供 IoT 中樞的名稱。
* **edge-enabled**：此參數會宣告裝置是要 IoT Edge 搭配使用。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>使用 Azure CLI 檢視 IoT Edge 裝置

使用 [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令檢視 IoT 中樞內的所有裝置。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>使用 Azure CLI 擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。 使用 [az iot hub device identity connection-string show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) 命令來傳回單一裝置的連接字串：

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

## <a name="next-steps"></a>後續步驟

在 IoT 中樞註冊裝置身分識別之後，即可在裝置上安裝 IoT Edge 執行階段。 根據裝置的作業系統安裝執行階段：

* [在 Windows 上安裝 Azure IoT Edge](how-to-install-iot-edge-windows.md)
* [在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)
