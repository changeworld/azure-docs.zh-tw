---
title: 開始使用 Azure IoT 中樞裝置對應項 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來新增標籤，然後使用 IoT 中樞查詢。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作裝置應用程式，也可以使用適用於 Java 的 Azure IoT 服務 SDK，實作服務應用程式，以新增標籤和執行 IoT 中樞查詢。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e0114c37b2204a7ad1d7b0cf9c7f336dcd85883a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110501"
---
# <a name="get-started-with-device-twins-java"></a>開始使用裝置對應項 (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程中，您將建立兩個 Java 主控台應用程式：

* **add-tags-query**，可新增標籤和查詢裝置對應項的 Java 後端應用程式。
* **simulated-device**，Java 裝置應用程式，可連線到 IoT 中樞，並使用回報屬性報告其連線狀況。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。

## <a name="prerequisites"></a>Prerequisites

* [JAVA SE 開發工具組 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). 請務必選取 [長期支援]**** 下的 [Java 8]****，以取得 JDK 8 的下載。

* [馬文 3](https://maven.apache.org/download.cgi)

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您要建立一個 Java 應用程式，將位置中繼資料當成標籤新增至 IoT 中樞中與 **myDeviceId** 建立關聯的裝置對應項。 應用程式會先查詢 IoT 中樞是否有位於美國的裝置，再查詢是否有回報行動電話通訊網路連線的裝置。

1. 在開發電腦上，創建一個名為**iot-java-twin 啟動的**空資料夾。

2. 在**iot-java-twin-get 啟動**資料夾中，使用命令提示符處的以下命令創建名為**add-tag 查詢**的 Maven 專案：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 在命令提示符下，導航到**委任標記查詢**資料夾。

4. 使用文字編輯器，在**add-tags-查詢**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 此依賴項使您能夠在應用中使用**iot 服務用戶端**包與 IoT 中心進行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-service-client**。

5. 將下列 [建置]**** 節點新增至 [相依性]**** 節點之後。 此配置指示 Maven 使用 JAVA 1.8 構建應用程式。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 保存並關閉**pom.xml**檔。

7. 使用文字編輯器打開**委任標記查詢\src\main_java_com_mycompany_App.java**檔。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. 將以下類級變數添加到**App**類。 替換為`{youriothubconnectionstring}`在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. 更新 **Main** 方法簽章，以包含下列 `throws` 子句：

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. 將**主**方法中的代碼替換為以下代碼，以創建**DeviceTwin**和設備**TwinDevice**物件。 **DeviceTwin** 物件會處理與 IoT 中樞的通訊。 **DeviceTwinDevice** 物件會以其屬性和標籤代表裝置對應項：

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. 將以下 `try/catch` 區塊新增至 **Main** 方法：

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. 若要更新裝置對應項的**區域**和**工廠**裝置對應項標籤，請在 `try` 區塊中新增下列程式碼：

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. 若要在 IoT 中樞中查詢裝置對應項，請將下列程式碼新增至 `try` 區塊，此區塊在上個步驟所新增的程式碼後面。 程式碼執行兩個查詢。 每個查詢最多返回 100 個設備。

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. 保存並關閉**附加標記查詢\src\main_java_com_mycompany_App.java**檔

16. 建置 **add-tags-query** 應用程式，並更正所有錯誤。 在命令提示符下，導航到**委任標記查詢**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>建立裝置應用程式

在本節中，您要建立 Java 主控台應用程式，以設定傳送到 IoT 中樞的回報屬性值。

1. 在**iot-java-twin-get 啟動**資料夾中，使用命令提示符處的以下命令創建名為**類比設備的**Maven 專案：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，導航到**類比設備**資料夾。

3. 使用文字編輯器，在**類比設備**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 此依賴項使您能夠在應用中使用**iot 設備用戶端**包與 IoT 中心進行通信。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-device-client**。

4. 將以下依賴項添加到**依賴項**節點。 此依賴項為 Apache [SLF4J](https://www.slf4j.org/)日誌記錄外觀配置 NOP，設備用戶端 SDK 用於實現日誌記錄。 此配置是可選的，但是，如果您省略它，則在運行應用時，您可能會在主控台中看到警告。 有關在設備用戶端 SDK 中登錄的詳細資訊，請參閱[在](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)JAVA 讀取檔的*Azure IoT 設備 SDK 的示例中*登錄。

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. 將下列 [建置]**** 節點新增至 [相依性]**** 節點之後。 此設定會指示 Maven 使用 Java 1.8 來建置應用程式：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 保存並關閉**pom.xml**檔。

7. 使用文字編輯器打開**類比設備\src_main_java_com_mycompany_App.java**檔。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. 將以下類級變數添加到**App**類。 替換為`{yourdeviceconnectionstring}`在[註冊 IoT 中心中的新設備](#register-a-new-device-in-the-iot-hub)中複製的設備連接字串。

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    此範例應用程式在具現化 **DeviceClient** 物件時使用 **protocol** 變數。

10. 將下列方法新增至 **App**類別，以列印有關對應項更新的資訊：

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. 將**主**方法中的代碼替換為以下代碼，以便：

    * 建立與 IoT 中樞通訊的裝置用戶端。

    * 建立**裝置**物件以儲存裝置對應項屬性。

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. 將下列程式碼新增至 **Main** 方法，建立 **connectivityType** 回報屬性，並將它傳送到 IoT 中樞：

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. 將以下代碼添加到**主**方法的末尾。 等待**Enter**鍵允許 IoT 中心有時間報告設備孿生操作的狀態。

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. 修改 **main** 方法的簽章以新增如下所示的例外狀況：

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. 保存並關閉**類比設備\src\main_java_com_mycompany_App.java**檔。

16. 建置 **simulated-device** 應用程式，並更正所有錯誤。 在命令提示符下，導航到**類比設備**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>執行應用程式

您現在已經準備好執行主控台應用程式。

1. 在**委任標記查詢**資料夾中的命令提示符下，運行以下命令以運行**委任標記查詢**服務應用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![更新標籤值並執行裝置查詢的 Java IoT 中樞服務應用程式](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    您可以看到新增至裝置對應項的**工廠**和**區域**標籤。 第一個查詢會傳回您的裝置，但第二個則不然。

2. 在**類比設備**資料夾中的命令提示符下，運行以下命令以將**連線類型**報告的屬性添加到設備孿生：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![裝置用戶端會新增 **connectivityType** 回報屬性](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. 在**委任標記查詢**資料夾中的命令提示符下，運行以下命令以第二次運行**委任標記查詢**服務應用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![更新標籤值並執行裝置查詢的 Java IoT 中樞服務應用程式](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    現在您的設備將**連線類型**屬性發送到 IoT 中心，第二個查詢將返回您的設備。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也了解如何使用類似 SQL 的 IoT 中樞查詢語言來查詢裝置對應項資訊。

使用下列資源來了解如何：

* 使用[IoT 中心](quickstart-send-telemetry-java.md)教程從設備發送遙測資料。

* 使用["使用直接方法"](quickstart-control-device-java.md)教程，以對話模式控制設備（例如從使用者控制的應用程式打開風扇）。
