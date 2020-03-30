---
title: 註冊新的 Azure IoT 邊緣設備 |微軟文檔
description: 使用 Azure CLI 的 IoT 擴充功能來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235716"
---
# <a name="register-an-azure-iot-edge-device"></a>註冊 Azure IoT 邊緣設備

在將 IoT 設備與 Azure IoT Edge 一起使用之前，必須將它們註冊到 IoT 中心。 註冊設備後，可以檢索連接字串以為 IoT Edge 工作負載設置設備。

您可以選擇使用以下工具之一進行註冊：

* 如果希望使用圖形化使用者介面來創建、查看和管理 Azure 資源，[請在 Azure 門戶中註冊設備](#register-in-the-azure-portal)。
* 如果您希望在開發 IoT 解決方案的同一位置管理 Azure IoT 資源，請[使用 Visual Studio 代碼註冊設備](#register-with-visual-studio-code)。
* 如果您喜歡命令列工具來管理 Azure 資源，或者打算自動執行任務，請[向 Azure CLI 註冊設備](#register-with-the-azure-cli)。

## <a name="register-in-the-azure-portal"></a>在 Azure 門戶中註冊

您可以在 Azure 門戶中執行所有註冊任務。

### <a name="prerequisites-for-the-azure-portal"></a>Azure 門戶的先決條件

Azure 訂閱中的免費或標準[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 門戶中創建 IoT 邊緣設備

在 Azure 門戶中的 IoT 中心中，IoT 邊緣設備與未啟用邊緣的 IOT 設備分開創建和管理。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
2. 在左側窗格中，從功能表中選擇**IoT 邊緣**。
3. 選取 [新增 IoT Edge 裝置]****。
4. 提供描述性裝置識別碼。 使用預設設置自動生成身份驗證金鑰並將新設備連接到集線器。
5. 選取 [儲存]****。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 門戶中查看 IoT 邊緣設備

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge]**** 頁面。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>檢索 Azure 門戶中的連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 從門戶中的**IoT 邊緣**頁面，按一下 IoT 邊緣設備清單中的裝置識別碼。
2. 複製**主連接字串**或**輔助連接字串**的值。

## <a name="register-with-visual-studio-code"></a>註冊視覺工作室代碼

目前有多種方式可以在 VS Code 中執行大部分的操作。 本文使用資源管理器，但您也可以使用命令調色板運行這些步驟。

### <a name="prerequisites-for-visual-studio-code"></a>視覺化工作室代碼的先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [視覺工作室代碼](https://code.visualstudio.com/)
* 用於視覺化工作室代碼的[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

可以使用視覺化工作室代碼的 Azure IoT 擴展對 IoT 中心執行操作。 要使這些操作正常工作，您需要登錄到 Azure 帳戶並選擇 IoT 中心。

1. 在 Visual Studio Code 中，開啟 [總管]**** 檢視。
1. 在資源管理器的底部，展開**Azure IoT 中心**部分。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device/azure-iot-hub-devices.png)

1. 按一下**Azure IoT 中心**部分標題中的 **...** 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選擇 [選取 IoT 中樞]****。
1. 如果未登錄到 Azure 帳戶，請按照提示執行此操作。
1. 選取 Azure 訂用帳戶。
1. 選取您的 IoT 中樞。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>使用視覺化工作室代碼創建 IoT 邊緣設備

1. 在 VS 代碼資源管理器中，展開**Azure IoT 中心設備**部分。
1. 按一下 [Azure IoT 中樞裝置]**** 區段標題中的 **...**。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選取 [建立 IoT Edge 裝置]****。
1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用視覺化工作室代碼查看 IoT 邊緣設備

連接到 IoT 中心的所有設備都列在視覺化工作室代碼資源管理器的**Azure IoT 中心**部分。 IoT Edge 設備與具有不同圖示的非邊緣設備不同，並且 **$edgeAgent**和 **$edgeHub**模組部署到每個 IoT Edge 設備。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>使用視覺化工作室代碼檢索連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 按右鍵**Azure IoT 中心**部分中的設備的 ID。
1. 選取 [複製裝置連接字串]****。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]****，以在輸出視窗中查看所有裝置資訊，包括連接字串。

## <a name="register-with-the-azure-cli"></a>向 Azure CLI 註冊

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)是一種開源跨平臺命令列工具，用於管理 Azure 資源（如 IoT Edge）。 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 的功能。

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的先決條件

* Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 至少，Azure CLI 版本必須為 2.0.70 或以上。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [Azure CLI 的 IoT 擴展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>使用 Azure CLI 創建 IoT 邊緣設備

使用[az iot 中心設備標識創建](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)命令在 IoT 中心中創建新的設備標識。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* **device-id**：提供 IoT 中樞內獨一無二的描述性名稱。
* **hub-name**：提供 IoT 中樞的名稱。
* **edge-enabled**：這個參數會宣告裝置是要 IoT Edge 搭配使用。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>使用 Azure CLI 查看 IoT 邊緣設備

使用[az iot 中心設備識別欄位表](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list)命令查看 IoT 中心中的所有設備。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>使用 Azure CLI 檢索連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。 使用[az iot 中心設備標識顯示連接字串](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string)命令返回單個設備的連接字串：

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

## <a name="next-steps"></a>後續步驟

現在，您已在 IoT 中心註冊了設備標識，現在可以在設備上安裝 IoT Edge 運行時了。 根據設備的作業系統安裝運行時：

* [在 Windows 上安裝 Azure IoT 邊緣](how-to-install-iot-edge-windows.md)
* [在 Linux 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-linux.md)
