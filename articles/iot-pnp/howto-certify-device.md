---
title: 如何認證 IoT 隨插即用裝置 |Microsoft Docs
description: 作為裝置產生器，瞭解如何執行測試並提交裝置以進行認證
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6aa4273933190ccfe495bcaf243ee15a5ce823fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577640"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>如何認證 IoT 隨插即用裝置

IoT 隨插即用裝置認證方案包含可檢查裝置是否符合 IoT 隨插即用認證需求的工具。 這些工具也可協助組織為其 IoT 隨插即用裝置的可用性帶來認知。 這些認證裝置專為 IoT 解決方案量身打造，可協助縮短上市時間。

本文示範如何：

- 安裝適用于 Azure CLI 的 Azure IoT 命令列工具擴充功能
- 在開發階段執行 IoT 隨插即用測試以驗證您的裝置應用程式  
- 使用 Azure 認證裝置入口網站來驗證裝置應用程式

## <a name="prepare-your-device"></a>準備您的裝置

在您的 IoT 隨插即用上執行的應用程式程式碼必須：

- 使用裝置布建 [服務 (DPS) ](../iot-dps/about-iot-dps.md)連接到 Azure IoT 中樞。
- 遵循 [IoT 隨插即用慣例](concepts-convention.md) 來實行遙測、屬性和命令。

應用程式是與作業系統分開安裝的軟體，或是與在裝置上閃爍的固件映射中的作業系統配套。

認證程式會驗證裝置是否 IoT 隨插即用相容，方法是驗證裝置實符合 [數位 Twins 定義語言 ](https://github.com/Azure/opendigitaltwins-dtdl) 中定義的遙測、屬性和命令 (DTDL) 裝置型號，而且該模型可在 [Azure IoT 公用模型存放庫](concepts-model-repository.md)中使用。

為了符合認證需求，您的裝置必須：

- 使用 [DPS](../iot-dps/about-iot-dps.md)連接到 Azure IoT 中樞。
- 遵循 IoT 隨插即用慣例來執行遙測、屬性或命令。
- 描述與 [DTDL v2](https://aka.ms/dtdl) 模型的裝置互動。
- 發佈[Azure IoT 公用模型存放庫](https://devicemodels.azureiotsolutions.com/)中的模型和所有必要的介面
- 在 dps 布建承載的 [dps 註冊](concepts-developer-guide-device-csharp.md#dps-payload) 期間傳送模型識別碼。
- 宣告 [MQTT 連接](concepts-developer-guide-device-csharp.md#model-id-announcement)期間的模型識別碼。

## <a name="test-with-the-azure-iot-extension-cli"></a>使用 Azure IoT 擴充功能 CLI 進行測試

[Azure IOT CLI 擴充](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/product?view=azure-cli-latest&preserve-view=true)功能可讓您在透過 Azure 認證裝置入口網站提交裝置以進行認證之前，驗證裝置的執行是否符合模型。

下列步驟示範如何使用 CLI 來準備和執行認證測試：

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>安裝適用于 Azure CLI 的 Azure IoT 擴充功能

請參閱安裝指示，以在您的環境中設定 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) 。

若要安裝 Azure IoT 擴充功能，請執行下列命令：

```azurecli
az extension add --name azure-iot
```

若要深入瞭解，請參閱 [適用于 Azure IoT 的 Azure CLI](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot?view=azure-cli-latest&preserve-view=true)。

### <a name="create-a-new-product-test"></a>建立新的產品測試

下列命令會使用具有對稱金鑰證明方法的 DPS 來建立測試：

- 建立要測試的新產品，並產生測試設定。 輸出會顯示裝置必須用於布建的 DPS 資訊：主要金鑰、裝置識別碼和識別碼範圍。
- 指定具有 DTDL 檔案的資料夾，以描述您的模型。

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> 當您使用 CLI 時，必須登 [入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) 您的訂用帳戶。

命令的 JSON 輸出包含 `primaryKey` 、 `registrationId` 和，以在 `scopeID` 您連接裝置時使用。

預期輸出：

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>連接您的裝置

使用上一個命令的 DPS 資訊輸出，將您的裝置連線到測試 IoT 中樞實例。

### <a name="manage-and-configure-the-product-tests"></a>管理及設定產品測試

當裝置連線並準備好與 IoT 中樞互動時，產生產品測試組態檔案。 若要建立檔案：

- 使用 `id` 上一個命令的輸出中的測試。
- 使用 `--wait` 參數來取得測試案例。

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

預期輸出：

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

您可以使用 `az iot product test case update` 命令來修改測試組態檔案。

### <a name="run-the-tests"></a>執行測試

當您產生測試設定之後，下一步就是執行測試。 使用與 `devicetestId` 先前命令相同的參數來執行測試。 請檢查測試結果，確定所有測試都有狀態 `Passed` 。

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

範例測試回合輸出

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>使用 Azure 認證裝置入口網站進行測試

下列步驟說明如何使用 [Azure 認證裝置入口網站](https://aka.ms/acdp) 來上架、註冊產品詳細資料、提交入門指南，以及執行認證測試。

### <a name="onboarding"></a>登入

若要使用 [認證入口網站](https://aka.ms/acdp)，您必須使用工作或學校租使用者中的 Azure Active Directory。

若要將模型發佈至 Azure IoT 公用模型儲存機制，您的帳戶必須是 [Microsoft 合作夥伴網路](https://partner.microsoft.com)的成員。 系統會檢查 Microsoft 合作夥伴網路識別碼是否存在，並在發佈至裝置目錄之前完全通過該帳戶。

### <a name="company-profile"></a>公司設定檔

您可以從左側導覽功能表管理公司設定檔。 公司設定檔包含公司 URL、電子郵件地址及公司標誌。 在您執行任何憑證作業之前，必須先在此頁面上接受程式合約。

公司設定檔資訊是在裝置目錄中的 [裝置描述] 展示中使用。

### <a name="create-new-project"></a>建立新專案

若要認證裝置，您必須先建立新專案。

流覽至 [認證入口網站](https://aka.ms/acdp)。 在 [ **專案** ] 頁面上，選取 [ *+ 建立新專案*]。 然後，輸入專案的名稱、裝置名稱，然後選取裝置類別。

您在認證程式期間提供的產品資訊分為四類：

- 裝置資訊。 收集裝置的相關資訊，例如其名稱、描述、認證和作業系統。
- 入門**指南。** 您必須將本指南提交為 PDF 檔，以供系統管理員核准，然後再發佈裝置。
- 行銷詳細資料。 為您的裝置提供客戶就緒的行銷資訊。 行銷資訊包括「描述」、「相片」和「散發者」。
- 其他產業認證。 此選用區段可讓您提供裝置已取得之任何其他認證的其他相關資訊。

### <a name="connect-and-test"></a>連接和測試

[連線和測試] 步驟會檢查您的裝置是否符合 IoT 隨插即用認證需求。

要完成的步驟有三個：

1. 連接及探索介面。 裝置必須透過 DPS 連接至 Azure IoT 認證服務。 選擇要使用的驗證方法 (x.509 憑證、對稱金鑰或可信賴平臺模組) ，並使用 DPS 資訊更新裝置應用程式。
1. 查看介面。 請檢查介面，並確定每一個都有適合測試的承載輸入。
1. 測試。 系統會測試每個裝置模型，以檢查模型中所述的遙測、屬性和命令是否遵循 IoT 隨插即用慣例。 測試完成時，請選取 [ **查看記錄** ] 連結，以查看裝置的遙測資料，以及傳送至 IoT 中樞裝置對應項屬性的原始資料。

### <a name="submit-and-publish"></a>提交併發行

最後一個必要階段是提交專案以供審查。 此步驟會通知 Azure 認證裝置團隊成員查看您的專案是否完整，包括裝置和行銷詳細資料，以及入門指南。 小組成員可能會在核准之前，先與您提出問題的公司電子郵件地址或編輯要求聯繫。

如果您的裝置需要進一步的手動驗證作為認證的一部分，您此時將會收到通知。

當裝置通過認證時，您可以選擇使用 [產品摘要] 頁面中的 [ **發佈至類別目錄** ] 功能，將您的產品詳細資料發佈至 Azure 認證裝置目錄。

## <a name="next-steps"></a>後續步驟

裝置提交完成後，您可以聯繫裝置認證小組， [iotcert@microsoft.com](mailto:iotcert@microsoft.com) 以繼續進行後續步驟，其中包括 Microsoft 合作夥伴網路成員資格驗證，以及使用者入門指南的評論。 滿足所有需求時，您可以選擇讓裝置包含在「 [Azure IoT 認證」裝置目錄](https://aka.ms/devicecatalog)中。
