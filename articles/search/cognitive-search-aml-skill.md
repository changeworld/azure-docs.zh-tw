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
ms.openlocfilehash: d017472715e8fe924a11080fc837ac837f5bd48f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84982147"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure 認知搜尋擴充管線中的 AML 技能

**AML**技能可讓您使用自訂[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) （AML）模型擴充 AI 擴充。 一旦將 AML 模型[定型並部署](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workflow)之後， **aml**技能就會將它整合到 AI 擴充中。

與內建技能一樣， **AML**技能也具有輸入和輸出。 輸入會以 JSON 物件的形式傳送至您已部署的 AML 服務，這會將 JSON 承載輸出為回應以及成功狀態碼。 回應預期會有您**AML**技能所指定的輸出。 任何其他的回應會被視為錯誤，並且不會執行任何擴充。

> [!NOTE]
> 索引子會針對從 AML 服務傳回的特定標準 HTTP 狀態碼重試兩次。 這些 HTTP 狀態碼為：
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>必要條件

* [AML 工作區](https://docs.microsoft.com/azure/machine-learning/concept-workspace)
* 此工作區中具有已[部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)的[Azure Kubernetes Service AML 計算目標](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)
  * [計算目標應啟用 SSL](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service#deploy-on-aks-and-field-programmable-gate-array-fpga)。 Azure 認知搜尋只允許存取**HTTPs**端點
  * 可能不會使用自我簽署憑證。

## <a name="odatatype"></a>@odata.type  
AmlSkill 的 Microsoft 技術。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。 您選擇要使用的參數取決於[您的 AML 服務所需的驗證（如果有的話）](#WhatSkillParametersToUse)

| 參數名稱 | 描述 |
|--------------------|-------------|
| `uri` | （不需要[驗證或金鑰驗證](#WhatSkillParametersToUse)）將傳送_JSON_承載之[AML 服務的評分 URI](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service) 。 只允許**HTTPs** URI 配置。 |
| `key` | （[金鑰驗證](#WhatSkillParametersToUse)的必要項）[AML 服務](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service#authentication-with-keys)的索引鍵。 |
| `resourceId` | （[權杖驗證](#WhatSkillParametersToUse)所需）。 AML 服務的 Azure Resource Manager 資源識別碼。 其格式應為訂用帳戶/{guid}/resourceGroups/{資源群組-名稱}/Microsoft.machinelearningservices/工作區/{工作區-名稱}/服務/{service_name}。 |
| `region` | （選擇性用於[權杖驗證](#WhatSkillParametersToUse)）。 AML 服務部署所在的[區域](https://azure.microsoft.com/global-infrastructure/regions/)。 |
| `timeout` | (選擇性) 指定時，表示進行 API 呼叫的 http 用戶端逾時。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 例如，`PT60S` 為 60 秒。 如果沒有設定，則選擇的預設值為 30 秒。 [超時] 可以設定為最大值230秒，最小值為1秒。 |
| `degreeOfParallelism` | 選擇性指定時，表示索引子將會平行地對您提供的端點進行的呼叫次數。 如果您的端點在要求負載過高的情況下失敗，您可以減少這個值，如果您的端點能夠接受更多要求，而且您想要增加索引子的效能，則會引發此值。  如果未設定，則會使用預設值5。 DegreeOfParallelism 可以設定為最大值10，最小值為1。

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>要使用哪些技能參數

需要哪些 AML 技能參數取決於 AML 服務所使用的驗證（如果有的話）。 AML 服務提供三種驗證選項：

* 以[金鑰為基礎的驗證](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#authentication-for-web-service-deployment)。 提供靜態金鑰來驗證 AML 技能的評分要求
  * 使用_uri_和_金鑰_參數
* 以[權杖為基礎的驗證](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#authentication)。 AML 服務是[使用以權杖為基礎的驗證來部署](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service#authentication-with-tokens)。 Azure 認知搜尋服務的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)會被授與 AML 服務工作區中的 [讀取者][角色](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles)。 然後，AML 技能會使用 Azure 認知搜尋服務的受控識別來對 AML 服務進行驗證，而不需要靜態金鑰。
  * 使用_resourceId_參數。
  * 如果 Azure 認知搜尋服務與 AML 工作區位於不同的區域，請使用_region_參數來設定要在其中部署 aml 服務的區域
* 無驗證。 不需要驗證就能使用 AML 服務
  * 使用_uri_參數

## <a name="skill-inputs"></a>技能輸入

此技能沒有任何「預先定義的」輸入。 您可以選擇一或多個欄位，當此技能的執行做為輸入，而傳送到 AML 服務的_JSON_承載將會有不同的欄位時，就會提供這些欄位。

## <a name="skill-outputs"></a>技能輸出

此技能沒有任何「預先定義的」輸出。 根據 AML 服務將傳回的回應，新增輸出欄位，讓它們可以從_JSON_回應中挑選。

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

此_JSON_結構代表將傳送至 AML 服務的承載。 結構的最上層欄位會對應至 `inputs` 技能定義之區段中所指定的「名稱」。 這些欄位的值將來自這些欄位的 `source` (可能來自文件中的欄位，也可能來自另一個技能)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>範例輸出 JSON 結構

輸出會對應到從 AML 服務傳回的回應。 AML 服務應該只傳回_JSON_承載（藉由查看 `Content-Type` 回應標頭來驗證），而且應該是物件，其中的欄位會與中的「名稱」擴充相符 `output` ，而且其值會視為擴充。

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
除了您的 AML 無法使用或送出不成功的狀態碼，以下將視為錯誤的情況：

* 如果 AML 服務傳回成功狀態碼，但回應指出它不是 `application/json` ，則回應會被視為無效，而且不會執行任何擴充。
* 如果 AML 服務傳回不正確 json

對於 AML 服務無法使用或傳回 HTTP 錯誤的情況，將會在索引子執行歷程記錄中加入一個易記錯誤，其中包含 HTTP 錯誤的任何可用詳細資料。

## <a name="see-also"></a>另請參閱

+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [AML 服務疑難排解](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment)
