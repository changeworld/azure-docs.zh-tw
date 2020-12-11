---
title: 從 Azure IoT Central 匯出資料 |Microsoft Docs
description: 如何使用新的資料匯出，將您的 IoT 資料匯出至 Azure 和自訂雲端目的地。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/05/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1
ms.openlocfilehash: b84f1efd77ca757fd2ceaa8bb5605e3fc78297d0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032366"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>使用資料匯出將 IoT 資料匯出至雲端目的地

> [!Note]
> 本文說明 IoT Central 中的資料匯出功能。
>
> - 如需舊版資料匯出功能的相關資訊，請參閱 [使用資料匯出 (舊版) 將 IoT 資料匯出至雲端目的地 ](./howto-export-data-legacy.md)。
> - 若要瞭解資料匯出與舊版資料匯出功能之間的差異，請參閱下 [表中的比較表](#comparison-of-legacy-data-export-and-data-export) 。

本文說明如何使用 Azure IoT Central 中的新資料匯出功能。 使用此功能可從您的 IoT Central 應用程式持續匯出已篩選和擴充的 IoT 資料。 資料匯出會以近乎即時的方式將變更推送至雲端解決方案的其他部分，以取得暖路徑見解、分析和儲存體。

例如，您可以：

- 以近乎即時的方式連續匯出 JSON 格式的遙測資料和屬性變更。
- 篩選資料串流，以匯出符合自訂條件的資料。
- 使用來自裝置的自訂值和屬性值來擴充資料流程。
- 將資料傳送至目的地，例如 Azure 事件中樞、Azure 服務匯流排、Azure Blob 儲存體和 webhook 端點。

> [!Tip]
> 當您開啟資料匯出時，您只會取得該時間的資料。 目前，資料匯出關閉時無法取出資料。 若要保留更多歷程記錄資料，請提早開啟資料匯出。

## <a name="prerequisites"></a>必要條件

若要使用資料匯出功能，您必須有 [V3 應用程式](howto-get-app-info.md)，而且必須擁有 [資料匯出](howto-manage-users-roles.md) 許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

設定資料匯出之前，您必須先有匯出目的地。 目前提供下列目的地類型：

- Azure 事件中心
- Azure 服務匯流排佇列
- Azure 服務匯流排主題
- Azure Blob 儲存體
- Webhook

### <a name="create-an-event-hubs-destination"></a>建立事件中樞目的地

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

1. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞] 以建立事件中樞執行個體。

1. 當您在 IoT Central 中設定資料匯出時，請產生要使用的金鑰：

    - 選取您建立的事件中樞實例。
    - 選取 [ **設定] > 共用存取原則**。
    - 建立新的金鑰，或選擇具有 **傳送** 許可權的現有金鑰。
    - 複製主要連接字串或次要連接字串。 您可以使用此連接字串，在 IoT Central 中設定新的目的地。
    - 或者，您可以為整個事件中樞命名空間產生連接字串：
        1. 移至 Azure 入口網站中的事件中樞命名空間。
        2. 在 [**設定**] 底下，選取 [**共用存取原則**]
        3. 建立新的金鑰，或選擇具有 **傳送** 許可權的現有金鑰。
        4. 複製主要連接字串或次要連接字串
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>建立服務匯流排佇列或主題目的地

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。

1. 若要建立要匯出的佇列或主題，請移至您的服務匯流排命名空間，然後選取 [ **+ 佇列** ] 或 [ **+ 主題**]。

1. 當您在 IoT Central 中設定資料匯出時，請產生要使用的金鑰：

    - 選取您建立的佇列或主題。
    - 選取 [ **設定]/[共用存取原則**]。
    - 建立新的金鑰，或選擇具有 **傳送** 許可權的現有金鑰。
    - 複製主要連接字串或次要連接字串。 您可以使用此連接字串，在 IoT Central 中設定新的目的地。
    - 或者，您可以為整個服務匯流排命名空間產生連接字串：
        1. 在 Azure 入口網站中，移至您的服務匯流排命名空間。
        2. 在 [**設定**] 底下，選取 [**共用存取原則**]
        3. 建立新的金鑰，或選擇具有 **傳送** 許可權的現有金鑰。
        4. 複製主要連接字串或次要連接字串

### <a name="create-an-azure-blob-storage-destination"></a>建立 Azure Blob 儲存體目的地

如果您沒有要匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的 [Azure Blob 儲存體帳戶](../../storage/blobs/storage-quickstart-blobs-portal.md) 或 [Azure Data Lake Storage v2 儲存體帳戶](../../storage/common/storage-account-create.md)。 資料匯出只能將資料寫入支援區塊 blob 的儲存體帳戶。 下列清單顯示已知的相容儲存體帳戶類型：

    |效能層級|帳戶類型|
    |-|-|
    |標準|一般用途 V2|
    |標準|一般用途 V1|
    |標準|Blob 儲存體|
    |進階|區塊 Blob 儲存體|

1. 若要在儲存體帳戶中建立容器，請移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。

1. 前往 [ **設定 > 存取金鑰**]，以產生儲存體帳戶的連接字串。 複製這兩個連接字串的其中一個。

### <a name="create-a-webhook-endpoint"></a>建立 webhook 端點

您可以將資料匯出至公開可用的 HTTP webhook 端點。 您可以使用 [RequestBin](https://requestbin.net/)來建立測試 webhook 端點。 當達到要求限制時，RequestBin 會對要求進行節流：

1. 開啟 [RequestBin](https://requestbin.net/)。
2. 建立新的 RequestBin，並複製 **Bin URL**。 當您測試資料匯出時，會使用此 URL。

## <a name="set-up-data-export"></a>設定資料匯出

現在您已有目的地可將資料匯出至，請在 IoT Central 應用程式中設定資料匯出：

1. 登入您的 IoT Central 應用程式。

1. 在左窗格中，選取 [ **資料匯出**]。

    > [!Tip]
    > 如果您在左窗格中看不到 [ **資料匯出** ]，則表示您沒有在應用程式中設定資料匯出的許可權。 請連絡系統管理員來設定資料匯出。

1. 選取 [ **+ 新增匯出**]。

1. 輸入新匯出的顯示名稱，並確定 **已啟用** 資料匯出。

1. 選擇要匯出的資料類型。 下表列出支援的資料匯出類型：

    | 資料類型 | 描述 | 資料格式 |
    | :------------- | :---------- | :----------- |
    |  遙測 | 以近乎即時的方式從裝置匯出遙測訊息。 每個匯出的訊息都包含原始裝置訊息的完整內容，並正規化。   |  [遙測訊息格式](#telemetry-format)   |
    | 屬性變更 | 以近乎即時的方式將變更匯出至裝置和雲端屬性。 針對唯讀裝置屬性，系統會匯出回報值的變更。 若為讀寫屬性，則會匯出報告和所需的值。 | [屬性變更訊息格式](#property-changes-format) |

<a name="DataExportFilters"></a>
1. （選擇性）新增篩選器來減少匯出的資料量。 有不同類型的篩選適用于每種資料匯出類型：

    若要篩選遙測，您可以：

    - **篩選** 匯出的資料流程，使其只包含符合裝置名稱、裝置識別碼和裝置範本篩選準則之裝置的遙測。
    - **篩選** 功能：如果您在 [ **名稱** ] 下拉式清單中選擇遙測專案，則匯出的資料流程只會包含符合篩選準則的遙測。 如果您在 [ **名稱** ] 下拉式清單中選擇 [裝置] 或 [雲端] 屬性專案，則匯出的資料流程只會包含具有符合篩選準則之屬性之裝置的遙測。
    - **訊息屬性篩選**：使用裝置 sdk 的裝置可以在每個遙測訊息上傳送 *訊息屬性* 或 *應用程式屬性* 。 這些屬性是一組索引鍵/值組，會以自訂識別碼標記訊息。 若要建立訊息屬性篩選，請輸入您要尋找的訊息屬性索引鍵，然後指定條件。 只會匯出具有符合指定篩選準則之屬性的遙測訊息。 以下是支援的字串比較運算子： equals、不等於、contains、不包含、exists、不存在。 [深入瞭解 IoT 中樞檔中的應用程式屬性](../../iot-hub/iot-hub-devguide-messages-construct.md)。

    若要篩選屬性變更，請使用 **功能篩選**。 選擇下拉式清單中的屬性專案。 匯出的資料流程只包含符合篩選準則之選定屬性的變更。

<a name="DataExportEnrichmnents"></a>
1. （選擇性）使用額外的索引鍵/值組中繼資料來擴充匯出的訊息。 下列擴充適用于遙測和屬性變更資料匯出類型：

    - **自訂字串**：將自訂靜態字串加入至每個訊息。 輸入任何金鑰，並輸入任何字串值。
    - **屬性**：將目前裝置回報的屬性或雲端屬性值新增至每個訊息。 輸入任何金鑰，然後選擇裝置或雲端屬性。 如果匯出的訊息來自沒有指定屬性的裝置，則匯出的訊息不會取得擴充。

1. 加入新的目的地，或新增已建立的目的地。 選取 [ **建立新的一個** ] 連結並新增下列資訊：

    - **目的地名稱**： IoT Central 中目的地的顯示名稱。
    - **目的地類型**：選擇目的地的類型。 如果尚未設定目的地，請參閱 [設定匯出目的地](#set-up-export-destination)。
    - 針對 Azure 事件中樞、Azure 服務匯流排佇列或主題，貼上資源的連接字串，並視需要輸入區分大小寫的事件中樞、佇列或主題名稱。
    - 針對 Azure Blob 儲存體，貼上資源的連接字串，並視需要輸入區分大小寫的容器名稱。
    - 針對 Webhook，貼上 webhook 端點的回呼 URL。 您可以選擇性地 (OAuth 2.0 與授權權杖) 設定 webhook 授權，並新增自訂標頭。 
        - 針對 OAuth 2.0，僅支援用戶端認證流程。 儲存目的地之後，IoT Central 會與您的 OAuth 提供者進行通訊，以抓取授權權杖。 此權杖會附加至每個傳送至此目的地之訊息的「授權」標頭。
        - 針對授權權杖，您可以針對每個傳送至此目的地的訊息，指定將直接附加至「授權」標頭的權杖值。
    - 選取 [建立]。

1. 選取 [ **+ 目的地** ]，然後從下拉式清單中選擇目的地。 您最多可以在單一匯出中新增五個目的地。

1. 當您完成匯出的設定時，請選取 [ **儲存**]。 幾分鐘後，您的資料就會出現在您的目的地中。

## <a name="export-contents-and-format"></a>匯出內容和格式

### <a name="azure-blob-storage-destination"></a>Azure Blob 儲存體目的地

資料會每分鐘匯出一次，每個檔案都包含自上一次匯出之後的變更批次。 匯出的資料會以 JSON 格式儲存。 您儲存體帳戶中匯出資料的預設路徑為：

- 遙測： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 屬性變更： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要流覽 Azure 入口網站中匯出的檔案，請流覽至該檔案，然後選取 [ **編輯 blob**]。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure 事件中樞和 Azure 服務匯流排目的地

資料會以近乎即時的方式匯出。 資料會在訊息本文中，並以 JSON 格式編碼為 UTF-8。

訊息的注釋或系統屬性包包含 `iotcentral-device-id` 、 `iotcentral-application-id` 、 `iotcentral-message-source` 和 `iotcentral-message-type` 欄位，這些欄位的值與訊息本文中的對應欄位相同。

### <a name="webhook-destination"></a>Webhook 目的地

針對 webhook 目的地，資料也會以近乎即時的方式匯出。 訊息本文中的資料會與事件中樞和服務匯流排的格式相同。

### <a name="telemetry-format"></a>遙測格式

每個匯出的訊息都包含在訊息本文中傳送之完整訊息的標準化形式。 此訊息為 JSON 格式，並編碼為 UTF-8。 每則訊息中的資訊包括：

- `applicationId`： IoT Central 應用程式的識別碼。
- `messageSource`：訊息的來源 `telemetry` 。
- `deviceId`：傳送遙測訊息的裝置識別碼。
- `schema`：裝載架構的名稱和版本。
- `templateId`：與裝置相關聯之裝置範本的識別碼。
- `enrichments`：在匯出時設定的任何擴充。
- `messageProperties`：裝置隨訊息傳送的其他屬性。 這些屬性有時稱為 *應用程式屬性*。 [深入瞭解 IoT 中樞](../../iot-hub/iot-hub-devguide-messages-construct.md)檔。

針對事件中樞和服務匯流排，IoT Central 會在收到來自裝置的訊息之後，快速匯出新訊息。 在 [使用者屬性] (也稱為每個訊息) 的應用程式屬性， `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` 會自動包含、和。

針對 Blob 儲存體，每分鐘會批次處理和匯出訊息一次。

下列範例顯示匯出的遙測訊息：

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>屬性變更格式

每個訊息或記錄都代表一個對裝置或雲端屬性的變更。 針對裝置屬性，只會將回報值的變更匯出為個別的訊息。 匯出訊息中的資訊包括：

- `applicationId`： IoT Central 應用程式的識別碼。
- `messageSource`：訊息的來源 `properties` 。
- `messageType`： `cloudPropertyChange` 、 `devicePropertyDesiredChange` 或 `devicePropertyReportedChange` 。
- `deviceId`：傳送遙測訊息的裝置識別碼。
- `schema`：裝載架構的名稱和版本。
- `templateId`：與裝置相關聯之裝置範本的識別碼。
- `enrichments`：在匯出時設定的任何擴充。

針對事件中樞和服務匯流排，IoT Central 會以近乎即時的方式將新的訊息資料匯出至事件中樞或服務匯流排佇列或主題。 在 [使用者屬性] (也稱為每個訊息) 的應用程式屬性， `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` `iotcentral-message-type` 會自動包含、、和。

針對 Blob 儲存體，每分鐘會批次處理和匯出訊息一次。

下列範例顯示 Azure Blob 儲存體中收到的已匯出屬性變更訊息。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>舊版資料匯出和資料匯出的比較

下表顯示 [舊版資料匯出](howto-export-data-legacy.md) 與新的資料匯出功能之間的差異：

| 功能  | 舊版資料匯出 | 新增資料匯出 |
| :------------- | :---------- | :----------- |
| 可用的資料類型 | 遙測、裝置、裝置範本 | 遙測，屬性變更 |
| 篩選 | 無 | 取決於匯出的資料類型。 針對遙測，依遙測篩選、訊息屬性、屬性值 |
| 擴充 | 無 | 使用裝置上的自訂字串或屬性值進行擴充 |
| 目的地 | Azure 事件中樞、Azure 服務匯流排佇列和主題，Azure Blob 儲存體 | 與舊版資料匯出和 webhook 相同|
| 支援的應用程式版本 | V2、V3 | 僅限第 3 版 |
| 值得注意的限制 | 每個應用程式5個匯出，每個匯出1個目的地 | 10個匯出-每個應用程式的目的地連線數 |

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用新的資料匯出，建議的下一個步驟是瞭解 [如何在 IoT Central 中流量分析](./howto-create-analytics.md)
