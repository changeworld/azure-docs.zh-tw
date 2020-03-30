---
title: 管理設備證書 - Azure IoT 邊緣 |微軟文檔
description: 在 Azure IoT Edge 設備上創建測試憑證、安裝和管理這些證書，以便為生產部署做好準備。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539204"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>在 IoT 邊緣設備上管理證書

所有 IoT Edge 設備都使用證書在運行時和設備上運行的任何模組之間創建安全連線。 充當閘道的 IoT Edge 設備也使用這些相同的證書連接到其下游設備。

## <a name="install-production-certificates"></a>安裝生產憑證

首次安裝 IoT Edge 並預配設備時，設備將設置臨時證書，以便可以測試服務。
這些臨時證書將在 90 天后過期，或者可以通過重新開機電腦來重置。
準備好將設備移動到生產方案中，或者想要創建閘道方案後，需要提供自己的證書。
本文演示了在 IoT Edge 設備上安裝證書的步驟。

要瞭解有關 IoT Edge 方案中不同類型的證書及其角色的更多資訊，請參閱瞭解 Azure [IoT 邊緣如何使用證書](iot-edge-certs.md)。

>[!NOTE]
>本文中使用的術語"根 CA"是指 IoT 解決方案憑證連結的最頂層頒發機構公共證書。 您無需使用聯合憑證授權單位的證書根或組織的憑證授權單位的根。 在許多情況下，它實際上是一個中間的CA公共證書。

### <a name="prerequisites"></a>Prerequisites

* 在[Windows](how-to-install-iot-edge-windows.md)或[Linux](how-to-install-iot-edge-linux.md)上運行的 IoT 邊緣設備。
* 擁有根憑證授權單位 （CA） 證書，由自簽名或從受信任的商業憑證授權單位（如巴爾的摩、威瑞信、DigiCert 或 GlobalSign）購買。

如果您還沒有根憑證授權單位，但想要嘗試需要生產證書（如閘道方案）的 IoT Edge 功能，則可以[創建演示證書來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。

### <a name="create-production-certificates"></a>創建生產證書

應使用自己的憑證授權單位創建以下檔：

* 根 CA
* 裝置 CA 憑證
* 設備 CA 私密金鑰

在本文中，我們所說的*根 CA*不是組織最重要的憑證授權單位。 它是 IoT Edge 方案的最高憑證授權單位，IoT Edge 中心模組、使用者模組和任何下游設備都使用該憑證授權單位來在彼此之間建立信任。

要查看這些證書的示例，請查看[在管理示例和教程的測試 CA 憑證](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)中創建演示證書的腳本。

### <a name="install-certificates-on-the-device"></a>在設備上安裝證書

在 IoT Edge 設備上安裝憑證連結，並將 IoT Edge 運行時配置為引用新證書。

例如，如果您使用示例腳本[創建演示證書](how-to-create-test-certificates.md)，請將以下檔案複製到 IoT 邊緣設備上：

* 設備 CA 憑證：`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 設備 CA 私密金鑰：`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA：`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 將三個證書和金鑰檔案複製到 IoT Edge 設備上。

   您可以使用[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來移動證書檔。  如果在 IoT Edge 設備本身上生成證書，則可以跳過此步驟並使用工作目錄的路徑。

1. 開啟 IoT Edge 安全性精靈組態檔。

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

1. 將 config.yaml 檔中的**證書**屬性設置為 IoT Edge 設備上證書和金鑰檔的完整路徑。 刪除證書`#`屬性之前的字元以取消注釋四行。 確保**證書：** 行沒有前面的空格，並且嵌套項由兩個空格縮進。 例如：

   * Windows：

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux：

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. 在 Linux 設備上，請確保使用者**iotedge**具有持有證書的目錄的讀取權限。

1. 如果您以前在設備上使用了 IoT Edge 的任何其他證書，請在啟動或重新開機 IoT Edge 之前刪除以下兩個目錄中的檔：

   * 視窗：`C:\ProgramData\iotedge\hsm\certs`和`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux：`/var/lib/iotedge/hsm/certs`和`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>自訂證書存留期

IoT Edge 在若干種情況下自動在設備上生成證書，包括：

* 如果在安裝和預配 IoT Edge 時未提供自己的生產證書，IoT Edge 安全管理器將自動生成設備**CA 憑證**。 此自簽章憑證僅用於開發和測試方案，而不是用於生產。 此證書將在 90 天后過期。
* IoT Edge 安全管理器還生成由設備 CA 憑證簽名**的工作負載 CA 憑證**

有關 IoT Edge 設備上不同證書的功能的詳細資訊，請參閱瞭解 Azure [IoT 邊緣如何使用證書](iot-edge-certs.md)。

對於這兩個自動生成的證書，您可以選擇在 config.yaml 中設置**auto_generated_ca_lifetime_days**標誌，以配置證書存留期的天數。

>[!NOTE]
>IoT Edge 安全管理器創建了第三個自動生成的證書，**即 IoT Edge 中心伺服器憑證**。 此證書始終有 90 天，但在過期之前會自動續訂。 **auto_generated_ca_lifetime_days**值不會影響此證書。

要將證書過期配置為預設 90 天以外的內容，請將該值以天形式添加到 config.yaml 檔的**證書**部分。

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

如果您提供了自己的設備 CA 憑證，則此值仍適用于工作負載 CA 憑證，前提是您設置的存留期值短于設備 CA 憑證的存留期。

在 config.yaml 檔中指定標誌後，請執行以下步驟：

1. 刪除`hsm`資料夾的內容。

   視窗`C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys`： Linux：`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. 重新開機 IoT 邊緣服務。

   Windows：

   ```powershell
   Restart-Service iotedge
   ```

   Linux：

   ```bash
   sudo systemctl restart iotedge
   ```

1. 確認存留期設置。

   Windows：

   ```powershell
   iotedge check --verbose
   ```

   Linux：

   ```bash
   sudo iotedge check --verbose
   ```

   檢查生產就緒狀態的輸出 **：證書**檢查，其中列出自動生成的設備 CA 憑證過期之前的天數。

## <a name="next-steps"></a>後續步驟

在 IoT Edge 設備上安裝證書是在生產中部署解決方案之前的必要步驟。 詳細瞭解如何[準備在生產中部署 IoT 邊緣解決方案](production-checklist.md)。
