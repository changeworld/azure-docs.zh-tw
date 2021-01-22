---
title: 發佈區域 & 端點-LUIS
description: Azure 入口網站中指定的區域與您發佈 LUIS 應用程式的位置相同，且會為此相同區域產生端點 URL。
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681602"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>撰寫和發佈區域及相關聯的金鑰

LUIS 入口網站支援 LUIS 撰寫區域。 若要將 LUIS 應用程式發行至多個區域，針對每個區域，您至少需要有一個金鑰。

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS 撰寫區域

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

無論區域 [www.luis.ai](https://www.luis.ai)為何，LUIS 都有一個您可以使用的入口網站。 您仍然必須在相同的區域中撰寫併發布。

撰寫區域具有 [配對的容錯移轉區域](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>發佈區域和 Azure 資源

應用程式會發佈至與 LUIS 入口網站中新增的 LUIS 資源相關聯的所有區域。 例如，針對在 [www.luis.ai][www.luis.ai]上建立的應用程式，如果您在 **WESTUS** 中建立 luis 或認知服務資源，並 [將它新增至應用程式作為資源](luis-how-to-azure-subscription.md)，則會在該區域中發佈應用程式。

## <a name="public-apps"></a>公用應用程式
公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與其資源金鑰相關聯的區域中存取應用程式。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>發佈區域系結至撰寫區域

您只能將撰寫區域應用程式發行至相對應的發行區域。 若您的應用程式目前位於錯誤的撰寫區域，請匯出該應用程式，然後針對您的發行區域將它匯入至正確的撰寫區域。

> [!NOTE]
> 在上建立的 LUIS 應用程式 https://www.luis.ai 現在可以發佈至所有端點，包括 [歐洲](#publishing-to-europe) 和 [澳大利亞](#publishing-to-australia) 地區。

## <a name="publishing-to-europe"></a>發行至歐洲

 全球區域 | 撰寫 API 區域 | 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| 歐洲 | `westeurope`| 法國中部<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 歐洲 | `westeurope`| 北歐<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 歐洲 | `westeurope`| 西歐<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 歐洲 | `westeurope`| 英國南部<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>發行至澳大利亞

 全球區域 | 撰寫 API 區域 | 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| 澳大利亞 | `australiaeast` | 澳大利亞東部<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>其他發佈區域

 全球區域 | 撰寫 API 區域 | 發行與查詢區域<br>`API region name`   |  端點 URL 格式   |
|-----|------|------|------|
| 非洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南非北部<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 印度中部<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東亞<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本東部<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本西部<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南韓中部<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 東南亞<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亞洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 北阿拉伯聯合大公國<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 加拿大中部<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中部<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國東部<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國東部 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中北部<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國中南部<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國中西部<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 美國西部<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美洲 |`westus`<br>[www.luis.ai][www.luis.ai] | 美國西部 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 南美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 巴西南部<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>端點

深入瞭解 [撰寫和預測端點](developer-reference-resource.md)。

## <a name="failover-regions"></a>容錯移轉區域

每個區域都有可容錯移轉的次要區域。 歐洲內的歐洲容錯移轉會在澳大利亞內進行容錯移轉。

撰寫區域具有 [配對的容錯移轉區域](../../best-practices-availability-paired-regions.md)。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [預先建置的實體參考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai