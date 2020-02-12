---
title: 在裝置上安裝憑證-Azure IoT Edge |Microsoft Docs
description: 建立測試憑證，並瞭解如何將它們安裝在 Azure IoT Edge 裝置上，以準備進行生產環境部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149893"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>在 IoT Edge 裝置上安裝生產憑證

所有 IoT Edge 裝置都會使用憑證，在執行時間和裝置上執行的任何模組之間建立安全的連線。
做為閘道的 IoT Edge 裝置也會使用這些相同的憑證來連線至其下游裝置。

當您第一次安裝 IoT Edge 並布建裝置時，會使用暫時憑證設定裝置，讓您可以測試服務。
這些暫時性憑證會在90天后到期，或者可以藉由重新開機電腦來重設。
當您準備好要將裝置移到生產環境案例，或想要建立閘道案例時，您需要提供自己的憑證。
本文示範在您的 IoT Edge 裝置上安裝憑證的步驟。

若要在 IoT Edge 案例中深入瞭解不同類型的憑證及其角色，請參閱[瞭解 Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

>[!NOTE]
>本文中使用的「根 CA」一詞指的是 IoT 解決方案的憑證鏈最上層授權公開憑證。 您不需要使用聯合憑證授權單位單位的憑證根目錄，或組織的憑證授權單位單位的根。 在許多情況下，它實際上是中繼 CA 公開憑證。

## <a name="prerequisites"></a>必要條件

* 在[Windows](how-to-install-iot-edge-windows.md)或[Linux](how-to-install-iot-edge-linux.md)上執行的 IoT Edge 裝置。
* 擁有根憑證授權單位（CA）憑證，可以自我簽署或從受信任的商業憑證授權單位單位（例如巴爾的摩、Verisign、DigiCert 或透過 globalsign）購買。

如果您還沒有根憑證授權單位，但想要嘗試 IoT Edge 需要生產憑證的功能（例如閘道案例），您可以[建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。

## <a name="create-production-certificates"></a>建立生產環境憑證

您應該使用自己的憑證授權單位單位來建立下列檔案：

* 根 CA
* 裝置 CA 憑證
* 裝置 CA 私密金鑰

在本文中，我們指的是*根 CA*不是組織最上層的憑證授權單位單位。 這是 IoT Edge 案例的最上層憑證授權單位單位，其中 IoT Edge 中樞模組、使用者模組和任何下游裝置都使用它來建立彼此之間的信任。

若要查看這些憑證的範例，請參閱在[管理測試 CA 憑證以取得範例和教學](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)課程中建立示範憑證的腳本。

## <a name="install-certificates-on-the-device"></a>在裝置上安裝憑證

在 IoT Edge 裝置上安裝您的憑證鏈，並將 IoT Edge 執行時間設定為參考新的憑證。

例如，如果您使用範例腳本來[建立示範憑證](how-to-create-test-certificates.md)，則需要複製到 IoT Edge 裝置上的三個檔案如下所示：

* 裝置 CA 憑證： `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 裝置 CA 私密金鑰： `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA： `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 將三個憑證和金鑰檔案複製到您的 IoT Edge 裝置上。

   您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。  如果您在 IoT Edge 裝置本身產生憑證，可以略過此步驟，並使用工作目錄的路徑。

2. 開啟 IoT Edge 安全性精靈組態檔。

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 將 yaml 檔案中的**憑證**屬性，設定為 IoT Edge 裝置上憑證和金鑰檔的檔案 URI。 移除憑證屬性之前的 `#` 字元，以取消批註四行。 請確定 [**憑證：** ] 行沒有前面的空白字元，且已將該嵌套專案縮排為兩個空格。 例如，

   * Windows：

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux：

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. 在 Linux 裝置上，請確定使用者**iotedge**具有保存憑證之目錄的 [讀取] 許可權。

5. 如果您之前已在裝置上使用任何其他憑證來進行 IoT Edge，請先刪除下列兩個目錄中的檔案，然後再啟動或重新開機 IoT Edge：

   * Windows： `C:\ProgramData\iotedge\hsm\certs` 和 `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux： `/var/lib/iotedge/hsm/certs` 和 `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>後續步驟

在生產環境中部署解決方案之前，在 IoT Edge 裝置上安裝憑證是必要的步驟。 深入瞭解如何[準備在生產環境中部署您的 IoT Edge 解決方案](production-checklist.md)。
