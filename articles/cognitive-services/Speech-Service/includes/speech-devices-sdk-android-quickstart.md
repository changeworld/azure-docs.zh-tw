---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 4dbaaad4b75e6d706d77522f45a2d596c49bea79
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658341"
---
在本快速入門中，您將了解如何使用適用於 Android 的語音裝置 SDK 建置具備語音功能的產品，或使用它作為[交談轉譯](../conversation-transcription-service.md)裝置。

本指南需要 [Azure 認知服務帳戶](../get-started.md)和語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

範例應用程式的原始程式碼包含在「語音裝置 SDK」中。 您也可以[在 GitHub 上取得](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)該原始程式碼。

## <a name="prerequisites"></a>Prerequisites

開始使用語音裝置 SDK 之前，您需要：

- 請遵循[開發套件](../get-speech-devices-sdk.md)隨附的指示來開啟裝置電源。

- 下載最新版的[語音裝置 SDK](https://aka.ms/sdsdk-download)，並將 .zip 擷取至您的工作目錄。

  > [!NOTE]
  > 本快速入門會假設應用程式已解壓縮至 C:\SDSDK\Android-Sample-Release

- 取得[適用於語音服務的 Azure 訂用帳戶金鑰](../get-started.md)

- 如果您打算使用交談轉譯，則必須使用[環形麥克風裝置](../get-speech-devices-sdk.md)，這項功能目前僅適用於 “centralus” 和 “eastasia” 區域的 "en-US" 和 "zh-CN"。 您在其中一個區域中必須具有語音金鑰，才能使用交談轉譯。

- 如果您打算使用語音服務來識別使用者語句中的意圖 (或動作)，您將需要 [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 訂用帳戶。 若要深入了解 LUIS 和意圖辨識，請參閱[使用 LUIS (C#) 辨識語音意圖](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。

  您可以[建立簡單 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用簡單 LUIS 模型 LUIS-example.json。 您可以從[語音裝置 SDK 下載網站](https://aka.ms/sdsdk-luis)取得範例 LUIS 模型。 若要將您模型的 JSON 檔案上傳到 [LUIS 入口網站](https://www.luis.ai/home)，請選取 [Import new app] \(匯入新應用程式\)  ，然後選取 JSON 檔案。

- 在您的電腦上安裝 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](https://vysor.io/download/)。

## <a name="set-up-the-device"></a>設定裝置

1. 在您的電腦上啟動 Vysor。

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. 您的裝置應該會列在 [Choose a device] \(選擇裝置\)  底下。 選取裝置旁邊的 [View] \(檢視\)  按鈕。

1. 選取資料夾圖示，然後選取 [Settings] \(設定\)   > [WLAN]  ，以連線到您的無線網路。

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > 如果貴公司有關於將裝置連線到 Wi-Fi 系統的政策，您就必須取得 Mac 位址並連絡 IT 部門，以了解如何將其連線到公司的 Wi-Fi 系統。
   >
   > 若要尋找開發套件的 MAC 位址，請選取開發套件桌面上的檔案資料夾圖示。
   >
   > ![Vysor 檔案資料夾](../media/speech-devices-sdk/qsg-10.png)
   >
   > 選取 [Settings] \(設定)  。 搜尋 "mac address"，然後選取 [Mac address] \(Mac 位址\)   > [Advanced WLAN] \(進階 WLAN\)  。 記下出現在靠近對話方塊底部的 MAC 位址。
   >
   > ![Vysor MAC 位址](../media/speech-devices-sdk/qsg-11.png)
   >
   > 有些公司針對裝置可連線到其 Wi-Fi 系統的時間長度可能有限制。 您可能需要在特定天數之後，將開發套件的 Wi-Fi 系統註冊延長。

## <a name="run-the-sample-application"></a>執行範例應用程式

若要驗證您的開發套件設定，請建置並安裝範例應用程式：

1. 啟動 Android Studio。

1. 選取 [開啟現有 Android Studio 專案]  。

   ![Android Studio - 開啟現有專案](../media/speech-devices-sdk/qsg-5.png)

1. 前往 C:\SDSDK\Android-Sample-Release\example。 選取 [OK] \(確定\)  以開啟範例專案。

1. 將 Gradle 設定為參考語音 SDK。 您可以在 Android Studio 中的 **Gradle 指令碼**下找到下列檔案。

    藉由新增 maven 行來更新 **build.gradle(Project:example)** (allprojects 區塊應符合下列內容)。

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    將這一行新增至 [相依性] 區段，以更新 **build.gradle(Module:app)** 。 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
    ```
    
1. 將您的語音訂用帳戶金鑰加入至原始程式碼中。 如果您想要試用意圖辨識，請一併新增您的 [Language Understanding 服務](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)訂用帳戶金鑰和應用程式識別碼。

   若為語音和 LUIS，您的資訊會進入 MainActivity.java：

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   如果您使用交談轉譯，conversation.java 中也需要有您的語音金鑰和區域資訊：

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. 預設關鍵字是 "Computer"。 您也可以嘗試其中一個其他提供的關鍵字，例如 "Machine" 或 "Assistant"。 這些替代關鍵字的資源檔位於語音裝置 SDK 的 [keyword] 資料夾中。 例如 C:\SDSDK\Android-Sample-Release\keyword\Computer 便包含用於 "Computer" 關鍵字的檔案。

   > [!TIP]
   > 您也可以[建立自訂關鍵字](../speech-devices-sdk-create-kws.md)。

   若要使用新的關鍵字，請更新 `MainActivity.java` 中的下列兩行，並將關鍵字套件複製到您的應用程式。 例如，若要使用來自關鍵字套件 kws-machine.zip 的關鍵字 'Machine'：

   - 將關鍵字套件複製到 “C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\” 資料夾中。
   - 以關鍵字和套件名稱更新 `MainActivity.java`：

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 更新包含麥克風陣列幾何設定的下列各行：

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   下表列出支援的值：

   | 變數 | 意義 | 可用值 |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | 實體麥克風設定 | 針對圓形裝置套件：`Circular6+1` |
   |          |         | 針對線性裝置套件：`Linear4` |
   | `SelectedGeometry` | 軟體麥克風設定 | 針對使用所有麥克風的圓形開發套件：`Circular6+1` |
   |          |         | 針對使用四個麥克風的圓形開發套件：`Circular3+1` |
   |          |         | 針對使用所有麥克風的線性開發套件：`Linear4` |
   |          |         | 針對使用兩個麥克風的線性開發套件：`Linear2` |

1. 若要建置應用程式，請在 [Run] \(執行\)  功能表上選擇 [Run 'app'] \(執行「應用程式」\)  。 隨即會顯示 [Select Deployment Target] \(選取部署目標\)  對話方塊。

1. 選取您的裝置，然後選取 [OK] \(確定\)  ，以將應用程式部署到裝置。

   ![[Select Deployment Target] \(選取部署目標\) 對話方塊](../media/speech-devices-sdk/qsg-7.png)

1. 「語音裝置 SDK」範例應用程式會啟動，並顯示下列選項：

   ![「語音裝置 SDK」範例應用程式和選項](../media/speech-devices-sdk/qsg-8.png)

1. 嘗試新的交談轉譯示範。 透過 [啟動工作階段] 開始轉譯。 根據預設，每個人都是來賓。 不過，如果您有參與者的語音簽章，可以將其放入裝置上的 `/video/participants.properties` 檔案中。 若要產生語音簽章，請查看[轉譯交談 (SDK)](../how-to-use-conversation-transcription-service.md)。

   ![示範交談轉譯應用程式](../media/speech-devices-sdk/qsg-15.png)

1. 進行實驗！

## <a name="troubleshooting"></a>疑難排解

如果您無法連線到語音裝置。 在命令提示字元視窗中輸入下列命令。 這會傳回裝置清單：

```powershell
 adb devices
```

> [!NOTE]
> 此命令會使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安裝的一部分。 此工具位於 C:\Users\[使用者名稱]\AppData\Local\Android\Sdk\platform-tools。 您可以將此目錄新增至您的路徑，以便更方便地叫用 `adb`。 否則，您必須在叫用 `adb` 的每個命令中指定 adb.exe 的完整安裝路徑。
>
> 如果您看到 `no devices/emulators found` 錯誤，請確認您已接好 USB 纜線並確保使用高品質的纜線。
