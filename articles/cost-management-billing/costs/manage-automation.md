---
title: 使用自動化管理 Azure 成本
description: 此文章說明如何使用自動化來管理 Azure 成本。
author: bandersmsft
ms.author: banders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 47d9c2838c5c806214e3be2f9ba7ce335bc0af67
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956087"
---
# <a name="manage-costs-with-automation"></a>使用自動化管理成本

您可以使用成本管理自動化來建置一組自訂的解決方案，以擷取及管理成本資料。 此文章涵蓋成本管理自動化的常見案例，以及根據您的情況所提供的選項。 如果您想要使用 API 進行開發，可使用常見的 API 要求範例，以幫助加快開發程序。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>將成本資料擷取作業自動化以用於離線分析

您可能需要下載您的 Azure 成本資料，以將其與其他資料集合併。 或者，您可能需要將成本資料整合到您自己的系統中。 視涉及的資料量而定，會有不同的選項可用。 在任何情況下，您都必須擁有適當範圍的成本管理權限，才能使用 API 與工具。 如需詳細資訊，請參閱[指派資料存取權](./assign-access-acm-data.md)。

## <a name="suggestions-for-handling-large-datasets"></a>處理大型資料集的相關建議

如果您的組織擁有跨許多資源或訂用帳戶運作的大型 Azure，您將會有大量的使用量詳細資料。 Excel 通常無法載入這類大型檔案。 在此情況下，我們建議您採用下列選項：

**Power BI**

Power BI 可用來內嵌及處理大量資料。 如果您是 Enterprise 合約客戶，可以使用 Power BI 範本應用程式來分析您計費帳戶的成本。 報表會包含客戶所使用的重要檢視。 如需詳細資訊，請參閱[使用 Power BI 範本應用程式分析 Azure 成本](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)。

**Power BI 資料連接器**

如果您想要每天分析資料，建議使用 [Power BI 資料連接器](/power-bi/connect-data/desktop-connect-azure-cost-management)取得資料以進行詳細分析。 連接器會將您建立的任何報表保持在最新狀態，因而會導致產生更多成本。

**成本管理匯出**

您可能並不需要每天分析資料。 如果是這樣，請考慮使用成本管理的[匯出](./tutorial-export-acm-data.md)功能，來排定將資料匯出到 Azure 儲存體帳戶的排程。 然後您可以視需要將資料載入到 Power BI 中，或如果檔案夠小時，就可以在 Excel 中分析。 Azure 入口網站提供匯出功能，或者您可以使用[匯出 API](/rest/api/cost-management/exports) \(部分機器翻譯\) 設定匯出作業。

**使用量詳細資料 API**

如果您有小型成本資料集，請考慮採用[使用量詳細資料 API](/rest/api/consumption/usageDetails) \(部分機器翻譯\)。 如果您有大量成本資料，應該盡可能要求某段時間範圍內最少量的使用量資料。 若要這樣做，請指定小型時間範圍，或在您的要求中使用篩選條件。 例如，在您需要三年成本資料的情況下，若針對不同的時間範圍進行多次呼叫 (而非使用單次呼叫) 時，API 確實會比較好。 您可以從該處將資料載入到 Excel，以供進一步分析。

## <a name="automate-retrieval-with-usage-details-api"></a>運用使用量詳細資料 API 將擷取自動化

[使用量詳細資料 API](/rest/api/consumption/usageDetails) 提供一個簡單的方法，來取得與您的 Azure 帳單相對應的原始未彙總成本資料。 當您的組織需要程式設計資料擷取解決方案時，API 會很有用。 如果您想要分析較小型的成本資料集，請考慮使用 API。 但如果您有較大型的資料集，應該使用先前已經確定的其他解決方案。 使用量詳細資料中的資料是依據每天每一計量的方式提供的。 計算您的每月帳單時就是使用這個方式。 這些 API 的公開推出 (GA) 版本是 `2019-10-01`。 使用 `2019-04-01-preview` 來存取保留的預覽版本及使用 API 購買的 Azure Marketplace 項目。

### <a name="usage-details-api-suggestions"></a>使用量詳細資料 API 的相關建議

**要求排程**

我們建議您每天對使用量詳細資料 API 發出的要求「不要超過一個」。 如需成本資料重新整理頻率，以及如何處理進位的詳細資訊，請參閱[了解成本管理資料](./understand-cost-mgt-data.md)。

**以最上層範圍為目標但不篩選**

使用 API 可取得您在最高層級範圍所需的所有資料。 等到所有需要的資料都內嵌之後，再執行任何篩選、群組或彙總分析。 API 已特別針對提供大量未彙總的原始成本資料最佳化。 若要深入了解成本管理中可用的範圍，請參閱[了解並使用範圍](./understand-work-scopes.md)。 當您下載了某個範圍需要的資料之後，請使用 Excel 搭配篩選條件與樞紐分析表進一步分析資料。

## <a name="example-usage-details-api-requests"></a>使用量詳細資料 API 要求範例

Microsoft 客戶會使用下列範例要求來解決您可能會遇到的常見情況。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>取得特定日期範圍期間某個範圍的使用量詳細資料

要求所傳回的資料會與計費系統收到使用量的日期相對應。 其中可能包含來自多張發票的成本。 所應使用的呼叫會隨著您的訂用帳戶類型而有所不同。

若為具有 Enterprise 合約 (EA) 或隨用隨付訂用帳戶的舊版客戶，請使用下列呼叫：

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

若為具有 Microsoft 客戶合約的新版客戶，請使用下列呼叫：

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>取得分攤成本詳細資料

如果您需要實際成本來顯示所產生的購買，請在下列要求中將「計量」變更為 `ActualCost`。 若要使用攤銷與實際成本，您必須使用 `2019-04-01-preview` 版本。 目前 API 版本的運作方式與 `2019-10-01` 版本相同，不同之處在於新的類型/計量屬性已變更的屬性名稱。 如果您有 Microsoft 客戶合約，您的篩選條件就會是下列範例中的 `startDate` 與 `endDate`。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>使用「匯出」功能重複擷取大型成本資料集

您可以從成本管理透過匯出來定期匯出大量資料。 建議您使用匯出來擷取未彙總成本資料。 特別是當使用量檔案太大，而無法使用使用量詳細資料 API 可靠地進行呼叫和下載時。 匯出的資料會放在您選擇的 Azure 儲存體帳戶中。 您可以將資料從該位置載入到您自己的系統中，並視需要加以分析。 若要在 Azure 入口網站中設定匯出功能，請參閱[匯出資料](tutorial-export-acm-data.md)。

如果您想要將各種範圍的匯出自動化，下一節中的範例 API 要求是很好的起點。 您可以使用匯出 API 來將自動匯出建立為一般環境設定的一部分。 自動匯出有助於確保您擁有所需的資料。 當您擴充 Azure 使用量時，則可以在自己的組織系統中使用。

### <a name="common-export-configurations"></a>一般匯出設定

在建立第一個匯出之前，請考慮您的案例以及為了實現此案例所需要的設定選項。 請考慮下列匯出選項：

- **週期** - 決定匯出作業的執行頻率，以及何時要將檔案放在您的 Azure 儲存體帳戶中。 可選擇 [每天]、[每週] 和 [每月]。 請嘗試設定您的週期，使其符合貴組織內部系統所使用的資料匯入作業。
- **週期期間** - 決定匯出保持有效的時間長度。 檔案只會在週期期間內匯出。
- **時間範圍** - 決定匯出在指定的執行時所產生的資料量。 常見的選項為 MonthToDate 和 WeekToDate。
- **開始日期** - 設定您想要的匯出排程開始時間。 系統會根據您的週期，在開始日期和之後的時間建立匯出。
- **類型** - 有三種匯出類型：
  - ActualCost - 顯示指定期間所產生的總使用量和成本，並在帳單上顯示。
  - AmortizedCost - 顯示指定期間的總使用量和成本，並將分攤套用至適用的保留購買成本。
  - 使用量 - 在 2020 年 7 月 20 日之前建立的匯出全都是「使用量」類型。 請將所有已排程的匯出更新為 ActualCost 或 AmortizedCost。
- **資料行** - 定義您想要包含在匯出檔案中的資料欄位。 其會對應至使用量詳細資料 API 中所提供的欄位。 如需詳細資訊，請參閱[使用量詳細資料 API](/rest/api/consumption/usagedetails/list)。

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>針對訂用帳戶建立每日的月初至今匯出

要求 URL：`PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>使用預算將警示和動作自動化

有兩個關鍵元件可讓您的雲端投資創造最大價值。 其中一個是自動建立預算。 另一個則是設定成本型協調流程，以回應預算警示。 有不同的方式可將 Azure 預算的建立作業自動化。 超過設定的警示閾值時，就會發生各種警示回應。

下列各節會說明可用的選項，並提供範例 API 要求，讓您可以開始使用預算自動化。

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>如何針對您的預算限額來評估成本

系統會每天一次針對您的預算限額來評估成本。 當您建立新預算或到達預算重設日的時候，相較於限額的成本就會變成零/Null，因為可能還未開始評估。

當 Azure 偵測到您的成本超過限額時，就會在偵測期間所在的該小時內觸發通知。

#### <a name="view-your-current-cost"></a>檢視您目前的成本

若要檢視您目前的成本，您需要使用[查詢 API](/rest/api/cost-management/query) 進行 GET 呼叫。

針對預算 API 的 GET 呼叫不會傳回成本分析中顯示的目前成本。 相反地，該呼叫會傳回您上次評估時的成本。

### <a name="automate-budget-creation"></a>自動建立預算

您可以使用[預算 API](/rest/api/consumption/budgets)，將預算的建立作業自動化。 您也可以使用[預算範本](quick-create-budget-template.md)來建立預算。 範本可讓您輕鬆地將 Azure 部署標準化，同時確保會正確設定和強制執行成本控制。

#### <a name="supported-locales-for-budget-alert-emails"></a>預算警示電子郵件支援的地區設定

有了預算，當成本超過設定的閾值時會收到警示。 每個預算對多可以設定五個電子郵件收件者。 收件者會在超過預算閾值的 24 小時內收到電子郵件警示。 不過，您的收件者可能需要收到不同語言的電子郵件。 您可以搭配預算 API 使用下列語言文化特性代碼。 使用與下列範例類似的 `locale` 參數來設定文化特性代碼。

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

文化特性代碼支援的語言：

| 文化特性代碼| 語言 |
| --- | --- |
| zh-tw | 英文 (美國) |
| ja-jp | 日文 (日本) |
| zh-cn | 中文 (簡體，中國) |
| de-de | 德文 (德國) |
| es-es | 西班牙文 (西班牙，國際) |
| fr-fr | 法文 (法國) |
| it-it | 義大利文 (義大利) |
| ko-kr | 韓文 (韓國) |
| pt-br | 葡萄牙文 (巴西) |
| ru-ru | 俄文 (俄羅斯) |
| zh-tw | 中文 (繁體，台灣) |
| cs-cz | 捷克文 (捷克共和國) |
| pl-pl | 波蘭文 (波蘭) |
| tr-tr | 土耳其文 (土耳其) |
| da-dk | 丹麥文 (丹麥) |
| dn-gb | 英文 (英國) |
| hu-hu | 匈牙利文 (匈牙利) |
| nb-bo | 挪威文 (巴克摩) (挪威) |
| nl-nl | 荷蘭文 (荷蘭) |
| pt-pt | 葡萄牙文 (葡萄牙) |
| sv-se | 瑞典文 (瑞典) |

#### <a name="common-budgets-api-configurations"></a>一般的預算 API 設定

有許多方法可在您的 Azure 環境中設定預算。 請先考慮您的案例，然後再找出可實現該案例的設定選項。 檢閱下列選項：

- **時間粒紋** - 代表您的預算用來產生和評估成本的週期性期間。 最常見的選項有 [每月]、[每季] 和 [每年]。
- **時間週期** - 代表預算的有效時間長度。 預算會主動監視，並且只有在仍然有效時才會向您發出警示。
- **通知**
  - 連絡人電子郵件 - 當預算產生成本且超過定義的限額時，電子郵件地址就會收到警示。
  - 連絡人角色 - 在給定範圍上具有相符 Azure 角色的所有使用者都會透過此選項而收到電子郵件警示。 例如，訂用帳戶擁有者會收到在訂用帳戶範圍上所建立預算的警示。
  - 連絡人群組 - 當超過警示限額時，預算會呼叫已設定的動作群組。
- **成本維度篩選** - 您可以在成本分析或查詢 API 中執行的相同篩選，也可以在您的預算上進行。 使用此篩選可減少您所監視的預算成本範圍。

找出符合您需求的預算建立選項之後，請使用 API 建立預算。 下列範例可協助您開始使用一般的預算設定。

**建立已針對多個資源和標籤來篩選的預算**

要求 URL：`PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>針對預算警示來設定成本型協調流程

您可以設定預算，以使用 Azure 動作群組來啟動自動化動作。 若要深入了解如何使用預算將動作自動化，請參閱[使用 Azure 預算進行自動化](../manage/cost-management-budget-scenario.md)。

## <a name="data-latency-and-rate-limits"></a>資料延遲與速率限制

我們建議您每天呼叫 API 的次數不要超過一次。 成本管理資料會在每四小時從 Azure 資源提供者處接收新使用量資料時重新整理一次。 較頻繁地呼叫並不會提供任何額外的資料。 這樣反而會加重負載。 若要深入了解資料變更頻率，以及資料延遲的處理方式，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>錯誤碼 429 - 呼叫計數已超過速率限制

為了讓所有成本管理訂閱者都能擁有一致的體驗，成本管理 API 會受到速率限制。 當您達到限制時，您會收到 HTTP 狀態碼 `429: Too many requests`。 我們 API 目前的輸送量限制如下：

- 每分鐘 30 次呼叫 - 其是根據個別範圍、個別使用者，或應用程式來設定限制的。
- 每分鐘 200 次呼叫 - 其是根據個別租用戶、個別使用者，或應用程式來設定限制的。

## <a name="next-steps"></a>後續步驟

- [使用 Power BI 範本應用程式分析 Azure 成本](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)。
- 使用「匯出」功能[建立和管理匯出的資料](./tutorial-export-acm-data.md)。
- 深入了解[使用量詳細資料 API](/rest/api/consumption/usageDetails) \(部分機器翻譯\)。