---
title: 將資料匯出至 Azure Blob 儲存體 | Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至「Azure Blob 儲存體」
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 95874ed505886f92c59c78a8aab6354a84bfba7f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985657"
---
# <a name="export-your-data-to-azure-blob-storage"></a>將資料匯出至 Azure Blob 儲存體

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*此主題適用於系統管理員。*

本文說明如何使用 Azure 中的連續資料匯出功能 IoT Central，定期將資料匯出至您的**Azure Blob 儲存體帳戶**或**Azure Data Lake Storage Gen2 儲存體帳戶**。 您可以將**度量**、**裝置**和**裝置範本**匯出至 JSON 或 Apache Avro 格式的檔案。 所匯出的資料可用來進行冷路徑分析 (例如，在 Azure Machine Learning 中為模型定型) 或在 Microsoft Power BI 中進行長期趨勢分析。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。


## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員


## <a name="set-up-export-destination"></a>設定匯出目的地

如果您沒有可匯出的現有儲存體，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)。

    > [!Note] 
    > 如果您選擇將資料匯出至 ADLS v2 儲存體帳戶，您必須選擇 [**帳戶種類**] 做為 [ **BlobStorage**]。 

    > [!Note] 
    > 您可以將資料匯出至訂用帳戶中不同于 IoT Central 應用程式的儲存體帳戶。 在此情況下，您將使用連接字串來進行連線。

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。

## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在您已有要匯出資料的儲存體目的地，請遵循下列步驟來設定連續資料匯出。 

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**資料匯出**]。

    > [!Note]
    > 如果您在左窗格中沒有看到 [資料匯出]，則您不是應用程式中的系統管理員。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇 [ **Azure Blob 儲存體**] 做為匯出的目的地。 

    > [!NOTE] 
    > 每一應用程式的匯出數目上限是 5。 

    ![建立新的連續資料匯出](media/howto-export-data/export-new2.png)

4. 在下拉式清單方塊中，選取您的**儲存體帳戶命名空間**。 您也可以挑選清單中的最後一個選項，也就是 [輸入連接字串]。 

    > [!NOTE] 
    > 您只會在與**IoT Central 應用程式相同的訂**用帳戶中看到儲存體帳戶命名空間。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。

    > [!NOTE] 
    > 如果是 7 天試用版應用程式，則設定連續資料匯出的唯一方式是透過連接字串。 這是因為 7 天試用版應用程式並沒有相關聯的 Azure 訂用帳戶。

    ![建立新的匯出至 Blob](media/howto-export-data/export-create-blob2.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 若要取得儲存體帳戶的連接字串，請移至 Azure 入口網站中的儲存體帳戶：在 [**設定**] 底下，選取 [**存取金鑰**]-複製 [key1 連接字串] 或 [key2 連接字串]
 
6. 從下拉式清單方塊中選擇容器。 如果您沒有容器，請移至您在 Azure 入口網站中的儲存體帳戶：
    - 在 [ **Blob 服務**] 底下，選取 [ **blob**]。 按一下 [ **+ 容器**]，並為您的容器提供名稱。 選擇適用于您資料的公用存取層級（任何都可以使用連續資料匯出）。 從[Azure 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)檔深入瞭解。

7. 選擇您偏好的**資料格式**： JSON 或[Apache Avro](https://avro.apache.org/docs/current/index.html)格式。

8. 在 [要匯出的資料] 下，透過將類型設定為 [開啟] 以指定每種要匯出的資料類型。

9. 若要開啟連續資料匯出，請確定 [**資料匯出**切換] 為 [**開啟**]。 選取 [儲存]。

   ![設定連續資料匯出](media/howto-export-data/export-list-blob2.png)

10. 幾分鐘後，您的資料就會出現在您的儲存體帳戶中。


## <a name="path-structure"></a>路徑結構

度量、裝置及裝置範本資料會以每分鐘一次的頻率，匯出到您的儲存體帳戶，其中每個檔案都會包含自上次匯出檔案之後的一批變更。 匯出的資料會以 JSON 或 Avro 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：
- 訊息： {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 裝置： {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 裝置範本： {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

您可以流覽至檔案，然後選擇 [**編輯 blob** ] 索引標籤，以流覽 Azure 入口網站中匯出的檔案。

## <a name="data-format"></a>資料格式 

### <a name="measurements"></a>度量

匯出的度量資料包含 IoT Central 在該時間期間從所有裝置收到的所有新訊息。 匯出之檔案所使用的格式與 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)匯出至 Blob 儲存體的訊息檔案格式相同。

> [!NOTE]
> 確定您的裝置正在傳送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16`，`utf-32`）的訊息。 如需範例，請參閱[IoT 中樞檔](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body)。

> [!NOTE]
> 傳送量測的裝置會以裝置識別碼來表示 (請參閱以下各節)。 若要取得裝置名稱，請匯出裝置快照集。 請使用與裝置記錄之 **deviceId** 相符的 **connectionDeviceId** 來相互關聯每個訊息記錄。

下列範例顯示已解碼之 Avro 檔案中的記錄：

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>裝置

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置的單一快照集。 每個裝置包含：
- IoT Central 中裝置的 `id`
- 裝置的 `name`
- 來自[裝置佈建服務](/azure/iot-central/core/howto-connect-nodejs) 的 `deviceId`
- 裝置範本資訊
- 屬性值
- 設定值

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的裝置。
- 自上一個快照集以來屬性與設定值已變更的裝置。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置則不會匯出。 目前，快照集沒有已刪除之裝置的指標。
>
> 每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，請匯出裝置範本快照集。

匯出的檔案包含每筆記錄一行。 下列範例顯示 Avro 格式的記錄，已解碼：

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>裝置範本

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置範本的單一快照集。 每個裝置範本包含：
- 裝置範本的 `id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 量測資料類型和最小值/最大值。
- 屬性資料類型和預設值。
- 設定資料類型和預設值。

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的新裝置範本。
- 自上一個快照集以來量測、屬性與設定定義已變更的裝置範本。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置範本則不會匯出。 目前，快照集沒有已刪除之裝置範本的指標。

匯出的檔案包含每筆記錄一行。 下列範例顯示 Avro 格式的記錄，已解碼：

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>讀取匯出的 Avro 檔案

Avro 是二進位格式，因此讀取檔案時，無法以檔案原始狀態來讀取。 該檔案可解碼為 JSON 格式。 下列範例說明如何剖析度量、裝置及裝置範本 Avro 檔案。 這些範例對應到上一節中所述的範例。

### <a name="read-avro-files-by-using-python"></a>使用 Python 來讀取 Avro 檔案

#### <a name="install-pandas-and-the-pandavro-package"></a>安裝 pandas 與 pandavro 套件

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>使用 C# 來讀取 Avro 檔案

#### <a name="install-the-microsofthadoopavro-package"></a>安裝 Microsoft.Hadoop.Avro 套件

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>使用 Javascript 來讀取 Avro 檔案

#### <a name="install-the-avsc-package"></a>安裝 avsc 套件

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and version properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>後續步驟

現在您已經知道如何匯出資料，請繼續下一個步驟：

> [!div class="nextstepaction"]
> [如何在 Power BI 中將資料視覺化](howto-connect-powerbi.md)
