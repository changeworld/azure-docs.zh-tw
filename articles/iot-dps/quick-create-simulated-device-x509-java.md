---
title: 使用 Java 將模擬 X.509 裝置佈建到 Azure IoT 中樞
description: Azure 快速入門 - 使用適用於 IoT 中樞裝置佈建服務 (DPS) 的 Java 裝置 SDK 來建立及佈建模擬 X.509 裝置。本快速入門使用個別註冊。
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 5122726cbda2145d190e3dbeb10a3c5b4ae65947
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82588437"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>快速入門：使用適用於 IoT 中樞裝置佈建服務的 Java 裝置 SDK 來建立及佈建模擬 X.509 裝置

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

在本快速入門中，您會在 Windows 電腦上建立模擬的 X.509 裝置。 您可以使用裝置範例 Java 程式碼，使用以裝置佈建服務 (DPS) 所進行的個別註冊，讓此模擬裝置與 IoT 中樞進行連線。

## <a name="prerequisites"></a>Prerequisites

- 檢閱[自動佈建概念](concepts-auto-provisioning.md)。
- 完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)。
- 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Java SE 開發套件 8](https://aka.ms/azure-jdks)。
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/)。

## <a name="prepare-the-environment"></a>準備環境 

1. 請確定您已在電腦上安裝 [Java SE 開發套件 8](https://aka.ms/azure-jdks)。

2. 下載並安裝 [Maven](https://maven.apache.org/install.html)。

3. 確定 Git 已安裝在機器上，並已新增至命令視窗可存取的環境變數。 請參閱[軟體自由保護協會的 Git 用戶端工具](https://git-scm.com/download/)以取得所要安裝的最新版 `git` 工具，其中包括 **Git Bash** (您可用來與本機 Git 存放庫互動的命令列應用程式)。 

4. 開啟命令提示字元。 複製裝置模擬程式碼範例的 GitHub 存放庫：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. 瀏覽至 `azure-iot-sdk-`java` 根目錄並建置專案，以下載所需的所有套件。
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. 瀏覽至憑證產生器專案並建置專案。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>建立自我簽署的 X.509 裝置憑證和個別的註冊項目

在本節中，您會使用自我簽署 X.509 憑證，請務必記住下列事項：

* 自我簽署憑證僅適用於測試，不應該用於生產環境。
* 自我簽署憑證的預設到期日為一年。

您會使用[適用於 Java 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java.git) 中的範例程式碼建立憑證，以便與模擬裝置的個別註冊項目搭配使用。

Azure IoT 裝置佈建服務支援兩種類型的註冊：

- [註冊群組](concepts-service.md#enrollment-group)：用來註冊多個相關的裝置。
- [個別註冊](concepts-service.md#individual-enrollment)：用來註冊單一裝置。

本文會示範個別註冊。

1. 使用先前步驟中的命令提示字元，瀏覽至 `target` 資料夾，然後執行先前步驟中建立的 .jar 檔案。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. 針對_您是否要輸入一般名稱_，輸入 **N**。 將從 ---BEGIN CERTIFICATE---  開始到 *-----END CERTIFICATE-----* 的 `Client Cert` 輸出複製到剪貼簿。

   ![個別憑證產生器](./media/java-quick-create-simulated-device-x509/individual.png)

3. 在 Windows 電腦上建立名為 _X509individual.pem_  的檔案，在您選擇的編輯器中開啟該檔案，並將剪貼簿內容複製到這個檔案。 儲存檔案並關閉編輯器。

4. 在命令提示字元中，針對_您是否要輸入驗證碼_，輸入 **N**，並保持程式輸出開啟，以供稍後在快速入門中參考。 稍後您需複製 `Client Cert` 和 `Client Cert Private Key` 值，以便在下一節中使用。

5. 登入 [Azure 入口網站](https://portal.azure.com)，選取左側功能表上的 [所有資源]  按鈕，然後開啟您的裝置佈建服務執行個體。

6. 從 [裝置佈建服務] 功能表中，選取 [管理註冊]  。 選取 [個別註冊]  索引標籤，然後選取頂端的 [新增個別註冊]  按鈕。 

7. 在 [新增註冊]  面板中，輸入下列資訊：
   - 選取 [X.509]  作為身分識別證明「機制」  。
   - 在 [主要 .pem 或 .cer 憑證檔案]  之下選擇 [選取檔案]  ，以選取在先前步驟中建立的憑證檔案 **X509individual.pem**。  
   - 您可以選擇性地提供下列資訊：
     - 選取與您的佈建服務連結的 IoT 中樞。
     - 輸入唯一的裝置識別碼。 替您的裝置命名時，務必避免使用敏感性資料。 
     - 使用裝置所需的初始組態更新**初始裝置對應項狀態**。
     - 完成後，按 [儲存]  按鈕。 

     [![在入口網站中新增 X.509 證明的個別註冊](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     註冊成功時，您的 X.509 裝置會在 [個別註冊]  索引標籤之下的 [註冊識別碼]  資料行中顯示為 **microsoftriotcore**。 



## <a name="simulate-the-device"></a>模擬裝置

1. 從 [裝置佈建服務] 功能表中，選取 [概觀]  ，並記下您的 [識別碼範圍]  和 [佈建服務全域端點]  。

    ![服務資訊](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. 開啟命令提示字元。 瀏覽至 Java SDK 存放庫的範例專案資料夾。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. 在程式碼中輸入佈建服務和 X.509 身分識別資訊。 這會在為模擬裝置的證明進行自動佈建時使用 (進行裝置註冊之前)：

   - 編輯 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` 檔案，使其包含您前面記下的_識別碼範圍_和_佈建服務全域端點_。 另外如上一節所述，也要包含_用戶端憑證_和_用戶端憑證私密金鑰_。

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - 複製/貼上您的憑證和私密金鑰時，請使用下列格式：
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. 建置範例。 瀏覽至 `target` 資料夾並執行建立的 .jar 檔案。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. 在 Azure 入口網站中，瀏覽到連結至您佈建服務的 IoT 中樞，並開啟 [Device Explorer]  刀鋒視窗。 X.509 模擬裝置成功佈建到 IoT 中樞時，其裝置識別碼會出現在 [Device Explorer]  刀鋒視窗上，且 [狀態]  顯示為 [已啟用]  。  如果您在執行範例裝置應用程式之前開啟了刀鋒視窗，可能需要按頂端的 [重新整理]  按鈕。 

    ![已向 IoT 中樞註冊裝置](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> 如果您在裝置的註冊項目中變更了*初始裝置對應項狀態* (變更自預設值)，它即可從中樞提取所需的對應項狀態並採取適當的動作。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用並探索裝置用戶端範例，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 在您的電腦上關閉裝置用戶端範例輸出視窗。
2. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的裝置佈建服務。 開啟您服務的 [管理註冊]  刀鋒視窗，然後選取 [個別註冊]  索引標籤。選取您在本快速入門中所註冊裝置的 [註冊識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。 
3. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的 IoT 中樞。 開啟您中樞的 [IoT 裝置]  刀鋒視窗，選取您在本快速入門所註冊裝置的 [裝置識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。


## <a name="next-steps"></a>後續步驟

本快速入門中，您已在 Windows 機器上建立 X.509 模擬裝置。 並在您的 Azure IoT 中樞裝置佈建服務中設定其註冊，然後將裝置自動佈建到您的 IoT 中樞。 若要了解如何以程式設計方式註冊您的 X.509 裝置，請繼續閱讀以程式設計方式註冊 X.509 裝置的快速入門。 

> [!div class="nextstepaction"]
> [Azure 快速入門 - 向 Azure IoT 中樞裝置佈建服務註冊 X.509 裝置](quick-enroll-device-x509-java.md)
