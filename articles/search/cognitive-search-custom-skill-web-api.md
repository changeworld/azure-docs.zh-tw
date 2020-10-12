---
title: 技能集中的自訂 Web API 技能
titleSuffix: Azure Cognitive Search
description: 藉由呼叫 Web Api 來擴充 Azure 認知搜尋技能集的功能。 使用自訂 Web API 技能來整合您的自訂程式碼。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84982113"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure 認知搜尋擴充管線中的自訂 Web API 技能

**自訂 WEB api**技能可讓您透過呼叫提供自訂作業的 Web API 端點來擴充 AI 擴充。 與內建的技能類似，**自訂 Web API** 技能具有輸入和輸出。 根據輸入，您的 Web API 會在索引子執行時接收 JSON 承載，並輸出 JSON 承載作為回應，以及成功狀態碼。 預期回應應該具有您的自訂技能所指定的輸出。 任何其他的回應會被視為錯誤，並且不會執行任何擴充。

JSON 承載的結構會在本文件中進一步描述。

> [!NOTE]
> 針對從 Web API 傳回的特定標準 HTTP 狀態碼，索引子將重試兩次。 這些 HTTP 狀態碼為： 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 參數名稱     | 描述 |
|--------------------|-------------|
| `uri` | 將傳送 _JSON_ 承載的 WEB API URI。 僅允許 **https** URI 配置 |
| `httpMethod` | 傳送承載時使用的方法。 允許的方法為 `PUT` 和 `POST` |
| `httpHeaders` | 機碼值組的集合，其中機碼代表標頭名稱，而值代表將與承載一起傳送至 Web API 的標頭值。 下列標頭禁止加入此集合：`Accept`、`Accept-Charset`、`Accept-Encoding`、`Content-Length`、`Content-Type`、`Cookie`、`Host`、`TE`、`Upgrade`、`Via` |
| `timeout` | (選擇性) 指定時，表示進行 API 呼叫的 http 用戶端逾時。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 例如，`PT60S` 為 60 秒。 如果沒有設定，則選擇的預設值為 30 秒。 Timeout 最多可設定為230秒，最少可設定為1秒。 |
| `batchSize` | (選擇性) 指出每個 API 呼叫將傳送多少「資料記錄」(請參閱下面的 _JSON_ 承載結構)。 如果未設定，則選擇的預設值為 1000。 我們建議您使用此參數在編製索引的輸送量和 API 負載之間達到適當的取捨 |
| `degreeOfParallelism` |  (選擇性) 指定時，表示索引子將平行處理至您所提供之端點的呼叫數目。 如果您的端點在要求負載過高時失敗，或如果您的端點能夠接受更多要求，且您想要增加索引子的效能，您可以減少此值。  如果未設定，則會使用預設值5。 `degreeOfParallelism`最多可設定為10，最小值為1。 |

## <a name="skill-inputs"></a>技能輸入

此技能沒有任何「預先定義的」輸入。 您可以選擇在此技能執行時已經可用的一或多個欄位作為輸入，且傳送至 Web API 的 _JSON_ 承載將會有不同的欄位。

## <a name="skill-outputs"></a>技能輸出

此技能沒有任何「預先定義的」輸出。 根據您的 Web API 將傳回的回應，加入輸出欄位，以便可以從 _JSON_ 回應中挑選它們。


## <a name="sample-definition"></a>範例定義

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>範例輸入 JSON 結構

此 _JSON_ 結構代表將會傳送至您的 Web API 的承載。
它一律會遵循這些限制式：

* 最上層的實體稱為 `values`，且將是物件陣列。 此類物件的數目最多為 `batchSize`
* `values` 陣列中的每個物件都有
    * `recordId` 屬性，它是**唯一**字串，用來識別該記錄。
    * `data` 屬性，它是 _JSON_ 物件。 `data` 屬性的欄位將對應到技能定義的 `inputs` 區段中指定的「名稱」。 這些欄位的值將來自這些欄位的 `source` (可能來自文件中的欄位，也可能來自另一個技能)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>範例輸出 JSON 結構

「輸出」對應至 Web API 傳回的回應。 Web API 應該只傳回 _JSON_ 承載， (藉由查看 `Content-Type` 回應標頭) 來進行驗證，且應符合下列條件約束：

* 應該有一個名為 `values` 的最上層實體，它應該是物件陣列。
* 陣列中的物件數目應該和傳送至 Web API 的物件數相同。
* 每個物件都應該有：
   * `recordId` 屬性
   * `data` 屬性，它是一個物件，其中的欄位是與 `output` 中的「名稱」相符的擴充，其值會被視為擴充。
   * `errors` 屬性，一個將新增至索引子執行歷程記錄中的陣列，其中列出發生的任何錯誤。 此屬性是必要的，但可以具有 `null` 值。
   * `warnings` 屬性，一個將新增至索引子執行歷程記錄中的陣列，其中列出發生的任何警告。 此屬性是必要的，但可以具有 `null` 值。
* `values` 陣列中的物件不需要與 `values` 陣列 (作為傳送至 Web API 的要求) 中的物件順序相同。 不過，`recordId` 用於相互關聯，因此包含 `recordId` (不是 Web API 的原始要求的一部分) 的回應中的任何記錄都將會捨棄。

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>錯誤案例
除了您的 Web API 無法使用或傳送不成功的狀態碼之外，下列各項被視為錯誤情況：

* 如果 Web API 傳回成功狀態碼，但回應指出它不是 `application/json`，則回應被視為無效，並且不會執行任何擴充。
* 如果回應 `values` 陣列中有**無效** (包含原始請求中沒有的 `recordId` 或具有重複值) 記錄，則不會對**那些**記錄執行任何擴充。

針對 Web API 無法使用或傳回 HTTP 錯誤的情況，將在索引子執行歷程記錄中加入有關 HTTP 錯誤的任何可用詳細資料的易懂錯誤。

## <a name="see-also"></a>另請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [將自訂技能新增至 AI 擴充管線](cognitive-search-custom-skill-interface.md)
+ [範例：建立 AI 擴充的自訂技能](cognitive-search-create-custom-skill-example.md)
