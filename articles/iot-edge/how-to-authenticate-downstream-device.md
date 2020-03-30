---
title: 驗證下游設備 - Azure IoT 邊緣 |微軟文檔
description: 如何對下游設備或葉設備進行身份驗證到 IoT 中心，並通過 Azure IoT 邊緣閘道設備路由其連接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b8db3fedc5886e86d5f49739b87b26535665bdbc
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389319"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道方案中，下游設備（有時稱為葉設備或子設備）需要 IoT 中心中的標識，就像任何其他設備一樣。 本文介紹將下游設備驗證到 IoT 中心的選項，然後演示如何聲明閘道連接。

有三個常規步驟來設置成功的透明閘道連接。 本文介紹第二步：

1. 閘道設備需要能夠安全地連接到下游設備，接收來自下游設備的通信，並將消息路由到正確的目標。 有關詳細資訊，請參閱配置[IoT 邊緣設備以充當透明閘道](how-to-create-transparent-gateway.md)。
2. **下游設備需要具有設備標識才能使用 IoT 中心進行身份驗證，並知道通過閘道設備進行通信。**
3. 下游設備需要安全地連接到其閘道設備。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游設備可以使用三種方法之一使用 IoT 中心進行身份驗證：對稱金鑰（有時稱為共用訪問金鑰）、X.509 自簽章憑證或 X.509 憑證頒發機構 （CA） 簽章憑證。 身份驗證步驟類似于用於使用 IoT 中心設置任何非 IoT 邊緣設備的步驟，聲明閘道關係的差異很小。

本文中的步驟顯示手動設備預配，而不是使用 Azure IoT 中心設備佈建服務 （DPS） 進行自動預配。 不支援使用 DPS 預配下游設備。

## <a name="prerequisites"></a>Prerequisites

完成["配置 IoT Edge"設備以充當透明閘道](how-to-create-transparent-gateway.md)的步驟。 如果您對下游設備使用 X.509 身份驗證，則需要使用在透明閘道文章中設置的相同證書生成腳本。

本文在多個點引用*閘道主機名稱*。 閘道主機名稱在 IoT Edge 閘道設備上的 config.yaml 檔的**主機名稱**參數中聲明。 它在下游設備的連接字串中引用。 閘道主機名稱需要解析為 IP 位址，使用 DNS 或主機檔條目。

## <a name="register-device-symmetric-key"></a>註冊設備（對稱鍵）

對稱金鑰身份驗證或共用訪問金鑰身份驗證是使用 IoT 中心進行身份驗證的最簡單方法。 通過對稱金鑰身份驗證，Base64 金鑰與 IoT 中心中的 IoT 裝置識別碼 相關聯。 在 IoT 應用程式中包括該金鑰，以便設備在連接到 IoT 中心時可以顯示該金鑰。

### <a name="create-the-device-identity"></a>創建設備標識

使用 Azure 門戶、Azure CLI 或視覺化工作室代碼的 IoT 擴展，在 IoT 中心添加新 IoT 設備。 請記住，下游設備需要在 IoT 中心中標識為常規 IoT 設備，而不是 IoT 邊緣設備。

創建新設備標識時，請提供以下資訊：

* 為設備創建 ID。

* 選擇**對稱鍵**作為身份驗證類型。

* 或者，選擇**設置父設備**並選擇此下游設備將連接的 IoT Edge 閘道設備。 此步驟對於對稱金鑰身份驗證是可選的，但建議這樣做，因為設置父設備可為下游設備啟用[離線功能](offline-capabilities.md)。 您始終可以更新設備詳細資訊以稍後添加或更改父級。

   ![在門戶中使用對稱金鑰 au00 創建裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

可以使用 Azure [CLI 的 IoT 擴展](https://github.com/Azure/azure-iot-cli-extension)來完成相同的操作。 以下示例創建具有對稱金鑰身份驗證的新 IoT 設備並分配父設備：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

有關用於設備創建和父/子管理的 Azure CLI 命令的詳細資訊，請參閱 az [iot 中心設備標識命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)參考內容。


接下來，[檢索和修改連接字串](#retrieve-and-modify-connection-string)，以便設備知道通過閘道進行連接。

## <a name="register-device-x509-self-signed"></a>註冊設備（X.509 自簽名）

對於 X.509 自簽名身份驗證（有時稱為指紋身份驗證），您需要創建新證書以放置在 IoT 設備上。 這些證書有一個指紋，您與 IoT 中心共用以進行身份驗證。

如果沒有憑證授權單位來創建 X.509 憑證，則可以[創建演示證書來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游設備生成測試憑證時，請使用為閘道設備生成證書的相同根 CA 憑證。

1. 使用 CA 憑證，為下游設備創建兩個設備證書（主證書和輔助證書）。

   設備證書必須將主題名稱設置為在 Azure IoT 中心中註冊 IoT 設備時將使用的裝置識別碼。 此設置是身份驗證所必需的。

2. 從每個證書（即 40 個十六進位字串）中檢索 SHA1 指紋（稱為 IoT 中心介面中的指紋）。 使用以下 openssl 命令查看證書並找到指紋：

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   運行此命令兩次，一次用於主證書，一次用於輔助證書。 當您使用自簽名 X.509 憑證註冊新的 IoT 設備時，您可以為兩個證書提供指紋。

3. 導航到 Azure 門戶中的 IoT 中心，並使用以下值創建新的 IoT 設備標識：

   * 提供與設備證書的主題名稱匹配**的裝置識別碼。**
   * 選擇**X.509 自簽名**作為身份驗證類型。
   * 粘貼從設備的主證書和輔助證書複製的十六進位字串。
   * 選擇 **"設置父設備**"並選擇此下游設備將連接的 IoT Edge 閘道設備。 下游設備的 X.509 身份驗證需要父設備。

   ![在門戶中使用 X.509 自簽名 au00 創建裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 將設備證書和金鑰複製到下游設備上的任何位置。 還要移動生成閘道設備證書和下游設備證書的共用根 CA 憑證的副本。

   您將在連接到 IoT 中心的葉設備應用程式中引用這些檔。 您可以使用[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來移動證書檔。

5. 根據您的首選語言，請查看如何在 IoT 應用程式中引用 X.509 憑證的示例：

   * C#：[在 Azure IoT 中心中設置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * JAVA： [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python： [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

可以使用 Azure [CLI 的 IoT 擴展](https://github.com/Azure/azure-iot-cli-extension)來完成相同的設備創建操作。 以下示例創建具有 X.509 自簽名身份驗證並分配父設備的新 IoT 設備：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有關用於設備創建、證書生成以及父和子管理的 Azure CLI 命令的詳細資訊，請參閱 az [iot 中心設備標識命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)參考內容。

接下來，[檢索和修改連接字串](#retrieve-and-modify-connection-string)，以便設備知道通過閘道進行連接。

## <a name="register-device-x509-ca-signed"></a>註冊設備（X.509 CA 已簽名）

對於 X.509 憑證頒發機構 （CA） 簽名身份驗證，您需要在 IoT 中心註冊的根 CA 憑證，用於為 IoT 設備簽章憑證。 任何使用根 CA 憑證或其任何中繼憑證頒發的證書的設備都允許進行身份驗證。

本節基於 IoT 中心文章"[在 Azure IoT 中心中設置 X.509 安全性"中](../iot-hub/iot-hub-security-x509-get-started.md)詳細說明的說明。 按照本節中的步驟瞭解使用哪些值來設置通過閘道連接的下游設備。

如果沒有憑證授權單位來創建 X.509 憑證，則可以[創建演示證書來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游設備生成測試憑證時，請使用為閘道設備生成證書的相同根 CA 憑證。

1. 按照[註冊 X.509 CA 憑證中的說明，到](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)Azure *IoT 中心中的"設置 X.509 安全性"的*IoT 中心部分。 在本節中，您將執行以下步驟：

   1. 上傳根 CA 憑證。 如果使用演示證書，則根 CA 是**\<路徑>/證書/azure-iot-測試僅.root.ca.cert.pem**。

   2. 驗證您是否擁有該根 CA 憑證。

2. 按照 Azure IoT 中心中的 IoT 中心部分的"[為 I.509 創建 X.509 設備](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)"*中的*說明操作。 在本節中，您將執行以下步驟：

   1. 添加新設備。 為**裝置識別碼**提供小寫名稱，然後選擇已簽名的**X.509 CA 的**身份驗證類型。
   2. 設置父設備。 對於下游設備，選擇 **"設置父設備"，** 然後選擇將提供與 IoT 中心連接的 IoT 邊緣閘道設備。

3. 為下游設備創建憑證連結。 使用上載到 IoT 中心的相同根 CA 憑證來製作此鏈。 使用您在門戶中為設備標識提供的相同小寫裝置識別碼。

4. 將設備證書和金鑰複製到下游設備上的任何位置。 還要移動生成閘道設備證書和下游設備證書的共用根 CA 憑證的副本。

   您將在連接到 IoT 中心的葉設備應用程式中引用這些檔。 您可以使用[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來移動證書檔。

5. 根據您的首選語言，請查看如何在 IoT 應用程式中引用 X.509 憑證的示例：

   * C#：[在 Azure IoT 中心中設置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * JAVA： [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python： [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

可以使用 Azure [CLI 的 IoT 擴展](https://github.com/Azure/azure-iot-cli-extension)來完成相同的設備創建操作。 以下示例創建具有 X.509 CA 簽名身份驗證並分配父設備的新 IoT 設備：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有關詳細資訊，請參閱[az iot 中心設備標識命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)Azure CLI 參考內容。

接下來，[檢索和修改連接字串](#retrieve-and-modify-connection-string)，以便設備知道通過閘道進行連接。

## <a name="retrieve-and-modify-connection-string"></a>檢索和修改連接字串

在門戶中創建 IoT 設備標識後，可以檢索其主金鑰或輔助金鑰。 這些金鑰之一需要包含在應用程式用於與 IoT 中心通信的連接字串中。 對於對稱金鑰身份驗證，IoT 中心在設備詳細資訊中提供完全成形的連接字串，以方便您使用。 您需要向連接字串添加有關閘道設備的額外資訊。

下游設備的連接字串需要以下元件：

* 設備連接到的 IoT 中心：`Hostname={iothub name}.azure-devices.net`
* 在集線器中註冊的裝置識別碼：`DeviceID={device ID}`
* 主鍵或輔助鍵：`SharedAccessKey={key}`
* 設備連接的閘道設備。 從 IoT Edge 閘道設備的 config.yaml 檔中提供**主機名稱**值：`GatewayHostName={gateway hostname}`

總之，完整的連接字串如下所示：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果為此下游設備建立了父/子關係，則可以通過將閘道直接稱為連接主機來簡化連接字串。 X.509 身份驗證需要父/子關係，但對稱金鑰身份驗證是可選的。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此時，您應該將 IoT Edge 設備註冊並配置為閘道。 您還註冊了下游 IoT 設備並指向其閘道設備。 最後一步是在下游設備上放置證書，以便它可以安全地連接到閘道。

繼續到閘道系列的下一篇文章，[將下游設備連接到 Azure IoT 邊緣閘道](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

通過完成本文，您應該讓 IoT Edge 設備用作透明閘道，下游設備註冊為 IoT 中心。 接下來，您需要配置下游設備以信任閘道設備並安全地連接到它。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
