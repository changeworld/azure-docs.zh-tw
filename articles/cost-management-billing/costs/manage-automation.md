---
title: 使用自動化管理 Azure 成本
description: 此文章說明如何使用自動化來管理 Azure 成本。
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449826"
---
# <a name="manage-costs-with-automation"></a>使用自動化管理成本

您可以使用成本管理自動化來建置一組自訂的解決方案，以擷取及管理成本資料。 此文章涵蓋成本管理自動化的常見案例，以及根據您的情況所提供的選項。 如果您想要使用 API 進行開發，可使用常見的 API 要求範例，以幫助加快開發程序。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>將成本資料擷取作業自動化以用於離線分析

您可能需要下載您的 Azure 成本資料，以將其與其他資料集合併。 或者，您可能需要將成本資料整合到您自己的系統中。 視涉及的資料量而定，會有不同的選項可用。 在任何情況下，您都必須擁有適當範圍的成本管理權限，才能使用 API 與工具。 如需詳細資訊，請參閱[指派資料存取權](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data)。

## <a name="suggestions-for-handling-large-datasets"></a>處理大型資料集的相關建議

如果您的組織擁有跨許多資源或訂用帳戶運作的大型 Azure，您將會有大量的使用量詳細資料。 Excel 通常無法載入這類大型檔案。 在此情況下，我們建議您採用下列選項：

**Power BI**

Power BI 可用來內嵌及處理大量資料。 如果您是 Enterprise 合約客戶，可以使用 Power BI 範本應用程式來分析您計費帳戶的成本。 報表會包含客戶所使用的重要檢視。 如需詳細資訊，請參閱[使用 Power BI 範本應用程式分析 Azure 成本](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。

**Power BI 資料連接器**

如果您想要每天分析資料，建議使用 [Power BI 資料連接器](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)取得資料以進行詳細分析。 連接器會將您建立的任何報表保持在最新狀態，因而會導致產生更多成本。

**成本管理匯出**

您可能並不需要每天分析資料。 如果是這樣，請考慮使用成本管理的[匯出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)功能，來排定將資料匯出到 Azure 儲存體帳戶的排程。 然後您可以視需要將資料載入到 Power BI 中，或如果檔案夠小時，就可以在 Excel 中分析。 Azure 入口網站提供匯出功能，或者您可以使用[匯出 API](https://docs.microsoft.com/rest/api/cost-management/exports) \(部分機器翻譯\) 設定匯出作業。

**使用量詳細資料 API**

如果您有小型成本資料集，請考慮採用[使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usageDetails) \(部分機器翻譯\)。 如果您有大量成本資料，應該盡可能要求某段時間範圍內最少量的使用量資料。 若要這樣做，請指定小型時間範圍，或在您的要求中使用篩選條件。 例如，在您需要三年成本資料的情況下，若針對不同的時間範圍進行多次呼叫 (而非使用單次呼叫) 時，API 確實會比較好。 您可以從該處將資料載入到 Excel，以供進一步分析。

## <a name="automate-retrieval-with-usage-details-api"></a>運用使用量詳細資料 API 將擷取自動化

[使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usageDetails) 提供一個簡單的方法，來取得與您的 Azure 帳單相對應的原始未彙總成本資料。 當您的組織需要程式設計資料擷取解決方案時，API 會很有用。 如果您想要分析較小型的成本資料集，請考慮使用 API。 但如果您有較大型的資料集，應該使用先前已經確定的其他解決方案。 使用量詳細資料中的資料是依據每天每一計量的方式提供的。 計算您的每月帳單時就是使用這個方式。 這些 API 的公開推出 (GA) 版本是 `2019-10-01`。 使用 `2019-04-01-preview` 來存取保留的預覽版本及使用 API 購買的 Azure Marketplace 項目。

### <a name="usage-details-api-suggestions"></a>使用量詳細資料 API 的相關建議

**要求排程**

我們建議您每天對使用量詳細資料 API 發出的要求「不要超過一個」。 如需成本資料重新整理頻率，以及如何處理進位的詳細資訊，請參閱[了解成本管理資料](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule)。

**以最上層範圍為目標但不篩選**

使用 API 可取得您在最高層級範圍所需的所有資料。 等到所有需要的資料都內嵌之後，再執行任何篩選、群組或彙總分析。 API 已特別針對提供大量未彙總的原始成本資料最佳化。 若要深入了解成本管理中可用的範圍，請參閱[了解並使用範圍](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes)。 當您下載了某個範圍需要的資料之後，請使用 Excel 搭配篩選條件與樞紐分析表進一步分析資料。

## <a name="example-usage-details-api-requests"></a>使用量詳細資料 API 要求範例

Microsoft 客戶會使用下列範例要求來解決您可能會遇到的常見情況。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>取得特定日期範圍期間某個範圍的使用量詳細資料

要求所傳回的資料會與計費系統收到使用量的日期相對應。 其中可能包含來自多張發票的成本。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>取得分攤成本詳細資料

如果您需要實際成本來顯示所產生的購買，請在下列要求中將「計量」變更為 `ActualCost`。 若要使用攤銷與實際成本，您必須使用 `2019-04-01-preview` 版本。 目前 API 版本的運作方式與 `2019-10-01` 版本相同，不同之處在於新的類型/計量屬性已變更的屬性名稱。 如果您有 Microsoft 客戶合約，您的篩選條件就會是下列範例中的 `startDate` 與 `endDate`。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>使用「匯出」功能重複擷取大型成本資料集

「匯出」功能是用於排定定期備份成本資料的解決方案。 針對使用量檔案可能太大，導致無法用使用量詳細資料 API 可靠地呼叫及下載資料的組織，建議使用此功能擷取未彙總的成本資料。 資料會放在您選擇的 Azure 儲存體帳戶中。 您可以將資料從該位置載入到您自己的系統中，並由您的小組視需要加以分析。 若要在 Azure 入口網站中設定匯出功能，請參閱[匯出資料](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。

## <a name="data-latency-and-rate-limits"></a>資料延遲與速率限制

我們建議您每天呼叫 API 的次數不要超過一次。 成本管理資料會在每四小時從 Azure 資源提供者處接收新使用量資料時重新整理一次。 較頻繁地呼叫並不會提供任何額外的資料。 這樣反而會加重負載。 若要深入了解資料變更頻率，以及資料延遲的處理方式，請參閱[了解成本管理資料](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>錯誤碼 429 - 呼叫計數已超過速率限制

為了讓所有成本管理訂閱者都能擁有一致的體驗，成本管理 API 會受到速率限制。 當您達到限制時，您會收到 HTTP 狀態碼 `429: Too many requests`。 我們 API 目前的輸送量限制如下：

- 每分鐘 30 次呼叫 - 其是根據個別範圍、個別使用者，或應用程式來設定限制的。
- 每分鐘 200 次呼叫 - 其是根據個別租用戶、個別使用者，或應用程式來設定限制的。

## <a name="next-steps"></a>後續步驟

- [使用 Power BI 範本應用程式分析 Azure 成本](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。
- 使用「匯出」功能[建立和管理匯出的資料](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。
- 深入了解[使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usageDetails) \(部分機器翻譯\)。