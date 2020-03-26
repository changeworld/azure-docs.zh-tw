---
title: 教學課程：透過透明閘道傳送裝置資料 - Azure IoT Edge 上的 Machine Learning
description: 本教學課程說明如何使用您的開發電腦作為模擬 IoT Edge 裝置，透過設定為透明閘道的裝置將資料傳送至 IoT 中樞。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706901"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>教學課程：透過透明閘道傳送資料

> [!NOTE]
> 此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 如果您是被直接引導至此文章，我們建議您先從本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)開始，以取得最佳成效。

在此文章中，我們會再次使用開發電腦作為模擬裝置，但與其直接將資料傳送至 IoT 中樞，裝置會將資料傳送至設定為透明閘道的 IoT Edge 裝置。

我們會在模擬裝置傳送資料的期間，監視 IoT Edge 裝置的作業。 當裝置停止執行之後，我們會查看儲存體帳戶中的資料，以確認一切皆如預期般運作。

此步驟通常是由雲端或裝置開發人員來執行。

## <a name="review-device-harness"></a>檢閱裝置載入器

重新使用 [DeviceHarness 專案](tutorial-machine-learning-edge-03-generate-data.md)來模擬下游 (或分葉) 裝置。 連線至透明閘道還有額外的兩個必要項目：

* 註冊憑證以使下游裝置 (在此案例中為我們的開發機器) 信任由 IoT Edge 執行階段所使用的憑證授權單位。
* 將邊緣閘道完整網域名稱 (FQDN) 加入裝置連接字串。

查看程式碼以了解這兩個項目的實作方式。

1. 在您的開發電腦上開啟 Visual Studio Code。

2. 使用 [檔案]   > [開啟資料夾]  來開啟 C:\\source\\IoTEdgeAndMlSample\\DeviceHarness。

3. 查看 Program.cs 中的 InstallCertificate() 方法。

4. 請注意，如果程式碼找到憑證路徑，便會呼叫 CertificateManager.InstallCACert 方法將該憑證安裝至電腦上。

5. 現在，請查看 TurbofanDevice 類別上的 GetIotHubDevice 方法。

6. 當使用者使用 “-g” 選項指定閘道的 FQDN 時，該值會以 gatewayFqdn 的形式被傳遞至此方法；其會被附加至裝置連接字串。

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>建置並執行分葉裝置

1. 在 DeviceHarness 專案仍在 Visual Studio Code 中開啟的情況下建置專案 (Ctrl + Shift + B 或 [終端機]   > [執行建置工作]  )，然後從對話方塊中選取 [建置]  。

2. 在入口網站中瀏覽至您的 IoT Edge 裝置虛擬機器，並從概觀中複製 [DNS 名稱]  的值，來取得邊緣閘道的完整網域名稱 (FQDN)。

3. 開啟 Visual Studio Code 終端機 ([終端機]   > [新增終端機]  ) 並執行下列命令，將 `<edge_device_fqdn>` 取代為您從虛擬機器所複製的 DNS 名稱：

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. 應用程式會嘗試將憑證安裝至您的開發電腦上。 成功時，請接受安全性警告。

5. 當系統提示您提供 IoT 中樞連接字串時，請按一下 Azure IoT 中樞裝置面板上的省略符號 ( **...** )，然後選取 [複製 IoT 中樞連接字串]  。 將值貼上至終端機中。

6. 您將看到類似以下的輸出內容：

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   請注意，將 “GatewayHostName” 加入裝置連接字串會導致裝置透過 IoT Edge 透明閘道與 IoT 中樞通訊。

## <a name="check-output"></a>檢查輸出

### <a name="iot-edge-device-output"></a>IoT Edge 裝置輸出

來自 avroFileWriter 模組的輸出可以直接透過查看 IoT Edge 裝置來觀察。

1. 透過 SSH 連線至您的 IoT Edge 虛擬機器。

2. 尋找寫入至磁碟的檔案。

   ```bash
   find /data/avrofiles -type f
   ```

3. 命令的輸出看起來會類似下列範例：

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   根據執行的時機，您可能會有超過一個檔案。

4. 請特別留意時間戳記。 avroFileWriter 模組會在上次修改時間超過目前時間 10 分鐘以上時將檔案上傳至雲端 (請參閱 avroFileWriter module 的 uploader.py 中的 MODIFIED\_FILE\_TIMEOUT)。

5. 經過 10 分鐘之後，模組應該會開始上傳檔案。 如果上傳成功，它便會從磁碟將檔案刪除。

### <a name="azure-storage"></a>Azure 儲存體

我們可以透過查看儲存體帳戶中的預期資料路由位置，來觀察分葉裝置傳送資料的結果。

1. 在開發電腦上開啟 Visual Studio Code。

2. 在探索視窗的 [AZURE 儲存體] 面板中，瀏覽至樹狀以找出您的儲存體帳戶。

3. 展開 [Blob 容器]  節點。

4. 根據我們在先前教學課程部分中所處理的工作，我們預期 **ruldata** 容器應該會包含具有 RUL 的訊息。 展開 **ruldata** 節點。

5. 您將會看到一或多個具有類似下列名稱的 Blob 檔案：`<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`。

6. 以滑鼠右鍵按一下其中一個檔案，然後選擇 [下載 Blob]  來將檔案儲存至您的開發電腦。

7. 接下來請展開 **uploadturbofanfiles** 節點。 在上一篇文章中，我們將此位置設定為由 avroFileWriter 模組所上傳之檔案的目標。

8. 以滑鼠右鍵按一下這些檔案，然後選擇 [下載 Blob]  來將它儲存至您的開發電腦。

### <a name="read-avro-file-contents"></a>讀取 Avro 檔案內容

我們已包含簡單的命令列公用程式來讀取 Avro 檔案，並傳回檔案中訊息的 JSON 字串。 在此節中，我們將會安裝並執行它。

1. 在 Visual Studio Code 中開啟終端機 ([終端機]   > [新增終端機]  )。

2. 安裝 hubavroreader：

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. 使用 hubavroreader 來讀取從 **ruldata** 所下載的 Avro 檔案。

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. 請注意，訊息的本文看起來和我們所預期的相同，包含裝置識別碼和預測的 RUL。

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. 執行相同的命令來傳遞您從 **uploadturbofanfiles** 下載的 Avro 檔案。

6. 正如我們所預期的，這些訊息包含來自原始訊息的所有感應器資料和作業設定。 此資料可以用來改善我們邊緣裝置上的 RUL 模型。

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>清除資源

如果您打算探索本端對端教學課程中所使用的資源，請等到您探索完畢之後再清理您所建立的資源。 如果您不打算繼續，請使用下列步驟來刪除它們：

1. 刪除用來儲存 Dev VM、IoT Edge VM、IoT 中樞、儲存體帳戶、Machine Learning 工作區服務 (以及所建立的資源：容器登錄、Application Insights、金鑰保存庫、儲存體帳戶) 的資源群組。

2. 刪除 [Azure Notebooks](https://notebooks.azure.com)(英文\) 中的機器學習專案。

3. 如果您將存放庫複製到本機，請關閉參考該本機存放庫的任何 PowerShell 或 VS Code 視窗，然後刪除存放庫目錄。

4. 如果您在本機建立憑證，請刪除 c:\\edgeCertificates 資料夾。

## <a name="next-steps"></a>後續步驟

在此文章中，我們已使用開發電腦來模擬將感應器和作業資料傳送至邊緣裝置的分葉裝置。 我們以透過先檢查邊緣裝置的即時作業，然後查看上傳至儲存體帳戶的檔案，來驗證路由、分類、保存及上傳資料之裝置上的模組。

您可以在下列頁面上找到更多資訊：

* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
* [在 IoT Edge (預覽) 使用 Azure Blob 儲存體，以便在邊緣儲存資料](how-to-store-data-blob.md)
