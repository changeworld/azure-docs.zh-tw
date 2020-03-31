---
title: Azure IoT Central 中的裝置連線能力 | Microsoft Docs
description: 本文介紹 Azure IoT Central 裝置連線能力的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8178e585ecb7b1cdfd5e530f3d3406b7397f0968
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476044"
---
# <a name="get-connected-to-azure-iot-central"></a>連接到 Azure IoT 中心

本文介紹將設備連接到 Azure IoT 中心應用程式的選項。

通常，必須在應用程式中註冊設備，然後才能連接。 但是，IoT Central 確實支援[設備無需先註冊即可進行連接](#connect-without-registering-devices)的情況。

IoT 中心使用[Azure IoT 中心設備佈建服務 （DPS）](../../iot-dps/about-iot-dps.md)來管理連接過程。 設備首先連接到 DPS 終結點，以檢索連接到應用程式所需的資訊。 在內部，IoT 中心應用程式使用 IoT 中心來處理設備連接。 使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

為了保護設備與應用程式之間的通信，IoT Central 同時支援共用訪問簽名 （SAS） 和 X.509 憑證。 X.509 憑證建議在生產環境中使用。

本文介紹以下用例：

- [使用 SAS 連接單個設備](#connect-a-single-device)
- [使用 SAS 大規模連接設備](#connect-devices-at-scale-using-sas)
- [使用 X.509 憑證大規模連接設備](#connect-devices-using-x509-certificates)- 生產環境的建議方法。
- [無需先註冊即可連接設備](#connect-without-registering-devices)
- [連接使用 DPS 個人註冊的設備](#individual-enrollment-based-device-connectivity)
- [使用 IoT 隨插即用（預覽）功能連接設備](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>將單一裝置連線

當您嘗試 IoT 中心或測試設備時，此方法非常有用。 您可以使用 IoT 中央應用程式中的設備連接 SAS 金鑰將設備連接到 IoT 中央應用程式。 從已註冊設備的連接資訊複製_設備 SAS 金鑰_：

![單個設備的 SAS 金鑰](./media/concepts-get-connected/single-device-sas.png)

要瞭解更多資訊，請參閱創建[Node.js 用戶端應用程式並將其連接到 Azure IoT 中央應用程式](./tutorial-connect-device.md)教程。

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大規模連接設備

要使用 SAS 金鑰大規模將設備連接到 IoT 中心，您需要註冊並設置設備：

### <a name="register-devices-in-bulk"></a>批量註冊設備

要將大量設備註冊到 IoT 中央應用程式，請使用 CSV 檔[導入裝置識別碼 和設備名稱](howto-manage-devices.md#import-devices)。

要檢索匯入裝置的連接資訊，[請從 IoT 中央應用程式匯出 CSV 檔](howto-manage-devices.md#export-devices)。 匯出的 CSV 檔包括設備 ED 和 SAS 金鑰。

### <a name="set-up-your-devices"></a>設定您的裝置

使用設備代碼中匯出檔中的連接資訊，使設備能夠將資料連線到 IoT 到 IoT 中心應用程式。 您還需要應用程式的 DPS **ID 作用域**。 您可以在**管理>設備連接**中找到此值。

> [!NOTE]
> 要瞭解如何在不首先在 IoT 中央註冊設備的情況下連接設備，請參閱[在不首先註冊設備的情況下連接設備](#connect-without-registering-devices)。

## <a name="connect-devices-using-x509-certificates"></a>使用 X.509 憑證連接設備

在生產環境中，使用 X.509 憑證是 IoT Central 推薦的設備身份驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../../iot-hub/iot-hub-x509ca-overview.md)。

在使用 X.509 憑證連接設備之前，請添加和驗證應用程式中的中間或根 X.509 憑證。 設備必須使用從根憑證或中繼憑證生成的葉 X.509 憑證。

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>添加並驗證根或中繼憑證

導航到**管理>設備連接>管理主證書**並添加用於生成設備證書的 X.509 根或中繼憑證。

![連線設定](media/concepts-get-connected/manage-x509-certificate.png)

驗證憑證擁有權可確保上載證書的人員具有證書的私密金鑰。 要驗證憑證：

  1. 選擇**驗證碼**旁邊的按鈕以生成代碼。
  1. 使用上一步中生成的驗證碼創建 X.509 驗證憑證。 將證書另存為 .cer 檔。
  1. 上傳已簽名的驗證憑證並選擇 **"驗證**"。 驗證成功時，證書標記為 **"已驗證**"。

如果您有安全性漏洞或主證書設置為過期，請使用輔助證書來減少停機時間。 在更新主證書時，可以繼續使用輔助證書預配設備。

### <a name="register-and-connect-devices"></a>註冊和連接設備

要使用 X.509 憑證批量連接設備，請首先使用 CSV 檔在應用程式中註冊設備以[導入裝置識別碼 和設備名稱](howto-manage-devices.md#import-devices)。 設備指示器應都處於小寫操作中。

使用上載的根或中繼憑證為您的設備生成 X.509 葉證書。 使用**裝置識別碼**作為`CNAME`葉證書中的值。 您的設備代碼需要應用程式的**ID 範圍**值、**裝置識別碼**和相應的設備證書。

### <a name="for-testing-purposes-only"></a>僅用於測試目的

僅對於測試，可以使用以下實用程式生成根憑證、中繼憑證和設備證書：

- [Azure IoT 設備預配設備 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)：Node.js 工具的集合，可用於生成和驗證 X.509 憑證和金鑰。
- 如果您使用的是 DevKit 設備，則此[命令列工具](https://aka.ms/iotcentral-docs-dicetool)將生成一個 CA 憑證，您可以將其添加到 IoT Central 應用程式以驗證憑證。
- [管理示例和教程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)：PowerShell 和 Bash 腳本的集合，以：
  - 創建憑證連結。
  - 將證書另存為 .cer 檔，以上載到 IoT 中央應用程式。
  - 使用 IoT 中央應用程式的驗證碼生成驗證憑證。
  - 使用設備 I 作為工具的參數，為設備創建葉證書。

### <a name="further-reference"></a>進一步的參考

- [樹莓皮的樣品實現](https://aka.ms/iotcentral-docs-Raspi-releases)
- [C 中的示例設備用戶端](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>無需註冊設備即可連接

前面描述的方案都要求您在連接設備之前在應用程式中註冊設備。 IoT 中心還使 OEM 能夠大規模製造可連接而無需首次註冊的設備。 OEM 生成適當的設備憑據，並在工廠中配置設備。 當客戶首次打開設備時，它將連接到 DPS，然後 DPS 會自動將設備連接到正確的 IoT 中央應用程式。 IoT 管理中心操作員在開始向應用程式發送資料之前必須批准設備。

流略有不同，具體取決於設備是使用 SAS 權杖還是 X.509 憑證：

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>連接使用 SAS 權杖而無需註冊的設備

1. 複製 IoT 中心應用程式的組主金鑰：

    ![應用程式組主 SAS 金鑰](media/concepts-get-connected/group-sas-keys.png)

1. 使用[dps 鍵鍵生成](https://www.npmjs.com/package/dps-keygen)設備 SAS 鍵。 使用上一步的組主鍵。 裝置識別碼 必須是小寫：

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM 使用裝置識別碼、生成的設備 SAS 金鑰和應用程式**ID 範圍**值閃爍每個設備。

1. 當您打開設備時，它首先連接到 DPS 以檢索其 IoT 中央註冊資訊。

    設備最初在 **"設備"** 頁上具有 **"取消關聯**"的設備狀態，並且未分配給設備範本。 在"**設備"** 頁上，**將設備遷移到**相應的設備範本。 設備預配現已完成，設備狀態現已**預配**，設備可以開始發送資料。

    在 **"管理>設備連接**頁上，"**自動核准**"選項控制是否需要手動批准設備，然後才能開始發送資料。

    > [!NOTE]
    > 要瞭解如何自動將設備與設備範本關聯，請參閱使用[IoT 隨插即用（預覽）連接設備](#connect-devices-with-iot-plug-and-play-preview)。

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>連接使用 X.509 憑證而不註冊的設備

1. 向 IoT 中央應用程式[添加和驗證根或中間 X.509 憑證](#connect-devices-using-x509-certificates)。（#connect設備使用-x509-證書）

1. 使用添加到 IoT 中央應用程式的根或中繼憑證為設備生成葉證書。 使用小寫設備指示符作為葉證書`CNAME`中的。

1. OEM 使用裝置識別碼、生成的左 X.509 憑證和應用程式**ID 範圍**值閃爍每個設備。

1. 當您打開設備時，它首先連接到 DPS 以檢索其 IoT 中央註冊資訊。

    設備最初在 **"設備"** 頁上具有 **"取消關聯**"的設備狀態，並且未分配給設備範本。 在"**設備"** 頁上，**將設備遷移到**相應的設備範本。 設備預配現已完成，設備狀態現已**預配**，設備可以開始發送資料。

    在 **"管理>設備連接**頁上，"**自動核准**"選項控制是否需要手動批准設備，然後才能開始發送資料。

    > [!NOTE]
    > 要瞭解如何自動將設備與設備範本關聯，請參閱使用[IoT 隨插即用（預覽）連接設備](#connect-devices-with-iot-plug-and-play-preview)。

## <a name="individual-enrollment-based-device-connectivity"></a>基於註冊的個人設備連接

對於連接每個設備都有自己的身份驗證憑據的客戶，請使用各個註冊。 單個註冊是允許連接的單個設備的條目。 單個註冊可以使用 X.509 葉證書或 SAS 權杖（來自物理或虛擬受信任的平臺模組）作為證明機制。 單個註冊中的裝置識別碼（也稱為註冊 ID）是字母數位、小寫，可能包含連字號。 有關詳細資訊，請參閱[DPS 個人註冊](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)。

> [!NOTE]
> 為設備創建單個註冊時，它優先于 IoT 中央應用程式中的預設組註冊選項。

### <a name="creating-individual-enrollments"></a>創建個人註冊

IoT 中心支援以下個人註冊的證明機制：

- **對稱金鑰證明：** 對稱金鑰證明是使用 DPS 實例對設備進行身份驗證的簡單方法。 要創建使用對稱鍵的單個註冊，請打開**設備連接**頁，選擇 **"個人註冊**"作為連接方法，選擇**共用訪問簽名 （SAS）** 作為機制。 輸入 base64 編碼的主鍵和輔助鍵，並保存更改。 使用**ID 作用域**、**裝置識別碼**和主金鑰或輔助鍵連接設備。

    > [!TIP]
    > 對於測試，可以使用**OpenSSL**生成基 64 編碼金鑰：`openssl rand -base64 64`

- **X.509 憑證：** 要使用 X.509 憑證創建單個註冊，請打開**設備連接**頁，選擇 **"個人註冊**"作為連接方法，選擇**證書 （X.509）** 作為機制。 與單個註冊條目一起使用的設備證書要求頒發者和主題 CN 設置為裝置識別碼。

    > [!TIP]
    > 對於測試，可以使用用於[Node.js 的 Azure IoT 設備配置設備 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)生成自簽章憑證：`node create_test_cert.js device "mytestdevice"`

- **可信平臺模組 （TPM） 證明：**[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)是一種硬體安全模組。 使用 TPM 是連接設備最安全的方法之一。 本文假定您使用的是離散、固件或集成的 TPM。 軟體類比的 TM 非常適合原型設計或測試，但它們不能提供與離散、固件或集成 TM 相同的安全級別。 不要在生產中使用軟體 TM。 要創建使用 TPM 的單個註冊，請打開 **"設備連接**"頁，選擇 **"個人註冊**"作為連接方法，選擇**TPM**作為機制。 輸入 TPM 背書金鑰並保存設備連接資訊。

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>使用 IoT 隨插即用連接設備（預覽）

IoT 隨插即用（預覽）與 IoT Central 的關鍵功能之一是在設備連接上自動關聯設備範本的能力。 除了設備憑據外，設備現在可以作為設備註冊調用的一部分發送**功能模型Id。** 此功能使 IoT Central 能夠發現設備範本並將其與設備關聯。 發現過程的工作方式如下：

1. 如果設備範本已在 IoT 中央應用程式中發佈，則與該設備範本關聯。
1. 從已發佈和經過認證的功能模型的公共存儲庫中獲取。

以下是設備在 DPS 註冊調用期間將發送的額外有效負載的格式

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> 請注意，必須啟用 **"管理>設備連接**上的**自動核准**選項，以便設備自動連接、發現設備範本並開始發送資料。

## <a name="device-status-values"></a>設備狀態值

當實際設備連接到 IoT 中央應用程式時，其設備狀態將如下所示：

1. 設備狀態是首次**註冊**。 此狀態表示設備是在 IoT 中心創建的，並且具有裝置識別碼。 設備註冊時：
    - "設備"頁上添加了新的真實**設備**。
    - 使用"設備"頁上的 **"導入**"添加一組**設備**。

1. 當使用有效憑據連接到 IoT 中央應用程式的設備完成預配步驟時，設備狀態將更改為**預配**。 在此步驟中，設備使用 DPS 從 IoT 中央應用程式使用的 IoT 中心自動檢索連接字串。 設備現在可以連接到 IoT 中心並開始發送資料。

1. 操作員可以阻止設備。 當設備被阻止時，它無法將資料發送到 IoT 中心應用程式。 已阻止的設備的狀態為 **"已阻止**"。 操作員必須先重置設備，然後才能恢復發送資料。 當操作員取消阻止設備時，狀態將返回到其以前的值 **"已註冊**"或 **"預配**"。

1. 如果設備狀態為 **"等待審批**"，則表示禁用 **"自動核准**"選項。 操作員在開始發送資料之前必須顯式批准設備。 未在 **"設備"** 頁上手動註冊但使用有效憑據連接的設備將具有設備狀態 **"等待審批**"。 操作員可以使用"**批准**"按鈕從 **"設備"** 頁批准這些設備。

1. 如果設備狀態為 **"未關聯**"，則表示連接到 IoT Central 的設備沒有關聯的設備範本。 這種情況通常發生在以下情況下：

    - 使用"**設備**"頁上的 **"導入**"添加一組設備，而不指定設備範本。
    - 設備是在"**設備"** 頁上手動註冊的，但沒有指定設備範本。 然後，設備使用有效的憑據進行連接。  

    操作員可以使用 **"遷移**"按鈕將設備與 **"設備"** 頁面的設備範本相關聯。

## <a name="best-practices"></a>最佳作法

不要保留或緩存 DPS 首次連接設備時返回的設備連接字串。 要重新連接設備，請通過標準設備註冊流獲取正確的設備連接字串。 如果設備緩存連接字串，則如果 IoT Central 更新它使用的基礎 Azure IoT 中心，設備軟體將面臨具有陳舊連接字串的風險。

## <a name="sdk-support"></a>SDK 支援

Azure 設備 SDK 提供了實現設備代碼的最簡單方法。 可用的裝置 SDK 如下：

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 的功能和 IoT 中樞連線能力

所有裝置在與 IoT 中樞通訊時，都會使用下列 IoT 中樞連線能力選項：

- [裝置到雲端傳訊](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [裝置對應項](../../iot-hub/iot-hub-devguide-device-twins.md)

下表摘要說明 Azure IoT Central 裝置功能與 IoT 中樞功能的對應方式：

| Azure IoT 中心 | Azure IoT 中樞 |
| ----------- | ------- |
| 遙測 | 裝置到雲端傳訊 |
| 屬性 | 裝置對應項的報告屬性 |
| 屬性（可寫入） | 裝置對應項所需和所報告的屬性 |
| Command | 直接方法 |

要瞭解有關使用設備 SDK 的更多詳細資訊，請參閱[將 DevDiv 工具組設備連接到 Azure IoT 中央應用程式](howto-connect-devkit.md)，例如代碼。

### <a name="protocols"></a>通訊協定

裝置 SDK 支援使用下列網路通訊協定來連線至 IoT 中樞：

- MQTT
- AMQP
- HTTPS

如需這些差異通訊協定和其選擇方針的資訊，請參閱[選擇通訊協定](../../iot-hub/iot-hub-devguide-protocols.md)。

如果裝置無法使用任何所支援的通訊協定，則可以使用 Azure IoT Edge 來轉換通訊協定。 IoT Edge 支援其他 intelligence-on-the-edge 案例，可從 Azure IoT Central 應用程式將處理卸載至邊緣。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 有關詳細資訊，請參閱[控制對 IoT 中心的訪問](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了 Azure IoT 中心中的設備連接情況，以下是建議的後續步驟：

- [準備及連線 DevKit 裝置](howto-connect-devkit.md)
- [C SDK：預配設備用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
