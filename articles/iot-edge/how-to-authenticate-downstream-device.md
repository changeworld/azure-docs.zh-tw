---
title: 驗證下游裝置 - Azure IoT Edge | Microsoft Docs
description: 如何向 IoT 中樞驗證下游裝置或分葉裝置，然後透過 Azure IoT Edge 閘道裝置路由傳送其連線。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3edd29703f74c7671537fbcf08159dd830e5453c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726221"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道案例中，下游裝置 (有時稱為分葉裝置或子裝置) 需要在 IoT 中樞中具有身分識別，如同任何其他裝置。 本文將逐步解說用來向 IoT 中樞驗證下游裝置的選項，然後示範如何宣告閘道連線。

設定成功的透明閘道連線有三個一般步驟。 本文將涵蓋第二個步驟：

1. 閘道裝置必須能夠安全地連線到下游裝置、接收來自下游裝置的通訊，以及將訊息路由傳送至適當的目的地。 如需詳細資訊，請參閱[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。
2. **下游裝置必須具有裝置身分識別，才能向 IoT 中樞進行驗證，以及知道透過其閘道裝置進行通訊。**
3. 下游裝置必須安全地連線到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游裝置可使用下列三種方法的其中一種向 IoT 中樞進行驗證：對稱金鑰 (有時稱為共用存取金鑰)、X.509 自我簽署憑證或 X.509 憑證授權單位 (CA) 簽署憑證。 這些驗證步驟與用來向 IoT 中樞設定任何非 IoT Edge 裝置的步驟很類似，但稍微不同，那就是必須宣告閘道關聯性。

本文中的步驟示範手動裝置佈建，而不是使用 Azure IoT 中樞裝置佈建服務 (DPS) 的自動佈建。 不支援使用 DPS 佈建下游裝置。

## <a name="prerequisites"></a>Prerequisites

完成[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)中的步驟。 如果針對下游裝置使用 X.509 驗證，則需要使用在透明閘道一文中設定的相同憑證產生指令碼。

本文在數個位置參考「閘道主機名稱」。 閘道主機名稱是在 IoT Edge 閘道裝置上 config.yaml 檔案的 **hostname** 參數中宣告。 這會在下游裝置的連接字串中參考。 閘道主機名稱必須可使用 DNS 或主機檔案項目解析為 IP 位址。

## <a name="register-device-symmetric-key"></a>註冊裝置 (對稱金鑰)

對稱金鑰驗證 (或共用存取金鑰驗證) 是向 IoT 中樞進行驗證的最簡單方式。 使用對稱金鑰驗證時，base64 金鑰會與 IoT 中樞中的 IoT 裝置識別碼建立關聯。 您會在 IoT 應用程式中包含該金鑰，讓裝置可在連線到 IoT 中樞時呈現該金鑰。

### <a name="create-the-device-identity"></a>建立裝置身分識別

使用 Azure 入口網站、Azure CLI 或適用於 Visual Studio Code 的 IoT 延伸模組，在 IoT 中樞中新增 IoT 裝置。 請記住，下游裝置必須在 IoT 中樞中識別為一般 IoT 裝置，而不是 IoT Edge 裝置。

當建立新的裝置身分識別時，請提供下列資訊：

* 建立裝置的識別碼。

* 選取 [對稱金鑰] 作為 [驗證類型]。

* (選擇性) 選擇 [設定父裝置]，然後選取此下游裝置連線所要透過的 IoT Edge 閘道裝置。 此步驟對於對稱金鑰驗證是選擇性的，但建議使用，因為設定父裝置可啟用下游裝置的[離線功能](offline-capabilities.md)。 您隨時可在稍後更新裝置詳細資料來新增或變更父裝置。

   ![在入口網站中使用對稱金鑰驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

您可使用[適用於 Azure CLI 的 IoT 延伸模組](https://github.com/Azure/azure-iot-cli-extension)來完成相同作業。 下列範例會使用對稱金鑰驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

如需用於裝置建立和父子式管理的 Azure CLI 命令詳細資訊，請參閱 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的參考內容。


接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

## <a name="register-device-x509-self-signed"></a>註冊裝置 (X.509 自我簽署)

針對 X.509 自我簽署驗證 (有時稱為指紋驗證)，您必須建立新的憑證以放在 IoT 裝置上。 這些憑證內含會與 IoT 中樞共用以進行驗證的指紋。

如果沒有憑證授權單位可建立 X.509 憑證，則可[建立示範憑證來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游裝置產生測試憑證時，請使用為閘道裝置產生憑證的相同根 CA 憑證。

1. 使用 CA 憑證，為下游裝置建立兩個裝置憑證 (主要和次要)。

   裝置憑證的主體名稱必須設定為在 Azure IoT 中樞內註冊 IoT 裝置時所要使用的裝置識別碼。 需要此設定才能進行驗證。

2. 從每個憑證擷取 SHA1 指紋 (在 IoT 中樞介面中稱為指紋)，這是 40 個十六進位字元字串。 使用下列 openssl 命令來檢視憑證並尋找指紋：

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   執行此命令兩次，一次用於主要憑證，一次用於次要憑證。 當使用 X.509 自我簽署憑證來註冊新的 IoT 裝置時，您會為這兩個憑證提供指紋。

3. 在 Azure 入口網站中巡覽至 IoT 中樞，然後使用下列值建立新的 IoT 裝置身分識別：

   * 提供符合裝置憑證主體名稱的 [裝置識別碼]。
   * 選取 [X.509 自我簽署] 作為 [驗證類型]。
   * 貼上從裝置的主要和次要憑證所複製十六進位字串。
   * 選取 [設定父裝置]，然後選擇此下游裝置連線所要透過的 IoT Edge 閘道裝置。 需要父裝置才能進行下游裝置的 X.509 驗證。

   ![在入口網站中使用 X.509 自我簽署驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 將裝置憑證和金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將會在連線到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可使用如 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)等功能來移動憑證檔案。

5. 視偏好的語言而定，檢閱如何在 IoT 應用程式中參考 X.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C：[iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java：[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

您可使用[適用於 Azure CLI 的 IoT 延伸模組](https://github.com/Azure/azure-iot-cli-extension)來完成相同裝置建立作業。 下列範例會使用 X.509 自我簽署驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

如需用於裝置建立、憑證產生和父子式管理的 Azure CLI 命令詳細資訊，請參閱 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的參考內容。

接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

## <a name="register-device-x509-ca-signed"></a>註冊裝置 (X.509 CA 簽署)

針對 X.509 憑證授權單位 (CA) 簽署驗證，您需要在 IoT 中樞中註冊根 CA 憑證，以用來簽署 IoT 裝置的憑證。 所有使用由根 CA 憑證或其任何中繼憑證所發出憑證的裝置，都可進行驗證。

本節是以 IoT 中樞文章[在 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中詳述的指示為依據。 遵循本節中的步驟，以了解要使用哪些值來設定透過閘道連線的下游裝置。

如果沒有憑證授權單位可建立 X.509 憑證，則可[建立示範憑證來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游裝置產生測試憑證時，請使用為閘道裝置產生憑證的相同根 CA 憑證。

1. 遵循＜在 IoT 中樞中設定 X.509 安全性＞的＜ IoT 中樞註冊 X.509 CA 憑證＞[向](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一節中指示。 在該節中，您將執行下列步驟：

   1. 上傳根 CA 憑證。 如果使用示範憑證，則根 CA 會是 **\<路徑>/certs/azure-iot-test-only.root.ca.cert.pem**。

   2. 確認擁有該根 CA 憑證。

2. 遵循＜在 IoT 中樞中設定 X.509 安全性＞的＜[建立 IoT 中樞的 X.509 裝置](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)＞一節中指示。 在該節中，您將執行下列步驟：

   1. 新增裝置。 為 [裝置識別碼] 提供小寫名稱，然後選擇 [X.509 CA 簽署] 驗證類型。
   2. 設定父裝置。 針對下游裝置，選取 [設定父裝置]，然後選擇將提供 IoT 中樞連線的 IoT Edge 閘道裝置。

3. 為下游裝置建立憑證鏈結。 使用上傳至 IoT 中樞的相同根 CA 憑證來建立此鏈結。 使用在入口網站中為裝置身分識別所提供的相同小寫裝置識別碼。

4. 將裝置憑證和金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將會在連線到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可使用如 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)等功能來移動憑證檔案。

5. 視偏好的語言而定，檢閱如何在 IoT 應用程式中參考 X.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C：[iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java：[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

您可使用[適用於 Azure CLI 的 IoT 延伸模組](https://github.com/Azure/azure-iot-cli-extension)來完成相同裝置建立作業。 下列範例會使用 X.509 CA 簽署驗證來建立新的 IoT 裝置，然後指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

如需詳細資訊，請參閱 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的參考內容。

接下來，[擷取並修改連接字串](#retrieve-and-modify-connection-string)，讓裝置知道透過其閘道進行連線。

## <a name="retrieve-and-modify-connection-string"></a>擷取並修改連接字串

在入口網站中建立 IoT 裝置身分識別之後，即可擷取其主要或次要金鑰。 您必須在應用程式用來與 IoT 中樞通訊的連接字串中，包含這兩個金鑰的其中一個。 針對對稱金鑰驗證，IoT 中樞會在裝置詳細資料中提供完整格式的連接字串，以方便使用。 您必須將閘道裝置的額外資訊新增至連接字串。

下游裝置的連接字串需要下列元件：

* 與裝置連線的 IoT 中樞：`Hostname={iothub name}.azure-devices.net`
* 向中樞註冊的裝置識別碼：`DeviceID={device ID}`
* 主要或次要金鑰：`SharedAccessKey={key}`
* 裝置連線所透過的閘道裝置。 提供 IoT Edge 閘道裝置 config.yaml 檔案中的 **hostname** 值：`GatewayHostName={gateway hostname}`

結合所有元件的完整連接字串如下所示：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果已為此下游裝置建立父子式關聯性，則可藉由直接呼叫閘道作為連線主機來簡化連接字串。 父子式關聯性對於 X.509 驗證是必要的，但對於對稱金鑰驗證則為選擇性。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此時，您應該已註冊 IoT Edge 裝置，並將其設定為閘道。 您也已註冊下游 IoT 裝置，並將其指向其閘道裝置。 最後一個步驟是將憑證放在下游裝置上，讓裝置可安全地連線到閘道。

繼續進行閘道系列中的下一篇文章：[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

完成本文之後，您應該會有一部作為透明閘道的 IoT Edge 裝置，以及一部向 IoT 中樞註冊的下游裝置。 接下來，您必須設定下游裝置以信任閘道裝置，並安全地連線到該裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
