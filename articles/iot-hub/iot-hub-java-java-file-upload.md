---
title: 將裝置中的檔案上傳至使用 Java 的 Azure IoT 中樞 | Microsoft Docs
description: 如何使用適用於 Java 的 Azure IoT 裝置 SDK 將檔案從裝置上傳至雲端。 上傳的檔案會儲存在 Azure 儲存體 blob 容器中。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 38816b616179ec16e06e987f82b498085c34ba3f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327577"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>使用 IoT 中樞將檔案從裝置上傳至雲端 (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教學課程是以[使用 IoT 中樞傳送雲端到裝置訊息](iot-hub-java-java-c2d.md)教學課程中的程式碼來建置，其示範如何使用 [IoT 中樞的檔案上傳功能](iot-hub-devguide-file-upload.md)將檔案上傳至 [Azure Blob 儲存體](../storage/index.yml)。 本教學課程說明如何：

* 安全地將 Azure Blob URI 提供給裝置，以便上傳檔案。

* 您可以使用 IoT 中樞檔案上傳通知來觸發在您的應用程式後端中處理此檔案。

[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-java.md)快速入門和[使用 IoT 中樞傳送雲端到裝置訊息](iot-hub-java-java-c2d.md)教學課程，會示範 IoT 中樞的基本裝置到雲端和雲端到裝置傳訊功能。 [使用 IoT 中樞設定訊息路由](tutorial-routing.md)教學課程說明了能在 Azure Blob 儲存體中，可靠地儲存裝置到雲端訊息的方法。 不過，在某些情況下，您無法輕易地將裝置傳送的資料對應到 IoT 中樞接受且相對較小的裝置到雲端訊息。 例如：

* 包含映像的大型檔案
* 影片
* 取樣高頻率的震動資料
* 某種經前置處理過的資料。

這些檔案通常會使用工具 (例如 [Azure Data Factory](../data-factory/introduction.md) 或 [Hadoop](../hdinsight/index.yml) 堆疊) 在雲端中進行批次處理。 當您需要從裝置上傳檔案時，您仍然可以使用安全可靠的 IoT 中樞。

在本教學課程結尾，您將執行兩個 Java 主控台應用程式：

* **simulated-device** 是在 [使用 IoT 中樞傳送雲端到裝置訊息] 教學課程內建立的應用程式修改版本。 此應用程式可以使用 IoT 中樞提供的 SAS URI，將檔案上傳到儲存體。

* **read-file-upload-notification**，它會接收來自 IoT 中樞的檔案上傳通知。

> [!NOTE]
> IoT 中樞透過 Azure IoT 裝置 SDK 來支援許多裝置平台和語言 (包括 C、.NET 及 Javascript)。 如需如何將您的裝置連接到 Azure IoT 中樞的逐步指示，請參閱 [Azure IoT 開發人員中心](https://azure.microsoft.com/develop/iot)。

## <a name="prerequisites"></a>必要條件

* [Java SE 開發套件 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。 請務必選取 [長期支援] 下的 [Java 8]，以取得 JDK 8 的下載。

* [Maven 3](https://maven.apache.org/download.cgi)

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>從裝置應用程式上傳檔案

在本節中，您會修改在[使用 IoT 中樞傳送雲端到裝置訊息](iot-hub-java-java-c2d.md)內建立的裝置應用程式，以將檔案上傳到 IoT 中樞。

1. 將映像檔複製到 `simulated-device` 資料夾，並重新命名為 `myimage.png`。

2. 使用文字編輯器開啟 `simulated-device\src\main\java\com\mycompany\app\App.java` 檔案。

3. 將變數宣告新增至**應用程式**類別：

    ```java
    private static String fileName = "myimage.png";
    ```

4. 若要處理檔案上傳狀態回呼訊息，請在**應用程式**類別中新增下列巢狀類別：

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. 若要將映像上傳到 IoT 中樞，請在**應用程式**類別中新增下列方法，將映像上傳到 IoT 中樞：

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. 修改 **Main** 方法以呼叫 **uploadFile** 方法，如下列程式碼片段所示：

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. 使用下列命令建置 **simulated-device** 應用程式並檢查錯誤：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中，您會建立後端服務，從在[將遙測從裝置傳送至 IoT 中樞](quickstart-send-telemetry-java.md)內建立的 IoT 中樞來接收檔案上傳通知訊息。 若要接收檔案上傳通知訊息，則服務需要**服務連線**權限。 根據預設，每個 IoT 中樞都是透過授與此權限且名為**服務**的共用存取原則所建立。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收檔案上傳通知

在本節中，您要建立一個 Java 主控台應用程式，接收來自 IoT 中樞的檔案上傳通知訊息。

1. 在命令提示字元中使用下列命令，建立名為 **read-file-upload-notification** 的 Maven 專案。 注意，此命令是單一且非常長的命令：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示字元中，巡覽至新的 `read-file-upload-notification` 資料夾。

3. 使用文字編輯器，開啟 `read-file-upload-notification` 資料夾中的 `pom.xml` 檔案，並在 [相依性] 節點中新增下列相依性。 新增相依性可讓您在應用程式中使用 **iothub-java-service-client** 套件與 IoT 中樞服務進行通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-service-client**。

4. 儲存並關閉 `pom.xml` 檔案。

5. 使用文字編輯器開啟 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 檔案。

6. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. 將下列類別層級變數新增到 **App** 類別中。 將 `{Your IoT Hub connection string}` 預留位置值取代為先前在[取得 IoT 中樞連接字串](#get-the-iot-hub-connection-string)內複製的 IoT 中樞連接字串：

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. 若要列印檔案上傳至主控台的資訊，請在**應用程式**類別中新增下列巢狀類別：

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. 若要啟動接聽檔案上傳通知的執行緒，請將下列程式碼新增至 **Main** 方法：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. 儲存並關閉 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 檔案。

11. 使用下列命令建置 **read-file-upload-notification** 應用程式並檢查錯誤：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

在 `read-file-upload-notification` 資料夾的命令提示字元中，執行下列命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

在 `simulated-device` 資料夾的命令提示字元中，執行下列命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

下列螢幕擷取畫面顯示 **simulated-device** 應用程式的輸出：

![simulated-device 應用程式的輸出](media/iot-hub-java-java-upload/simulated-device.png)

下列螢幕擷取畫面顯示 **read-file-upload-notification** 應用程式的輸出：

![read-file-upload-notification 應用程式的輸出](media/iot-hub-java-java-upload/read-file-upload-notification.png)

您可以使用入口網站檢視您所設定之儲存體容器中的上傳檔案：

![上傳的檔案](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的檔案上傳功能來簡化從裝置上傳檔案。 您可以利用下列文章繼續探索 IoT 中樞功能和案例：

* [以程式設計方式建立 IoT 中樞](iot-hub-rm-template-powershell.md)

* [C SDK 簡介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 IoT Edge 來模擬裝置](../iot-edge/tutorial-simulate-device-linux.md)
