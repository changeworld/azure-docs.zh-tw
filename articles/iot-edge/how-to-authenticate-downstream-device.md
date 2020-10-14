---
title: 驗證下游裝置 - Azure IoT Edge | Microsoft Docs
description: 如何向 IoT 中樞驗證下游裝置或分葉裝置，然後透過 Azure IoT Edge 閘道裝置路由傳送其連線。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 73584353d0d003588ef7de6131d3c3c4bbfcff59
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046718"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道案例中，下游裝置 (有時稱為分葉裝置或子裝置) 需要在 IoT 中樞中具有身分識別，如同任何其他裝置。 本文將逐步解說用來向 IoT 中樞驗證下游裝置的選項，然後示範如何宣告閘道連線。

設定成功的透明閘道連線有三個一般步驟。 本文將涵蓋第二個步驟：

1. 將閘道裝置設定為伺服器，讓下游裝置可以安全地連接到該裝置。 設定閘道以接收來自下游裝置的訊息，並將其路由傳送至適當的目的地。 如需詳細資訊，請參閱[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。
2. **建立下游裝置的裝置身分識別，使其可以向 IoT 中樞進行驗證。設定下游裝置以透過閘道裝置傳送訊息。**
3. 將下游裝置連線到閘道裝置，並開始傳送訊息。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游裝置可使用下列三種方法的其中一種向 IoT 中樞進行驗證：對稱金鑰 (有時稱為共用存取金鑰)、X.509 自我簽署憑證或 X.509 憑證授權單位 (CA) 簽署憑證。 這些驗證步驟與用來向 IoT 中樞設定任何非 IoT Edge 裝置的步驟很類似，但稍微不同，那就是必須宣告閘道關聯性。

本文中的步驟顯示手動裝置布建。 不支援使用 Azure IoT 中樞裝置布建服務 (DPS) 自動布建下游裝置。

## <a name="prerequisites"></a>Prerequisites

完成[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)中的步驟。

如果您使用的是 x.509 authentication，您將會產生下游裝置的憑證。 擁有相同的根 CA 憑證和產生腳本的憑證，您可以使用這些腳本來重新使用透明閘道文章。

本文在數個位置參考「閘道主機名稱」。 閘道主機名稱是在 IoT Edge 閘道裝置上 config.yaml 檔案的 **hostname** 參數中宣告。 這會在下游裝置的連接字串中參考。 閘道主機名稱必須可解析為 IP 位址，方法是使用 DNS 或下游裝置上的主機檔案專案。

## <a name="register-device-with-iot-hub"></a>使用 IoT 中樞註冊裝置

選擇您要讓下游裝置向 IoT 中樞進行驗證的方式：

* [對稱金鑰驗證](#symmetric-key-authentication)： IoT 中樞會建立您放置在下游裝置上的金鑰。 當裝置進行驗證時，IoT 中樞會檢查兩個金鑰是否相符。 您不需要建立其他憑證來使用對稱金鑰驗證。
* [X.509 自我簽署驗證](#x509-self-signed-authentication)：有時稱為指紋驗證，因為您會與 IoT 中樞共用裝置的 x.509 憑證指紋。
* [X.509 CA 簽署的驗證](#x509-ca-signed-authentication)：將根 CA 憑證上傳至 IoT 中樞。 當裝置出示其 x.509 憑證進行驗證時，IoT 中樞會檢查它是否屬於相同根 CA 憑證所簽署的信任鏈。

使用這三種方法的其中一種來註冊您的裝置之後，請繼續下一節，以抓取 [和修改下游裝置的連接字串](#retrieve-and-modify-connection-string) 。

### <a name="symmetric-key-authentication"></a>對稱金鑰驗證

對稱金鑰驗證 (或共用存取金鑰驗證) 是向 IoT 中樞進行驗證的最簡單方式。 使用對稱金鑰驗證時，base64 金鑰會與 IoT 中樞中的 IoT 裝置識別碼建立關聯。 您會在 IoT 應用程式中包含該金鑰，讓裝置可在連線到 IoT 中樞時呈現該金鑰。

使用 Azure 入口網站、Azure CLI 或適用於 Visual Studio Code 的 IoT 延伸模組，在 IoT 中樞中新增 IoT 裝置。 請記住，下游裝置必須在 IoT 中樞中識別為一般 IoT 裝置，而不是 IoT Edge 裝置。

當建立新的裝置身分識別時，請提供下列資訊：

* 建立裝置的識別碼。

* 選取 [對稱金鑰] 作為 [驗證類型]。

* 選取 [ **設定父裝置** ]，然後選取此下游裝置將連線的 IoT Edge 閘道裝置。 此步驟會啟用下游裝置的 [離線功能](offline-capabilities.md) 。 您稍後可以隨時變更父系。

   ![在入口網站中使用對稱金鑰驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

您也可以使用 [Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension) 功能來完成相同的操作。 下列範例會使用對稱金鑰驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

如需用於裝置建立和父子式管理的 Azure CLI 命令詳細資訊，請參閱 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 命令的參考內容。

接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

### <a name="x509-self-signed-authentication"></a>X.509 自我簽署驗證

若為 x.509 自我簽署驗證（有時也稱為指紋驗證），您必須建立要放置在下游裝置上的憑證。 這些憑證內含會與 IoT 中樞共用以進行驗證的指紋。

1. 使用 CA 憑證，為下游裝置建立兩個裝置憑證 (主要和次要)。

   如果您沒有建立 x.509 憑證的憑證授權單位單位，您可以使用 IoT Edge 示範憑證腳本來 [建立下游裝置憑證](how-to-create-test-certificates.md#create-downstream-device-certificates)。 遵循建立自我簽署憑證的步驟。 使用為您的閘道裝置產生憑證的相同根 CA 憑證。

   如果您建立自己的憑證，請確定裝置憑證的主體名稱已設為您在 Azure IoT 中樞中註冊 IoT 裝置時所使用的裝置識別碼。 需要此設定才能進行驗證。

2. 從每個憑證擷取 SHA1 指紋 (在 IoT 中樞介面中稱為指紋)，這是 40 個十六進位字元字串。 使用下列 openssl 命令來檢視憑證並尋找指紋：

   * Windows：

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux：

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   執行此命令兩次，一次用於主要憑證，一次用於次要憑證。 當使用 X.509 自我簽署憑證來註冊新的 IoT 裝置時，您會為這兩個憑證提供指紋。

3. 在 Azure 入口網站中巡覽至 IoT 中樞，然後使用下列值建立新的 IoT 裝置身分識別：

   * 提供符合裝置憑證主體名稱的 [裝置識別碼]。
   * 選取 [X.509 自我簽署] 作為 [驗證類型]。
   * 貼上從裝置的主要和次要憑證所複製十六進位字串。
   * 選取 [設定父裝置]，然後選擇此下游裝置連線所要透過的 IoT Edge 閘道裝置。 需要父裝置才能進行下游裝置的 X.509 驗證。

   ![在入口網站中使用 X.509 自我簽署驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 將主要和次要裝置憑證及其金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將在連線到 IoT 中樞的下游裝置上的任何應用程式中，參考這些憑證檔案。 您可使用如 [Azure Key Vault](../key-vault/index.yml) 的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)等功能來移動憑證檔案。

5. 視偏好的語言而定，檢閱如何在 IoT 應用程式中參考 X.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C：[iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java：[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

您也可以使用 [適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension) 功能來完成相同的裝置建立操作。 下列範例會使用 X.509 自我簽署驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

如需用於裝置建立、憑證產生和父子式管理的 Azure CLI 命令詳細資訊，請參閱 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 命令的參考內容。

接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

### <a name="x509-ca-signed-authentication"></a>X.509 CA 簽署的驗證

針對 x.509 憑證授權單位單位 (CA) 簽署的驗證，您需要在 IoT 中樞內註冊的根 CA 憑證，以用來簽署下游裝置的憑證。 所有使用由根 CA 憑證或其任何中繼憑證所發出憑證的裝置，都可進行驗證。

本節是以 IoT 中樞文章[在 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中詳述的指示為依據。

1. 使用 CA 憑證，為下游裝置建立兩個裝置憑證 (主要和次要)。

   如果您沒有建立 x.509 憑證的憑證授權單位單位，您可以使用 IoT Edge 示範憑證腳本來 [建立下游裝置憑證](how-to-create-test-certificates.md#create-downstream-device-certificates)。 遵循建立 CA 簽署憑證的步驟。 使用為您的閘道裝置產生憑證的相同根 CA 憑證。

2. 遵循＜在 IoT 中樞中設定 X.509 安全性＞的＜ IoT 中樞註冊 X.509 CA 憑證＞[向](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一節中指示。 在該節中，您將執行下列步驟：

   1. 上傳根 CA 憑證。 如果您使用的是示範憑證，根 CA 會是** \<path> /certs/azure-iot-test-only.root.ca.cert.pem**。

   2. 確認擁有該根 CA 憑證。

3. 遵循＜在 IoT 中樞中設定 X.509 安全性＞的＜[建立 IoT 中樞的 X.509 裝置](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)＞一節中指示。 在該節中，您將執行下列步驟：

   1. 新增裝置。 為 [裝置識別碼] 提供小寫名稱，然後選擇 [X.509 CA 簽署] 驗證類型。

   2. 設定父裝置。 針對下游裝置，選取 [設定父裝置]，然後選擇將提供 IoT 中樞連線的 IoT Edge 閘道裝置。

4. 為下游裝置建立憑證鏈結。 使用上傳至 IoT 中樞的相同根 CA 憑證來建立此鏈結。 使用在入口網站中為裝置身分識別所提供的相同小寫裝置識別碼。

5. 將裝置憑證和金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將在連線到 IoT 中樞的下游裝置上的任何應用程式中，參考這些檔案。 您可使用如 [Azure Key Vault](../key-vault/index.yml) 的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)等功能來移動憑證檔案。

6. 視偏好的語言而定，檢閱如何在 IoT 應用程式中參考 X.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C：[iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java：[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

您也可以使用 [適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension) 功能來完成相同的裝置建立操作。 下列範例會使用 X.509 CA 簽署驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

如需詳細資訊，請參閱 [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) 命令的參考內容。

接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

## <a name="retrieve-and-modify-connection-string"></a>擷取並修改連接字串

在入口網站中建立 IoT 裝置身分識別之後，即可擷取其主要或次要金鑰。 您必須在應用程式用來與 IoT 中樞通訊的連接字串中，包含這兩個金鑰的其中一個。 針對對稱金鑰驗證，IoT 中樞會在裝置詳細資料中提供完整格式的連接字串，以方便使用。 您必須將閘道裝置的額外資訊新增至連接字串。

下游裝置的連接字串需要下列元件：

* 與裝置連線的 IoT 中樞：`Hostname={iothub name}.azure-devices.net`
* 向中樞註冊的裝置識別碼：`DeviceID={device ID}`
* 驗證方法，無論對稱金鑰或 x.509 憑證
  * 如果使用對稱金鑰驗證，請提供主要或次要金鑰： `SharedAccessKey={key}`
  * 如果使用 x.509 憑證驗證，請提供旗標： `x509=true`
* 裝置連線所透過的閘道裝置。 提供 IoT Edge 閘道裝置 config.yaml 檔案中的 **hostname** 值：`GatewayHostName={gateway hostname}`

結合所有元件的完整連接字串如下所示：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

或：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

由於有父子式關聯性，您可以直接以連線主機的形式呼叫閘道，以簡化連接字串。 例如︰

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

您將在「透明閘道」系列的下一篇文章中使用這個修改過的連接字串。

## <a name="next-steps"></a>後續步驟

此時，您會有向 IoT 中樞註冊的 IoT Edge 裝置，並已設定為透明閘道。 您也有向 IoT 中樞註冊的下游裝置，並指向其閘道裝置。

本文中的步驟會設定您的下游裝置以向 IoT 中樞進行驗證。 接下來，您必須設定下游裝置以信任閘道裝置，並安全地連線到該裝置。 繼續進行透明閘道系列中的下一篇文章、 [將下游裝置連線至 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。