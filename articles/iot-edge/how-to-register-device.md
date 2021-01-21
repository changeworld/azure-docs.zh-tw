---
title: 註冊新的裝置-Azure IoT Edge |Microsoft Docs
description: 在 IoT 中樞註冊單一 IoT Edge 裝置，以使用對稱金鑰或 x.509 憑證進行手動布建
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 97faf5eff7187bbabe23bbcab60514eef4acc063
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633964"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>在 IoT 中樞註冊 IoT Edge 裝置

本文提供在 IoT 中樞註冊新 IoT Edge 裝置的步驟。

每個連線到 IoT 中樞的裝置都有一個裝置識別碼，可用來追蹤雲端到裝置或裝置到雲端通訊。 您可以使用其連線資訊來設定裝置，包括 IoT 中樞主機名稱、裝置識別碼，以及裝置用來向 IoT 中樞進行驗證的資訊。

本文中的步驟會逐步解說稱為手動布建的程式，您可以在其中將單一裝置連線至其 IoT 中樞。 針對手動布建，您有兩個選項可驗證 IoT Edge 裝置：

* **對稱金鑰**：當您在 IoT 中樞中建立新的裝置身分識別時，服務會建立兩個金鑰。 您可以將其中一個金鑰放在裝置上，並在驗證時向 IoT 中樞顯示金鑰。

  這種驗證方法會更快速地開始使用，但不是安全的。

* **X.509 自我簽署**：您會建立兩個 x.509 身分識別憑證，並將其放在裝置上。 當您在 IoT 中樞中建立新的裝置身分識別時，您會提供這兩個憑證的指紋。 當裝置向 IoT 中樞進行驗證時，會顯示一個憑證，而 IoT 中樞會驗證憑證是否符合其指紋。

  這種驗證方法較為安全，建議用於生產案例。

本文涵蓋兩種驗證方法。

如果您有許多要設定的裝置，而不想要手動布建每個裝置，請使用下列其中一篇文章，瞭解 IoT Edge 如何搭配 IoT 中樞裝置布建服務運作：

* [使用 x.509 憑證來建立和布建 IoT Edge 裝置](how-to-auto-provision-x509-certs.md)
* [使用 TPM 建立和布建 IoT Edge 裝置](how-to-auto-provision-simulated-device-linux.md)
* [使用對稱金鑰來建立和布建 IoT Edge 裝置](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>必要條件

# <a name="portal"></a>[入口網站](#tab/azure-portal)

Azure 訂閱的免費或標準 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure 訂用帳戶中的免費或標準[IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 訂閱的免費或標準 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。

---

## <a name="option-1-register-with-symmetric-keys"></a>選項1：向對稱金鑰註冊

您可以使用數個工具，在 IoT 中樞註冊新的 IoT Edge 裝置，並視您的喜好設定取得其連接字串。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站的 IoT 中樞內，IoT Edge 裝置會與未啟用邊緣的 IoT 裝置分開建立及管理。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 在左窗格中，從功能表中選取 **IoT Edge** ，然後選取 [ **新增 IoT Edge 裝置**]。

   ![從 Azure 入口網站新增 IoT Edge 裝置](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. 在 [ **建立裝置** ] 頁面上，提供下列資訊：

   * 建立描述性的裝置識別碼。
   * 選取 [對稱金鑰] 作為 [驗證類型]。
   * 使用預設設定自動產生驗證金鑰，將新裝置連線到中樞。

1. 選取 [儲存]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用於 Visual Studio Code 的 Azure IoT 擴充功能來透過 IoT 中樞執行各種操作。 若要讓這些作業正常運作，您必須登入 Azure 帳戶並選取您的中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。
1. 在 Explorer 底部，展開 [Azure IoT 中樞] 區段。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device/azure-iot-hub-devices.png)

1. 按一下 [Azure IoT 中樞] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選擇 [選取 IoT 中樞]。
1. 如未登入 Azure 帳戶，請遵循提示登入。
1. 選取 Azure 訂用帳戶。
1. 選取您的 IoT 中樞。

### <a name="register-a-new-device-with-visual-studio-code"></a>使用 Visual Studio Code 註冊新裝置

1. 在 Visual Studio Code Explorer 中，展開 [ **Azure IoT 中樞** ] 區段。
1. 按一下 [Azure IoT 中樞] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選取 [建立 IoT Edge 裝置]。
1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令，在 IoT 中樞中建立新的裝置身分識別。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* `--device-id` 或者 `-d` ：提供在 IoT 中樞內唯一的描述性名稱。
* `--hub-name` 或者 `-n` ：提供 IoT 中樞的名稱。
* `--edge-enabled` 或者 `--ee` ：宣告裝置是 IoT Edge 裝置。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device/create-edge-device-cli.png)

---

現在您已在 IoT 中樞註冊裝置，請取出用來完成安裝和布建 IoT Edge 執行時間的連接字串。 請依照本文稍後的步驟來 [查看已註冊的裝置，並取得連接字串](#view-registered-devices-and-retrieve-connection-strings)。

## <a name="option-2-register-with-x509-certificates"></a>選項2：使用 x.509 憑證註冊

使用 x.509 憑證進行手動布建需要 IoT Edge 版本1.0.10 或更新版本。

若是 x.509 憑證驗證，則會以從您的裝置身分識別憑證取得的 *指紋* 形式來提供每個裝置的驗證資訊。 在裝置註冊時，系統會將這些指紋提供給 IoT 中樞，讓服務可以在裝置連線時辨識該裝置。

### <a name="create-certificates-and-thumbprints"></a>建立憑證和指紋

當您使用 x.509 憑證布建 IoT Edge 裝置時，您會使用所謂的裝置身分 *識別憑證*。 此憑證僅用於布建 IoT Edge 裝置，以及使用 Azure IoT 中樞驗證裝置。 這是不會簽署其他憑證的分葉憑證。 裝置身分識別憑證與憑證授權單位單位分開， (CA) 憑證 IoT Edge 裝置提供給模組或下游裝置以進行驗證。 如需有關如何在 IoT Edge 裝置中使用 CA 憑證的詳細資訊，請參閱 [瞭解 Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

您需要下列檔案，才能使用 x.509 進行手動布建：

* 其中兩個裝置身分識別憑證與其相符的私密金鑰憑證（.cer 或 pem 格式）。

  IoT Edge 執行時間提供一組憑證/金鑰檔。 當您建立裝置身分識別憑證時，請使用您想讓裝置在 IoT 中樞內的裝置識別碼，將憑證一般名稱 (CN) 。

* 從這兩個裝置身分識別憑證取得的指紋。

  憑證指紋值為 40-SHA-1 雜湊的十六進位字元或 SHA-256 雜湊的 64-十六進位字元。 在註冊裝置時，會提供兩個指紋給 IoT 中樞。

如果您沒有可用的憑證，您可以 [建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。 遵循該文章中的指示來設定憑證建立腳本、建立根 CA 憑證，然後建立兩個 IoT Edge 的裝置身分識別憑證。

從憑證取出指紋的其中一種方式是使用下列 openssl 命令：

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>註冊新裝置

您可以使用數個工具在 IoT 中樞註冊新的 IoT Edge 裝置，並上傳其憑證指紋。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站的 IoT 中樞內，IoT Edge 裝置會與未啟用邊緣的 IoT 裝置分開建立及管理。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 在左窗格中，從功能表中選取 **IoT Edge** ，然後選取 [ **新增 IoT Edge 裝置**]。

   ![從 Azure 入口網站新增 IoT Edge 裝置](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. 在 [ **建立裝置** ] 頁面上，提供下列資訊：

   * 建立描述性的裝置識別碼。 請記下此裝置識別碼，您將在下一節中使用。
   * 選取 [X.509 自我簽署] 作為 [驗證類型]。
   * 提供主要和次要身分識別憑證指紋。 憑證指紋值為 40-SHA-1 雜湊的十六進位字元或 SHA-256 雜湊的 64-十六進位字元。

1. 選取 [儲存]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

目前，適用于 Visual Studio Code 的 Azure IoT 擴充功能不支援使用 x.509 憑證來註冊裝置。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令，在 IoT 中樞中建立新的裝置身分識別。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

此命令包含數個參數：

* `--device-id` 或者 `-d` ：提供對 IoT 中樞而言是唯一的描述性名稱。 請記下此裝置識別碼，您將在下一節中使用。
* `hub-name` 或者 `-n` ：提供 IoT 中樞的名稱。
* `--edge-enabled` 或者 `--ee` ：宣告裝置是 IoT Edge 裝置。
* `--auth-method` 或者 `--am` ：宣告裝置即將使用的授權類型。 在此情況下，我們使用的是 x.509 憑證指紋。
* `--primary-thumbprint` 或者 `--ptp` ：提供要做為主鍵使用的 x.509 憑證指紋。
* `--secondary-thumbprint` 或者 `--stp` ：提供要作為次要金鑰使用的 x.509 憑證指紋。

---

現在您已在 IoT 中樞註冊裝置，您已準備好在裝置上安裝和布建 IoT Edge 執行時間。 IoT Edge 使用 x.509 憑證進行驗證的裝置不會使用連接字串，因此您可以繼續進行下一個步驟：

* [安裝或卸載 Linux Azure IoT Edge](how-to-install-iot-edge.md)
* [安裝或卸載 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>查看已註冊的裝置並取出連接字串

使用對稱金鑰驗證的裝置需要其連接字串，才能完成 IoT Edge 執行時間的安裝和布建。

使用 x.509 憑證驗證的裝置不需要連接字串。 相反地，這些裝置需要其 IoT 中樞名稱、裝置名稱及其憑證檔案，才能完成 IoT Edge 執行時間的安裝和布建。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![使用 Azure 入口網站來查看 IoT 中樞內的所有 IoT Edge 裝置](./media/how-to-register-device/portal-view-devices.png)

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

使用對稱金鑰進行驗證的裝置，可以在入口網站中複製其連接字串。

1. 在入口網站的 [IoT Edge] 頁面中，按一下 IoT Edge 裝置清單中的裝置識別碼。
2. 複製 [主要連接字串] 或 [次要連接字串] 的值。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>使用 Visual Studio Code 檢視 IoT Edge 裝置

所有連線到 IoT 中樞的裝置都會列在 Visual Studio Code 總管其 [Azure IoT 中樞] 區段中。 IoT Edge 裝置與非 Edge 裝置使用不同的圖示區別，且 **$edgeAgent** 和 **$edgeHub** 模組已部署至每個 IoT Edge 裝置。

![使用 VS Code 來查看 IoT 中樞內的所有 IoT Edge 裝置](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>使用 Visual Studio Code 擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 以滑鼠右鍵按一下 [Azure IoT 中樞] 區段中的裝置識別碼。
1. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

>[!TIP]
>此 `connection-string show` 命令是在 Azure IoT 擴充功能的版本0.9.8 中引進，取代已被取代的 `show-connection-string` 命令。 如果您在執行此命令時收到錯誤，請確定您的延伸模組版本已更新為0.9.8 或更新版本。 如需詳細資訊和最新的更新，請參閱 [適用于 Azure CLI Microsoft Azure IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能。

`device-id` 參數的值區分大小寫。

複製要在裝置上使用的連接字串時，請勿在連接字串前後加上引號。

---

## <a name="next-steps"></a>下一步

現在您已在 IoT 中樞註冊裝置，您已準備好在裝置上安裝和布建 IoT Edge 執行時間。

* [安裝或卸載 Linux Azure IoT Edge](how-to-install-iot-edge.md)
* [安裝或卸載 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)