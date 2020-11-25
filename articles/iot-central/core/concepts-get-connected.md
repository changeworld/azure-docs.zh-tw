---
title: Azure IoT Central 中的裝置連線能力 | Microsoft Docs
description: 本文介紹 Azure IoT Central 裝置連線能力的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 10/22/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 1a050daa3a4b3ae9be5ef40961c40adaa90dc72b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121821"
---
# <a name="get-connected-to-azure-iot-central"></a>連線至 Azure IoT Central

*本文適用于操作員和裝置開發人員。*

本文說明裝置如何連接到 Azure IoT Central 應用程式。 在裝置可以與 IoT Central 交換資料之前，它必須：

- *驗證*。 使用 IoT Central 應用程式進行驗證時，會使用 _共用存取簽章 (SAS) token_ 或 _x.509 憑證_。 在生產環境中，建議使用 x.509 憑證。
- *註冊*。 裝置必須向 IoT Central 的應用程式註冊。 您可以在應用程式的 [ **裝置** ] 頁面上，查看已註冊的裝置。
- *與裝置範本建立關聯*。 在 IoT Central 應用程式中，裝置範本會定義操作員用來查看和管理連線裝置的 UI。

IoT Central 支援下列兩個裝置註冊案例：

- *自動註冊*。 裝置會在第一次連線時自動註冊。 此案例可讓 Oem 在未先註冊的情況下，大量製造可連接的裝置。 OEM 會產生適當的裝置認證，並設定工廠中的裝置。 （選擇性）您可以要求操作員在裝置開始傳送資料之前核准該裝置。 此案例需要您在應用程式中設定 x.509 或 SAS _群組註冊_ 。
- *手動註冊*。 操作員可以在 [ **裝置** ] 頁面上註冊個別裝置，或匯 [入 CSV](howto-manage-devices.md#import-devices) 檔案以大量註冊裝置。 在此案例中，您可以使用 x.509 或 SAS _群組註冊_，或 X.509 或 sas _個別註冊_。

連接至 IoT Central 的裝置應遵循 *IoT 隨插即用慣例*。 其中一個慣例是裝置應該會在連線時傳送它所建立之裝置模型的 _模型識別碼_ 。 模型識別碼可讓 IoT Central 應用程式將裝置與正確的裝置範本產生關聯。

IoT Central 使用 [Azure IoT 中樞裝置布建服務 (DPS) ](../../iot-dps/about-iot-dps.md) 來管理連接程式。 裝置會先連線至 DPS 端點，以取得連線到應用程式所需的資訊。 就內部而言，您的 IoT Central 應用程式會使用 IoT 中樞來處理裝置連線能力。 使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

本文說明下列裝置連接步驟：

- [X.509 群組註冊](#x509-group-enrollment)
- [SAS 群組註冊](#sas-group-enrollment)
- [個別申請](#individual-enrollment)
- [裝置註冊](#device-registration)
- [將裝置與裝置範本建立關聯](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X.509 群組註冊

在生產環境中，使用 x.509 憑證是建議的 IoT Central 裝置驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../../iot-hub/iot-hub-x509ca-overview.md)。

若要使用 x.509 憑證將裝置連線到您的應用程式：

1. 建立使用 **(x.509)** 證明類型憑證的 *註冊群組*。
1. 新增並驗證註冊群組中的中繼或根 x.509 憑證。
1. 從註冊群組中的根或中繼憑證產生分葉憑證。 連接到您的應用程式時，從裝置傳送分葉憑證。

若要深入瞭解，請參閱[如何使用 x.509 憑證將裝置](how-to-connect-devices-x509.md)連線

### <a name="for-testing-purposes-only"></a>僅供測試之用

若只進行測試，您可以使用下列公用程式來產生根、中繼和裝置憑證：

- [適用于 Azure IoT 裝置布建裝置 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)： Node.js 工具的集合，可用來產生及驗證 x.509 憑證和金鑰。
- 如果您使用 DevKit 裝置，此 [命令列工具](https://aka.ms/iotcentral-docs-dicetool) 會產生 CA 憑證，您可以將其新增至您的 IoT Central 應用程式以驗證憑證。
- [管理範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)： PowerShell 和 Bash 腳本的集合，以：
  - 建立憑證鏈。
  - 將憑證儲存為 .cer 檔案，以上傳至您的 IoT Central 應用程式。
  - 使用 IoT Central 應用程式的驗證碼來產生驗證憑證。
  - 使用您的裝置識別碼作為工具的參數，為您的裝置建立分葉憑證。

## <a name="sas-group-enrollment"></a>SAS 群組註冊

若要使用裝置 SAS 金鑰將裝置連線到您的應用程式：

1. 建立使用 **共用存取簽章 (SAS)** 證明類型的 *註冊群組*。
1. 從註冊群組複製主要或次要金鑰群組。
1. 使用 Azure CLI 從群組金鑰產生裝置金鑰：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 當裝置連接到您的 IoT Central 應用程式時，請使用產生的裝置金鑰。

## <a name="individual-enrollment"></a>個別申請

如果客戶連接的裝置都有自己的驗證認證，請使用個別註冊。 個別註冊是允許連接的單一裝置專案。 個別註冊可以使用從實體或虛擬的可信賴平臺模組 (的 x.509 分葉憑證或 SAS 權杖，) 作為證明機制。 裝置識別碼可以包含字母、數字和 `-` 字元。 如需詳細資訊，請參閱 [DPS 個別註冊](../../iot-dps/concepts-service.md#individual-enrollment)。

> [!NOTE]
> 當您建立裝置的個別註冊時，它會優先于 IoT Central 應用程式中的預設群組註冊選項。

### <a name="create-individual-enrollments"></a>建立個別註冊

IoT Central 支援個別註冊的下列證明機制：

- **對稱金鑰證明：** 對稱金鑰證明是使用 DPS 實例驗證裝置的簡單方法。 若要建立使用對稱金鑰的個別註冊，請開啟裝置的 [ **裝置** 連線] 頁面，選取 [ **個別註冊** ] 作為連線方法，並將 **共用存取簽章 (SAS)** 作為機制。 輸入 base64 編碼的主要和次要金鑰，並儲存您的變更。 使用 **識別碼範圍**、 **裝置識別碼**，以及主要或次要金鑰來連接您的裝置。

    > [!TIP]
    > 若要進行測試，您可以使用 **OpenSSL** 來產生 base64 編碼的金鑰： `openssl rand -base64 64`

- **X.509 憑證：** 若要使用 x.509 憑證來建立個別註冊，請開啟 [ **裝置** 連線] 頁面，選取 [ **個別註冊** ] 作為連線方法，然後將 **憑證 (x.509)** 作為機制。 與個別註冊專案搭配使用的裝置憑證，必須將簽發者和主體 CN 設定為裝置識別碼。

    > [!TIP]
    > 若要進行測試，您可以使用適用于 [Node.js的 Azure IoT 裝置布建裝置 SDK 工具 ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) 來產生自我簽署憑證： `node create_test_cert.js device "mytestdevice"`

- **信賴平臺模組 (TPM) 證明：**[TPM](../../iot-dps/concepts-tpm-attestation.md)是一種硬體安全模組。 使用 TPM 是連接裝置最安全的方式之一。 本文假設您使用的是獨立、固件或整合式 TPM。 軟體模擬 Tpm 適用于原型設計或測試，但不提供與離散、固件或整合式 Tpm 相同的安全性層級。 請勿在生產環境中使用軟體 Tpm。 若要建立使用 TPM 的個別註冊，請開啟 [ **裝置** 連線] 頁面，選取 [ **個別註冊** ] 作為連線方法，然後選取 [ **TPM** ] 作為機制。 輸入 TPM 簽署金鑰並儲存裝置連接資訊。

## <a name="device-registration"></a>裝置註冊

裝置必須先在應用程式中註冊，才能連接到 IoT Central 應用程式：

- 裝置可在第一次連線時自動註冊。 若要使用此選項，您必須使用 [x.509 群組註冊](#x509-group-enrollment) 或 [SAS 群組註冊](#sas-group-enrollment)。
- 操作員可以匯入 CSV 檔案，將應用程式中的裝置清單大量註冊。
- 操作員可以在應用程式的 [ **裝置** ] 頁面上手動註冊個別裝置。

IoT Central 可讓 Oem 大量製造可自動註冊的裝置。 OEM 會產生適當的裝置認證，並設定工廠中的裝置。 當客戶第一次開啟裝置時，它會連線至 DPS，然後自動將裝置連接到正確的 IoT Central 應用程式。 （選擇性）您可以要求操作員核准裝置，然後才開始將資料傳送至應用程式。

> [!TIP]
> 在 [系統 **管理 > 裝置** 連線] 頁面上，[ **自動核准** ] 選項會控制操作員是否必須手動核准裝置，才能開始傳送資料。

### <a name="automatically-register-devices-that-use-x509-certificates"></a>自動註冊使用 x.509 憑證的裝置

1. 使用您新增至 [x.509 註冊群組](#x509-group-enrollment)的根或中繼憑證，為您的裝置產生分葉憑證。 使用裝置識別碼做為分 `CNAME` 葉憑證中的。 裝置識別碼可以包含字母、數字和 `-` 字元。

1. 如果是 OEM，請使用裝置識別碼、產生的 x.509 分葉憑證和應用程式 **識別碼範圍** 值來閃爍每個裝置。 裝置程式碼也應該傳送它所執行之裝置模型的模型識別碼。

1. 當您在裝置上切換時，它會先連線至 DPS 以抓取其 IoT Central 的連接資訊。

1. 裝置會使用 DPS 的資訊來連線到您的 IoT Central 應用程式，並向其註冊。

IoT Central 應用程式會使用裝置所傳送的模型識別碼，將 [已註冊的裝置與裝置範本產生關聯](#associate-a-device-with-a-device-template)。

### <a name="automatically-register-devices-that-use-sas-tokens"></a>自動註冊使用 SAS 權杖的裝置

1. 從 **SAS-IoT-裝置** 註冊群組複製群組主要金鑰：

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="從 SAS 群組主要金鑰-IoT-裝置註冊群組":::

1. 使用 `az iot central device compute-device-key` 命令來產生裝置 SAS 金鑰。 使用上一個步驟中的群組主要金鑰。 裝置識別碼可以包含字母、數位和 `-` 字元：

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 若為 OEM，請使用裝置識別碼、產生的裝置 SAS 金鑰和應用程式 **識別碼範圍** 值來閃爍每部裝置。 裝置程式碼也應該傳送它所執行之裝置模型的模型識別碼。

1. 當您在裝置上切換時，它會先連線至 DPS 以抓取其 IoT Central 的註冊資訊。

1. 裝置會使用 DPS 的資訊來連線到您的 IoT Central 應用程式，並向其註冊。

IoT Central 應用程式會使用裝置所傳送的模型識別碼，將 [已註冊的裝置與裝置範本產生關聯](#associate-a-device-with-a-device-template)。

### <a name="bulk-register-devices-in-advance"></a>預先大量註冊裝置

若要向您的 IoT Central 應用程式註冊大量裝置，請使用 CSV 檔案匯 [入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。

如果您的裝置使用 SAS 權杖進行驗證，請 [從您的 IoT Central 應用程式匯出 CSV](howto-manage-devices.md#export-devices)檔案。 匯出的 CSV 檔案包含裝置識別碼和 SAS 金鑰。

如果您的裝置使用 x.509 憑證進行驗證，請使用您上傳至 x.509 註冊群組中的根或中繼憑證，為您的裝置產生 x.509 分葉憑證。 使用您匯入的裝置識別碼做為分 `CNAME` 葉憑證中的值。

裝置必須使用您的應用程式的 **識別碼範圍** 值，並在連接時傳送模型識別碼。

> [!TIP]
> 您可以在 [**管理 > 裝置** 連線] 中找到 [**識別碼範圍**] 的值。

### <a name="register-a-single-device-in-advance"></a>事先註冊單一裝置

當您想要實驗 IoT Central 或測試裝置時，此方法很有用。 在 [**裝置**] 頁面上選取 [ **+ 新增**] 來註冊個別裝置。 您可以使用裝置連線 SAS 金鑰，將裝置連線到您的 IoT Central 應用程式。 從已註冊裝置的連接資訊複製 _裝置 SAS 金鑰_ ：

![適用于個別裝置的 SAS 金鑰](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>將裝置與裝置範本建立關聯

IoT Central 會在裝置連線時自動將裝置與裝置範本建立關聯。 裝置連接時，會傳送模型識別碼。 IoT Central 使用模型識別碼來識別該特定裝置型號的裝置範本。 探索進程的運作方式如下：

1. 如果裝置範本已在 IoT Central 應用程式中發佈，則裝置會與裝置範本相關聯。
1. 如果裝置範本尚未在 IoT Central 應用程式中發佈，IoT Central 會在公用模型存放庫中尋找裝置型號。 如果 IoT Central 找到模型，它會使用它來產生基本的裝置範本。
1. 如果 IoT Central 在公用模型儲存機制中找不到模型，則裝置會標示為無 **關聯**。 操作員可以建立裝置的裝置範本，然後將未關聯的裝置遷移至新的裝置範本。

## <a name="device-status-values"></a>裝置狀態值

當真實裝置連線到您的 IoT Central 應用程式時，其裝置狀態會變更，如下所示：

1. 裝置狀態為第一次 **註冊**。 此狀態表示裝置是在 IoT Central 中建立，並具有裝置識別碼。 裝置會在下列情況註冊：
    - 新的真實裝置會新增至 [ **裝置** ] 頁面。
    - 您可以使用 [**裝置**] 頁面上的 [匯 **入**] 來新增一組裝置。

1. 當連接到您的 IoT Central 應用程式且具有有效認證的裝置完成布建步驟時，裝置狀態會變更為 [已布 **建** ]。 在此步驟中，裝置會使用 DPS 自動從 IoT Central 應用程式所使用的 IoT 中樞取出連接字串。 裝置現在可以連接到 IoT Central 並開始傳送資料。

1. 操作員可以封鎖裝置。 當裝置被封鎖時，就無法將資料傳送到您的 IoT Central 應用程式。 封鎖的裝置狀態為 [已 **封鎖**]。 操作員必須先重設裝置，才能繼續傳送資料。 當操作員解除封鎖裝置時，狀態會回到先前的值，即 **已註冊或已** 布 **建**。

1. 如果裝置狀態正在 **等候核准**，則表示 [ **自動核准** ] 選項已停用。 操作員必須先明確核准裝置，才能開始傳送資料。 未在 [ **裝置** ] 頁面上手動註冊的裝置，但使用有效的認證連線時，會有裝置狀態 **等候核准**。 操作員可以使用 [**核准**] 按鈕，從 [**裝置**] 頁面核准這些裝置。

1. 如果裝置狀態為 [無 **關聯**]，表示連接到 IoT Central 的裝置沒有相關聯的裝置範本。 這種情況通常會發生在下列情況：

    - 您可以使用 [**裝置**] 頁面上的 [匯 **入**] 來新增一組裝置，而不需要指定裝置範本。
    - 裝置已在 [ **裝置** ] 頁面上手動註冊，但未指定裝置範本。 裝置接著會使用有效的認證來連線。  

    操作員可以使用 [**遷移**] 按鈕，從 [**裝置**] 頁面將裝置與裝置範本建立關聯。

## <a name="best-practices"></a>最佳作法

當您第一次連接裝置時，請勿保存或快取 DPS 傳回的裝置連接字串。 若要重新連線裝置，請流覽標準裝置註冊流程，以取得正確的裝置連接字串。 如果裝置快取連接字串，裝置軟體就會面臨具有過時連接字串的風險。 如果 IoT Central 更新其所使用的基礎 Azure IoT 中樞，則具有過時連接字串的裝置將無法連線。

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

如果您的裝置無法使用任何支援的通訊協定，請使用 Azure IoT Edge 來進行通訊協定轉換。 IoT Edge 支援從 Azure IoT Central 應用程式卸載處理的其他最先進智慧案例。

## <a name="security"></a>安全性

在裝置與 Azure IoT Central 之間交換的資料會都加密。 IoT 中樞會對每個要從裝置連線至任何裝置面向 IoT 中樞端點的要求進行驗證。 為了避免透過網路交換認證，裝置會使用已簽署的權杖來進行驗證。 如需詳細資訊，請參閱 [控制 IoT 中樞的存取權](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>後續步驟

如果您是裝置開發人員，則建議的後續步驟如下：

- 請參閱[教學課程：建立用戶端應用程式並將其連接到您的 Azure IoT Central 應用程式](tutorial-connect-device.md)，以瞭解如何使用 SAS 權杖的一些範例程式碼
- 瞭解如何 [使用適用于 IoT Central 應用程式 Node.js 裝置 SDK，將裝置連線到 x.509 憑證](how-to-connect-devices-x509.md)
- 了解如何[使用 Azure CLI 監視裝置連線能力](./howto-monitor-devices-azure-cli.md)
- 瞭解如何 [在您的 Azure IoT Central 應用程式中定義新的 IoT 裝置類型](./howto-set-up-template.md)
- 閱讀[Azure IoT Edge 裝置和 Azure IoT Central 的](./concepts-iot-edge.md)相關資訊
