---
title: 開始使用 Azure IoT 中樞裝置管理 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作模擬的裝置應用程式 (包含直接方法)，也可以使用適用於 Java 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110877"
---
# <a name="get-started-with-device-management-java"></a>開始使用裝置管理 (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置識別。

* 建立模擬的裝置應用程式，以實作重新啟動裝置的直接方法。 直接方法是從雲端叫用。

* 建立應用程式，以透過您的 IoT 中樞在模擬的裝置應用程式中叫用 reboot 直接方法。 此應用程式會接著監視從裝置回報的屬性，以查看何時完成重新開機作業。

在本教學課程結尾處，您會有兩個 Java 主控台應用程式：

**類比設備**。 此應用程式會：

* 使用稍早建立的裝置識別連線到您的 IoT 中樞。

* 收到 reboot 直接方法呼叫。

* 模擬實體重新開機。

* 透過回報屬性報告上次重新開機的時間。

**trigger-reboot**。 此應用程式會：

* 在模擬的裝置應用程式中呼叫直接方法。

* 顯示對模擬的裝置所傳送之直接方法呼叫的回應。

* 顯示更新的回報屬性。

> [!NOTE]
> 如需可用來建置應用程式，以在裝置與您的解決方案後端執行之 SDK 的資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="prerequisites"></a>Prerequisites

* [JAVA SE 開發工具組 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). 請務必選取 [長期支援]**** 下的 [Java 8]****，以取得 JDK 8 的下載。

* [馬文 3](https://maven.apache.org/download.cgi)

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* 請確定您的防火牆已開啟連接埠 8883。 本文中的設備示例使用 MQTT 協定，該協定通過埠 8883 進行通信。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>獲取 IoT 中心連接字串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您將建立 Java 主控台應用程式以：

1. 在模擬的裝置應用程式中叫用 reboot 直接方法。

2. 顯示回應。

3. 輪詢從裝置傳送的回報屬性，來判斷何時完成重新開機。

此主控台應用程式會連線到您的 IoT 中樞來叫用直接方法，並讀取回報屬性。

1. 創建一個稱為**dm-開始的**空資料夾。

2. 在**dm-get-開始**資料夾中，使用命令提示符處的以下命令創建稱為**觸發器重新開機**的 Maven 專案：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 在命令提示符下，導航到**觸發器重新開機**資料夾。

4. 使用文字編輯器，在**觸發器重新開機**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

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

7. 使用文字編輯器打開**觸發器重新開機\src_main_java_com_mycompany_App.java**原始檔案。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. 將以下類級變數添加到**App**類。 替換為`{youriothubconnectionstring}`以前在[獲取 IoT 中心連接字串中複製的 IoT 中心連接字串](#get-the-iot-hub-connection-string)：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. 若要實作執行緒以每隔 10 秒從裝置對應項讀取回報屬性，請將下列巢狀類別新增至 **App** 類別：

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException
    ```

12. 要在類比設備上調用重新開機直接方法，請將**主**方法中的代碼替換為以下代碼：

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. 若要啟動執行緒以從模擬的裝置輪詢回報屬性，請將下列程式碼新增至 **main** 方法：

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. 若要讓您停止應用程式，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. 保存並關閉**觸發器重新開機\src\main_java_com_mycompany_App.java**檔。

16. 建置 **trigger-reboot** 後端應用程式，並更正任何錯誤。 在命令提示符下，導航到**觸發器重新開機**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您將建立模擬裝置的 Java 主控台應用程式。 此應用程式會從您的 IoT 中樞接聽 reboot 直接方法呼叫，並立即回應該呼叫。 然後會休眠一段時間，以模擬重新開機程序，再使用回報屬性來通知 **trigger-reboot** 後端應用程式重新開機已完成。

1. 在**dm-get-開始**資料夾中，使用命令提示符處的以下命令創建稱為**類比設備的**Maven 專案：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，導航到**類比設備**資料夾。

3. 使用文字編輯器，在**類比設備**資料夾中打開**pom.xml**檔，並將以下依賴項添加到**依賴項**節點。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

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

7. 使用文字編輯器打開**類比設備\src_main_java_com_mycompany_App.java**原始檔案。

8. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. 將以下類級變數添加到**App**類。 替換為`{yourdeviceconnectionstring}`在[IoT 中心部分中註冊新設備中](#register-a-new-device-in-the-iot-hub)記錄的設備連接字串：

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. 若要實作直接方法狀態事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. 若要實作裝置對應項狀態事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. 若要實作屬性事件的回呼處理常式，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. 若要實作執行緒以模擬裝置重新開機，請將下列巢狀類別新增至 **App** 類別。 此執行緒會休眠五秒，再設定 **lastReboot** 回報屬性：

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. 若要在裝置上實作直接方法，請將下列巢狀類別新增至 **App** 類別。 當類比應用收到對**重新開機**直接方法的調用時，它將確認返回給調用方，然後啟動執行緒來處理重新開機：

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. 要具現化**DeviceClient，** 請將**主**方法中的代碼替換為以下代碼：

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. 若要開始接聽直接方法呼叫，請將下列程式碼新增至 **main** 方法：

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. 若要關閉裝置模擬器，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

20. 建置 **simulated-device** 應用程式，並更正所有錯誤。 在命令提示符下，導航到**類比設備**資料夾並運行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>執行應用程式

現在，您可以運行這些應用。

1. 在**類比設備**資料夾中的命令提示符下，運行以下命令以開始偵聽來自 IoT 中心的重新開機方法調用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![會接聽 reboot 直接方法呼叫的 Java IoT 中樞模擬裝置應用程式](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. 在**觸發器重新開機**資料夾中的命令提示符下，運行以下命令，從 IoT 中心調用類比設備上的重新開機方法：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![會呼叫 reboot 直接方法的 Java IoT 中樞服務應用程式](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. 會回應 reboot 直接方法呼叫的模擬裝置：

    ![Java IoT 中樞模擬裝置會回應直接方法呼叫](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
