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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: aa6aa7a8d98ae756a65a2618371c320118875c42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710434"
---
# <a name="get-connected-to-azure-iot-central"></a>連線至 Azure IoT Central

*本文適用于操作員和裝置開發人員。*

本文說明將您的裝置連接至 Azure IoT Central 應用程式的選項。

一般來說，您必須先在應用程式中註冊裝置，才能進行連接。 不過，IoT Central 支援[可在未先註冊裝置](#connect-without-registering-devices)的情況下進行連線的案例。

IoT Central 使用[Azure IoT 中樞裝置布建服務（DPS）](../../iot-dps/about-iot-dps.md)來管理連接程式。 裝置會先連線到 DPS 端點，以取得連線到您的應用程式所需的資訊。 就內部而言，您的 IoT Central 應用程式會使用 IoT 中樞來處理裝置連線能力。 使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

為了保護裝置與應用程式之間的通訊安全，IoT Central 支援共用存取簽章（SAS）和 x.509 憑證。 X.509 憑證建議用於生產環境中。

本文說明下列使用案例：

- [使用 SAS 連接單一裝置](#connect-a-single-device)
- [使用 SAS 大規模連接裝置](#connect-devices-at-scale-using-sas)
- [使用 x.509 憑證大規模連接裝置](#connect-devices-using-x509-certificates)-適用于生產環境的建議方法。
- [連接裝置而不先進行註冊](#connect-without-registering-devices)
- [連接使用 DPS 個別註冊的裝置](#individual-enrollment-based-device-connectivity)
- [自動將裝置與裝置範本建立關聯](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>將單一裝置連線

當您要試驗 IoT Central 或測試裝置時，這個方法很有用。 您可以使用 IoT Central 應用程式中的裝置連線 SAS 金鑰，將裝置連線到您的 IoT Central 應用程式。 從已註冊裝置的連接資訊複製_裝置 SAS 金鑰_：

![個別裝置的 SAS 金鑰](./media/concepts-get-connected/single-device-sas.png)

若要深入瞭解，請參閱[建立並將 Node.js 用戶端應用程式連線到 Azure IoT Central 應用程式](./tutorial-connect-device-nodejs.md)教學課程。

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大規模連接裝置

若要使用 SAS 金鑰將裝置連線到大規模 IoT Central，您必須先註冊裝置，然後再加以設定：

### <a name="register-devices-in-bulk"></a>大量註冊裝置

若要向 IoT Central 應用程式註冊大量裝置，請使用 CSV 檔案來匯[入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。

若要取出已匯入之裝置的連線資訊，請[從您的 IoT Central 應用程式匯出 CSV](howto-manage-devices.md#export-devices)檔案。 匯出的 CSV 檔案包含裝置識別碼和 SAS 金鑰。

### <a name="set-up-your-devices"></a>設定您的裝置

使用裝置程式碼中匯出檔案的連線資訊，讓您的裝置能夠連線到 IoT 並傳送至您的 IoT Central 應用程式。 您也需要應用程式的 DPS**識別碼範圍**。 您可以在 [管理] **> [裝置**連線] 中找到此值。

> [!NOTE]
> 若要瞭解如何連接裝置，而不需先在 IoT Central 中註冊，請參閱連線[但不先註冊裝置](#connect-without-registering-devices)。

## <a name="connect-devices-using-x509-certificates"></a>使用 x.509 憑證來連接裝置

在生產環境中，使用 x.509 憑證是 IoT Central 的建議裝置驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../../iot-hub/iot-hub-x509ca-overview.md)。

在您使用 x.509 憑證來連接裝置之前，請先在應用程式中新增並驗證中繼或根 x.509 憑證。 裝置必須使用從根或中繼憑證產生的分葉 x.509 憑證。

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>新增及驗證根或中繼憑證

流覽至 [系統管理] > [裝置連線] **> 管理主要憑證**]，然後新增您用來產生裝置憑證的 x.509 根或中繼憑證。

![連線設定](media/concepts-get-connected/manage-x509-certificate.png)

驗證憑證擁有權可確保上傳憑證的人員擁有憑證的私密金鑰。 若要驗證憑證：

  1. 選取 [**驗證碼**] 旁的按鈕，以產生程式碼。
  1. 使用您在上一個步驟中產生的驗證碼來建立 x.509 驗證憑證。 將憑證儲存為 .cer 檔案。
  1. 上傳已簽署的驗證憑證，然後選取 [**驗證**]。 驗證成功時，會將憑證標示為**已驗證**。

如果您有安全性缺口或主要憑證設定為過期，請使用次要憑證來縮短停機時間。 當您更新主要憑證時，您可以繼續使用次要憑證來布建裝置。

### <a name="register-and-connect-devices"></a>註冊並連接裝置

若要使用 x.509 憑證大量連線裝置，請先在應用程式中註冊裝置，方法是使用 CSV 檔案匯[入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。 裝置識別碼的大小寫都應該是小寫。

使用上傳的根或中繼憑證，為您的裝置產生 x.509 分葉憑證。 使用**裝置識別碼**作為分 `CNAME` 葉憑證中的值。 您的裝置程式碼需要應用程式的**識別碼範圍**值、**裝置識別碼**和對應的裝置憑證。

#### <a name="sample-device-code"></a>範例裝置程式碼

下列來自[Azure IoT Node.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js)的範例顯示 Node.js 裝置用戶端如何使用 x.509 分葉憑證和 DPS 向 IoT Central 應用程式註冊：

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

如需對等的 C 範例，請參閱[Azure IoT C 布建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)中的[prov_dev_client_sample。](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)

### <a name="for-testing-purposes-only"></a>僅供測試之用

僅供測試之用，您可以使用下列公用程式來產生根、中繼和裝置憑證：

- [適用于 Azure IoT 裝置布建裝置 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)： Node.js 工具的集合，您可以用來產生和驗證 x.509 憑證和金鑰。
- 如果您使用 DevKit 裝置，此[命令列工具](https://aka.ms/iotcentral-docs-dicetool)會產生可新增至 IoT Central 應用程式的 CA 憑證，以驗證憑證。
- [管理範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)： PowerShell 和 Bash 腳本的集合，以：
  - 建立憑證鏈。
  - 將憑證儲存為 .cer 檔案，以上傳至您的 IoT Central 應用程式。
  - 使用來自 IoT Central 應用程式的驗證碼來產生驗證憑證。
  - 使用您的裝置識別碼作為工具的參數，為您的裝置建立分葉憑證。

## <a name="connect-without-registering-devices"></a>連接而不註冊裝置

先前所述的案例都需要您先在應用程式中註冊裝置，然後才能進行連線。 IoT Central 也可讓 Oem 大量製造可連線的裝置，而不需先進行註冊。 OEM 會產生適當的裝置認證，並設定 factory 中的裝置。 當客戶第一次開啟裝置時，它會連線到 DPS，然後自動將裝置連接到正確的 IoT Central 應用程式。 IoT Central 操作員在開始將資料傳送至應用程式之前，必須先核准裝置。

根據裝置是使用 SAS 權杖或 x.509 憑證，此流程會有些許不同：

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>連接使用 SAS 權杖的裝置而不進行註冊

1. 複製 IoT Central 應用程式的群組主要金鑰：

    ![應用程式群組主要 SAS 金鑰](media/concepts-get-connected/group-sas-keys.png)

1. 使用[dps-keygen](https://www.npmjs.com/package/dps-keygen)工具來產生裝置 SAS 金鑰。 使用上一個步驟中的群組主要金鑰。 裝置識別碼必須是小寫：

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM 會使用裝置識別碼、產生的裝置 SAS 金鑰和應用程式**識別碼範圍**值來閃爍每個裝置。

1. 當您在裝置上切換時，它會先連線到 DPS 以取得其 IoT Central 的註冊資訊。

    裝置一開始會在 [**裝置**] 頁面上有未**關聯**的裝置狀態，而且不會指派給裝置範本。 在 [**裝置**] 頁面上，將裝置**遷移**至適當的裝置範本。 裝置布建已完成，現在已布**建**裝置狀態，而且裝置可以開始傳送資料。

    在 [系統**管理] > [裝置**連線] 頁面上，[**自動核准**] 選項會控制您是否需要手動核准裝置，才能開始傳送資料。

    > [!NOTE]
    > 若要瞭解如何自動將裝置與裝置範本建立關聯，請參閱[自動將裝置與裝置範本建立關聯](#automatically-associate-with-a-device-template)。

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>連接使用 x.509 憑證但不註冊的裝置

1. 在 IoT Central 應用程式中[新增並驗證根或中繼的 x.509 憑證](#connect-devices-using-x509-certificates)。

1. 使用您新增至 IoT Central 應用程式的根或中繼憑證，為您的裝置產生分葉憑證。 使用與分葉憑證中的小寫裝置識別碼 `CNAME` 。

1. OEM 會使用裝置識別碼、產生的分葉 x.509 憑證和應用程式**識別碼範圍**值來閃爍每個裝置。

1. 當您在裝置上切換時，它會先連線到 DPS 以取得其 IoT Central 的註冊資訊。

    裝置一開始會在 [**裝置**] 頁面上有未**關聯**的裝置狀態，而且不會指派給裝置範本。 在 [**裝置**] 頁面上，將裝置**遷移**至適當的裝置範本。 裝置布建已完成，現在已布**建**裝置狀態，而且裝置可以開始傳送資料。

    在 [系統**管理] > [裝置**連線] 頁面上，[**自動核准**] 選項會控制您是否需要手動核准裝置，才能開始傳送資料。

    > [!NOTE]
    > 若要瞭解如何自動將裝置與裝置範本建立關聯，請參閱[自動將裝置與裝置範本建立關聯](#automatically-associate-with-a-device-template)。

## <a name="individual-enrollment-based-device-connectivity"></a>以個人註冊為基礎的裝置連線能力

若客戶連線的裝置都有自己的驗證認證，請使用個別註冊。 個別註冊是允許連接的單一裝置專案。 個別註冊可以使用 x.509 分葉憑證或 SAS 權杖（來自實體或虛擬的可信賴平臺模組）作為證明機制。 個別註冊中的裝置識別碼（也稱為註冊識別碼）是英數位元、小寫，而且可能包含連字號。 如需詳細資訊，請參閱[DPS 個別註冊](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)。

> [!NOTE]
> 當您建立裝置的個別註冊時，其優先順序會高於 IoT Central 應用程式中的預設群組註冊選項。

### <a name="create-individual-enrollments"></a>建立個別註冊

IoT Central 支援個別註冊的下列證明機制：

- **對稱金鑰證明：** 對稱金鑰證明是使用 DPS 實例驗證裝置的簡單方法。 若要建立使用對稱金鑰的個別註冊，請開啟 [**裝置**連線] 頁面，選取 [**個別註冊**] 做為連線方法，以及 [**共用存取簽章（SAS）** ] 做為機制。 輸入 base64 編碼的主要和次要金鑰，然後儲存您的變更。 使用**識別碼範圍**、**裝置識別碼**，以及主要或次要金鑰來連接您的裝置。

    > [!TIP]
    > 若要進行測試，您可以使用**OpenSSL**來產生 base64 編碼的金鑰：`openssl rand -base64 64`

- **X.509 憑證：** 若要建立使用 x.509 憑證的個別註冊，請開啟 [**裝置**連線] 頁面，選取 [**個別註冊**] 做為連線方法，以及 [**憑證（x.509）** ] 做為機制。 與個別註冊專案搭配使用的裝置憑證，必須將簽發者和主體 CN 設定為裝置識別碼。

    > [!TIP]
    > 若要進行測試，您可以使用[適用于 Node.js的 Azure IoT 裝置布建裝置 SDK 工具](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)來產生自我簽署憑證：`node create_test_cert.js device "mytestdevice"`

- **信賴平臺模組（TPM）證明：**[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)是一種硬體安全性模組。 使用 TPM 是連接裝置的其中一個最安全的方式。 本文假設您使用的是獨立、固件或整合的 TPM。 軟體模擬 Tpm 非常適合用於原型設計或測試，但不提供與離散、固件或整合式 Tpm 相同層級的安全性。 請勿在生產環境中使用軟體 Tpm。 若要建立使用 TPM 的個別註冊，請開啟 [**裝置**連線] 頁面，選取 [**個別註冊**] 做為 [連線方法]，將 [ **TPM** ] 做為機制。 輸入 TPM 簽署金鑰並儲存裝置連接資訊。

## <a name="automatically-associate-with-a-device-template"></a>自動與裝置範本產生關聯

IoT Central 的主要功能之一，就是能夠在裝置連線上自動建立裝置範本的關聯。 除了裝置認證之外，裝置也可以在裝置註冊呼叫中傳送**CapabilityModelId** 。 **CapabilityModelID**是一種 URN，可識別裝置所執行的功能模型。 IoT Central 應用程式可以使用**CapabilityModelID**來識別要使用的裝置範本，然後自動將裝置與裝置範本產生關聯。 探索程式的運作方式如下：

1. 如果裝置範本已在 IoT Central 應用程式中發佈，則裝置會與裝置範本相關聯。
1. 針對預先認證的 IoT 隨插即用裝置，如果尚未在 IoT Central 應用程式中發佈裝置範本，則會從公用存放庫提取裝置範本。

下列程式碼片段顯示裝置必須在 DPS 註冊呼叫期間傳送的其他承載格式，才能讓自動關聯正常執行。

這是使用已正式運作的裝置 SDK （不支援 IoT 隨插即用）之裝置的格式：

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

這是使用公開預覽裝置 SDK （支援 IoT 隨插即用）之裝置的格式：

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> 系統**管理 > 裝置**連線上的 [**自動核准**] 選項必須啟用，裝置才能自動連線、探索裝置範本，以及開始傳送資料。

## <a name="device-status-values"></a>裝置狀態值

當實際裝置連線到您的 IoT Central 應用程式時，其裝置狀態會變更如下：

1. 裝置狀態會是 [第一次**註冊**]。 此狀態表示裝置是在 IoT Central 中建立，並具有裝置識別碼。 裝置會在下列情況中註冊：
    - 新的實際裝置會新增至 [**裝置**] 頁面。
    - 在 [**裝置**] 頁面上使用 [匯**入**] 來新增一組裝置。

1. 當使用有效認證連線到您的 IoT Central 應用程式的裝置完成布建步驟時，裝置狀態會變更為 [已布**建**]。 在此步驟中，裝置會使用 DPS 自動從 IoT Central 應用程式所使用的 IoT 中樞中抓取連接字串。 裝置現在可以連接到 IoT Central 並開始傳送資料。

1. 操作員可以封鎖裝置。 當裝置遭到封鎖時，它無法將資料傳送至您的 IoT Central 應用程式。 封鎖的裝置狀態為 [已**封鎖**]。 操作員必須先重設裝置，才能繼續傳送資料。 當操作員解除封鎖裝置時，狀態會回到其先前的值（**已註冊或已**布**建**）。

1. 如果裝置狀態為 [**正在等候核准**]，則表示 [**自動核准**] 選項已停用。 操作員必須先明確核准裝置，才能開始傳送資料。 裝置未在 [**裝置**] 頁面上手動註冊，但使用有效的認證連線時，裝置狀態會是 [**正在等候核准**]。 操作員可以使用 [**核准**] 按鈕，從 [**裝置**] 頁面核准這些裝置。

1. 如果裝置狀態為 [未**關聯**]，表示連接到 IoT Central 的裝置沒有相關聯的裝置範本。 這種情況通常會發生在下列情況：

    - 在 [**裝置**] 頁面上使用 [匯**入**] 來新增一組裝置，而不需指定裝置範本。
    - 裝置已在 [**裝置**] 頁面上手動註冊，但未指定裝置範本。 然後，裝置會使用有效的認證進行連接。  

    操作員可以使用 [**遷移**] 按鈕，從 [**裝置**] 頁面將裝置關聯至裝置範本。

## <a name="best-practices"></a>最佳作法

當您第一次連接裝置時，請勿保存或快取 DPS 傳回的裝置連接字串。 若要重新連線裝置，請執行標準裝置註冊流程，以取得正確的裝置連接字串。 如果裝置快取連接字串，當 IoT Central 更新其使用的基礎 Azure IoT 中樞時，裝置軟體會面臨具有過時連接字串的風險。

## <a name="sdk-support"></a>SDK 支援

Azure 裝置 Sdk 提供最簡單的方式來執行您的裝置程式碼。 可用的裝置 SDK 如下：

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
| 屬性 (可寫入) | 裝置對應項所需和所報告的屬性 |
| Command | 直接方法 |

若要深入瞭解如何使用裝置 Sdk，請參閱[將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式](howto-connect-devkit.md)，以取得範例程式碼。

### <a name="protocols"></a>通訊協定

裝置 SDK 支援使用下列網路通訊協定來連線至 IoT 中樞：

- MQTT
- AMQP
- HTTPS

如需這些差異通訊協定和其選擇方針的資訊，請參閱[選擇通訊協定](../../iot-hub/iot-hub-devguide-protocols.md)。

如果裝置無法使用任何所支援的通訊協定，則可以使用 Azure IoT Edge 來轉換通訊協定。 IoT Edge 支援其他 intelligence-on-the-edge 案例，可從 Azure IoT Central 應用程式將處理卸載至邊緣。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
- 瞭解如何[在您的 Azure IoT Central 應用程式中定義新的 IoT 裝置類型](./howto-set-up-template.md)
- 閱讀[Azure IoT Edge 裝置和 Azure IoT Central 的](./concepts-iot-edge.md)相關資訊
