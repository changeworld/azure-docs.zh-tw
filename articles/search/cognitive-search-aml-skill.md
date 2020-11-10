---
title: 技能集中的自訂 AML 技能
titleSuffix: Azure Cognitive Search
description: 使用 Azure Machine Learning 模型擴充 Azure 認知搜尋技能集的功能。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: d1e6f4e16e3eda8519913a9e2ae14f7cc909bf61
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445450"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure 認知搜尋擴充管線中的 AML 技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有 .NET SDK 支援。

**Aml** 技能可讓您使用自訂 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML) 模型來擴充 AI 擴充。 一旦將 AML 模型 [定型及部署](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)之後， **aml** 技能就會將其整合到 AI 擴充中。

和內建技能一樣， **AML** 技能具有輸入和輸出。 輸入會以 JSON 物件的形式傳送至您部署的 AML 服務，其會輸出 JSON 承載作為回應，以及成功狀態碼。 回應預期會有由 **AML** 技能指定的輸出。 任何其他的回應會被視為錯誤，並且不會執行任何擴充。

> [!NOTE]
> 索引子會重試兩次，以進行從 AML 服務傳回的特定標準 HTTP 狀態碼。 這些 HTTP 狀態碼為：
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>先決條件

* [AML 工作區](../machine-learning/concept-workspace.md)
* 此工作區中具有已[部署模型](../machine-learning/how-to-deploy-azure-kubernetes-service.md)的[Azure Kubernetes Service AML 計算目標](../machine-learning/concept-compute-target.md)
  * [計算目標應啟用 SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga)。 Azure 認知搜尋只允許存取 **HTTPs** 端點
  * 可能不會使用自我簽署的憑證。

## <a name="odatatype"></a>@odata.type  
AmlSkill。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。 您選擇使用的參數取決於 [AML 服務所需的驗證（如果有的話）](#WhatSkillParametersToUse)

| 參數名稱 | 描述 |
|--------------------|-------------|
| `uri` |  (不需要 [驗證或金鑰驗證](#WhatSkillParametersToUse)，) 將傳送 _JSON_ 承載之 [AML 服務的評分 URI](../machine-learning/how-to-consume-web-service.md) 。 只允許 **HTTPs** URI 配置。 |
| `key` | [金鑰驗證](#WhatSkillParametersToUse)所需的 () [AML 服務的金鑰](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)。 |
| `resourceId` | [權杖驗證](#WhatSkillParametersToUse)) 所需的 (。 AML 服務的 Azure Resource Manager 資源識別碼。 其格式應為：訂用帳戶/{guid}/resourceGroups/{資源組名}/MachineLearningServices/workspace/{workspace-name}/services/{service_name}。 |
| `region` | [權杖驗證](#WhatSkillParametersToUse))  (選擇性。 AML 服務部署所在的 [區域](https://azure.microsoft.com/global-infrastructure/regions/) 。 |
| `timeout` | (選擇性) 指定時，表示進行 API 呼叫的 http 用戶端逾時。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 例如，`PT60S` 為 60 秒。 如果沒有設定，則選擇的預設值為 30 秒。 Timeout 最多可設定為230秒，最少可設定為1秒。 |
| `degreeOfParallelism` |  (選擇性) 指定時，表示索引子將平行處理至您所提供之端點的呼叫數目。 如果您的端點在要求負載過高時失敗，或如果您的端點能夠接受更多要求，且您想要增加索引子的效能，您可以減少此值。  如果未設定，則會使用預設值5。 DegreeOfParallelism 最多可設定為10，最小值為1。

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>要使用的技能參數

需要哪些 AML 技能參數取決於您的 AML 服務所使用的驗證（如果有的話）。 AML 服務提供三種驗證選項：

* 以[金鑰為基礎的驗證](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication)。 提供靜態金鑰以驗證來自 AML 技能的評分要求
  * 使用 _uri_ 和 _金鑰_ 參數
* 以[權杖為基礎的驗證](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)。 AML 服務是 [使用以權杖為基礎的驗證來部署](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)。 Azure 認知搜尋服務的 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 會授與 AML 服務工作區中的 [讀取者角色](../machine-learning/how-to-assign-roles.md) 。 AML 技能接著會使用 Azure 認知搜尋服務的受控識別來對 AML 服務進行驗證，而不需要靜態金鑰。
  * 使用 _resourceId_ 參數。
  * 如果 Azure 認知搜尋服務與 AML 工作區位於不同的區域，請使用 _region_ 參數設定要在其中部署 AML 服務的區域。
* 無驗證。 使用 AML 服務不需要驗證
  * 使用 _uri_ 參數

## <a name="skill-inputs"></a>技能輸入

此技能沒有任何「預先定義的」輸入。 您可以選擇一或多個在此技能執行時已可供使用的欄位，因為輸入和傳送至 AML 服務的 _JSON_ 承載將會有不同的欄位。

## <a name="skill-outputs"></a>技能輸出

此技能沒有任何「預先定義的」輸出。 根據 AML 服務將傳回的回應，新增輸出欄位，以便從 _JSON_ 回應中挑選它們。

## <a name="sample-definition"></a>範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>範例輸入 JSON 結構

此 _JSON_ 結構代表將傳送至 AML 服務的承載。 結構的最上層欄位會對應至 `inputs` 技能定義之區段中指定的「名稱」。 這些欄位的值將來自這些欄位的 `source` (可能來自文件中的欄位，也可能來自另一個技能)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>範例輸出 JSON 結構

輸出會對應至 AML 服務所傳回的回應。 AML 服務應該只傳回 _JSON_ 承載， (藉由查看 `Content-Type` 回應標) 頭來進行驗證，而且應該是一個物件，其中擴充的欄位符合中的「名稱」 `output` ，且其值會被視為擴充。

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>內嵌成形範例定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>內嵌成形輸入 JSON 結構

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>內嵌成形範例輸出 JSON 結構

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>錯誤案例
除了您的 AML 無法使用或送出不成功的狀態碼之外，下列視為錯誤的案例：

* 如果 AML 服務傳回成功狀態碼，但回應指出它不是 `application/json` ，則會將回應視為無效，且不會執行任何擴充。
* 如果 AML 服務傳回不正確 json

在 AML 服務無法使用或傳回 HTTP 錯誤的情況下，將會在索引子執行歷程記錄中加入易記的錯誤，其中包含 HTTP 錯誤的任何可用詳細資料。

## <a name="see-also"></a>請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [AML 服務疑難排解](../machine-learning/how-to-troubleshoot-deployment.md)