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
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759644"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>在您的 Azure IoT 中樞中設定 X.509 安全性

本教學課程說明使用*X.509 憑證驗證*來保護 Azure IoT 中樞所需的步驟。 為了說明，我們使用開放原始碼工具 OpenSSL，在您的 Windows 電腦本機上建立憑證。 建議您僅將本教學課程用於測試目的。 針對生產環境，您應該向根憑證授權單位 (CA) 購買憑證**。

## <a name="prerequisites"></a>先決條件

本教學課程需要您準備下列資源：

* 您已使用 Azure 訂用帳戶建立 IoT 中樞。 如需詳細步驟，請參閱[透過入口網站建立 IoT 中樞](iot-hub-create-through-portal.md)。

* 您已安裝[Visual Studio 2017 或 Visual Studio 2019](https://www.visualstudio.com/vs/) 。

## <a name="get-x509-ca-certificates"></a>取得 X.509 CA 憑證

「IoT 中樞」中的 X.509 憑證型安全性會要求您從 [X.509 憑證鏈結](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)開始著手，此鏈結包含根憑證，以及向上包含任何中繼憑證，一直到分葉憑證為止。

您可以選擇下列任何方式來取得您的憑證：

* 從根憑證授權單位 (CA)** 購買 X.509 憑證。 建議在生產環境中使用此方法。

* 使用協力廠商工具（例如[OpenSSL](https://www.openssl.org/)）來建立您自己的 x.509 憑證。 這項技術適用于測試和開發目的。 如需使用 PowerShell 或 Bash 產生測試 CA 憑證的相關資訊，請參閱[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)產生的相關資訊。 本教學課程的其餘部分會使用依照[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的指示而產生的測試 CA 憑證。

* 產生以現有根 CA 憑證簽署的[x.509 中繼 CA 憑證](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust)，並將它上傳至中樞。 當中繼憑證上傳並驗證之後，如以下指示，它可以用來取代下面所述的根 CA 憑證。 OpenSSL （OpenSSL 的[需求](https://www.openssl.org/docs/man1.1.0/man1/req.html)和[OpenSSL ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)）之類的工具可以用來產生和簽署中繼 ca 憑證。

> [!NOTE]
> 請勿上傳協力廠商根，因為它不是唯一的，因為這可讓協力廠商的其他客戶將其裝置連線到您的 IoT 中樞。

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>向 IoT 中樞註冊 X.509 CA 憑證

這些步驟示範如何透過入口網站將新的「憑證授權單位」新增至您的 IoT 中樞。

1. 在 [Azure 入口網站中，流覽至您的 IoT 中樞，然後選取 [**設定** > ] 中樞的 [**憑證**]。

1. 選取 **[新增]** 以新增憑證。

1. 在 [**憑證名稱**] 中，輸入易記的顯示名稱，然後選取您在上一節中從電腦建立的憑證檔案。

1. 當您收到憑證成功上傳的通知後，請選取 [**儲存**]。

    ![Upload certificate](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   您的憑證會出現在 [憑證] 清單中，且狀態為 [未**驗證**]。

1. 選取您剛才新增來顯示**憑證詳細資料**的憑證，然後選取 [**產生驗證碼**]。

   ![驗證憑證](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. 將**驗證程式代碼**複製到剪貼簿。 您可以用它來驗證憑證擁有權。

1. 依照[管理範例和教學課程的測試 CA 憑證中的](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)步驟3進行操作。  此程式會使用與您的 x.509 CA 憑證相關聯的私密金鑰來簽署您的驗證碼，以產生簽章。 有一些工具可用來執行此簽署程序，例如 OpenSSL。 此程式稱為「[擁有權證明](https://tools.ietf.org/html/rfc5280#section-3.1)」。

1. 在 [**憑證詳細資料**] 的 [**驗證憑證. pem 或 .cer**檔案] 底下，尋找並開啟簽章檔案。 然後選取 [**驗證**]。

   憑證的狀態會變更為 [**已驗證**]。 如果**憑證**未自動更新，請選取 [重新整理]。

## <a name="create-an-x509-device-for-your-iot-hub"></a>建立 IoT 中樞的 X.509 裝置

1. 在 [Azure 入口網站中，流覽至您的 IoT 中樞，然後**選取** > [流覽] [**iot 裝置**]。

1. 選取 [**新增**] 來新增裝置。

1. 在 [**裝置識別碼**] 中，輸入易記的顯示名稱。 在 [**驗證類型**] 中，選擇 [ **X.509 CA 已簽署**]，然後選取 [**儲存**]。

   ![在入口網站中建立 X.509 裝置](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>使用 X.509 憑證驗證您的 X.509 裝置

若要驗證您的 X.509 裝置，您必須先使用 CA 憑證簽署裝置。 分葉裝置的簽章通常會在製造工廠完成，並在此據以啟用製造工具。 當裝置進入另一個製造商時，每個製造商的簽署動作都會被視為連鎖內的中繼憑證。 結果是從 CA 憑證到裝置分葉憑證的憑證鏈。 [管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步驟 4 會產生裝置憑證。

接下來，我們將示範如何建立 C# 應用程式來模擬為您 IoT 中樞註冊的 X.509 裝置。 我們會將氣溫和溼度值從模擬裝置傳送至您的中樞。 在本教學課程中，我們只會建立裝置應用程式。 它會保留給讀者作為練習建立 IoT 中樞服務應用程式，將回應傳送給此模擬裝置所傳送的事件。 C# 應用程式假設您已依照[管理用於範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步驟操作。

1. 開啟 Visual Studio，選取 [**建立新專案**]，然後選擇 [**主控台應用程式（.NET Framework）** ] 專案範本。 選取 [下一步]  。

1. 在 [**設定您的新專案**] 中，將專案命名為*SimulateX509Device*，然後選取 [**建立**]。

   ![在 Visual Studio 中建立 X.509 裝置專案](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 在方案總管中，以滑鼠右鍵按一下**SimulateX509Device**專案，然後選取 [**管理 NuGet 套件**]。

1. 在**NuGet 套件管理員**中，選取 **[流覽]** 並搜尋並選擇 [ **Microsoft. Azure. 用戶端**]。 選取 [安裝]  。

   ![在 Visual Studio 中新增裝置 SDK NuGet 套件](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    此步驟會下載及安裝 Azure IoT 裝置 SDK NuGet 套件及其相依專案，並新增對它的參考。

1. 在 Program.cs 檔案`using`的頂端新增下列語句： **Program.cs**

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. 將下欄欄位新增至**Program**類別：

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    使用您在上一節中使用的易記裝置名稱來取代 _<your_device_id>_。

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

1. 最後，將下列幾行程式碼新增至**Main**函式，並根據您的設定來取代預留位置_裝置識別碼_、_您的 iot 中樞名稱_和_絕對路徑到裝置的 pfx_檔案。

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

1. 執行應用程式。 因為此應用程式會存取 *.pfx*檔案，所以您可能需要以系統管理員身分執行此應用程式。

   1. 建置 Visual Studio 解決方案。

   1. 使用 [以**系統管理員身分執行**] 開啟新的命令提示字元視窗。  

   1. 流覽至包含您方案的資料夾，然後流覽至 [方案] 資料夾內的*bin/Debug*路徑。

   1. 從命令提示字元執行應用程式**SimulateX509Device** 。

   您應該會看到裝置成功連線至中樞並傳送事件。

   ![執行裝置應用程式](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何保護您的 IoT 解決方案，請參閱︰

* [IoT 安全性最佳做法](../iot-fundamentals/iot-security-best-practices.md)

* [IoT 安全性架構](../iot-fundamentals/iot-security-architecture.md)

* [保護您的 IoT 部署](../iot-fundamentals/iot-security-deployment.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
