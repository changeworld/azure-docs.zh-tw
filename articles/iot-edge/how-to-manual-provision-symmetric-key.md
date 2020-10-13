---
title: 使用對稱金鑰布建-Azure IoT Edge |Microsoft Docs
description: 安裝之後，使用對稱金鑰布建具有對稱金鑰的 IoT Edge 裝置，並使用其連接字串進行驗證並向 IoT 中樞進行驗證
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979437"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>使用對稱金鑰驗證設定 Azure IoT Edge 裝置

本文提供在 IoT 中樞註冊新的 IoT Edge 裝置，以及設定裝置以對稱金鑰進行驗證的步驟。

本文中的步驟會逐步解說稱為手動布建的程式，您可以在其中手動將每個裝置連線至其 IoT 中樞。 替代方法是使用 IoT 中樞裝置布建服務進行自動布建，這在您有許多裝置要布建時很有説明。

<!--TODO: Add auto-provision info/links-->

針對手動布建，您有兩個選項可驗證 IoT Edge 裝置：

* **對稱金鑰**：當您在 IoT 中樞中建立新的裝置身分識別時，服務會建立兩個金鑰。 您可以將其中一個金鑰放在裝置上，並在驗證時向 IoT 中樞顯示金鑰。

  這種驗證方法會更快速地開始使用，但不是安全的。

* **X.509 自我簽署**：您會建立兩個 x.509 身分識別憑證，並將其放在裝置上。 當您在 IoT 中樞中建立新的裝置身分識別時，您會提供這兩個憑證的指紋。 當裝置向 IoT 中樞進行驗證時，它會呈現其憑證，而 IoT 中樞可以驗證它們是否符合指紋。

  這種驗證方法較為安全，建議用於生產案例。

本文將逐步解說使用對稱金鑰驗證的註冊和布建程式。 如果您想要瞭解如何使用 x.509 憑證來設定裝置，請參閱 [使用 x.509 憑證驗證設定 Azure IoT Edge 裝置](how-to-manual-provision-x509.md)。

## <a name="prerequisites"></a>必要條件

遵循本文中的步驟之前，您應該已在其上安裝 IoT Edge 執行時間的裝置。 如果沒有，請依照 [安裝或卸載 Azure IoT Edge 運行](how-to-install-iot-edge.md)時間中的步驟執行。

## <a name="register-a-new-device"></a>註冊新裝置

每個連線到 IoT 中樞的裝置都有一個裝置識別碼，可用來追蹤雲端到裝置或裝置到雲端通訊。 您可以使用其連線資訊來設定裝置，包括 IoT 中樞主機名稱、裝置識別碼，以及裝置用來向 IoT 中樞進行驗證的資訊。

針對對稱金鑰驗證，這項資訊會在您可以從 IoT 中樞取出的 *連接字串* 中收集，然後放在您的 IoT Edge 裝置上。

您可以使用數個工具，在 IoT 中樞註冊新的 IoT Edge 裝置，並視您的喜好設定取得其連接字串。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure 入口網站的先決條件

Azure 訂閱的免費或標準 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 入口網站中建立 IoT Edge 裝置

在 Azure 入口網站的 IoT 中樞中，您會在未啟用 Edge 的 IOT 裝置上分別建立與管理 IoT Edge 裝置。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 在左窗格中，從功能表中選取 **IoT Edge** ，然後選取 [ **新增 IoT Edge 裝置**]。

   ![從 Azure 入口網站新增 IoT Edge 裝置](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. 在 [ **建立裝置** ] 頁面上，提供下列資訊：

   * 建立描述性的裝置識別碼。
   * 選取 [對稱金鑰] 作為 [驗證類型]。
   * 使用預設設定自動產生驗證金鑰，將新裝置連線到中樞。

1. 選取 [儲存]。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 入口網站中檢視 IoT Edge 裝置

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![使用 Azure 入口網站來查看 IoT 中樞內的所有 IoT Edge 裝置](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>在 Azure 入口網站中擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 在入口網站的 [IoT Edge] 頁面中，按一下 IoT Edge 裝置清單中的裝置識別碼。
2. 複製 [主要連接字串] 或 [次要連接字串] 的值。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code 的先決條件

* Azure 訂用帳戶中的免費或標準[IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可使用適用於 Visual Studio Code 的 Azure IoT 延伸模組，以利用 IoT 中樞來執行各種操作。 為了讓這些操作發揮作用，您必須登入 Azure 帳戶並選取 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。
1. 在 Explorer 底部，展開 [Azure IoT 中樞] 區段。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![使用 VS Code 來查看 IoT 中樞內的所有 IoT Edge 裝置](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>使用 Visual Studio Code 擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 以滑鼠右鍵按一下 [Azure IoT 中樞] 區段中的裝置識別碼。
1. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>使用 Azure CLI 建立 IoT Edge 裝置

使用 [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令，在 IoT 中樞中建立新的裝置身分識別。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* `--device-id` 或者 `-d` ：提供對 IoT 中樞而言是唯一的描述性名稱。
* `hub-name` 或者 `-n` ：提供 IoT 中樞的名稱。
* `--edge-enabled` 或者 `--ee` ：宣告裝置是 IoT Edge 裝置。

   ![az iot hub device-identity create 輸出](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>使用 Azure CLI 檢視 IoT Edge 裝置

使用 [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令檢視 IoT 中樞內的所有裝置。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>使用 Azure CLI 擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。 使用 [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令傳回單一裝置的連接字串：

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

---

## <a name="provision-an-iot-edge-device"></a>佈建 IoT Edge 裝置

一旦 IoT Edge 裝置具有 IoT 中樞的身分識別，以及可用於驗證的連接字串，您就必須使用這項資訊來布建裝置本身。

在 Linux 裝置上，您可以藉由編輯 yaml 檔案來提供連接字串。 在 Windows 裝置上，您可以藉由執行 PowerShell 腳本來提供連接字串。

# <a name="linux"></a>[Linux](#tab/linux)

在 IoT Edge 裝置上，開啟設定檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定，並 **使用連接字串** 區段取消批註手動布建設定。 

以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定任何其他布建區段都已批註化。請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

將剪貼簿內容貼到 Nano `Shift+Right Click` 或按下 `Shift+Insert` 。

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. 在 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令，在您的電腦上設定 IoT Edge 執行時間。 此命令預設為 Windows 容器的手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 如果您使用 Linux 容器，請將 `-ContainerOs` 參數新增至旗標。 與您先前執行的命令所選擇的容器選項一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 如果您將 IoTEdgeSecurityDaemon.ps1 腳本下載到您的裝置上以進行離線或特定版本安裝，請務必參考腳本的本機複本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 出現提示時，請提供您在上一節中取出的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞內的裝置識別碼產生關聯，並提供驗證資訊。

   裝置連接字串採用下列格式，且不應包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

當您手動布建裝置時，您可以使用其他參數來修改進程，包括：

* 將流量導向經過 Proxy 伺服器
* 宣告特定的 edgeAgent 容器映射，並在私人登錄中提供認證

如需這些額外參數的詳細資訊，請參閱 [Windows 上適用于 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>後續步驟

繼續 [部署 IoT Edge 課程模組](how-to-deploy-modules-portal.md) ，以瞭解如何將模組部署到您的裝置。
