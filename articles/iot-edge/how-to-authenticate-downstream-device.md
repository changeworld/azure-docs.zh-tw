---
title: 認證下遊裝置 - Azure IoT 邊緣 |微軟文件
description: 如何對下游設備或葉設備進行身份驗證到 IoT 中心,並透過 Azure IoT 邊緣閘道裝置路由其連接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411503"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道方案中,下游設備(有時稱為葉設備或子設備)需要IoT中心中的標識,就像任何其他設備一樣。 本文介紹將下游設備驗證到IoT中心的選項,然後演示如何聲明閘道連接。

有三個常規步驟來設置成功的透明閘道連接。 本文介紹第二步:

1. 閘道需要能夠安全地連接到下游設備,接收來自下游設備的通信,並將消息路由到正確的目標。 有關詳細資訊,請參閱設定[IoT 邊緣裝置以充當透明閘道](how-to-create-transparent-gateway.md)。
2. **下游設備需要具有設備標識才能使用IoT中心進行身份驗證,並知道透過閘道裝置進行通訊。**
3. 下游設備需要安全地連接到其閘道設備。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游設備可以使用三種方法之一使用 IoT 中心進行身份驗證:對稱密鑰(有時稱為共用存取密鑰)、X.509 自簽署證書或 X.509 憑證頒發機構 (CA) 簽署證書。 身份驗證步驟類似於用於使用 IoT 中心設置任何非 IoT 邊緣設備的步驟,聲明閘道關係的差異很小。

本文中的步驟顯示手動設備預配,而不是使用 Azure IoT 中心設備配置服務 (DPS) 進行自動預配。 不支援使用 DPS 預配下游設備。

## <a name="prerequisites"></a>Prerequisites

完成[「設定 IoT Edge」裝置以充當透明閘道](how-to-create-transparent-gateway.md)的步驟。 如果您對下游設備使用 X.509 身份驗證,則需要使用在透明閘道文章中設置的相同證書生成腳稿。

本文在多個點參考*閘道主機名稱*。 閘道主機名稱在 IoT Edge 閘道上的 config.yaml 檔的**主機名**參數中聲明。 它在下游設備的連接字串中引用。 閘道主機名稱需要解析為 IP 位址,使用 DNS 或主機檔條目。

## <a name="register-device-symmetric-key"></a>註冊裝置 (對稱鍵)

對稱密鑰身份驗證或共享存取金鑰身份驗證是使用IoT中心進行身份驗證的最簡單方法。 通過對稱密鑰身份驗證,Base64 密鑰與IoT中心中的IoT設備ID相關聯。 在 IoT 應用程式中包括該密鑰,以便設備在連接到 IoT 中心時可以顯示該密鑰。

### <a name="create-the-device-identity"></a>建立裝置識別

使用 Azure 門戶、Azure CLI 或可視化工作室代碼的 IoT 擴展,在 IoT 中心添加新 IoT 設備。 請記住,下游設備需要在IoT中心中識別為常規IoT設備,而不是IoT邊緣設備。

建立新裝置識別時,請提供以下資訊:

* 為設備創建 ID。

* 選擇**對稱鍵**作為身份驗證類型。

* 或者,選擇**設置父設備**並選擇此下游設備將連接的 IoT Edge 閘道裝置。 此步驟對於對稱密鑰身份驗證是可選的,但建議這樣做,因為設定父設備可為下游設備啟用[離線功能](offline-capabilities.md)。 您始終可以更新設備詳細資訊以稍後添加或更改父級。

   ![在門戶中使用對稱金鑰 au00 建立裝置 ID](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

可以使用 Azure [CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)來完成相同的操作。 以下範例建立具有對稱金鑰身份驗證的新 IoT 裝置並分配父設備:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

有關用於設備創建和父/子管理的 Azure CLI 命令的詳細資訊,請參閱 az [iot 中心設備標識命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)參考內容。


接下來,[檢索和修改連接字串](#retrieve-and-modify-connection-string),以便設備知道通過閘道進行連接。

## <a name="register-device-x509-self-signed"></a>註冊裝置 (X.509 簽署)

對於 X.509 自簽署身份驗證(有時稱為指紋身份驗證),您需要創建新證書以放置在 IoT 設備上。 這些證書有一個指紋,您與IoT中心共用以進行身份驗證。

如果沒有憑證發行機構來建立 X.509 憑證,則可以[建立示範憑證來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游設備生成測試證書時,請使用為閘道設備生成證書的相同根 CA 證書。

1. 使用 CA 憑證,為下游設備創建兩個設備證書(主證書和輔助證書)。

   設備證書必須將主題名稱設置為在 Azure IoT 中心中註冊 IoT 設備時將使用的設備 ID。 此設置是身份驗證所必需的。

2. 從每個證書(即 40 個十六進位元字串)中檢索 SHA1 指紋(稱為 IoT 中心介面中的指紋)。 使用以下 openssl 指令檢視憑證並找到指紋:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   運行此命令兩次,一次用於主證書,一次用於輔助證書。 當您使用自簽名 X.509 憑證註冊新的 IoT 裝置時,您可以為兩個證書提供指紋。

3. 導覽到 Azure 門戶中的 IoT 中心,並使用以下值建立新的 IoT 裝置識別:

   * 提供與設備證書的主題名稱匹配**的設備ID。**
   * 選擇**X.509 自簽名**作為身份驗證類型。
   * 貼上從裝置的主證書和輔助證書複製的十六進位元字串。
   * 選擇 **「設定父設備**」並選擇此下游設備將連接的 IoT Edge 閘道裝置。 下游設備的 X.509 身份驗證需要父設備。

   ![在門戶中使用 X.509 自簽署 au00 建立裝置 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 將設備證書和金鑰複製到下游設備上的任何位置。 還要移動生成閘道裝置證書和下游設備證書的共用根 CA 證書的副本。

   您將在連接到 IoT 中心的葉設備應用程式中引用這些檔案。 您可以使用[Azure 金鑰保管庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來行動證書檔。

5. 依您的首選語言,請檢視如何在 IoT 應用程式中引用 X.509 憑證的範例:

   * C#:[在 Azure IoT 中心中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

可以使用 Azure [CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)來完成相同的設備創建操作。 以下範例建立具有 X.509 自我簽署身份驗證並分配父設備的新 IoT 裝置:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有關用於設備創建、證書生成以及父和子管理的 Azure CLI 命令的詳細資訊,請參閱 az [iot 中心設備識別命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)參考內容。

接下來,[檢索和修改連接字串](#retrieve-and-modify-connection-string),以便設備知道通過閘道進行連接。

## <a name="register-device-x509-ca-signed"></a>註冊裝置 (X.509 CA 已簽署)

對於 X.509 憑證頒發機構 (CA) 簽署身份驗證,您需要在 IoT 中心註冊的根 CA 證書,用於為 IoT 設備簽名證書。 任何使用根 CA 證書或其任何中間證書頒發的證書的設備都允許進行身份驗證。

本節基於 IoT 中心文章"[在 Azure IoT 中心中設置 X.509 安全性「中](../iot-hub/iot-hub-security-x509-get-started.md)詳細說明的說明。 按照本節中的步驟瞭解使用哪些值來設置通過網關連接的下游設備。

如果沒有憑證發行機構來建立 X.509 憑證,則可以[建立示範憑證來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 為下游設備生成測試證書時,請使用為閘道設備生成證書的相同根 CA 證書。

1. 按照[註冊 X.509 CA 憑證中的說明,到](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)Azure *IoT 中心中的"設置 X.509 安全性"的*IoT 中心部分。 在本節中,您將執行以下步驟:

   1. 上傳根 CA 證書。 如果使用示範憑證,則根 CA 是**\<路徑>/證書/azure-iot-測試僅.root.ca.cert.pem**。

   2. 驗證您是否擁有該根 CA 證書。

2. 按照 Azure IoT 中心中的 IoT 中心部分的[「 為 I.509 創建 X.509 裝置](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)」*中的*說明操作。 在本節中,您將執行以下步驟:

   1. 添加新設備。 為**設備ID**提供小寫名稱,然後選擇已簽名的**X.509 CA的**身分驗證類型。
   2. 設置父設備。 對於下游設備,選擇 **「設定父設備」,** 然後選擇將提供與 IoT 中心連接的 IoT 邊緣閘道裝置。

3. 為下游設備創建證書鏈。 使用上載到 IoT 中心的相同根 CA 證書來製作此鏈。 使用您在門戶中為設備標識提供的相同小寫設備 ID。

4. 將設備證書和金鑰複製到下游設備上的任何位置。 還要移動生成閘道裝置證書和下游設備證書的共用根 CA 證書的副本。

   您將在連接到 IoT 中心的葉設備應用程式中引用這些檔案。 您可以使用[Azure 金鑰保管庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來行動證書檔。

5. 依您的首選語言,請檢視如何在 IoT 應用程式中引用 X.509 憑證的範例:

   * C#:[在 Azure IoT 中心中設定 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

可以使用 Azure [CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)來完成相同的設備創建操作。 以下範例建立具有 X.509 CA 簽署識別碼管理並分配父設備的新 IoT 裝置:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有關詳細資訊,請參閱[az iot 中心設備識別命令的](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)Azure CLI 參考內容。

接下來,[檢索和修改連接字串](#retrieve-and-modify-connection-string),以便設備知道通過閘道進行連接。

## <a name="retrieve-and-modify-connection-string"></a>檢索與修改連接字串

在門戶中創建 IoT 設備標識後,可以檢索其主密鑰或輔助密鑰。 這些密鑰之一需要包含在應用程式用於與IoT中心通訊的連接字串中。 對於對稱密鑰身份驗證,IoT 中心在設備詳細資訊中提供完全成形的連接字串,以方便您使用。 您需要向連接字串添加有關閘道裝置的額外資訊。

下遊裝置的連接字串需要以下元件:

* 裝置連線到的 IoT 中心:`Hostname={iothub name}.azure-devices.net`
* 在集線器中註冊的裝置 ID:`DeviceID={device ID}`
* 主鍵或輔助鍵:`SharedAccessKey={key}`
* 設備連接的閘道裝置。 從 IoT Edge 閘道裝置的 config.yaml 檔案中提供**主機名稱**值:`GatewayHostName={gateway hostname}`

總之,完整的連接字串如下所示:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果為此下游設備建立了父/子關係,則可以通過將網關直接稱為連接主機來簡化連接字串。 X.509 身份驗證需要父/子關係,但對稱密鑰身份驗證是可選的。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此時,您應該將 IoT Edge 設備註冊並配置為閘道。 您還註冊了下游IoT設備並指向其閘道裝置。 最後一步是在下游設備上放置證書,以便它可以安全地連接到閘道。

繼續到閘道系列的下一篇文章,[將下游裝置連接到 Azure IoT 邊緣閘道](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

通過完成本文,您應該讓 IoT Edge 設備用作透明閘道,下游設備註冊為 IoT 中心。 接下來,您需要配置下游設備以信任閘道設備並安全地連接到它。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
