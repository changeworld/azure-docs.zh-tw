---
title: 使用 x.509 憑證布建-Azure IoT Edge |Microsoft Docs
description: 安裝之後，請使用裝置身分識別憑證布建 IoT Edge 裝置，並向 IoT 中樞進行驗證
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979441"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>使用 x.509 憑證驗證設定 Azure IoT Edge 裝置

本文提供在 IoT 中樞註冊新的 IoT Edge 裝置，以及設定裝置以 x.509 憑證進行驗證的步驟。

本文中的步驟會逐步解說稱為手動布建的程式，您可以在其中手動將每個裝置連線至其 IoT 中樞。 替代方法是使用 IoT 中樞裝置布建服務進行自動布建，這在您有許多裝置要布建時很有説明。

<!--TODO: Add auto-provision info/links-->

針對手動布建，您有兩個選項可驗證 IoT Edge 裝置：

* **對稱金鑰**：當您在 IoT 中樞中建立新的裝置身分識別時，服務會建立兩個金鑰。 您可以將其中一個金鑰放在裝置上，並在驗證時向 IoT 中樞顯示金鑰。

  這種驗證方法會更快速地開始使用，但不是安全的。

* **X.509 自我簽署**：您會建立兩個 x.509 身分識別憑證，並將其放在裝置上。 當您在 IoT 中樞中建立新的裝置身分識別時，您會提供這兩個憑證的指紋。 當裝置向 IoT 中樞進行驗證時，它會呈現其憑證，而 IoT 中樞可以驗證它們是否符合指紋。

  這種驗證方法較為安全，建議用於生產案例。

本文將逐步解說使用 x.509 憑證驗證的註冊和布建程式。 如果您想要瞭解如何使用對稱金鑰來設定裝置，請參閱 [使用對稱金鑰驗證設定 Azure IoT Edge 裝置](how-to-manual-provision-symmetric-key.md)。

## <a name="prerequisites"></a>必要條件

遵循本文中的步驟之前，您應該已在其上安裝 IoT Edge 執行時間的裝置。 如果沒有，請依照 [安裝或卸載 Azure IoT Edge 運行](how-to-install-iot-edge.md)時間中的步驟執行。

使用 x.509 憑證進行手動布建需要 IoT Edge 版本1.0.10 或更新版本。

## <a name="create-certificates-and-thumbprints"></a>建立憑證和指紋



<!-- TODO -->

## <a name="register-a-new-device"></a>註冊新裝置

每個連線到 IoT 中樞的裝置都有一個裝置識別碼，可用來追蹤雲端到裝置或裝置到雲端通訊。 您可以設定裝置的連線資訊，其中包含 IoT 中樞主機名稱、裝置識別碼，以及裝置用來向 IoT 中樞進行驗證的資訊。

若為 x.509 憑證驗證，則會以從您的裝置身分識別憑證取得的 *指紋* 形式來提供此資訊。 在裝置註冊時，系統會將這些指紋提供給 IoT 中樞，讓服務可以在裝置連線時辨識該裝置。

您可以使用數個工具在 IoT 中樞註冊新的 IoT Edge 裝置，並上傳其憑證指紋。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure 入口網站的先決條件

Azure 訂閱的免費或標準 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 入口網站中建立 IoT Edge 裝置

在 Azure 入口網站的 IoT 中樞中，您會在未啟用 Edge 的 IOT 裝置上分別建立與管理 IoT Edge 裝置。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

1. 在左窗格中，從功能表中選取 **IoT Edge** ，然後選取 [ **新增 IoT Edge 裝置**]。

   ![從 Azure 入口網站新增 IoT Edge 裝置](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. 在 [ **建立裝置** ] 頁面上，提供下列資訊：

   * 建立描述性的裝置識別碼。 請記下此裝置識別碼，您將在下一節中使用。
   * 選取 [X.509 自我簽署] 作為 [驗證類型]。
   * 提供主要和次要身分識別憑證指紋。 憑證指紋值為 40-SHA-1 雜湊的十六進位字元或 SHA-256 雜湊的 64-十六進位字元。

1. 選取 [儲存]。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 入口網站中檢視 IoT Edge 裝置

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本至少必須是 2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>使用 Azure CLI 建立 IoT Edge 裝置

使用 [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令，在 IoT 中樞中建立新的裝置身分識別。 例如：

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

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>使用 Azure CLI 檢視 IoT Edge 裝置

使用 [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令檢視 IoT 中樞內的所有裝置。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

新增旗標 `--edge-enabled` 或 `--ee` 只列出 IoT 中樞內 IoT Edge 裝置。

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

--- 

## <a name="configure-an-iot-edge-device"></a>設定 IoT Edge 裝置

一旦 IoT Edge 裝置在 IoT 中樞內有身分識別，您必須使用其雲端身分識別以及其身分識別憑證來設定裝置。

在 Linux 裝置上，您可以藉由編輯 yaml 檔案來提供此資訊。 在 Windows 裝置上，您可以藉由執行 PowerShell 腳本來提供此資訊。

# <a name="linux"></a>[Linux](#tab/linux)

1. 在 IoT Edge 裝置上，開啟設定檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 尋找檔案的 [布建設定] 區段。 

1. **使用連接字串區段來批註手動**布建設定。

1. **使用 x.509 身分識別憑證**區段取消批註手動布建設定。 請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. 更新下欄欄位：

   * **iothub_hostname**：裝置將連接的 IoT 中樞主機名稱。 例如： `{IoT hub name}.azure-devices.net` 。
   * **device_id**：您在註冊裝置時所提供的識別碼。
   * **identity_cert**：裝置上身分識別憑證的 URI。 例如： `file:///path/identity_certificate.pem` 。
   * **identity_pk**：提供的身分識別憑證之私密金鑰檔案的 URI。 例如： `file:///path/identity_key.pem` 。

1. 儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

1. 在組態檔中輸入佈建資訊之後，請重新啟動精靈：

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. 在 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令，在您的電腦上設定 IoT Edge 執行時間。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 如果您使用 Linux 容器，請將 `-ContainerOs` 參數新增至旗標。 與您先前執行的命令所選擇的容器選項一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * 如果您將 IoTEdgeSecurityDaemon.ps1 腳本下載到您的裝置上以進行離線或特定版本安裝，請務必參考腳本的本機複本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 出現提示時，請提供下列資訊：

   * **IotHubHostName**：裝置將連接的 IoT 中樞主機名稱。 例如： `{IoT hub name}.azure-devices.net` 。
   * **DeviceId**：您在註冊裝置時所提供的識別碼。
   * **X509IdentityCertificate**：裝置上身分識別憑證的絕對路徑。 例如： `C:\path\identity_certificate.pem` 。
   * **X509IdentityPrivateKey**：所提供身分識別憑證之私密金鑰檔案的絕對路徑。 例如： `C:\path\identity_key.pem` 。

當您手動布建裝置時，您可以使用其他參數來修改進程，包括：

* 將流量導向經過 Proxy 伺服器
* 宣告特定的 edgeAgent 容器映射，並在私人登錄中提供認證

如需這些額外參數的詳細資訊，請參閱 [Windows 上適用于 IoT Edge 的 PowerShell 腳本](reference-windows-scripts.md)。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>後續步驟

繼續 [部署 IoT Edge 課程模組](how-to-deploy-modules-portal.md) ，以瞭解如何將模組部署到您的裝置。
