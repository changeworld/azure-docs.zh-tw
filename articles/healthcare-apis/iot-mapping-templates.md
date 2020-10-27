---
title: 概念：適用于 FHIR 的 Azure IoT Connector 中的對應範本 (預覽) Azure API for FHIR 的功能
description: 瞭解如何在 Azure IoT Connector for FHIR (preview) 中建立兩種類型的對應範本。 裝置對應範本會將裝置資料轉換成標準化結構描述。 FHIR 對應範本會將標準化的訊息轉換成 FHIR 型觀察資源。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 4eede07b285614c061f4b59845c8f44d82083ec2
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558528"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Azure IoT Connector for FHIR (預覽) 對應範本
本文將詳細說明如何使用對應範本設定適用于 FHIR 的 Azure IoT Connector *。

適用于 FHIR 的 Azure IoT Connector 需要兩種類型的 JSON 型對應範本。 第一個類型（ **裝置對應** ）負責對應傳送至 `devicedata` Azure 事件中樞端點的裝置承載。 它會將類型、裝置識別碼、測量日期時間，以及測量值 (s 解壓縮) 。 第二種類型 **FHIR 對應** 會控制 FHIR 資源的對應。 它允許設定觀察期間的長度、用來儲存值的 FHIR 資料類型，以及術語代碼 (s) 。 

對應範本會根據其類型組成 JSON 檔。 這些 JSON 檔接著會透過 Azure 入口網站新增至您的 Azure IoT Connector for FHIR。 裝置對應檔是透過 [設定 **FHIR 對應** ] 頁面，在 [ **設定裝置對應** ] 頁面和 FHIR 對應檔中新增的。

> [!NOTE]
> 對應範本會儲存在基礎 blob 儲存體中，並在每次計算執行時從 blob 載入。 更新之後，它們應該會立即生效。 

## <a name="device-mapping"></a>裝置對應
裝置對應提供的對應功能可將裝置內容解壓縮為一般格式，以供進一步評估之用。 每個收到的訊息都會針對所有範本進行評估。 這種方法可將單一輸入訊息投射至多個輸出訊息，這些訊息稍後會對應到 FHIR 中的不同觀察。 結果是正規化的資料物件，代表範本所剖析的值或值。 正規化資料模型有幾個必要的屬性必須找到和解壓縮：

| 屬性 | 描述 |
| - | - |
|**型別**|要分類度量的名稱/型別。 這個值是用來系結至所需的 FHIR 對應範本。  多個範本可以輸出成相同的類型，讓您能夠將不同的標記法對應到多個裝置，以提供單一的一般輸出。|
|**OccurenceTimeUtc**|測量發生的時間。|
|**DeviceId**|裝置的識別碼。 此值應符合目的地 FHIR 伺服器上存在之裝置資源的識別碼。|
 |**屬性**|解壓縮至少一個屬性，以便將值儲存在所建立的觀察資源中。  屬性是正規化期間所解壓縮的機碼值組集合。|

以下是正規化期間發生的概念範例。

![正規化範例](media/concepts-iot-mapping-templates/normalization-example.png)

內容承載本身是由三個部分組成的 Azure 事件中樞訊息：主體、屬性和 SystemProperties。 `Body`是代表 utf-8 編碼字串的位元組陣列。 在範本評估期間，位元組陣列會自動轉換成字串值。 `Properties` 這是訊息建立者所使用的索引鍵值集合。 `SystemProperties` 也是 Azure 事件中樞架構保留的索引鍵值集合，其會自動填入專案。

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

### <a name="mapping-with-json-path"></a>使用 JSON 路徑對應
目前支援的兩種裝置內容範本類型依賴 JSON 路徑，以符合所需的範本和解壓縮的值。 您可以在 [這裡](https://goessner.net/articles/JsonPath/)找到 JSON 路徑的詳細資訊。 這兩種範本類型都會使用 [json .net 執行](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 來解析 json 路徑運算式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate 可讓您使用 JSON 路徑來比對和解壓縮事件中樞訊息中的值。

| 屬性 | 描述 |<div style="width:150px">範例</div>
| --- | --- | --- 
|**TypeName**|要與符合範本的度量產生關聯的型別。|`heartrate`
|**TypeMatchExpression**|針對事件中樞裝載評估的 JSON 路徑運算式。 如果找到相符的 JToken，則會將範本視為相符。 所有後續的運算式都會針對此處所符合的已解壓縮 JToken 進行評估。|`$..[?(@heartRate)]`
|**TimestampExpression**|JSON 路徑運算式，用來將量測 OccurenceTimeUtc 的時間戳記值解壓縮。|`$.endDate`
|**DeviceIdExpression**|用來將裝置識別碼解壓縮的 JSON 路徑運算式。|`$.deviceId`
|**PatientIdExpression**|*選擇性* ：要將患者識別碼解壓縮的 JSON 路徑運算式。|`$.patientId`
|**EncounterIdExpression**|*選擇性* ：要將遇到識別碼解壓縮的 JSON 路徑運算式。|`$.encounterId`
|**值 []。ValueName**|要與後續運算式所解壓縮之值相關聯的名稱。 用來系結 FHIR 對應範本中的必要值/元件。 |`hr`
|**值 []。ValueExpression**|要將必要值解壓縮的 JSON 路徑運算式。|`$.heartRate`
|**值 []。必填**|將需要值存在於裝載中。  如果找不到，將不會產生量值，而且會擲回 InvalidOperationException。|`true`

##### <a name="examples"></a>範例
---
**心率**

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

**從單一訊息投影多個度量**

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

**從訊息中的陣列投影多個度量**

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

使用此範本時的假設是使用 [Azure IoT 中樞裝置 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks)來傳送要評估的訊息。 使用這些 Sdk 時，裝置身分識別 (假設 Azure Iot 中樞/Central 中的裝置識別碼已註冊為目的地 FHIR 伺服器上裝置資源的識別碼) 且已知訊息的時間戳記。 如果您使用 Azure IoT 中樞裝置 Sdk，但在訊息本文中使用自訂屬性作為裝置身分識別或測量時間戳記，您仍然可以使用 JsonPathContentTemplate。

*注意：使用 IotJsonPathContentTemplate 時，TypeMatchExpression 應該將整個訊息解析為 JToken。請參閱下列範例。* 
##### <a name="examples"></a>範例
---
**心率**

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
一旦將裝置內容解壓縮到正規化模型之後，資料就會根據裝置識別碼、量測類型和時間週期進行收集和分組。 傳送此群組的輸出以轉換成 FHIR 資源 ([觀察](https://www.hl7.org/fhir/observation.html) 目前) 。 在這裡，FHIR 對應範本會控制資料如何對應至 FHIR 觀察。 是否應該針對某個時間點或一小時內的某個時間點建立觀察？ 應該將哪些代碼新增至觀察？ 值應該以 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 或 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)表示嗎？ 這些資料類型都是 FHIR mapping configuration 控制項的選項。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate 目前是目前在 FHIR 對應中唯一支援的範本。  它可讓您定義程式碼、有效期間，以及觀察值。 支援多個實數值型別： [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、 [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)和 [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 以及這些可設定的值，系統會自動處理觀察資源的識別碼，並連結至適當的裝置和患者資源。

| 屬性 | 描述 
| --- | ---
|**TypeName**| 此範本應系結的度量類型。 至少應該有一個輸出此類型的裝置對應範本。
|**PeriodInterval**|所建立的觀察所應代表的時間長度。 支援的值為 0 (實例) ，60 (一小時) 1440 (一天) 。
|**類別**|任何數目的 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) ，用來分類所建立的觀察型別。
|**代碼**|要套用至所建立之觀察的一或多個 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代碼 []。代碼**|[編寫](http://hl7.org/fhir/datatypes-definitions.html#coding)程式碼的程式碼。
|**代碼 []。系統**|撰寫程式 [代碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的系統。
|**代碼 []。顯示**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的顯示。
|**值**|要在觀察中解壓縮和表示的值。 如需詳細資訊，請參閱實 [值型別範本](#valuetypes)。
|**Components**|*選用：* 要在觀察上建立的一或多個元件。
|**元件 []。代碼**|要套用至元件的一或多個 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**元件 []。價值**|要在元件中解壓縮和表示的值。 如需詳細資訊，請參閱實 [值型別範本](#valuetypes)。

### <a name="value-type-templates"></a>實值型別範本 <a name="valuetypes"></a>
以下是目前支援的實數值型別範本。 未來可能會新增進一步的範本。
#### <a name="sampleddata"></a>SampledData
表示 [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 資料類型。觀察測量值會寫入值資料流程，從某個時間點開始，並使用定義的期間來遞增。 如果沒有任何值，將會 `E` 寫入資料流程中。 如果句點是讓兩個以上的值在資料流程中佔用相同的位置，則會使用最新的值。 更新使用 SampledData 的觀察時，會套用相同的邏輯。

| 屬性 | 描述 
| --- | ---
|**DefaultPeriod**|要使用的預設期間（以毫秒為單位）。 
|**單位**|要在 SampledData 原點上設定的單位。 

#### <a name="quantity"></a>數量
表示 [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) FHIR 資料類型。 如果群組中有一個以上的值，則只會使用第一個值。 當新值抵達時，對應到相同的觀察值，就會覆寫舊值。

| 屬性 | 描述 
| --- | --- 
|**單位**| 單位標記法。
|**程式碼**| 單位的編碼形式。
|**系統**| 定義編碼單元表單的系統。

### <a name="codeableconcept"></a>CodeableConcept
表示 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 資料類型。 不會使用實際值。

| 屬性 | 描述 
| --- | --- 
|**Text**|純文字標記法。 
|**代碼**|要套用至所建立之觀察的一或多個 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代碼 []。代碼**|[編寫](http://hl7.org/fhir/datatypes-definitions.html#coding)程式碼的程式碼。
|**代碼 []。系統**|撰寫程式 [代碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的系統。
|**代碼 []。顯示**|[編碼](http://hl7.org/fhir/datatypes-definitions.html#coding)的顯示。

### <a name="examples"></a>範例
**核心速率-SampledData**
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
**血糖壓力-SampledData**
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
**血壓力-數量**
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
**裝置已移除-CodeableConcept**
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

## <a name="next-steps"></a>下一步

查看適用于 FHIR (preview) 的 Azure IoT Connector 上的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT Connector 常見問題](fhir-faq.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
