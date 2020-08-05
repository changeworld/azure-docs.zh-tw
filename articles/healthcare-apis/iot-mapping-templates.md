---
title: 概念：在適用于 FHIR 的 Azure IoT 連接器中對應範本 (預覽的) 功能 Azure API for FHIR
description: 瞭解如何在適用于 FHIR (preview) 的 Azure IoT 連接器中建立兩種類型的對應範本。 裝置對應範本會將裝置資料轉換成標準化結構描述。 FHIR 對應範本會將標準化的訊息轉換成 FHIR 型觀察資源。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: da5eb43f8bc2fc8b4ac213f6ff90464de5995a47
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553641"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>適用于 FHIR (預覽) 對應範本的 Azure IoT Connector
本文詳細說明如何使用對應範本來設定適用于 FHIR 的 Azure IoT 連接器 *。

適用于 FHIR 的 Azure IoT 連接器需要兩種類型的 JSON 型對應範本。 第一種類型「**裝置對應**」負責對應傳送至 `devicedata` Azure 事件中樞端點的裝置承載。 它會將類型、裝置識別碼、測量日期時間和量值 () 中解壓縮。 第二種類型（ **FHIR 對應**）控制 FHIR 資源的對應。 它允許設定觀察期間的長度、用來儲存值的 FHIR 資料類型，以及術語代碼 (s) 。 

對應範本會根據其類型組成 JSON 檔。 然後，這些 JSON 檔會透過 Azure 入口網站新增至您的 Azure IoT 連接器以進行 FHIR。 裝置對應檔會透過 **[設定]** [對應] 頁面，以及透過 [**設定 FHIR 對應**] 頁面的 FHIR 對應檔來新增。

> [!NOTE]
> 對應範本會儲存在基礎 blob 儲存體中，並在每次計算執行時從 blob 載入。 更新之後，它們應該會立即生效。 

## <a name="device-mapping"></a>裝置對應
裝置對應提供將裝置內容解壓縮為一般格式以供進一步評估的對應功能。 每個收到的訊息都會針對所有範本進行評估。 這個方法可讓單一輸入訊息投射到多個輸出訊息，稍後會對應到 FHIR 中的不同觀察。 結果是正規化的資料物件，代表範本所剖析的值或值。 正規化資料模型有幾個必要的屬性，必須加以找到和解壓縮：

| 屬性 | 描述 |
| - | - |
|**型別**|用來分類度量的名稱/類型。 這個值是用來系結至所需的 FHIR 對應範本。  多個範本可以輸出到相同的類型，讓您能夠將不同的標記法對應至單一一般輸出。|
|**OccurenceTimeUtc**|測量發生的時間。|
|**DeviceId**|裝置的識別碼。 此值應符合目的地 FHIR 伺服器上存在的裝置資源上的識別碼。|
 |**屬性**|解壓縮至少一個屬性，使值可以儲存在所建立的觀測資源中。  屬性是在正規化期間解壓縮的金鑰值組集合。|

以下是正規化期間所發生之情況的概念範例。

![正規化範例](media/concepts-iot-mapping-templates/normalization-example.png)

內容承載本身是 Azure 事件中樞訊息，由三個部分組成： Body、Properties 和 SystemProperties。 `Body`是代表 utf-8 編碼字串的位元組陣列。 在範本評估期間，位元組陣列會自動轉換成字串值。 `Properties`這是訊息建立者所使用的索引鍵值集合。 `SystemProperties`也是 Azure 事件中樞架構所保留的索引鍵值集合，其中包含自動填入的專案。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>使用 JSON 路徑進行對應
目前支援的兩個裝置內容範本類型依賴 JSON 路徑，以符合所需的範本和已解壓縮的值。 如需 JSON 路徑的詳細資訊，請參閱[這裡](https://goessner.net/articles/JsonPath/)。 這兩種範本類型都使用[json .net 執行](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)來解析 json 路徑運算式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate 允許使用 JSON 路徑來比對事件中樞訊息中的值，並將其解壓縮。

| 屬性 | 描述 |<div style="width:150px">範例</div>
| --- | --- | --- 
|**類型**|要與符合範本的度量建立關聯的類型。|`heartrate`
|**TypeMatchExpression**|針對事件中樞裝載評估的 JSON 路徑運算式。 如果找到相符的 JToken，則會將範本視為相符。 所有後續的運算式都會針對此處相符的解壓縮 JToken 進行評估。|`$..[?(@heartRate)]`
|**TimestampExpression**|JSON 路徑運算式，用來將度量的 OccurenceTimeUtc 的時間戳記值解壓縮。|`$.endDate`
|**DeviceIdExpression**|要解壓縮裝置識別碼的 JSON 路徑運算式。|`$.deviceId`
|**PatientIdExpression**|*選擇性*：用來解壓縮患者識別碼的 JSON 路徑運算式。|`$.patientId`
|**EncounterIdExpression**|*選擇性*：用來解壓縮遇到識別碼的 JSON 路徑運算式。|`$.encounterId`
|**值 []。ValueName**|要與後續運算式所解壓縮的值產生關聯的名稱。 用來系結 FHIR 對應範本中的必要值/元件。 |`hr`
|**值 []。ValueExpression**|要將必要值解壓縮的 JSON 路徑運算式。|`$.heartRate`
|**值 []。必填**|需要值才會出現在裝載中。  如果找不到，將不會產生測量，而且會擲回 InvalidOperationException。|`true`

##### <a name="examples"></a>範例
---
**核心速率**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*範本*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血壓**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*範本*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**從單一訊息投影多個測量**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*範本1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*範本2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**從訊息中的陣列投影多個測量**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*範本*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
IotJsonPathContentTemplate 與 JsonPathContentTemplate 類似，但不需要 DeviceIdExpression 和 TimestampExpression。

使用此範本時的假設是使用[Azure IoT 中樞裝置 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks)來傳送所要評估的訊息。 使用這些 Sdk 時，裝置身分識別 (假設 Azure Iot 中樞/中央的裝置識別碼已註冊為目的地 FHIR 伺服器上裝置資源的識別碼) 而且訊息的時間戳記是已知的。 如果您使用 Azure IoT 中樞裝置 Sdk，但在訊息內文中針對裝置身分識別或測量時間戳記使用自訂屬性，您仍然可以使用 JsonPathContentTemplate。

*注意：使用 IotJsonPathContentTemplate 時，TypeMatchExpression 應解析為 JToken 的整個訊息。請參閱下列範例。* 
##### <a name="examples"></a>範例
---
**核心速率**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*範本*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血壓**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*範本*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>FHIR 對應
將裝置內容解壓縮至正規化模型後，資料會根據裝置識別碼、量測類型和時間週期進行收集和分組。 此群組的輸出會傳送至 FHIR 資源， ([觀察](https://www.hl7.org/fhir/observation.html)目前) 。 這裡的 FHIR 對應範本會控制資料對應至 FHIR 觀察的方式。 是否應該針對某個時間點或一小時的某個期間建立觀察？ 應該將哪些代碼加入至觀察中？ 值應該以[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)或[數量](https://www.hl7.org/fhir/datatypes.html#Quantity)表示嗎？ 這些資料類型全都是 FHIR 對應設定控制項的選項。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate 是目前唯一支援 FHIR 對應中的範本。  它可讓您定義代碼、有效期間和觀察值。 支援多個實數值型別： [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、 [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)和[Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 除了這些可設定的值之外，也會自動處理觀察資源的識別碼，以及連結到適當的裝置和患者資源。

| 屬性 | 描述 
| --- | ---
|**類型**| 此範本應系結的度量類型。 應該至少有一個輸出此類型的裝置對應範本。
|**PeriodInterval**|所建立之觀察應代表的時間長度。 支援的值為 0 (實例) 、60 (一小時) 、1440 (一天) 。
|**類別**|用來分類所建立之觀測類型的任意數目的[CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) 。
|**代碼**|要套用至所建立之觀察的一或多個[Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代碼 []。錯誤碼**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的程式碼。
|**代碼 []。筆記本電腦**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的系統。
|**代碼 []。顯示**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的顯示。
|**ReplTest1**|要在觀察中解壓縮和表示的值。 如需詳細資訊，請參閱實[數值型別範本](#valuetypes)。
|**元件**|*選擇性：* 要在觀察上建立的一或多個元件。
|**元件 []。代碼**|要套用至元件的一或多個[Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**元件 []。Value**|要在元件中解壓縮和表示的值。 如需詳細資訊，請參閱實[數值型別範本](#valuetypes)。

### <a name="value-type-templates"></a>實值型別範本<a name="valuetypes"></a>
以下是目前支援的實數值型別範本。 未來可能會加入進一步的範本。
#### <a name="sampleddata"></a>SampledData
表示[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 資料類型。觀察量測會寫入值資料流程，從某個時間點開始，並使用定義的期間遞增向前。 如果沒有值，則 `E` 會將寫入資料流程。 如果句點是讓兩個值在資料流程中佔用相同的位置，就會使用最新的值。 更新使用 SampledData 的觀察時，會套用相同的邏輯。

| 屬性 | 描述 
| --- | ---
|**DefaultPeriod**|使用的預設期間（以毫秒為單位）。 
|**單位**|要在 SampledData 原點上設定的單位。 

#### <a name="quantity"></a>數量
表示[Quantity](http://hl7.org/fhir/datatypes.html#Quantity) FHIR 資料類型。 如果群組中有一個以上的值，則只會使用第一個值。 當新的值抵達時，會對應到相同的觀測，它會覆寫舊值。

| 屬性 | 描述 
| --- | --- 
|**單位**| 單位表示。
|**Code**| 單元的編碼形式。
|**系統**| 定義自動程式碼單元表單的系統。

### <a name="codeableconcept"></a>CodeableConcept
表示[CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 資料類型。 不會使用實際值。

| 屬性 | 描述 
| --- | --- 
|**Text**|純文字標記法。 
|**代碼**|要套用至所建立之觀察的一或多個[Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代碼 []。錯誤碼**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的程式碼。
|**代碼 []。筆記本電腦**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的系統。
|**代碼 []。顯示**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的顯示。

### <a name="examples"></a>範例
**心形速率-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**步驟-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**血壓-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**血壓-數量**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**已移除裝置-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>後續步驟

查看 Azure IoT Connector for FHIR (preview) 的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT 連接器常見問題](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* 在 Azure 入口網站中，適用于 FHIR 的 Azure IoT 連接器稱為 IoT 連接器 (預覽) 。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
