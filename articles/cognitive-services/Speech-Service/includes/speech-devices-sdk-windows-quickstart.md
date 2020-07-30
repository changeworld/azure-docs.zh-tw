---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 259fc503b0de6c05196a7cb8096f828080705be8
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375382"
---
在本快速入門中，您將了解如何使用適用於 Windows 的語音裝置 SDK 建置具備語音功能的產品，或使用它作為[交談轉譯](../conversation-transcription-service.md)裝置。 針對對話轉譯，只支援 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)。 針對其他語音，支援使用提供麥克風陣列幾何的線性麥克風陣列。

此應用程式是使用語音 SDK 套件，以及 64 位元 Windows 上的 Eclipse Java IDE (v4.8) 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

本指南需要 [Azure 認知服務帳戶](../get-started.md)和語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

[範例應用程式](https://aka.ms/sdsdk-download-JRE)的原始程式碼包含在語音裝置 SDK 中。 您也可以[在 GitHub 上取得](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)該原始程式碼。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* 作業系統：64 位元 Windows
* 麥克風陣列，例如 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)。
* [Eclipse Java IDE](https://www.eclipse.org/downloads/) \(英文\)
* 僅限 [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](../get-started.md)。
* 下載適用於 Java 的最新版[語音裝置 SDK](https://aka.ms/sdsdk-download-JRE)，並將 .zip 擷取至您的工作目錄。
   > [!NOTE]
   > 本快速入門會假設應用程式已解壓縮至 C:\SDSDK\JRE-Sample-Release

交談轉譯目前只適用於 “centralus” 和 “eastasia” 區域中的 "en-US" 和 "zh-CN"。 您在其中一個區域中必須具有語音金鑰，才能使用交談轉譯。

如果您打算使用意圖，您需要 [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 訂用帳戶。 若要深入了解 LUIS 和意圖辨識，請參閱[使用 LUIS (C#) 辨識語音意圖](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。 [範例 LUIS 模型](https://aka.ms/sdsdk-luis)適用於此應用程式。

## <a name="create-and-configure-the-project"></a>建立和設定報告

1. 啟動 Eclipse。

1. 在 [Eclipse Launcher] 中，於 [工作區] 欄位中輸入新工作區目錄的名稱。 然後選取 [啟動]。

   ![Eclipse Launcher 的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-launcher.png)

1. 不久之後，Eclipse IDE 的主要視窗隨即出現。 如果出現 [歡迎使用] 畫面，請加以關閉。

1. 從 Eclipse 功能表列中，選擇 [檔案] > [新增] > [Java 專案] 以建立新專案。 如果無法使用，請選擇 [專案]，然後選擇 [Java 專案]。

1. [新增 Java 專案] 精靈隨即啟動。 [瀏覽] 範例專案的位置。 選取 [完成]。

   ![[新增 Java 專案] 精靈的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. 在 [套件總管] 中，以滑鼠右鍵按一下您的專案。 從操作功能表中選擇 [設定] > [轉換成 Maven 專案]。 選取 [完成]。

   ![套件總管的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. 開啟 pom.xml 檔案並加以編輯。

    在檔案結尾的結尾標記 `</project>` 前面，建立 `repositories` 和 `dependencies` 元素，並確定 `version` 符合您目前的版本：
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.13.0</version>
        </dependency>
    </dependencies>
   ```

1. 將 **Windows-x64** 的內容複製到 Java 專案位置，例如 **C:\SDSDK\JRE-Sample-Release**

1. 將`kws.table`、`participants.properties` 及 `Microsoft.CognitiveServices.Speech.extension.pma.dll`複製到專案資料夾 **target\classes**

## <a name="configure-the-sample-application"></a>設定範例應用程式

1. 將您的語音訂用帳戶金鑰加入至原始程式碼中。 如果您想要試用意圖辨識，請一併新增您的 [Language Understanding 服務](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)訂用帳戶金鑰和應用程式識別碼。

   若為語音和 LUIS，您的資訊會進入 `FunctionsList.java`：

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   如果您使用交談轉譯，`Cts.java` 中也需要有您的語音金鑰和區域資訊：

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. 預設關鍵字是「Computer」。 您也可以嘗試其中一個其他提供的關鍵字，例如 "Machine" 或 "Assistant"。 這些替代關鍵字的資源檔位於語音裝置 SDK 的 [keyword] 資料夾中。 例如，`C:\SDSDK\JRE-Sample-Release\keyword\Computer` 包含用於關鍵字「Computer」的檔案。

    > [!TIP]
    > 您也可以[建立自訂關鍵字](../speech-devices-sdk-create-kws.md)。

    若要使用新的關鍵字，請更新 `FunctionsList.java` 中的下列行，並將關鍵字複製到您的應用程式。 例如，若要使用來自關鍵字套件 `machine.zip` 的關鍵字「Machine」：

   * 將 `kws.table` 檔案從 zip 套件複製到專案資料夾 **target/classes**。
   * 使用關鍵字名稱更新 `FunctionsList.java`：

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>從 Eclipse 執行範例應用程式

1. 從 Eclipse 功能表列，選取 [執行] > [執行身分] > [Java 應用程式]。 然後選取 [FunctionsList] 和 [確定]。

   ![選取 Java 應用程式的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-run-sample.png)

1. 「語音裝置 SDK」範例應用程式會啟動，並顯示下列選項：

   ![「語音裝置 SDK」範例應用程式和選項](../media/speech-devices-sdk/java-sample-app-windows.png)

1. 嘗試新的 [交談轉譯] 示範。 透過 [工作階段] > [啟動] 開始轉譯。 根據預設，每個人都是來賓。 不過，如果您有參與者的語音簽章，可以將其放入專案資料夾**target/classes**的 `participants.properties` 檔案中。 若要產生語音簽章，請查看[轉譯交談 (SDK)](../how-to-use-conversation-transcription-service.md)。

   ![示範交談轉譯應用程式](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>建立及執行獨立應用程式

1. 在 [套件總管] 中，以滑鼠右鍵按一下您的專案。 選擇 [匯出]。

1. [匯出] 視窗隨即出現。 展開 [Java]，然後選取 [可執行的 JAR 檔案]，然後選取 [下一步]。

   ![[匯出] 視窗的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-export-windows.png)

1. [可執行的 JAR 檔案匯出] 視窗隨即出現。 選擇應用程式的 [匯出目的地]，然後選取 [完成]。

   ![可執行的 JAR 檔案匯出的螢幕擷取畫面](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. 請將 `kws.table`、`participants.properties`、`unimic_runtime.dll`、`pma.dll` 和 `Microsoft.CognitiveServices.Speech.extension.pma.dll` 放入上面所選的目的地資料夾，因為應用程式需要這些檔案。

1. 執行獨立應用程式

   ```powershell
   java -jar SpeechDemo.jar
   ```
