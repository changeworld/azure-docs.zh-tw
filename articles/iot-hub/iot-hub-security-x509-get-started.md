---
title: Azure IoT 中樞中的 X.509 安全性教學課程 | Microsoft Docs
description: 開始在模擬環境中使用您 Azure IoT 中樞中以 X.509 作為基礎的安全性。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: a22808b1d7ab2b2451f50470e8da3770d07407a5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985655"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>在您的 Azure IoT 中樞中設定 X.509 安全性

本教學示範了使用*X.509 憑證身份驗證*保護 Azure IoT 中心所需的步驟。 為了便於說明,我們使用開源工具 OpenSSL 在 Windows 計算機上本地創建證書。 建議您僅將本教學課程用於測試目的。 針對生產環境，您應該向根憑證授權單位 (CA) 購買憑證**。

## <a name="prerequisites"></a>Prerequisites

本教學課程需要您準備下列資源：

* 您已使用 Azure 訂用帳戶建立 IoT 中樞。 如需詳細步驟，請參閱[透過入口網站建立 IoT 中樞](iot-hub-create-through-portal.md)。

* 您安裝了[Visual Studio 2017 或視覺工作室 2019。](https://www.visualstudio.com/vs/)

## <a name="get-x509-ca-certificates"></a>取得 X.509 CA 憑證

「IoT 中樞」中的 X.509 憑證型安全性會要求您從 [X.509 憑證鏈結](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)開始著手，此鏈結包含根憑證，以及向上包含任何中繼憑證，一直到分葉憑證為止。

您可以選擇以下任何方法抓取憑證:

* 從根憑證授權單位 (CA)** 購買 X.509 憑證。 此方法建議用於生產環境。

* 使用第三方工具(如[OpenSSL)](https://www.openssl.org/)創建自己的 X.509 證書。 此技術適用於測試和開發目的。 如需使用 PowerShell 或 Bash 產生測試 CA 憑證的相關資訊，請參閱[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)產生的相關資訊。 本教學課程的其餘部分會使用依照[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的指示而產生的測試 CA 憑證。

* 生成由現有根 CA 證書簽名的[X.509 中間 CA 證書](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust)並將其上傳到中心。 按照以下指示上載和驗證中間證書后,可以在下面提到的根 CA 證書的位置使用。 OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html)和[openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) 等工具可用於生成和簽名中間 CA 憑證。

> [!NOTE]
> 如果第三方根不是您獨有的,請不要上傳第三方根,因為這將使第三方的其他客戶能夠將其設備連接到 IoT 中心。

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>向 IoT 中樞註冊 X.509 CA 憑證

這些步驟示範如何透過入口網站將新的「憑證授權單位」新增至您的 IoT 中樞。

1. 在 Azure 門戶中,導航到 IoT 中心**並選擇** > 中心設定**憑證**。

1. 選擇 **'新增**'新增新憑證。

1. 在**憑證名稱**中,輸入友好的顯示名稱,並從電腦中選擇您在上一節中創建的證書檔。

1. 收到成功上傳證書的通知後,選擇 **「儲存**」。

    ![Upload certificate](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   您的憑證將顯示在狀態為 **「未驗證」** 的憑證清單中。

1. 選擇您剛剛添加的證書以顯示**證書詳細資訊**,然後選擇 **「生成驗證碼**」 。

   ![驗證憑證](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. 將**驗證碼**複製到剪貼簿。 您可以使用它驗證證書擁有權。

1. 依第 3 步[管理範例與教學的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)。  此過程使用與生成簽名的 X.509 CA 證書的私鑰關聯簽署驗證碼。 有一些工具可用來執行此簽署程序，例如 OpenSSL。 這個過程被稱為[佔有證明](https://tools.ietf.org/html/rfc5280#section-3.1)。

1. 在**憑證詳細資訊**中,在**驗證證書 .pem 或 .cer 檔中**,尋找簽名檔。 然後選擇 **『驗證**』。

   證書的狀態更改為 **「已驗證」。** 如果證書未自動更新,請選擇 **「刷新**」。。

## <a name="create-an-x509-device-for-your-iot-hub"></a>建立 IoT 中樞的 X.509 裝置

1. 在 Azure 門戶中,導覽到 IoT 中心,然後選擇**資源管理器** > **IoT 裝置**。

1. 選擇 **「新建」** 以新增新裝置。

1. 在**設備ID**中,輸入友好的顯示名稱。 對於**認證類型**,請選擇**X.509 CA 已簽署**,然後選擇 **「儲存**」 。

   ![在入口網站中建立 X.509 裝置](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>使用 X.509 憑證驗證您的 X.509 裝置

若要驗證您的 X.509 裝置，您必須先使用 CA 憑證簽署裝置。 分葉裝置的簽章通常會在製造工廠完成，並在此據以啟用製造工具。 因為裝置會從一個製造商送至另一個製造商，每個製造商的簽署動作都會擷取作為鏈結內的中繼憑證。 結果是從 CA 證書到設備的葉證書的證書鏈。 [管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步驟 4 會產生裝置憑證。

接下來，我們將示範如何建立 C# 應用程式來模擬為您 IoT 中樞註冊的 X.509 裝置。 我們會將氣溫和溼度值從模擬裝置傳送至您的中樞。 在本教程中,我們將只創建設備應用程式。 它會保留給讀者作為練習建立 IoT 中樞服務應用程式，將回應傳送給此模擬裝置所傳送的事件。 C# 應用程式假設您已依照[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步驟操作。

1. 打開視覺化工作室,選擇 **「創建新專案**」,然後選擇**控制台應用 (.NET Framework)** 專案範本。 選取 [下一步]  。

1. 在 **「設定新專案」** 中,命名專案*類比X509Device,* 然後選擇 **「創建**」 。

   ![在 Visual Studio 中建立 X.509 裝置專案](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 在解決方案資源管理器中,右鍵單擊**類比X509設備**專案,然後選擇 **「管理 NuGet 包**」。

1. 在**NuGet 套件管理員**中,選擇 **「瀏覽**」並搜尋並選擇**Microsoft.Azure.Devices.Client**。 選取 [安裝]  。

   ![在 Visual Studio 中新增裝置 SDK NuGet 套件](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    此步驟下載、安裝並添加對 Azure IoT 設備 SDK NuGet 包及其依賴項的引用。

1. 在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. 將以下欄位新增到**程式**類別:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    使用上一節中使用的友好設備名稱代替 _<your_device_id>。_

1. 新增下列函式來建立氣溫和溼度的隨機數字，並將這些值傳送至中樞：

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. 最後,將以下代碼行新增到**主**函式中,根據設定的要求取代佔位元_裝置 ID、__您的 iot 集線器名稱_,_以及絕對路徑到裝置 pfx 檔_。

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   此程式碼會連線到 IoT 中樞，方法為建立 X.509 裝置的連接字串。 一旦成功連線後，就會將氣溫和溼度事件傳送至中樞，並等候其回應。

1. 執行應用程式。 由於此應用程式訪問 *.pfx*檔,您可能需要作為管理員運行此應用程式。

   1. 建置 Visual Studio 解決方案。

   1. 使用 **「作為管理員運行」** 打開新的命令提示視窗。  

   1. 導航到包含解決方案的資料夾,然後導航到解決方案資料夾中的*bin/調試*路徑。

   1. 從命令提示符執行應用程式**類比X509Device.exe。**

   您應該會看到裝置成功連線至中樞並傳送事件。

   ![執行裝置應用程式](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何保護您的 IoT 解決方案，請參閱︰

* [IoT 安全性最佳實作](../iot-fundamentals/iot-security-best-practices.md)

* [IoT 安全性架構](../iot-fundamentals/iot-security-architecture.md)

* [保護您的 IoT 部署](../iot-fundamentals/iot-security-deployment.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
