---
title: Azure IoT Central 中的裝置連線能力 | Microsoft Docs
description: 本文介紹 Azure IoT Central 裝置連線能力的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: f39efcbfe7f0094e9481049a1678dba8a045888f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714260"
---
# <a name="get-connected-to-azure-iot-central"></a>連線至 Azure IoT Central

*本文適用于操作員和裝置開發人員。*

本文說明將裝置連線至 Azure IoT Central 應用程式的選項。

一般而言，您必須先在應用程式中註冊裝置，才能進行連線。 不過，IoT Central 不 [需要先註冊，就能支援裝置無法](#connect-without-registering-devices)連線的案例。

IoT Central 使用 [Azure IoT 中樞裝置布建服務 (DPS) ](../../iot-dps/about-iot-dps.md) 來管理連接程式。 裝置會先連線至 DPS 端點，以取得連線到應用程式所需的資訊。 就內部而言，您的 IoT Central 應用程式會使用 IoT 中樞來處理裝置連線能力。 使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

為了保護裝置與應用程式之間的通訊，IoT Central 支援 (SAS) 和 x.509 憑證的共用存取簽章。 在生產環境中，建議使用 x.509 憑證。

本文說明下列使用案例：

- [使用 SAS 連接單一裝置](#connect-a-single-device)
- [使用 SAS 大規模連接裝置](#connect-devices-at-scale-using-sas)
- [使用 x.509 憑證來大規模連接裝置](#connect-devices-using-x509-certificates) ，這是生產環境的建議方法。
- [在不先註冊裝置的情況下將裝置連線](#connect-without-registering-devices)
- [連接使用 DPS 個別註冊的裝置](#individual-enrollment-based-device-connectivity)
- [自動建立裝置與裝置範本的關聯](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>將單一裝置連線

當您想要實驗 IoT Central 或測試裝置時，此方法很有用。 您可以使用 IoT Central 應用程式中的裝置連線 SAS 金鑰，將裝置連線到您的 IoT Central 應用程式。 從已註冊裝置的連接資訊複製 _裝置 SAS 金鑰_ ：

![適用于個別裝置的 SAS 金鑰](./media/concepts-get-connected/single-device-sas.png)

若要深入瞭解，請參閱 [建立 Node.js 用戶端應用程式，並將其連線至您的 Azure IoT Central 應用程式](./tutorial-connect-device-nodejs.md) 教學課程。

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大規模連接裝置

若要使用 SAS 金鑰將裝置連線到大規模 IoT Central，您必須註冊並設定裝置：

### <a name="register-devices-in-bulk"></a>大量註冊裝置

若要向您的 IoT Central 應用程式註冊大量裝置，請使用 CSV 檔案匯 [入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。

若要抓取匯入之裝置的連接資訊，請 [從您的 IoT Central 應用程式匯出 CSV](howto-manage-devices.md#export-devices)檔案。 匯出的 CSV 檔案包含裝置識別碼和 SAS 金鑰。

### <a name="set-up-your-devices"></a>設定您的裝置

使用裝置程式碼中匯出檔案的連線資訊，讓您的裝置能夠連線到 IoT 並將資料傳送至您的 IoT Central 應用程式。 您也需要應用程式的 DPS **識別碼範圍** 。 您可以在 [ **管理 > 裝置**連線] 中找到此值。

> [!NOTE]
> 若要瞭解如何在不先將裝置註冊到 IoT Central 的情況下連線裝置，請參閱 [不先註冊裝置即可連接](#connect-without-registering-devices)。

## <a name="connect-devices-using-x509-certificates"></a>使用 x.509 憑證來連接裝置

在生產環境中，使用 x.509 憑證是建議的 IoT Central 裝置驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../../iot-hub/iot-hub-x509ca-overview.md)。

若要使用 x.509 憑證將裝置連線到您的應用程式：

1. 建立使用** (x.509) **證明類型憑證的*註冊群組*。
2. 新增並驗證註冊群組中的中繼或根 x.509 憑證。
3. 註冊並連接使用分葉 x.509 憑證的裝置，這些憑證是由註冊群組中的根或中繼憑證所產生。

### <a name="create-an-enrollment-group"></a>建立註冊群組

[註冊群組](../../iot-dps/concepts-service.md#enrollment)是共用相同證明類型的一組裝置。 這兩種支援的證明類型為 x.509 憑證和 SAS：

- 在 x.509 註冊群組中，連接至 IoT Central 的所有裝置都會使用 x.509 憑證，這些憑證是由註冊群組中的根或中繼憑證所產生。
- 在 SAS 註冊群組中，連接至 IoT Central 的所有裝置都會使用註冊群組中的 SAS 權杖所產生的 SAS 權杖。

每個 IoT Central 應用程式中的兩個預設註冊群組都是 SAS 註冊群組-一個用於 IoT 裝置，另一個用於 Azure IoT Edge 裝置。 若要建立 x.509 註冊群組，請流覽至 [ **裝置** 連線] 頁面，然後選取 [ **+ 新增註冊群組**：

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="新增 x.509 註冊群組螢幕擷取畫面":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>新增並驗證根或中繼 x.509 憑證

新增並驗證註冊群組的根或中繼憑證：

1. 流覽至您剛才建立的 x.509 註冊群組。 您可以選擇同時新增主要和次要 x.509 憑證。 選取 [ **+ 管理主要**]。

1. 在 [ **主要憑證] 頁面**上，上傳您的主要 x.509 憑證。 這是您的根或中繼憑證：

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="新增 x.509 註冊群組螢幕擷取畫面":::

1. 按一下 [ **產生驗證碼** ]，為您要用來產生驗證憑證的工具產生程式碼。 然後選取 [ **驗證** ] 以上傳驗證憑證。

1. 驗證成功時，您會看到下列確認：

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="新增 x.509 註冊群組螢幕擷取畫面":::

確認憑證擁有權可確保上傳憑證的人員具有憑證的私密金鑰。

如果您有安全性缺口或主要憑證設定為過期，請使用次要憑證來減少停機時間。 當您更新主要憑證時，您可以繼續使用次要憑證來布建裝置。

### <a name="register-and-connect-devices"></a>註冊並連接裝置

若要使用 x.509 憑證大量連接裝置，請先使用 CSV 檔案匯 [入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)，以在您的應用程式中註冊裝置。 裝置識別碼應該全部都是小寫。

使用您上傳至 x.509 註冊群組的根或中繼憑證，為您的裝置產生 x.509 分葉憑證。 使用 **裝置識別碼** 做為分 `CNAME` 葉憑證中的值。 您的裝置程式碼需要您應用程式的 **識別碼範圍** 值、 **裝置識別碼**和對應的裝置憑證。

#### <a name="sample-device-code"></a>範例裝置程式碼

下列來自 [Azure IoT Node.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) 的範例顯示 Node.js 裝置用戶端如何使用 x.509 分葉憑證和 DPS 向 IoT Central 應用程式註冊：

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

如需對等的 C 範例，請參閱[Azure IoT C 布建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)中的[prov_dev_client_sample。](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)

### <a name="for-testing-purposes-only"></a>僅供測試之用

若只進行測試，您可以使用下列公用程式來產生根、中繼和裝置憑證：

- [適用于 Azure IoT 裝置布建裝置 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)： Node.js 工具的集合，可用來產生及驗證 x.509 憑證和金鑰。
- 如果您使用 DevKit 裝置，此 [命令列工具](https://aka.ms/iotcentral-docs-dicetool) 會產生 CA 憑證，您可以將其新增至您的 IoT Central 應用程式以驗證憑證。
- [管理範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)： PowerShell 和 Bash 腳本的集合，以：
  - 建立憑證鏈。
  - 將憑證儲存為 .cer 檔案，以上傳至您的 IoT Central 應用程式。
  - 使用 IoT Central 應用程式的驗證碼來產生驗證憑證。
  - 使用您的裝置識別碼作為工具的參數，為您的裝置建立分葉憑證。

## <a name="connect-without-registering-devices"></a>連接但不註冊裝置

先前所述的案例都需要您先在應用程式中註冊裝置，才能進行連接。 IoT Central 也可讓 Oem 在未先註冊的情況下，大量製造可連接的裝置。 OEM 會產生適當的裝置認證，並設定工廠中的裝置。 當客戶第一次開啟裝置時，它會連線至 DPS，然後自動將裝置連接到正確的 IoT Central 應用程式。 IoT Central 操作員必須先核准裝置，才會開始將資料傳送至應用程式。

根據裝置是否使用 SAS 權杖或 x.509 憑證，流程會稍微不同：

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>連接使用 SAS 權杖但不註冊的裝置

1. 從 **SAS-IoT-裝置** 註冊群組複製群組主要金鑰：

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="新增 x.509 註冊群組螢幕擷取畫面":::

1. 使用 `az iot central device compute-device-key` 命令來產生裝置 SAS 金鑰。 使用上一個步驟中的群組主要金鑰。 裝置識別碼必須是小寫：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. OEM 會使用裝置識別碼、產生的裝置 SAS 金鑰和應用程式 **識別碼範圍** 值來閃爍每部裝置。

1. 當您在裝置上切換時，它會先連線至 DPS 以抓取其 IoT Central 的註冊資訊。

    裝置一開始會在 [**裝置**] 頁面上具有未**關聯**的裝置狀態，且不會指派給裝置範本。 在 [ **裝置** ] 頁面上，將裝置 **遷移** 至適當的裝置範本。 裝置布建現在已完成，裝置狀態現在已布 **建**，且裝置可以開始傳送資料。

    在 [系統 **管理 > 裝置** 連線] 頁面上，[ **自動核准** ] 選項會控制您是否需要手動核准裝置，才能開始傳送資料。

    > [!NOTE]
    > 若要瞭解如何自動將裝置與裝置範本產生關聯，請參閱 [自動將裝置與裝置範本產生關聯](#automatically-associate-with-a-device-template)。

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>在不註冊的情況下，連接使用 x.509 憑證的裝置

1. [建立註冊群組](#create-an-enrollment-group) ，然後在您的 IoT Central 應用程式中 [新增並驗證根或中繼 x.509 憑證](#add-and-verify-a-root-or-intermediate-x509-certificate) 。

1. 使用您新增至 IoT Central 應用程式的根或中繼憑證，為您的裝置產生分葉憑證。 使用小寫裝置識別碼做為分 `CNAME` 葉憑證中的。

1. OEM 會使用裝置識別碼、產生的分葉 x.509 憑證和應用程式 **識別碼範圍** 值來閃爍每部裝置。

1. 當您在裝置上切換時，它會先連線至 DPS 以抓取其 IoT Central 的註冊資訊。

    裝置一開始會在 [**裝置**] 頁面上具有未**關聯**的裝置狀態，且不會指派給裝置範本。 在 [ **裝置** ] 頁面上，將裝置 **遷移** 至適當的裝置範本。 裝置布建現在已完成，裝置狀態現在已布 **建**，且裝置可以開始傳送資料。

    在 [系統 **管理 > 裝置** 連線] 頁面上，[ **自動核准** ] 選項會控制您是否需要手動核准裝置，才能開始傳送資料。

    > [!NOTE]
    > 若要瞭解如何自動將裝置與裝置範本產生關聯，請參閱 [自動將裝置與裝置範本產生關聯](#automatically-associate-with-a-device-template)。

## <a name="individual-enrollment-based-device-connectivity"></a>個別註冊型裝置連線能力

如果客戶連接的裝置都有自己的驗證認證，請使用個別註冊。 個別註冊是允許連接的單一裝置專案。 個別註冊可以使用從實體或虛擬的可信賴平臺模組 (的 x.509 分葉憑證或 SAS 權杖，) 作為證明機制。 個別註冊中的裝置識別碼 (也稱為註冊識別碼) ，其為英數位元、小寫字母，而且可能包含連字號。 如需詳細資訊，請參閱 [DPS 個別註冊](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)。

> [!NOTE]
> 當您建立裝置的個別註冊時，它會優先于 IoT Central 應用程式中的預設群組註冊選項。

### <a name="create-individual-enrollments"></a>建立個別註冊

IoT Central 支援個別註冊的下列證明機制：

- **對稱金鑰證明：** 對稱金鑰證明是使用 DPS 實例驗證裝置的簡單方法。 若要建立使用對稱金鑰的個別註冊，請開啟 [ **裝置** 連線] 頁面，選取 **個別註冊** 作為連線方法，並將 **共用存取簽章 (SAS) ** 作為機制。 輸入 base64 編碼的主要和次要金鑰，並儲存您的變更。 使用 **識別碼範圍**、 **裝置識別碼**，以及主要或次要金鑰來連接您的裝置。

    > [!TIP]
    > 若要進行測試，您可以使用 **OpenSSL** 來產生 base64 編碼的金鑰： `openssl rand -base64 64`

- **X.509 憑證：** 若要使用 x.509 憑證來建立個別註冊，請開啟 [ **裝置** 連線] 頁面，選取 [ **個別註冊** ] 作為連線方法，然後將 **憑證 (x.509) ** 作為機制。 與個別註冊專案搭配使用的裝置憑證，必須將簽發者和主體 CN 設定為裝置識別碼。

    > [!TIP]
    > 若要進行測試，您可以使用適用于 [Node.js的 Azure IoT 裝置布建裝置 SDK 工具 ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) 來產生自我簽署憑證： `node create_test_cert.js device "mytestdevice"`

- **信賴平臺模組 (TPM) 證明：**[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)是一種硬體安全模組。 使用 TPM 是連接裝置最安全的方式之一。 本文假設您使用的是獨立、固件或整合式 TPM。 軟體模擬 Tpm 適用于原型設計或測試，但不提供與離散、固件或整合式 Tpm 相同的安全性層級。 請勿在生產環境中使用軟體 Tpm。 若要建立使用 TPM 的個別註冊，請開啟 [ **裝置** 連線] 頁面，選取 [ **個別註冊** ] 作為連線方法，然後選取 [ **TPM** ] 作為機制。 輸入 TPM 簽署金鑰並儲存裝置連接資訊。

## <a name="automatically-associate-with-a-device-template"></a>自動與裝置範本建立關聯

IoT Central 的其中一個主要功能是能夠在裝置連線時自動建立裝置範本的關聯。 除了裝置認證，裝置也可以傳送 **CapabilityModelId** 作為裝置註冊呼叫的一部分。 **CapabilityModelID**是一個 URN，可識別裝置所執行的功能模型。 IoT Central 的應用程式可以使用 **CapabilityModelID** 來識別要使用的裝置範本，然後自動將裝置與裝置範本產生關聯。 探索進程的運作方式如下：

1. 如果裝置範本已在 IoT Central 應用程式中發佈，則裝置會與裝置範本相關聯。
1. 針對預先認證的 IoT 隨插即用裝置，如果 IoT Central 應用程式中尚未發佈裝置範本，則會從公用存放庫提取裝置範本。

下列程式碼片段顯示裝置在 DPS 註冊呼叫期間必須傳送的額外承載的格式，自動關聯才能運作。

這是使用正式可用裝置 SDK 之裝置的格式：

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

這是使用公開預覽裝置 SDK 之裝置的格式：

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> 您必須啟用 [**管理 > 裝置**連線] 的 [**自動核准**] 選項，裝置才能自動連線、探索裝置範本，以及開始傳送資料。

## <a name="device-status-values"></a>裝置狀態值

當真實裝置連線到您的 IoT Central 應用程式時，其裝置狀態會變更，如下所示：

1. 裝置狀態為第一次 **註冊**。 此狀態表示裝置是在 IoT Central 中建立，並具有裝置識別碼。 裝置會在下列情況註冊：
    - 新的真實裝置會新增至 [ **裝置** ] 頁面。
    - 您可以使用 [**裝置**] 頁面上的 [匯**入**] 來新增一組裝置。

1. 當連接到您的 IoT Central 應用程式且具有有效認證的裝置完成布建步驟時，裝置狀態會變更為 [已布 **建** ]。 在此步驟中，裝置會使用 DPS 自動從 IoT Central 應用程式所使用的 IoT 中樞取出連接字串。 裝置現在可以連接到 IoT Central 並開始傳送資料。

1. 操作員可以封鎖裝置。 當裝置被封鎖時，就無法將資料傳送到您的 IoT Central 應用程式。 封鎖的裝置狀態為 [已 **封鎖**]。 操作員必須先重設裝置，才能繼續傳送資料。 當操作員解除封鎖裝置時，狀態會回到先前的值，即 **已註冊或已** 布 **建**。

1. 如果裝置狀態正在 **等候核准**，則表示 [ **自動核准** ] 選項已停用。 操作員必須先明確核准裝置，才能開始傳送資料。 未在 [ **裝置** ] 頁面上手動註冊的裝置，但使用有效的認證連線時，會有裝置狀態 **等候核准**。 操作員可以使用 [**核准**] 按鈕，從 [**裝置**] 頁面核准這些裝置。

1. 如果裝置狀態為 [無 **關聯**]，表示連接到 IoT Central 的裝置沒有相關聯的裝置範本。 這種情況通常會發生在下列情況：

    - 您可以使用 [**裝置**] 頁面上的 [匯**入**] 來新增一組裝置，而不需要指定裝置範本。
    - 裝置已在 [ **裝置** ] 頁面上手動註冊，但未指定裝置範本。 裝置接著會使用有效的認證來連線。  

    操作員可以使用 [**遷移**] 按鈕，從 [**裝置**] 頁面將裝置與裝置範本建立關聯。

## <a name="best-practices"></a>最佳作法

當您第一次連接裝置時，請勿保存或快取 DPS 傳回的裝置連接字串。 若要重新連線裝置，請流覽標準裝置註冊流程，以取得正確的裝置連接字串。 如果裝置快取連接字串，如果 IoT Central 更新所使用的基礎 Azure IoT 中樞，裝置軟體就會面臨具有過時連接字串的風險。

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
- [裝置 twins](../../iot-hub/iot-hub-devguide-device-twins.md)

下表摘要說明 Azure IoT Central 裝置功能與 IoT 中樞功能的對應方式：

| Azure IoT 中心 | Azure IoT 中樞 |
| ----------- | ------- |
| 遙測 | 裝置到雲端傳訊 |
| 屬性 | 裝置對應項的報告屬性 |
| 屬性 (可寫入) | 裝置對應項所需和所報告的屬性 |
| Command | 直接方法 |

### <a name="protocols"></a>通訊協定

裝置 SDK 支援使用下列網路通訊協定來連線至 IoT 中樞：

- MQTT
- AMQP
- HTTPS

如需這些差異通訊協定和其選擇方針的資訊，請參閱[選擇通訊協定](../../iot-hub/iot-hub-devguide-protocols.md)。

如果裝置無法使用任何所支援的通訊協定，則可以使用 Azure IoT Edge 來轉換通訊協定。 IoT Edge 支援其他 intelligence-on-the-edge 案例，可從 Azure IoT Central 應用程式將處理卸載至邊緣。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 如需詳細資訊，請參閱 [控制 IoT 中樞的存取權](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
- 瞭解如何 [在您的 Azure IoT Central 應用程式中定義新的 IoT 裝置類型](./howto-set-up-template.md)
- 閱讀[Azure IoT Edge 裝置和 Azure IoT Central 的](./concepts-iot-edge.md)相關資訊
