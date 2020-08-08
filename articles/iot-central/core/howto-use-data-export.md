---
title: 從 IoT Central 匯出資料 |Microsoft Docs
description: 如何使用新的資料匯出，將您的 IoT 資料匯出至 Azure 和自訂雲端目的地。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 737fe4b334e60f1b51e8f60f39e8821588a6841c
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010288"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>使用資料匯出 (預覽將 IoT 資料匯出到雲端目的地) 

> [!Note]
> 正在尋找舊版資料匯出嗎？ 您可以在[這裡](./howto-export-data.md)找到資料匯出檔。 若要瞭解新的資料匯出和舊版資料匯出之間的差異，請參閱[比較表](#comparison-of-legacy-data-export-and-new-data-export)。

本文說明如何使用 Azure IoT Central 中的新資料匯出預覽功能。 您可以使用這項功能，將已篩選和擴充的 IoT 資料持續匯出至您的雲端服務。 您可以使用資料匯出，以近乎即時的方式將變更推送到雲端解決方案的其他部分，以取得暖路徑深入解析、分析和儲存體。 

 例如，您可以：
-   以近乎即時的方式連續匯出 JSON 格式的遙測資料和屬性變更
-   篩選這些資料流程，以匯出符合自訂條件的特定功能
-   使用來自裝置的自訂值和屬性值，來充實資料流程
-   將此資料傳送至目的地，例如 Azure 事件中樞、Azure 服務匯流排、Azure Blob 儲存體和 webhook 端點

> [!Note]
> 當您開啟資料匯出時，只會從該時間開始取得資料。 目前，當資料匯出已關閉時，無法取回資料。 若要保留更多歷程記錄資料，請提早開啟資料匯出。

## <a name="prerequisites"></a>必要條件

您必須是 IoT Central 應用程式中的系統管理員，或具有資料匯出許可權。

## <a name="set-up-export-destination"></a>設定匯出目的地

在設定資料匯出之前，您的匯出目的地必須存在。 以下是可用的目的地類型：
  - Azure 事件中心
  - Azure 服務匯流排佇列
  - Azure 服務匯流排主題
  - Azure Blob 儲存體
  - Webhook

### <a name="create-an-event-hubs-destination"></a>建立事件中樞目的地

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞]**** 以建立事件中樞執行個體。

3. 產生您將在 IoT Central 中用來設定資料匯出的金鑰。 
    - 按一下您建立的事件中樞實例。 
    - 按一下 [**設定/共用存取原則**]。 
    - 建立新的金鑰，或選擇具有 [**傳送**] 許可權的現有金鑰。 
    - 複製主要連接字串或次要連接字串。 您將使用此設定 IoT Central 中的新目的地。

### <a name="create-a-service-bus-queue-or-topic-destination"></a>建立服務匯流排的佇列或主題目的地

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。

2. 若要建立要匯出至的佇列或主題，請移至您的服務匯流排命名空間，然後選取 [ **+ 佇列**] 或 [ **+ 主題**]。

3. 產生您將在 IoT Central 中用來設定資料匯出的金鑰。 
    - 按一下您建立的佇列或主題。 
    - 按一下 [**設定/共用存取原則**]。 
    - 建立新的金鑰，或選擇具有 [**傳送**] 許可權的現有金鑰。 
    - 複製主要連接字串或次要連接字串。 您將使用此設定 IoT Central 中的新目的地。

### <a name="create-an-azure-blob-storage-destination"></a>建立 Azure Blob 儲存體目的地

如果您沒有可匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 資料匯出只能將資料寫入支援區塊 blob 的儲存體帳戶。 下列清單顯示已知的相容儲存體帳戶類型：

    |效能層級|帳戶類型|
    |-|-|
    |標準|一般用途 V2|
    |標準|一般用途 V1|
    |標準|Blob 儲存體|
    |Premium|區塊 Blob 儲存體|

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務]**** 下，選取 [瀏覽 Blob]****。 選取頂端的 [+ 容器]**** 以建立新的容器。

3. 前往 [**設定]/[存取金鑰**]，為您的儲存體帳戶產生連接字串。 複製兩個連接字串的其中一個。

### <a name="create-a-webhook-endpoint"></a>建立 webhook 端點
您可以提供可公開使用的 HTTP 端點，以供匯出至資料。 您可以使用 RequestBin 建立測試 webhook 端點。 請記住，在要求受到節流之前，RequestBin 有一個設定的要求限制。

1. 開啟 [RequestBin](https://requestbin.net/)。
2. 建立新的 RequestBin，並複製 Bin URL。

## <a name="set-up-data-export"></a>設定資料匯出

現在您已有目的地可匯出資料，請遵循下列步驟來設定資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**資料匯出**]。

    > [!Tip]
    > 如果您在左窗格中沒有看到 [**資料匯出**]，表示您沒有在應用程式中設定資料匯出的許可權。 請連絡系統管理員來設定資料匯出。

3. 選取 [ **+ 新增匯出**] 按鈕。 

4. 輸入新匯出的 [顯示名稱]，並確定已開啟 [**啟用**] 切換來進行資料流程。

## <a name="1-choose-the-type-of-data-to-export"></a>1. 選擇要匯出的資料類型
您可以選擇連續匯出不同類型的資料。 以下是支援的資料類型：

| 資料類型 | 描述 | 資料格式 |
| :------------- | :---------- | :----------- |
|  遙測 | 以近乎即時的方式從裝置匯出遙測訊息。 每個匯出的訊息都會包含原始裝置訊息的完整內容（正規化）。   |  [遙測訊息格式](#telemetry-format)   |
| 屬性變更 | 以近乎即時的方式將變更匯出至裝置和雲端屬性。 對於唯讀裝置屬性，系統將會匯出回報值的變更。 如果是讀寫屬性，則會匯出所報告和所需的值。 | [屬性變更訊息格式](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (選擇性) 新增篩選
新增篩選，以減少根據篩選準則匯出的資料量。 有不同類型的篩選器可用於匯出的每一種資料類型。

### <a name="telemetry-filters"></a>遙測篩選器
  - **功能篩選**：如果您在下拉式清單中選擇遙測專案，則匯出的資料流程只會包含符合篩選準則的遙測。 如果您在下拉式清單中選擇 [裝置] 或 [雲端] 內容專案，則匯出的串流只會包含來自具有符合篩選準則之屬性的裝置遙測。
  - **訊息屬性篩選**：使用裝置 sdk 的裝置可在每個遙測訊息上傳送*訊息屬性*或*應用程式屬性*，這是一組索引鍵/值組，通常用來以自訂識別碼標記訊息。 您可以在您要尋找的訊息屬性索引鍵中輸入，然後指定條件，來建立訊息屬性篩選。 只會匯出具有符合指定篩選準則之訊息屬性的遙測訊息。 僅支援字串比較運算子， (equals、不等於、contains、不包含、exists) 不存在。 [深入瞭解 IoT 中樞檔中的應用程式屬性](../../iot-hub/iot-hub-devguide-messages-construct.md)。

### <a name="property-changes-filters"></a>屬性變更篩選
**屬性篩選**：選擇下拉式清單中的屬性專案，匯出的資料流程只會包含符合篩選準則之所選屬性的變更。

## <a name="3-optional-add-enrichments"></a>3. (選擇性) 新增擴充
新增擴充，以在索引鍵/值組中以額外的中繼資料擴充匯出的訊息。 這些是遙測和屬性變更資料類型的可用擴充：
  - **自訂字串**：將自訂靜態字串新增至每個訊息。 輸入任何金鑰，並輸入任何字串值。
  - **屬性**：將目前裝置的回報屬性或雲端屬性值新增至每個訊息。 輸入任何金鑰，然後選擇 [裝置] 或 [雲端] 屬性。 如果匯出的訊息來自沒有指定之裝置或雲端屬性的裝置，則匯出的訊息將不會有該擴充。

## <a name="4-add-destinations"></a>4. 新增目的地
建立新的目的地，或新增您已經建立的目的地。 
  
1. 按一下 [**建立新的目的地**] 連結。 填寫下列資訊︰
    - **目的地名稱**：目的地的顯示名稱 IoT Central
    - **目的地類型**：選擇目的地的類型。 如果您還沒有這麼[做，請設定您的匯出目的地](#set-up-export-destination)
    - 針對 Azure 事件中樞，Azure 服務匯流排佇列或主題中，貼上資源的連接字串。 
    - 針對 Azure Blob 儲存體，貼上資源的連接字串，然後輸入容器名稱， (區分大小寫) 。
    - 針對 [Webhook]，貼上您 webhook 端點的 [回呼 URL]。 
    - 按一下 [建立]

2. 按一下 [ **+ 目的地**]，然後從下拉式清單中選擇目的地。 您最多可以將5個目的地新增至單一匯出。

3. 完成匯出設定後，請按一下 [**儲存**]。 幾分鐘後，您的資料就會出現在您的目的地。

## <a name="export-contents-and-format"></a>匯出內容和格式

對於事件中樞和服務匯流排目的地，資料會以近乎即時的方式匯出。 資料位於訊息本文中，並採用以 UTF-8 編碼的 JSON 格式。 如需範例，請參閱下文。

對於 Blob 儲存體，資料會每分鐘匯出一次，每個檔案都包含自上次匯出檔案之後的變更批次。 匯出的資料會以 JSON 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 屬性變更： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要流覽 Azure 入口網站中匯出的檔案，請流覽至該檔案，然後選取 [**編輯 blob** ] 索引標籤。

針對 webhook 目的地，資料也會以近乎即時的方式匯出。 資料在訊息內文中的格式與事件中樞和服務匯流排相同。


## <a name="telemetry-format"></a>遙測格式
每個匯出的訊息都包含標準化形式的完整訊息，裝置會以 JSON 格式在訊息本文中傳送，並以 UTF-8 編碼。 包含在每個訊息中的其他資訊包括：

- `applicationId`IoT Central 應用程式的
- `messageSource`這是用來匯出遙測資料的*遙測*
- `deviceId`傳送遙測訊息的裝置
- `schema`是裝載架構的名稱和版本
- `templateId`是與裝置相關聯的裝置範本識別碼
- `enrichments`是在匯出時設定的任何擴充
- `messageProperties`是裝置連同訊息一起傳送的其他資料片段。 這也稱為*應用程式屬性*，[從 IoT 中樞檔深入瞭解](../../iot-hub/iot-hub-devguide-messages-construct.md)。

針對事件中樞和服務匯流排，IoT Central 會在收到來自裝置的訊息之後，快速匯出新訊息。

針對 Blob 儲存體，每分鐘會批次處理並匯出訊息一次。

下列範例顯示匯出的遙測訊息：

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
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

每個訊息或記錄都代表裝置或雲端屬性的一項變更。 針對裝置屬性，只有回報值中的變更會匯出為個別的訊息。 匯出的訊息中包含的其他資訊包括：

- `applicationId`IoT Central 應用程式的
- `messageSource`這是用來匯出屬性變更資料的*屬性*
- `messageType`也就是*cloudPropertyChange*或*devicePropertyDesiredChange*， *devicePropertyReportedChange*
- `deviceId`其屬性已變更的裝置
- `schema`是裝載架構的名稱和版本
- `templateId`是與裝置相關聯的裝置範本識別碼
- `enrichments`是在匯出時設定的任何擴充

針對事件中樞和服務匯流排，IoT Central 會以近乎即時的方式，將新的訊息資料匯出至事件中樞或服務匯流排佇列或主題。

針對 Blob 儲存體，每分鐘會批次處理並匯出訊息一次。

下列範例顯示在 Azure Blob 儲存體中收到的已匯出屬性變更訊息。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>舊版資料匯出和新資料匯出的比較
這是一個表格，其中反白顯示舊版資料匯出和新資料匯出之間的差異。 您可以在[這裡](howto-export-data.md)瞭解舊版資料匯出。

| 功能  | 舊版資料匯出 | 新增資料匯出 |
| :------------- | :---------- | :----------- |
| 可用的資料類型 | 遙測、裝置、裝置範本 | 遙測，屬性變更 |
| 篩選 | None | 視匯出的資料類型而定。 針對遙測，依遙測篩選、訊息屬性、屬性值 |
| 擴充 | None | 使用裝置上的自訂字串或屬性值進行擴充 |
| Destinations | Azure 事件中樞，Azure 服務匯流排佇列和主題，Azure Blob 儲存體 | 與舊版資料匯出和 webhook 相同| 
| 值得注意的限制 | 每個應用程式5個匯出，每個匯出1個目的地 | 10個匯出-每個應用程式的目的地連線數 | 

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用新的資料匯出，接下來請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何在 IoT Central 中流量分析](./howto-create-analytics.md)
