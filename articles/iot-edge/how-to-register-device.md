---
title: 註冊新的 Azure IoT Edge 裝置 |Microsoft Docs
description: 使用 Azure CLI 的 IoT 擴充功能來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7facb539a84d39e6659627e80ecc560fc87591c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79285093"
---
# <a name="register-an-azure-iot-edge-device"></a>註冊 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能搭配 Azure IoT Edge 使用。 註冊裝置之後，您就可以抓取連接字串來設定裝置的 IoT Edge 工作負載。

您可以使用下列其中一個工具來選擇註冊：

* 如果您偏好使用圖形化使用者介面來建立、查看和管理 Azure 資源，請[在 Azure 入口網站中註冊裝置](#register-in-the-azure-portal)。
* 如果您想要在開發 IoT 解決方案所在的相同位置管理 Azure IoT 資源，請[使用 Visual Studio Code 註冊裝置](#register-with-visual-studio-code)。
* 如果您偏好使用命令列工具來管理 Azure 資源，或想要將工作自動化，請向[Azure CLI 註冊裝置](#register-with-the-azure-cli)。

## <a name="register-in-the-azure-portal"></a>在 Azure 入口網站中註冊

您可以在 Azure 入口網站中執行所有註冊工作。

### <a name="prerequisites-for-the-azure-portal"></a>Azure 入口網站的必要條件

Azure 訂用帳戶中的免費或標準[IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 入口網站中建立 IoT Edge 裝置

在 Azure 入口網站的 IoT 中樞中，IoT Edge 裝置會與未啟用 Edge 的 IOT 裝置分開建立和管理。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
2. 在左窗格中，從功能表中選取 [ **IoT Edge** ]。
3. 選取 [新增 IoT Edge 裝置]。
4. 提供描述性裝置識別碼。 使用預設設定來自動產生驗證金鑰，並將新的裝置連接到您的中樞。
5. 選取 [儲存]。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 入口網站中觀看 IoT Edge 裝置

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>取出 Azure 入口網站中的連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 從入口網站的 [ **IoT Edge** ] 頁面，按一下 IoT Edge 裝置清單中的 [裝置識別碼]。
2. 複製 [**主要連接字串**] 或 [**次要連接字串**] 的值。

## <a name="register-with-visual-studio-code"></a>向 Visual Studio Code 註冊

目前有多種方式可以在 VS Code 中執行大部分的操作。 本文使用 Explorer，但您也可以使用命令選擇區來執行這些步驟。

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用于 Visual Studio Code 的 Azure IoT 擴充功能來執行 IoT 中樞的作業。 若要讓這些作業運作，您必須登入您的 Azure 帳戶並選取您的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。
1. 在 [Explorer] 底部，展開 [ **Azure IoT 中樞**] 區段。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device/azure-iot-hub-devices.png)

1. 按一下 [ **Azure IoT 中樞**] 區段標頭中的 [ **...** ]。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選擇 [選取 IoT 中樞]。
1. 如果您未登入您的 Azure 帳戶，請依照提示執行此動作。
1. 選取 Azure 訂用帳戶。
1. 選取您的 IoT 中樞。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用 Visual Studio Code 建立 IoT Edge 裝置

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。
1. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選取 [建立 IoT Edge 裝置]。
1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 觀看 IoT Edge 裝置

所有連線到您 IoT 中樞的裝置都會列在 [Visual Studio Code Explorer] 的 [ **Azure IoT 中樞**] 區段中。 IoT Edge 裝置可從具有不同圖示的非邊緣裝置中辨別，而且 **$edgeAgent**和 **$edgeHub**模組會部署到每個 IoT Edge 裝置。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>使用 Visual Studio Code 取出連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 在 [ **Azure IoT 中樞**] 區段中，以滑鼠右鍵按一下您的裝置識別碼。
1. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。

## <a name="register-with-the-azure-cli"></a>向 Azure CLI 註冊

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一種開放原始碼跨平臺命令列工具，可用於管理 Azure 資源，例如 IoT Edge。 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 的功能。

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須為2.0.70 或以上。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>使用 Azure CLI 建立 IoT Edge 裝置

使用[az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)命令，在您的 iot 中樞建立新的裝置身分識別。 例如：

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* **device-id**：提供 IoT 中樞內獨一無二的描述性名稱。
* **hub-name**：提供 IoT 中樞的名稱。
* **edge-enabled**：這個參數會宣告裝置是要 IoT Edge 搭配使用。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>使用 Azure CLI 來觀看 IoT Edge 裝置

使用[az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list)命令來查看 iot 中樞內的所有裝置。 例如：

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>使用 Azure CLI 取出連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。 使用[az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string)命令來傳回單一裝置的連接字串：

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

## <a name="next-steps"></a>後續步驟

既然您已在 IoT 中樞註冊裝置身分識別，您就可以在裝置上安裝 IoT Edge 執行時間。 根據裝置的作業系統安裝執行時間：

* [在 Windows 上安裝 Azure IoT Edge](how-to-install-iot-edge-windows.md)
* [在 Linux 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-linux.md)
