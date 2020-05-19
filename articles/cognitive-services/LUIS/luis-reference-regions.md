---
title: 發佈區域 & 端點-LUIS
description: Azure 入口網站中指定的區域與您將發佈 LUIS 應用程式的位置相同，而且會為此相同區域產生端點 URL。
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 680887ecda0843bf770c62a4b9a4d88305ea9e73
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590905"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>撰寫和發佈區域及相關聯的金鑰

對應的 LUIS 入口網站支援三種撰寫區域。 若要將 LUIS 應用程式發行至多個區域，針對每個區域，您至少需要有一個金鑰。

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS 撰寫區域
有三個以區域為基礎的 LUIS 撰寫入口網站。 您必須在相同的區域內進行撰寫和發行。

|LUIS|撰寫區域|Azure 區功能變數名稱稱|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[previous.luis.ai](https://previous.luis.ai)|美式英文<br>非歐洲<br>非澳大利亞| `westus`|
|[au.luis.ai][au.luis.ai] <br>[previous.au.luis.ai](https://previous.au.luis.ai)|澳洲| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[previous.eu.luis.ai](https://previous.eu.luis.ai)|歐洲|`westeurope`|

撰寫區域具有[配對的故障時區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>發列區域和 Azure 資源
應用程式會發佈至與 LUIS 入口網站中新增的 LUIS 資源相關聯的所有區域。 例如，針對在[www.luis.ai][www.luis.ai]上建立的應用程式，如果您在**WESTUS**中建立 luis 或認知服務資源，並[將它新增至應用程式做為資源](luis-how-to-azure-subscription.md)，則會在該區域中發佈應用程式。

## <a name="public-apps"></a>公開應用程式
公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與其資源金鑰相關聯的區域中存取應用程式。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>發列區域系結至撰寫區域

您只能將撰寫區域應用程式發行至相對應的發行區域。 若您的應用程式目前位於錯誤的撰寫區域，請匯出該應用程式，然後針對您的發行區域將它匯入至正確的撰寫區域。

針對在 https://www.luis.ai 上建立的 LUIS 應用程式，除了不能將它發行至[歐洲](#publishing-to-europe)和[澳大利亞](#publishing-to-australia)區域之外，可以發行至其他所有端點。

## <a name="publishing-to-europe"></a>發行至歐洲

若要發行至歐洲區域，您僅能在 https://eu.luis.ai 建立 LUIS 應用程式。 若您嘗試使用歐洲區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://eu.luis.ai。 LUIS 建立的應用程式 [https://eu.luis.ai][eu.luis.ai] 不會自動遷移至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="europe-publishing-regions"></a>歐洲發行地區

 全球區域 | 撰寫 API 區域和編寫網站| 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| [歐洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 法國中部<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [歐洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 北歐<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [歐洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 西歐<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [歐洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 英國南部<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>發行至澳大利亞

若要發行至澳大利亞區域，您僅能在 https://au.luis.ai 建立 LUIS 應用程式。 若您嘗試使用澳大利亞區域的金鑰於其他區域發行，LUIS 會顯示警告訊息。 請改用 https://au.luis.ai。 LUIS 建立的應用程式 [https://au.luis.ai][au.luis.ai] 不會自動遷移至其他區域。 若要移轉它，請匯出然後再匯入該 LUIS 應用程式。

## <a name="australia-publishing-regions"></a>澳大利亞發行地區

 全球區域 | 撰寫 API 區域和編寫網站| 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| [澳洲](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| 澳大利亞東部<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>發行至其他區域

若要發佈到其他區域，您只需要建立 LUIS apps [https://www.luis.ai](https://www.luis.ai) 。

## <a name="other-publishing-regions"></a>其他發列區域

 全球區域 | 撰寫 API 區域和編寫網站| 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| 非洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南非北部<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 印度中部<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東亞<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本東部<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本西部<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南韓中部<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東南亞<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 加拿大中部<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中部<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國東部<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國東部 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中北部<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中南部<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中西部<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國西部<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國西部 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| 南美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 巴西南部<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>端點

深入瞭解[撰寫和預測端點](developer-reference-resource.md)。

## <a name="failover-regions"></a>容錯移轉區域

每個區域都有次要區域可進行故障切換。 歐洲和澳大利亞在澳大利亞的故障會進行故障切換。

撰寫區域具有[配對的故障時區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
