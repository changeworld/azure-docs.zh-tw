---
title: 在 Azure IoT Central 應用程式中使用 x.509 憑證來連接裝置
description: 如何使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置與 x.509 憑證連線
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: d36cf2344891bb70ab5499e77699b111429a936b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121834"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>如何使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置與 x.509 憑證連線

IoT Central 支援共用存取簽章 (SAS) 和 x.509 憑證來保護裝置與應用程式之間的通訊。 [建立用戶端應用程式並將其連接到您的 Azure IoT Central 應用程式](./tutorial-connect-device.md)教學課程使用 SAS。 在本文中，您將瞭解如何修改程式碼範例，以使用 x.509。  在生產環境中，建議使用 x.509 憑證。 如需詳細資訊，請參閱 [取得連接到 Azure IoT Central](./concepts-get-connected.md)。

本文說明兩種使用 x.509 的方式：通常用於生產環境中的 [群組註冊](how-to-connect-devices-x509.md#use-a-group-enrollment) ，以及 [個別註冊](how-to-connect-devices-x509.md#use-an-individual-enrollment) 適用于測試。

## <a name="prerequisites"></a>必要條件

- 完成 [建立用戶端應用程式，並將其連接到 Azure IoT Central 應用程式 (JavaScript) ](./tutorial-connect-device.md) 教學課程。
- [Git](https://git-scm.com/download/)。
- 下載並安裝 [OpenSSL](https://www.openssl.org/)。 如果您使用的是 Windows，則可以使用 [SourceForge 上 OpenSSL 頁面](https://sourceforge.net/projects/openssl/)中的二進位檔。

## <a name="use-a-group-enrollment"></a>使用群組註冊

在生產環境中使用 x.509 憑證與群組註冊。 在群組註冊中，您會將根或中繼 x.509 憑證新增至您的 IoT Central 應用程式。 具有衍生自根或中繼憑證之分葉憑證的裝置，可以連接到您的應用程式。

## <a name="generate-root-and-device-cert"></a>產生根憑證和裝置憑證

在本節中，您會使用 x.509 憑證將裝置與衍生自註冊群組憑證的憑證連線，以連接到您的 IoT Central 應用程式。

> [!WARNING]
> 這種產生 x.509 憑證的方法僅供測試之用。 在生產環境中，您應該使用您的官方安全機制來產生憑證。

1. 開啟命令提示字元。 複製憑證產生腳本的 GitHub 存放庫：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 流覽至憑證產生器腳本，並安裝所需的套件：

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. 建立根憑證，然後藉由執行腳本來衍生裝置憑證：

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > 裝置識別碼可以包含字母、數字和 `-` 字元。

這些命令會針對根目錄和裝置憑證產生三個檔案

filename | 內容
-------- | --------
\<name\>_cert pem | X509 憑證的公開部分
\<name\>_key pem | X509 憑證的私密金鑰
\<name\>_fullchain pem | 適用于 X509 憑證的整個 keychain。

## <a name="create-a-group-enrollment"></a>建立群組註冊

1. 開啟您的 IoT Central 應用程式，並流覽至左窗格中的 [系統 **管理**  ]，然後選取 [ **裝置** 連線]。

1. 選取 [ **+ 建立註冊群組**]，並建立名為 _MyX509Group_ 的新註冊群組，其中包含憑證的證明類型 **(x.509)**。

1. 開啟您所建立的註冊群組，然後選取 [ **管理主要**]。

1. 選取 [檔案] 選項，然後上傳您先前產生的根憑證檔案 _mytestrootcert_cert_ 的檔案名：

    ![憑證上傳](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 若要完成驗證，請產生驗證碼並複製它，然後在命令提示字元中使用它來建立 x.509 驗證憑證：

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 上傳已簽署的驗證憑證 _verification_cert pem_ 以完成驗證：

    ![驗證的憑證](./media/how-to-connect-devices-x509/verified.png)

您現在可以連接具有衍生自此主要根憑證之 x.509 憑證的裝置。

儲存註冊群組之後，請記下識別碼範圍。

## <a name="run-sample-device-code"></a>執行範例裝置程式碼

1. 將 **sampleDevice01_key pem** 和 **sampleDevice01_cert pem** 檔案複製到包含 **simple_thermostat.js** 應用程式的 _azure-iot-sdk-node/device/samples/pnp_ 資料夾。 當您完成將 [裝置連線 (JavaScript) 教學](./tutorial-connect-device.md)課程時，就會使用此應用程式。

1. 流覽至包含 **simple_thermostat.js** 應用程式的 _azure-iot-sdk-node/device/samples/pnp_ 資料夾，然後執行下列命令來安裝 x.509 套件：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. 在文字編輯器中開啟 **simple_thermostat.js** 檔案。

1. 編輯 `require` 語句以包含下列內容：

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. 將下列四行新增至「DPS 連接資訊」區段，以初始化 `deviceCert` 變數：

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. `provisionDevice`以下列程式碼取代第一行，以編輯建立用戶端的函式：

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 在相同的函式中，修改設定變數的程式程式碼，如下所示 `deviceConnectionString` ：

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. 在函式中 `main` ，將下面這一行新增至呼叫的行後面 `Client.fromConnectionString` ：

    ```javascript
    client.setOptions(deviceCert);
    ```

1. 在您的 shell 環境中，設定下列兩個環境變數：

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > 當您完成 [建立用戶端應用程式並將其連接到 Azure IoT Central 應用程式](./tutorial-connect-device.md) 教學課程時，您可以設定其他必要的環境變數。

1. 執行腳本，並確認裝置已成功布建：

    ```cmd/sh
    node simple_thermostat.js
    ```

    您也可以確認遙測會出現在儀表板上。

    ![驗證裝置遙測](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>使用個別註冊

使用 x.509 憑證搭配個別註冊來測試您的裝置與解決方案。 在個別註冊中，IoT Central 應用程式中沒有根或中繼的 x.509 憑證。 裝置會使用自我簽署的 x.509 憑證來連接到您的應用程式。

## <a name="generate-self-signed-device-cert"></a>產生自我簽署的裝置憑證

在本節中，您會使用自我簽署的 x.509 憑證來將裝置連線到個別註冊，以用來註冊單一裝置。 自我簽署憑證僅供測試之用。

藉由執行腳本來建立自我簽署的 x.509 裝置憑證。 請務必只針對憑證名稱使用小寫英數位元和連字號：

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>建立個別註冊

1. 在 Azure IoT Central 應用程式中，選取 [ **裝置**]，然後從控溫器裝置範本建立 **裝置識別碼** 為 _mytestselfcertprimary_ 的新裝置。 記下 **識別碼範圍**，稍後再使用它。

1. 開啟您所建立的裝置，然後選取 **[連線]**。

1. 選取 **個別註冊** 作為 **Connect 方法** ，並將 **憑證 (x.509)** 作為機制：

    ![個別申請](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. 選取 [主要] 下的 [檔案] 選項，然後上傳先前產生的 _mytestselfcertprimary_cert pem_ 的憑證檔案。

1. 選取次要憑證的 [檔案] 選項，然後上傳名為 _mytestselfcertsecondary_cert pem_ 的憑證檔案。 然後選取 [ **儲存**]：

    ![個別註冊憑證上傳](./media/how-to-connect-devices-x509/individual-enrollment.png)

裝置現在會以 x.509 憑證布建。

## <a name="run-a-sample-individual-enrollment-device"></a>執行範例個別註冊裝置

1. 將 _mytestselfcertprimary_key pem_ 和 _mytestselfcertprimary_cert pem_ 檔案複製到包含 **simple_thermostat.js** 應用程式的 _azure-iot-sdk-node/device/samples/pnp_ 資料夾。 當您完成將 [裝置連線 (JavaScript) 教學](./tutorial-connect-device.md)課程時，就會使用此應用程式。

1. 修改您在中使用的環境變數，如下所示：

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. 執行腳本，並確認裝置已成功布建：

    ```cmd/sh
    node environmentalSensor.js
    ```

    您也可以確認遙測會出現在儀表板上。

    ![遙測個別註冊](./media/how-to-connect-devices-x509/telemetry-primary.png)

您也可以針對 _mytestselfcertsecondary_ 憑證重複上述步驟。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 x.509 憑證來連接裝置，建議的下一個步驟是瞭解如何 [使用 Azure CLI 來監視裝置連線能力](howto-monitor-devices-azure-cli.md)
