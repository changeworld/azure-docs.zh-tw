---
title: 管理裝置憑證-Azure IoT Edge |Microsoft Docs
description: 在 Azure IoT Edge 裝置上建立測試憑證、安裝及管理，以準備進行生產環境部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4c49345f7036dfee7d1f37c15a4647202b3e5670
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257832"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>管理 IoT Edge 裝置上的憑證

所有 IoT Edge 裝置都會使用憑證，在執行時間和裝置上執行的任何模組之間建立安全的連線。 做為閘道的 IoT Edge 裝置也會使用這些相同的憑證來連線至其下游裝置。

## <a name="install-production-certificates"></a>安裝生產憑證

當您第一次安裝 IoT Edge 並布建裝置時，會使用暫時憑證設定裝置，讓您可以測試服務。
這些暫時性憑證會在90天后到期，或者可以藉由重新開機電腦來重設。
一旦您進入生產環境案例，或想要建立閘道裝置，您必須提供自己的憑證。
本文示範在您的 IoT Edge 裝置上安裝憑證的步驟。

若要深入瞭解不同類型的憑證和其角色，請參閱[瞭解 Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

>[!NOTE]
>本文中使用的「根 CA」一詞指的是 IoT 解決方案的憑證鏈最上層授權公開憑證。 您不需要使用聯合憑證授權單位單位的憑證根目錄，或組織的憑證授權單位單位的根。 在許多情況下，它實際上是中繼 CA 公開憑證。

### <a name="prerequisites"></a>必要條件

* 在[Windows](how-to-install-iot-edge-windows.md)或[Linux](how-to-install-iot-edge-linux.md)上執行的 IoT Edge 裝置。
* 具有根憑證授權單位 (CA) 憑證，可以自我簽署或從受信任的商業憑證授權單位單位（例如巴爾的摩、Verisign、DigiCert 或透過 globalsign）購買。

如果您還沒有根憑證授權單位，但想要嘗試 IoT Edge 需要生產憑證的功能 (例如閘道案例) 您可以[建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。

### <a name="create-production-certificates"></a>建立生產環境憑證

您應該使用自己的憑證授權單位單位來建立下列檔案：

* 根 CA
* 裝置 CA 憑證
* 裝置 CA 私密金鑰

在本文中，我們指的是*根 CA*不是組織最上層的憑證授權單位單位。 這是 IoT Edge 案例的最上層憑證授權單位單位，其中 IoT Edge 中樞模組、使用者模組和任何下游裝置都使用它來建立彼此之間的信任。

> [!NOTE]
> 目前，libiothsm 的限制可防止使用2050年1月1日之後到期的憑證。

若要查看這些憑證的範例，請參閱在[管理測試 CA 憑證以取得範例和教學](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)課程中建立示範憑證的腳本。

### <a name="install-certificates-on-the-device"></a>在裝置上安裝憑證

在 IoT Edge 裝置上安裝您的憑證鏈，並將 IoT Edge 執行時間設定為參考新的憑證。

例如，如果您使用範例腳本來[建立示範憑證](how-to-create-test-certificates.md)，請將下列檔案複製到您的 IoT Edge 裝置：

* 裝置 CA 憑證：`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 裝置 CA 私密金鑰：`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA：`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 將三個憑證和金鑰檔案複製到您的 IoT Edge 裝置上。

   您可使用如 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)等功能來移動憑證檔案。  如果您在 IoT Edge 裝置本身產生憑證，可以略過此步驟，並使用工作目錄的路徑。

1. 開啟 IoT Edge 安全性精靈組態檔。

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

1. 將 yaml 中的**憑證**屬性設為憑證的檔案 URI 路徑和 IoT Edge 裝置上的金鑰檔案。 移除 `#` 憑證屬性前面的字元，以取消批註四行。 請確定 [**憑證：** ] 行沒有前面的空白字元，且已將該嵌套專案縮排為兩個空格。 例如︰

   * Windows：

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux：

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. 在 Linux 裝置上，請確定使用者**iotedge**具有保存憑證之目錄的 [讀取] 許可權。

1. 如果您之前已在裝置上使用任何其他憑證來進行 IoT Edge，請先刪除下列兩個目錄中的檔案，然後再啟動或重新開機 IoT Edge：

   * Windows： `C:\ProgramData\iotedge\hsm\certs` 和`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux： `/var/lib/iotedge/hsm/certs` 和`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>自訂憑證存留期

IoT Edge 在數種情況下會在裝置上自動產生憑證，包括：

* 如果您在安裝和布建 IoT Edge 時未提供自己的實際執行憑證，IoT Edge 安全性管理員會自動產生**裝置 CA 憑證**。 此自我簽署憑證僅適用于開發和測試案例，而非生產環境。 此憑證會在90天后過期。
* IoT Edge 安全性管理員也會產生由裝置 CA 憑證簽署的**工作負載 ca 憑證**

如需 IoT Edge 裝置上不同憑證功能的詳細資訊，請參閱[瞭解 Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

針對這兩個自動產生的憑證，您可以選擇在 yaml 中設定**auto_generated_ca_lifetime_days**旗標，以設定憑證存留期的天數。

>[!NOTE]
>IoT Edge 的安全性管理員所建立的第三個自動產生的憑證， **IoT Edge 中樞伺服器憑證**。 此憑證一律會有90天的存留期，但會在到期前自動更新。 **Auto_generated_ca_lifetime_days**值不會影響此憑證。

若要將憑證到期設定為預設90天以外的值，請在 yaml 檔案的 [**憑證**] 區段中新增以天為單位的值。

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> 目前，libiothsm 的限制可防止使用2050年1月1日之後到期的憑證。

如果您提供自己的裝置 CA 憑證，則此值仍然適用于工作負載 CA 憑證，前提是您所設定的存留期值比裝置 CA 憑證的存留期短。

在 yaml 檔案中指定旗標之後，請執行下列步驟：

1. 刪除資料夾的內容 `hsm` 。

   Windows： `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux：`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. 重新開機 IoT Edge 服務。

   Windows：

   ```powershell
   Restart-Service iotedge
   ```

   Linux：

   ```bash
   sudo systemctl restart iotedge
   ```

1. 確認存留期設定。

   Windows：

   ```powershell
   iotedge check --verbose
   ```

   Linux：

   ```bash
   sudo iotedge check --verbose
   ```

   檢查 [**生產環境就緒：憑證**檢查] 的輸出，其中會列出自動產生的裝置 CA 憑證到期前的天數。

## <a name="next-steps"></a>後續步驟

在生產環境中部署解決方案之前，在 IoT Edge 裝置上安裝憑證是必要的步驟。 深入瞭解如何[準備在生產環境中部署您的 IoT Edge 解決方案](production-checklist.md)。
