---
title: 連線下游裝置 - Azure IoT Edge | Microsoft Docs
description: 如何配置下游或葉設備以連接到 Azure IoT 邊緣閘道裝置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ff9415ca20b859468528b56d27355430c4fc5a0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652076"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>將下游裝置連線到 Azure IoT Edge 閘道

本文提供有關在下游設備和IoT Edge透明閘道之間建立可信連接的說明。 在透明閘道方案中,一個或多個設備可以通過維護與IoT中心連接的單個閘道裝置傳遞其消息。 下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游設備甚至可以是在 IoT Edge 閘道裝置本身上運行的應用程式。

有三個常規步驟來設置成功的透明閘道連接。 本文介紹第三步:

1. 閘道需要安全地連接到下游設備,接收來自下游設備的通信,並將消息路由到正確的目標。 有關詳細資訊,請參閱設定[IoT 邊緣裝置以充當透明閘道](how-to-create-transparent-gateway.md)。
2. 下游設備需要設備標識才能使用IoT中心進行身份驗證,並知道透過閘道裝置進行通信。 有關詳細資訊,請參閱[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)。
3. **下游設備需要安全地連接到其閘道設備。**

本文可透過下列方式，找出下游裝置連線的常見問題，並引導您設定下游裝置：

* 說明傳輸層安全性 (TLS) 和憑證的基本概念。
* 說明 TLS 程式庫在不同作業系統之間的運作方式，以及每個作業系統處理憑證的方式。
* 以數種語言逐步解說 Azure IoT 範例，以協助您開始使用。

在本文中，*閘道*和 *IoT Edge 閘道*這兩個詞是指設定為透明閘道的 IoT Edge 裝置。

## <a name="prerequisites"></a>Prerequisites

* 在[設定 IoT Edge 裝置](how-to-create-transparent-gateway.md)中產生的**azure-iot-test.root.ca.cert.pem**憑證檔充當下游設備上可用的透明閘道。 您的下游設備使用此證書來驗證閘道設備的標識。
* 具有指向閘道裝置的已修改連接字串,如[將下游設備驗證到 Azure IoT 中心](how-to-authenticate-downstream-device.md)中所述。

## <a name="prepare-a-downstream-device"></a>準備下游裝置

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游設備甚至可以是在 IoT Edge 閘道裝置本身上運行的應用程式。 但是,另一個IoT邊緣設備不能位於IoT邊緣閘道的下游。

>[!NOTE]
>在 IoT 中心中註冊標識的 IoT 設備可以使用[模組孿生](../iot-hub/iot-hub-devguide-module-twins.md)來隔離單個設備上的不同進程、硬體或功能。 IoT Edge 閘道使用對稱密鑰身份驗證支援下游模組連接,但不支援 X.509 憑證身份驗證。

若要將下游裝置連線到 IoT Edge 閘道，您需要具備兩樣東西：

* 使用附加資訊的 IoT 中樞裝置連接字串設定的裝置或應用程式，可將其連線至閘道。

    此步驟在[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)中進行了解釋。

* 設備或應用程式必須信任閘道的**根憑證**才能驗證與閘道裝置的 TLS 連接。

    本文的其餘部分將詳細介紹此步驟。 此步驟可以執行以下兩種方法之一:通過在作業系統的憑證儲存中安裝 CA 證書,或者(對於某些語言)使用 Azure IoT SDK 在應用程式中引用證書。

## <a name="tls-and-certificate-fundamentals"></a>TLS 和憑證的基本概念

安全地將下游裝置連線到 IoT Edge 的挑戰，如同網際網路上發生的其他任何安全用戶端/伺服器通訊。 用戶端和伺服器會透過網際網路，使用[傳輸層安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 安全地進行通訊。 TLS 是使用標準[公開金鑰基礎結構 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 建構 (稱為憑證) 建置的。 TLS 是一個相當涉及的規範,它涉及與保護兩個終結點相關的廣泛主題。 本節總結了將設備安全地連接到 IoT Edge 閘道的相關概念。

當用戶端連線到伺服器時，伺服器會顯示一連串的憑證，稱為*伺服器憑證鏈結*。 憑證鏈結通常會包含根憑證授權單位 (CA) 憑證、一個或多個中繼 CA 憑證，以及最後的伺服器憑證本身。 用戶端會以密碼編譯方式驗證整個伺服器憑證鏈結，藉此建立與伺服器的信任。 伺服器憑證連結的此客戶端認證為*伺服器鍊驗證*。 用戶端加密挑戰服務,以證明擁有與伺服器證書關聯的私鑰在稱為*佔有證明*的過程。 伺服器鍊驗證與佔有證明的組合為*伺服器身份認證*。 若要驗證伺服器憑證鏈結，用戶端需要一份用來建立 (或核發) 伺服器憑證的根 CA 憑證。 一般而言，當連線到網站時，瀏覽器已預先設定常用的 CA 憑證，讓用戶端的程序順暢進行。

當裝置連線到 Azure IoT 中樞時，該裝置就是用戶端，而 IoT 中樞雲端服務則是伺服器。 IoT 中樞雲端服務是以稱為 **Baltimore CyberTrust Root** 的根 CA 憑證作為後盾，這是公開可用且最廣泛使用的憑證。 大多數裝置上已安裝 IoT 中樞 CA 憑證，因此許多 TLS 實作 (OpenSSL、Schannel、LibreSSL) 會自動在伺服器憑證驗證期間使用該憑證。 可成功連線到 IoT 中樞的裝置在嘗試連線到 IoT Edge 閘道時可能會發生問題。

當裝置連線到 IoT Edge 閘道時，下游裝置就是用戶端，而閘道裝置則是伺服器。 但是，Azure IoT Edge 可讓操作員 (或使用者) 建置他們認為合適的閘道憑證鏈結。 操作員可以選擇使用公用 CA 憑證 (例如 Baltimore)，或使用自我簽署 (或內部) 的根 CA 憑證。 公用 CA 憑證通常會有與其相關聯的成本，因此通常用於實際執行案例中。 自我簽署的 CA 憑證慣用於開發和測試。 簡介中列出的透明閘道設定文章使用自簽名根 CA 證書。

當您將自我簽署的根 CA 憑證用於 IoT Edge 閘道時，它必須安裝在或提供給嘗試連線到閘道的所有下游裝置。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

若要深入了解 IoT Edge 憑證以及一些實際執行的影響，請參閱 [IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

## <a name="provide-the-root-ca-certificate"></a>提供根憑證

要驗證閘道裝置的證書,下游設備需要其自己的根 CA 證書副本。 如果使用 IoT Edge git 儲存函式庫中的文稿建立測試憑證,則根憑證稱為**azure-iot-test.root.ca.cert.pem**。 如果尚未作為其他下游設備準備步驟的一部分,請將此證書文件移動到下游設備上的任何目錄。 您可以使用[Azure 金鑰保管庫](https://docs.microsoft.com/azure/key-vault)等服務或[安全複製協定](https://www.ssh.com/ssh/scp/)等函數來行動證書檔。

## <a name="install-certificates-in-the-os"></a>在作業系統中安裝憑證

在作業系統的憑證儲存中安裝根 CA 憑證通常允許大多數應用程式使用根 CA 證書。 有一些例外情況,如 NodeJS 應用程式不使用 OS 憑證存儲,而是使用節點運行時的內部證書存儲。 如果無法在作業系統等級安裝憑證,請跳下跳至[使用具有 Azure IoT SDK 的憑證](#use-certificates-with-azure-iot-sdks)。

### <a name="ubuntu"></a>Ubuntu

下列命令是如何在 Ubuntu 主機上安裝 CA 憑證的範例。 此示例假定您使用的是來自先決條件文章中的**azure-iot-test.root.ca.cert.pem**證書,並且已將證書複製到下游設備上的位置。

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

您應該會看到一條消息,指出「在 /etc/ssl/證書中更新證書...1 已添加,0 已刪除;完成。

### <a name="windows"></a>Windows

下列步驟是如何在 Windows 主機上安裝 CA 憑證的範例。 此示例假定您使用的是來自先決條件文章中的**azure-iot-test.root.ca.cert.pem**證書,並且已將證書複製到下游設備上的位置。

您可以使用 PowerShell 匯[入憑證](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps)作為管理員安裝憑證:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

您還可以使用**憑證**執行程式安裝憑證:

1. 在 [開始] 功能表中，搜尋並選取 [管理電腦憑證]****。 稱為 **certlm** 的公用程式隨即開啟。
2. 瀏覽到**憑證 ─本地電腦** > **信任的根憑證的頒發機構**。
3. 在 [憑證]**** 上按一下滑鼠右鍵，然後選取 [所有工作]**** > [匯入]****。 憑證匯入精靈應該會啟動。
4. 請依照所指示的步驟，匯入憑證檔案 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成時，您應該會看到「已成功匯入」訊息。

您也可以使用 .NET API，以程式設計方式安裝憑證，如本文稍後的 .NET 範例中所示。

應用程式通常會使用 Windows 提供的 TLS 堆疊 (稱為 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel))，透過 TLS 安全地連線。 Schannel *要求*先安裝 Windows 憑證存放區中的任何憑證，然後再嘗試建立 TLS 連線。

## <a name="use-certificates-with-azure-iot-sdks"></a>使用具有 Azure IoT SDK 的憑證

本節描述如何使用簡單的範例應用程式，將 Azure IoT SDK 連線到 IoT Edge 裝置。 所有範例的目標都是將裝置用戶端連線至閘道，並對其傳送遙測訊息，然後關閉連線並結束。

使用應用程式層級的範例之前，請準備好兩件事：

* 下游設備的 IoT 中心連接字串已修改以指向閘道裝置,以及將下游設備驗證為 IoT 中心所需的任何證書。 有關詳細資訊,請參閱[將下游設備驗證為 Azure IoT 中心](how-to-authenticate-downstream-device.md)。

* 您複製並儲存在下游裝置上某個位置之根 CA 憑證的完整路徑。

    例如： `<path>/azure-iot-test-only.root.ca.cert.pem` 。

### <a name="nodejs"></a>NodeJS

本節提供將 Azure IoT NodeJS 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 對於 NodeJS 應用程式,必須在應用程式級別安裝根 CA 證書,如下所示。 NodeJS 應用程式不使用系統的憑證存儲。

1. 從[適用於 Node.js 的 Azure IoT 裝置 SDK 範例存放庫](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)取得 **edge_downstream_device.js** 的範例。
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。
3. 在 edge_downstream_device.js 檔案中，更新 **connectionString** 和 **edge_ca_cert_path** 變數。
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

若要了解您正在執行的範例，下列程式碼片段是用戶端 SDK 讀取憑證檔案，並將其用於建立安全 TLS 連線的方式：

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

本節介紹將 Azure IoT .NET 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 不過，.NET 應用程式可以在 Linux 和 Windows 主機上，自動使用系統憑證存放區中任何已安裝的憑證。

1. 從 [IoT Edge .NET 範例資料夾](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)取得 **EdgeDownstreamDevice** 的範例。
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。
3. 在 **Properties / launchSettings.json** 檔案中，更新 **DEVICE_CONNECTION_STRING** 和 **CA_CERTIFICATE_PATH** 變數。 如果您想要在主機系統上使用受信任憑證存放區中安裝的憑證，請將此變數留空。
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

若要以程式設計方式，透過 .NET 應用程式安裝憑證存放區中受信任的憑證，請參閱 **EdgeDownstreamDevice / Program.cs** 檔案中的 **InstallCACert()** 函式。 這項作業是等冪的，因此可以使用相同的值執行多次，而不會有其他影響。

### <a name="c"></a>C

本節介紹將 Azure IoT C 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 C SDK 可以使用許多 TLS 程式庫運作，包括 OpenSSL、WolfSSL 和 Schannel。 如需詳細資訊，請參閱 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)。

1. 從[適用於 C 的 Azure IoT 裝置 SDK 範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)取得 **iotedge_downstream_device_sample** 應用程式。
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。
3. 在 iotedge_downstream_device_sample.c 檔案中，更新 **connectionString** 和 **edge_ca_cert_path** 變數。
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

適用於 C 的 Azure IoT 裝置 SDK 可以選擇在設定用戶端時註冊 CA 憑證。 這項作業不會在任何位置安裝憑證，而是使用記憶體中憑證的字串格式。 建立連線時，會將已儲存的憑證提供給基礎 TLS 堆疊。

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

在 Windows 主機上，如果您使用的不是 OpenSSL 或另一個 TLS 程式庫，則 SDK 會預設使用 Schannel。 若要讓 Schannel 運作，則應該安裝 Windows 憑證存放區中的 IoT Edge 根 CA 憑證，而不是使用 `IoTHubDeviceClient_SetOption` 作業設定。

### <a name="java"></a>Java

本節介紹將 Azure IoT Java 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。

1. 從[適用於 Java 的 Azure IoT 裝置 SDK 範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)取得 **Send-event** 的範例。
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。
3. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

### <a name="python"></a>Python

本節介紹將 Azure IoT Python 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。

1. **從**[Azure IoT 裝置 SDK 取得 send_message,用於 Python 範例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)。
2. 確保在 IoT Edge 容器中運行,或者在除錯`EdgeHubConnectionString`方案中`EdgeModuleCACertificateFile`設置和環境變數。
3. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

## <a name="test-the-gateway-connection"></a>測試閘道連線

使用此範例指令測試下遊裝置是否可以連接到閘道裝置:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

此命令測試 MQTTS(埠 8883)的連接。 如果您使用的是其他協定,請根據需要調整 AMQPS (5671) 或 HTTPS (433) 的指令

此命令的輸出可能很長,包括有關鏈中所有證書的資訊。 如果連線成功,您將看到一行類似於`Verification: OK``Verify return code: 0 (ok)`或 。

![驗證閘道連線](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>排除閘道連線故障

如果您的葉設備與其閘道裝置有間歇性連接,請嘗試以下步驟進行解析。

1. 連接字串中的閘道主機名稱是否與閘道裝置上 IoT Edge config.yaml 檔中的主機名值相同?
2. 閘道主機名稱是否可重新解析為 IP 位址? 您可以通過使用 DNS 或在葉設備上添加主機檔項目來解決間歇性連接。
3. 防火牆中通信埠打開嗎? 下游設備和透明 IoT 邊緣之間必須能夠基於所使用的協定(MQTTS:8883/AMQPS:5671/HTTPS:433)進行通訊。

## <a name="next-steps"></a>後續步驟

了解 IoT Edge 如何將[離線功能](offline-capabilities.md)擴充到下游裝置。
