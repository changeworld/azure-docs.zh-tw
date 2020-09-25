---
title: 在 Azure IoT Central 應用程式中使用 x.509 憑證來連接裝置
description: 如何使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置與 x.509 憑證連線
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6de711567e87bcdd1e58185f90264d0c9aecdfde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342301"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>如何使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置與 x.509 憑證連線

IoT Central 支援共用存取簽章 (SAS) 和 x.509 憑證來保護裝置與應用程式之間的通訊。 [建立用戶端應用程式並將其連接到您的 Azure IoT Central 應用程式](./tutorial-connect-device-nodejs.md)教學課程使用 SAS。 在本文中，您將瞭解如何修改程式碼範例，以使用 x.509。  在生產環境中，建議使用 x.509 憑證。 如需詳細資訊，請參閱 [取得連接到 Azure IoT Central](./concepts-get-connected.md)。

本文說明兩種使用 x.509 的方式：通常用於生產環境中的 [群組註冊](how-to-connect-devices-x509.md#use-a-group-enrollment) ，以及 [個別註冊](how-to-connect-devices-x509.md#use-an-individual-enrollment) 適用于測試。

## <a name="prerequisites"></a>Prerequisites

- 完成 [建立用戶端應用程式，並將其連接到您的 Azure IoT Central 應用程式 ( # A0) ](./tutorial-connect-device-nodejs.md) 教學課程。
- [Git](https://git-scm.com/download/)。
- 下載並安裝 [OpenSSL](https://www.openssl.org/)。 如果您使用的是 Windows，則可以使用 [SourceForge 上 OpenSSL 頁面](https://sourceforge.net/projects/openssl/)中的二進位檔。

## <a name="use-a-group-enrollment"></a>使用群組註冊

在生產環境中使用 x.509 憑證與群組註冊。 在群組註冊中，您會將根或中繼 x.509 憑證新增至您的 IoT Central 應用程式。 具有衍生自根或中繼憑證之分葉憑證的裝置，可以連接到您的應用程式。


## <a name="generate-root-and-device-cert"></a>產生根憑證和裝置憑證

在本節中，您將使用 x.509 憑證來將裝置與衍生自註冊群組憑證的憑證連線，該憑證可連接到您的 IoT Central 應用程式。

> [!WARNING]
> 這種產生 x.509 憑證的方式只是為了進行測試。 在生產環境中，您應該使用您的官方安全機制來產生憑證。

1. 開啟命令提示字元。 複製憑證產生腳本的 GitHub 存放庫：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

2. 流覽至憑證產生器腳本，並安裝所需的套件：

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. 建立根憑證，然後藉由執行腳本來衍生裝置憑證。 請務必只針對憑證名稱使用小寫英數位元和連字號。

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

這會針對根目錄和裝置憑證產生三個檔案

filename | 內容
-------- | --------
\<name\>_cert pem | X509 憑證的公開部分
\<name\>_key pem | X509 憑證的私密金鑰
\<name\>_fullchain pem | 適用于 X509 憑證的整個 keychain。


## <a name="create-a-group-enrollment"></a>建立群組註冊


1. 現在開啟您的 IoT Central 應用程式，並流覽至左窗格中的 [ **管理**  ]，然後按一下 [ **裝置**連線]。 

2. 選取 [+ **建立註冊群組**]，並建立名為 _MyX509Group_ 的新註冊群組，其中包含憑證的證明類型 ** (x.509) **：


3. 開啟您所建立的註冊群組，然後按一下 [ **管理主要**]。 

4. 選取 [檔案] 選項，然後上傳您先前產生的根憑證檔案 _mytestrootcert_cert_ 的檔案名：


    ![憑證上傳](./media/how-to-connect-devices-x509/certificate-upload.png)



5. 若要完成驗證，請複製驗證碼，並在命令提示字元中使用該程式碼建立 x.509 驗證憑證。

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

6. 上傳已簽署的驗證憑證 _verification_cert pem_ 以完成驗證。

    ![驗證的憑證](./media/how-to-connect-devices-x509/verified.png)


您現在可以連接具有衍生自此主要根憑證之 x.509 憑證的裝置。 儲存註冊群組之後，請記下識別碼範圍。


## <a name="run-sample-device-code"></a>執行範例裝置程式碼


1. 在 Azure IoT Central 應用程式中，按一下 [ **裝置**]，然後使用 _>mytestdevice_ 作為環境感應器裝置範本的 **裝置識別碼** 來建立新的裝置。


2. 當您完成將[裝置連線 ( # A1) 教學](./tutorial-connect-device-nodejs.md)課程時，請將_mytestdevice_key pem_和_mytestdevice_cert pem_複製到包含_environmentalSensor.js_應用程式的資料夾。

3. 流覽至包含 environmentalSensor.js 應用程式的資料夾，然後執行下列命令來安裝 x.509 套件：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

4. 編輯 **environmentalSensor.js** 檔案。
    - 將值取代為 `idScope` 您先前所記下的**識別碼範圍** 
    - 將 `registrationId` 值取代為 `mytestdevice` 。

5. 編輯語句，如下所示 `require` ：

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

6. 編輯建立用戶端的區段，如下所示：

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

7. 修改開啟連接的區段，如下所示：

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

8. 執行指令碼，並確認裝置已成功佈建。

    ```cmd/sh
    node environmentalSensor.js
    ```   

    您也可以確認遙測會出現在儀表板上。

    ![驗證裝置遙測](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>使用個別註冊

使用 x.509 憑證搭配個別註冊來測試您的裝置與解決方案。 在個別註冊中，IoT Central 應用程式中沒有根或中繼的 x.509 憑證。 裝置會使用自我簽署的 x.509 憑證來連接到您的應用程式。

## <a name="generate-self-signed-device-cert"></a>產生自我簽署的裝置憑證


在本節中，您會使用自我簽署的 x.509 憑證來將裝置連線到個別註冊，以用來註冊單一裝置。 自我簽署憑證僅供測試之用。

藉由執行腳本來建立自我簽署的 x.509 裝置憑證。 請務必只針對憑證名稱使用小寫英數位元和連字號。

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>建立個別註冊

1. 在 Azure IoT Central 應用程式中，選取 [ **裝置**]，然後從 [環境感應器] 裝置範本建立 **裝置識別碼** 為 _mytestselfcertprimary_ 的新裝置。 記下 **識別碼範圍**

2. 開啟您所建立的裝置，然後選取 **[連線]**

3. 選取 **個別註冊** 作為連線方法，並將 **憑證 (x.509) ** 為機制。

    ![個別申請](./media/how-to-connect-devices-x509/individual-device-connect.png)


4. 選取 [主要] 下的 [檔案] 選項，然後上傳先前產生的 _mytestselfcertprimary_cert pem_ 的憑證檔案。 

5. 選取次要憑證的 [檔案] 選項，然後上傳名為_mytestselfcertsecondary_cert pem_的憑證檔案。 然後選取 [**儲存**]

    ![個別註冊憑證上傳](./media/how-to-connect-devices-x509/individual-enrollment.png)

裝置現在會以 x.509 憑證布建。



## <a name="run-a-sample-individual-enrollment-device"></a>執行範例個別註冊裝置

1. 當您完成將[裝置連線 ( # A1) 教學](./tutorial-connect-device-nodejs.md)課程時，請將_mytestselfcertprimary_key pem_和_mytestselfcertprimary_cert pem_複製到包含 environmentalSensor.js 應用程式的資料夾。


2. 如下所示編輯 **environmentalSensor.js** 檔案並加以儲存。
    - 將值取代為 `idScope` 您先前所記下的 **識別碼範圍** 。
    - 將 `registrationId` 值取代為 `mytestselfcertprimary` 。
    - 將 **Var deviceCert** 取代為：
    ```cmd\sh
    var deviceCert = {
    cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
    key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
    };
    ```

3. 執行指令碼，並確認裝置已成功佈建。

    ```cmd/sh
    node environmentalSensor.js
    ```   

    您也可以確認遙測會出現在儀表板上。

    ![遙測個別註冊](./media/how-to-connect-devices-x509/telemetry-primary.png)

您也可以針對 _mytestselfcertsecondary_ 憑證重複上述步驟。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 x.509 憑證來連接裝置，建議的下一個步驟是瞭解如何 [使用 Azure CLI 來監視裝置連線能力](howto-monitor-devices-azure-cli.md)

