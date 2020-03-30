---
title: 使用 Azure IoT 中樞 (Java) 排定作業 | Microsoft Docs
description: 如何排定 Azure IoT 中樞作業在多個裝置上叫用直接方法和設定所需屬性。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式，也可以使用適用於 Java 的 Azure IoT 服務 SDK，實作服務應用程式以執行作業。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 9227192b2f7c554943fb3716ba1d1066f814c447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110331"
---
# <a name="schedule-and-broadcast-jobs-java"></a>排定及廣播作業 (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中樞排程和追蹤會更新數百萬部裝置的作業。 使用作業以：

* 更新所需屬性
* 更新標籤
* 叫用直接方法

作業會包裝上述其中一個動作，然後針對一組裝置追蹤執行進度。 裝置對應項查詢會定義對其執行作業的一組裝置。 例如，後端應用程式可以使用作業，以在 10,000 部裝置上叫用重新開機裝置的直接方法。 您以裝置對應項查詢指定一組裝置，並排程在未來的時間執行作業。 作業會在每部裝置接收和執行重新開機直接方法時追蹤進度。

若要一一了解這些功能，請參閱：

* 裝置對應項和屬性：[開始使用裝置對應項](iot-hub-java-java-twin-getstarted.md)

* 直接方法：[IoT 中樞開發人員指南 - 直接方法](iot-hub-devguide-direct-methods.md)和[教學課程：使用直接方法](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程說明如何：

* 建立實作 **lockDoor** 直接方法的裝置應用程式。 裝置應用程式也會從後端應用程式收到所需的屬性變更。

* 建立後端應用程式，以建立作業在多部裝置上呼叫 **lockDoor** 直接方法。 另一項作業會將所需的屬性更新傳送到多部裝置。

在本教學課程結束時，您會有 Java 主控台裝置應用程式和 Java 主控台後端應用程式：

**simulated-device** 會連線至 IoT 中樞，實作 **lockDoor** 直接方法，並處理所需的屬性變更。

**計畫作業**，使用作業調用**lockDoor**直接方法，並在多個設備上更新設備孿生所需屬性。

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

您也可以使用[適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\) 工具將裝置新增至 IoT 中樞。

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您將建立 Java 主控台應用程式，使用作業以：

* 在多個裝置上呼叫 **lockDoor** 直接方法。

* 將所需屬性傳送至多個裝置。

若要建立應用程式：

1. 在開發電腦上，創建一個稱為**iot-java-計畫作業的**空資料夾。

2. 在**iot-java-計畫作業**資料夾中，使用命令提示符處的以下命令創建稱為**計畫作業**的 Maven 專案。 注意，這是一個單一且非常長的命令：

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. 在命令提示符下，導航到**計畫作業**資料夾。

4. 使用文字編輯器，在**計畫作業**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 此依賴項使您能夠在應用中使用**iot 服務用戶端**包與 IoT 中心進行通信：

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

7. 使用文字編輯器，打開**計畫作業\src_main_java_com_mycompany_App.java**檔。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. 將以下類級變數添加到**App**類。 替換為`{youriothubconnectionstring}`以前在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. 將下列方法新增至 **App** 類別，排程作業以更新裝置對應項中的 **Building** 和 **Floor** 所需屬性：

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. 若要排程作業以呼叫 **lockDoor** 方法，將下列方法新增至 **App** 類別：

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. 若要監視作業，將下列方法新增至 **App** 類別：

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. 若要查詢您執行之作業的詳細資料，請新增下列方法：

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. 更新 **Main** 方法簽章，以包含下列 `throws` 子句：

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. 要按順序運行和監視兩個作業，請將**主**方法中的代碼替換為以下代碼：

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. 保存並關閉**計畫作業\src\main_java_com_mycompany_App.java**檔

17. 建置 **schedule-jobs** 應用程式，並更正所有錯誤。 在命令提示符下，導航到**計畫作業**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>建立裝置應用程式

在本節中，您會建立 Java 主控台應用程式來處理從 IoT 中樞傳送的所需屬性，以及實作直接方法呼叫。

1. 在**iot-java-計畫作業**資料夾中，使用命令提示符處的以下命令創建稱為**類比設備的**Maven 專案。 注意，這是一個單一且非常長的命令：

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. 在命令提示符下，導航到**類比設備**資料夾。

3. 使用文字編輯器，在**類比設備**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 此依賴項使您能夠在應用中使用**iot 設備用戶端**包與 IoT 中心進行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-device-client**。

4. 將以下依賴項添加到**依賴項**節點。 此依賴項為 Apache [SLF4J](https://www.slf4j.org/)日誌記錄外觀配置 NOP，設備用戶端 SDK 用於實現日誌記錄。 此配置是可選的，但如果省略它，則在運行應用時，在主控台中可能會看到警告。 有關在設備用戶端 SDK 中登錄的詳細資訊，請參閱[在](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)JAVA 讀取檔的*Azure IoT 設備 SDK 的示例中*登錄。

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

9. 將以下類級變數添加到**App**類。 替換為`{yourdeviceconnectionstring}`以前在[IoT 中心部分中註冊新設備中](#register-a-new-device-in-the-iot-hub)複製的設備連接字串：

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    此範例應用程式在具現化 **DeviceClient** 物件時使用 **protocol** 變數。

10. 若要將裝置對應項通知列印至主控台，請在 **App** 類別中新增下列巢狀類別：

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. 若要將直接方法通知列印至主控台，請在 **App** 類別中新增下列巢狀類別：

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. 若要處理來自 IoT 中樞的直接方法呼叫，請在 **App** 類別中新增下列巢狀類別：

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. 更新 **Main** 方法簽章，以包含下列 `throws` 子句：

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. 將**主**方法中的代碼替換為以下代碼，以便：
    * 建立與 IoT 中樞通訊的裝置用戶端。
    * 建立**裝置**物件以儲存裝置對應項屬性。

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. 若要啟動裝置用戶端服務，請將下列程式碼新增至 **main** 方法：

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. 若要在關閉之前等候使用者按下 **Enter** 鍵，請將下列程式碼新增至 **main** 方法的結尾：

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. 保存並關閉**類比設備\src\main_java_com_mycompany_App.java**檔。

18. 建置 **simulated-device** 應用程式，並更正所有錯誤。 在命令提示符下，導航到**類比設備**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>執行應用程式

您現在已經準備好執行主控台應用程式。

1. 在**類比設備**資料夾中的命令提示符下，運行以下命令以啟動設備應用以偵聽所需的屬性更改和直接方法調用：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![裝置用戶端啟動](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. 在 `schedule-jobs` 資料夾的命令提示字元中執行下列命令，執行 **schedule-jobs** 服務應用程式以執行兩個作業。 第一個作業會設定所需屬性值，第二個作業會呼叫直接方法：

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT 中樞服務應用程式會建立兩個作業](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. 裝置應用程式會處理所需屬性變更和直接方法呼叫：

   ![裝置用戶端會回應變更](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

使用下列資源來了解如何：

* 使用[IoT 中心](quickstart-send-telemetry-java.md)教程從設備發送遙測資料。

* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法](quickstart-control-device-java.md)教學課程
