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
ms.openlocfilehash: e67a8f6b9cc175932b09e6f576148656dd9da9ba
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298813"
---
# <a name="get-connected-to-azure-iot-central"></a>連線到 Azure IoT Central

本文介紹 Microsoft Azure IoT Central 裝置連線能力的重要相關概念。

Azure IoT Central 會使用 [Azure IoT 中樞裝置佈建服務 (DPS)](../../iot-dps/about-iot-dps.md) 來管理所有的裝置註冊和連線。

使用 DPS 可實現如下效果：

- 讓 IoT Central 支援大規模的裝置上線和連線。
- 讓您產生裝置認證並在離線狀態下設定裝置，而不需要透過 IoT Central UI 來註冊裝置。
- 要使用共用存取簽章（SAS）連接的裝置。
- 讓裝置使用業界標準的 X.509 憑證來連線。
- 讓您使用自己的裝置識別碼在 IoT Central 中註冊裝置。 使用自己的裝置識別碼可簡化與現有後台系統的整合。
- 讓您以單一且一致的方式將裝置連線到 IoT Central。

本文說明下列使用案例：

- [使用 SAS 快速連接單一裝置](#connect-a-single-device)
- [使用 SAS 大規模連接裝置](#connect-devices-at-scale-using-sas)
- [使用 x.509 憑證大規模](#connect-devices-using-x509-certificates)地連線裝置。這是建議用於生產環境的方法。
- [將裝置連線但先不註冊](#connect-without-registering-devices)
- [使用 IoT 隨插即用（預覽）功能來連接裝置](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>將單一裝置連線

當您要試驗 IoT Central 或測試裝置時，這個方法很有用。 您可以使用來自 IoT Central 應用程式的裝置連線資訊，使用裝置布建服務（DPS）將裝置連線到您的 IoT Central 應用程式。 您可以找到下列語言的範例 DPS 裝置用戶端程式代碼：

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大規模連接裝置

若要使用 SAS 將裝置連線到大規模 IoT Central，您必須註冊裝置，然後再進行設定：

### <a name="register-devices-in-bulk"></a>大量註冊裝置

若要向 IoT Central 應用程式註冊大量裝置，請使用 CSV 檔案來匯[入裝置識別碼和裝置名稱](howto-manage-devices.md#import-devices)。

若要取出已匯入之裝置的連線資訊，請[從您的 IoT Central 應用程式匯出 CSV](howto-manage-devices.md#export-devices)檔案。

> [!NOTE]
> 若要瞭解如何連接裝置，而不需先在 IoT Central 中註冊，請參閱連線[但不先註冊裝置](#connect-without-registering-devices)。

### <a name="set-up-your-devices"></a>設定您的裝置

使用裝置程式碼中匯出檔案的連線資訊，讓您的裝置能夠連線到 IoT 並傳送至您的 IoT Central 應用程式。 如需連接裝置的詳細資訊，請參閱[後續步驟](#next-steps)。

## <a name="connect-devices-using-x509-certificates"></a>使用 x.509 憑證來連接裝置

在生產環境中，使用 x.509 憑證是 IoT Central 的建議裝置驗證機制。 若要深入了解，請參閱[使用 X.509 CA 憑證進行裝置驗證](../../iot-hub/iot-hub-x509ca-overview.md)。

下列步驟說明如何使用 x.509 憑證，將裝置連線至 IoT Central：

1. 在 IoT Central 應用程式中，新增並驗證您要用來產生裝置憑證_的中繼或根 x.509 憑證_：

    - 流覽至 **[系統管理] > [裝置連線] [> 憑證（x.509）** ]，然後新增您用來產生分葉裝置憑證的 x.509 根或中繼憑證。

      ![連線設定](media/concepts-get-connected/connection-settings.png)

      如果您有安全性缺口或主要憑證設定為過期，請使用次要憑證來縮短停機時間。 當您更新主要憑證時，您可以繼續使用次要憑證來布建裝置。

    - 驗證憑證擁有權可確保憑證的上傳者擁有憑證的私密金鑰。 若要驗證憑證：
        - 選取 [**驗證碼**] 旁的按鈕，以產生程式碼。
        - 使用您在上一個步驟中產生的驗證碼來建立 x.509 驗證憑證。 將憑證儲存為 .cer 檔案。
        - 上傳已簽署的驗證憑證，然後選取 [**驗證**]。

          ![連線設定](media/concepts-get-connected/verify-cert.png)

1. 使用 CSV 檔案，在您的 IoT Central 應用程式中匯_入和註冊裝置_。

1. _設定您的裝置。_ 使用上傳的根憑證來產生分葉憑證。 使用**裝置識別碼**作為分葉憑證中的 CNAME 值。 裝置識別碼應為全部小寫。 然後使用布建服務資訊來為您的裝置進行程式設計。 當裝置第一次開啟時，它會從 DPS 抓取您的 IoT Central 應用程式的連接資訊。

### <a name="further-reference"></a>進一步參考

- 適用於 [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) 的範例實作。

- [C 範例裝置用戶端](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)。

### <a name="for-testing-purposes-only"></a>僅供測試之用

僅供測試之用，您可以使用這些公用程式來產生 CA 憑證和裝置憑證。

- 如果您使用 DevKit 裝置，此[命令列工具](https://aka.ms/iotcentral-docs-dicetool)會產生可新增至 IoT Central 應用程式的 CA 憑證，以驗證憑證。

- 使用此[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )來執行下列動作：
  - 建立憑證鏈。 遵循 GitHub 文章中的步驟2。
  - 將憑證儲存為 .cer 檔案，以上傳至您的 IoT Central 應用程式。
  - 使用來自 IoT Central 應用程式的驗證碼來產生驗證憑證。 遵循 GitHub 文章中的步驟3。
  - 使用您的裝置識別碼作為工具的參數，為您的裝置建立分葉憑證。 遵循 GitHub 文章中的步驟4。

## <a name="connect-without-registering-devices"></a>連接而不註冊裝置

IoT Central 的主要案例是讓 Oem 能夠大量製造可連線至 IoT Central 應用程式的裝置，而不需先進行註冊。 製造商必須產生適當的認證，並在 factory 中設定裝置。 當裝置第一次開啟時，它會自動連接到 IoT Central 應用程式。 IoT Central 操作員必須先核准裝置，才能傳送資料。

下圖概述此流程：

![連線設定](media/concepts-get-connected/device-connection-flow1.png)

下列步驟將更詳細地說明此程式。 根據您是使用 SAS 或 x.509 憑證來進行裝置驗證，步驟會有些許不同：

1. 設定您的連線設定：

    - **X.509 憑證：** [新增並驗證根/中繼憑證](#connect-devices-using-x509-certificates)，並在下列步驟中使用它來產生裝置憑證。
    - **SAS：** 複製 [主要金鑰]。 此金鑰是 IoT Central 應用程式的群組 SAS 金鑰。 在下列步驟中，使用金鑰來產生裝置 SAS 金鑰。
    ![連線設定 SAS](media/concepts-get-connected/connection-settings-sas.png)

1. 產生您的裝置認證
    - **憑證 X. x.509：** 使用您新增至 IoT Central 應用程式的根或中繼憑證，為您的裝置產生分葉憑證。 請確定您使用的是較低的**裝置識別碼**作為分葉憑證中的 CNAME。 僅供測試之用，請使用此[命令列工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )來產生裝置憑證。
    - **SAS：** 使用此[命令列工具](https://www.npmjs.com/package/dps-keygen)來產生裝置 SAS 金鑰。 使用上一個步驟中的群組**主要金鑰**。 裝置識別碼必須為小寫。

      若要安裝 [金鑰產生器公用程式](https://github.com/Azure/dps-keygen)，請執行下列命令：

      ```cmd/sh
      npm i -g dps-keygen
      ```

      若要從群組 SAS 主要金鑰產生裝置金鑰，請執行下列命令：

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. 若要設定您的裝置，請使用**範圍識別碼**、**裝置識別碼**和**x.509 裝置憑證**或**SAS 金鑰**來將每個裝置閃爍一次。

1. 然後開啟裝置以連接到您的 IoT Central 應用程式。 當您在裝置上切換時，它會先連線到 DPS 以取得其 IoT Central 的註冊資訊。

1. 連線裝置一開始會在 [**裝置**] 頁面上顯示為 [未**關聯**]。 裝置佈建狀態為 [已註冊]。 將裝置**遷移**至適當的裝置範本，並核准裝置以連接到您的 IoT Central 應用程式。 然後，裝置可以從 IoT 中樞取出連接字串，並開始傳送資料。 裝置布建已完成，且布建狀態現在已布**建**。

## <a name="individual-enrollment-based-device-connectivity"></a>以個人註冊為基礎的裝置連線能力

若客戶連線的裝置具有每個裝置個別註冊的驗證認證，則選項為。 個別註冊是可連線之單一裝置的專案。 個別註冊可使用 X.509 分葉憑證或 SAS 權杖 (從實際或虛擬的 TPM) 來作為證明機制。 個別註冊中的裝置識別碼（也稱為註冊識別碼）是英數位元、小寫，而且可能包含連字號。 在[這裡](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)深入瞭解個別註冊。

> [!NOTE]
> 當您建立裝置的個別註冊時，其優先順序高於應用程式中的預設群組註冊型證明（SAS、X509）。

### <a name="creating-individual-enrollments"></a>建立個別註冊
IoT Central 支援下列證明機制

1. **對稱金鑰證明：** 對稱金鑰證明是使用裝置布建服務實例來驗證裝置的簡單方法。 建立具有對稱金鑰的個別註冊;開啟 [連接] 對話方塊，選取個別註冊和機制「SAS」，並輸入主要和次要金鑰。 SAS 金鑰必須以 base64 編碼。 以下是程式碼範例的[連結](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample)，可協助您使用對稱金鑰和個別註冊來撰寫裝置程式碼，以布建裝置。
1. **X.509 憑證：** X.509 憑證的標題建議是以憑證為基礎的證明機制，這是調整生產環境的絕佳方式。 若要建立具有對稱金鑰的個別註冊，請選取個別註冊和機制 "x.509"，並上傳主要和次要憑證並儲存以建立註冊。 以下是程式碼範例的[連結](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample)，可協助您使用 X509 撰寫裝置程式碼以布建裝置。 與[個別註冊](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)專案搭配使用的裝置憑證，必須將 [主體名稱] 設定為個別註冊專案的 [裝置識別碼] （也稱為 [註冊識別碼]）。
1. **TPM 證明：** TPM 代表信賴平臺模組，它是一種硬體安全性模組（HSM），而且是連接裝置的其中一個最安全的方式。  本文假設您使用的是個別、韌體或整合式 TPM。 軟體模擬 Tpm 非常適合用於原型設計或測試，但不提供與離散、固件或整合式 Tpm 相同層級的安全性。 我們不建議將軟體 TPM 用在生產環境中。 若要建立具有對稱金鑰的個別註冊，請選取個別註冊和機制「TPM」，並輸入簽署金鑰以建立註冊。 如需 Tpm 類型的詳細資訊，您可以在[這裡](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)深入瞭解 TPM 證明。 以下是程式碼範例的[連結](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample)，可協助您使用 TPM 來撰寫裝置程式碼以布建裝置。 若要建立以 TPM 為基礎的證明，請在簽署金鑰中輸入並儲存。

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>使用 IoT 隨插即用連接裝置（預覽）

IoT 隨插即用（預覽）與 IoT Central 的其中一個重要功能，就是能夠在裝置連線上自動建立裝置範本的關聯。 除了裝置認證之外，裝置現在可以在裝置註冊呼叫中傳送**CapabilityModelId** ，IoT Central 將會探索並建立裝置範本的關聯。 探索程式會遵循下列順序：

1. 與裝置範本相關聯（如果已在 IoT Central 應用程式中發佈）。
1. 從已發行和已認證功能模型的公用存放庫提取。

以下是裝置會在 DPS 註冊呼叫期間傳送的其他承載格式

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> 請注意，您應該啟用自動核准選項，裝置才能自動連線、探索模型並開始傳送資料。

## <a name="device-status"></a>裝置狀態

當實際裝置連線到您的 IoT Central 應用程式時，其裝置狀態會變更如下：

1. 裝置狀態會是 [第一次**註冊**]。 此狀態表示裝置是在 IoT Central 中建立，並具有裝置識別碼。 裝置會在下列情況中註冊：
    - 新的實際裝置會新增至 [**裝置**] 頁面。
    - 在 [**裝置**] 頁面上使用 [匯**入**] 來新增一組裝置。

1. 當使用有效認證連線到您的 IoT Central 應用程式的裝置完成布建步驟時，裝置狀態會變更為 [已布**建**]。 在此步驟中，裝置會從 IoT 中樞抓取連接字串。 裝置現在可以連接到 IoT 中樞並開始傳送資料。

1. 操作員可以封鎖裝置。 當裝置遭到封鎖時，它無法將資料傳送至您的 IoT Central 應用程式。 封鎖的裝置狀態為 [已**封鎖**]。 操作員必須先重設裝置，才能繼續傳送資料。 當操作員解除封鎖裝置時，狀態會回到其先前的值（**已註冊或已**布**建**）。

1. 裝置狀態為 [**正在等候核准**]，這表示 [**自動核准**] 選項已停用，而且需要由操作員明確核准所有連接到 IoT Central 的裝置。 裝置未在 [**裝置**] 頁面上手動註冊，但使用有效的認證連線時，裝置狀態會是 [**正在等候核准**]。 操作員可以使用 [**核准**] 按鈕，從 [**裝置**] 頁面核准這些裝置。

1. 裝置狀態為 [未**關聯**]，表示連接至 IoT Central 的裝置沒有相關聯的裝置範本。 這通常會在下列案例中發生：
    - 在 [**裝置**] 頁面上使用 [匯**入**] 新增一組裝置，而不需指定裝置範本
    - 未在 [**裝置**] 頁面上以有效認證連線，但未在註冊期間指定範本識別碼的裝置手動註冊。  
操作員可以使用 [**遷移**] 按鈕，從 [**裝置**] 頁面將裝置與範本建立關聯。

## <a name="best-practices"></a>最佳作法 
1.  使用 DPS 將裝置連接到 IoT Central 時，請確定（IoT 中樞）裝置連接字串不是保存或快取。 若要重新連線裝置，請完成一般 DPS 裝置註冊流程，以取得正確的裝置連接字串。 如果快取連接字串，則在 IoT Central 更新基礎 Azure IoT 中樞的案例中，裝置軟體會面臨具有過時連接字串的風險。 

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
| 量測：遙測 | 裝置到雲端傳訊 |
| 裝置屬性 | 裝置對應項的報告屬性 |
| 設定 | 裝置對應項所需和所報告的屬性 |

若要深入瞭解如何使用裝置 Sdk，請參閱[將 DevDiv 套件裝置連線到您的 Azure IoT Central 應用程式](howto-connect-devkit.md)，以取得範例程式碼。

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

現在您已瞭解 Azure IoT Central 中的裝置連線能力，以下是建議的後續步驟：

- [準備及連線 DevKit 裝置](howto-connect-devkit.md)
- [C SDK：布建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
