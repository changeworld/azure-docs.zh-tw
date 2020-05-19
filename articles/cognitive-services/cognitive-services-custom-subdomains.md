---
title: 自訂子網域
titleSuffix: Azure Cognitive Services
description: 每個認知服務資源的自訂子功能變數名稱稱是透過 Azure 入口網站、Azure Cloud Shell 或 Azure CLI 所建立。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c7dd916b27cd8005162c09f7e6a090293e336719
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590633"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>認知服務的自訂子功能變數名稱稱

Azure 認知服務針對透過[Azure 入口網站](https://portal.azure.com)、 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)所建立的每個資源，使用自訂的子功能變數名稱稱。 不同于特定 Azure 區域中的所有客戶常見的區域端點，自訂子功能變數名稱稱對資源而言是唯一的。 需要有自訂子功能變數名稱稱，才能啟用 Azure Active Directory （Azure AD）之類的功能來進行驗證。

## <a name="how-does-this-impact-existing-resources"></a>這對現有資源的影響為何？

2019年7月1日前建立的認知服務資源將會使用相關服務的區域端點。 這些端點會使用現有的和新的資源。

如果您想要遷移現有的資源以利用自訂子功能變數名稱稱，讓您可以啟用 Azure AD 之類的功能，請遵循下列指示：

1. 登入 Azure 入口網站，並找出您想要新增自訂子功能變數名稱稱的認知服務資源。
2. 在 [**總覽**] 分頁中，找出並選取 [**產生自訂功能變數名稱**]。
3. 這會開啟一個面板，其中包含為您的資源建立唯一的自訂子域的指示。
   > [!WARNING]
   > 建立自訂子功能變數名稱稱之後，即**無法**變更。

## <a name="do-i-need-to-update-my-existing-resources"></a>我是否需要更新現有的資源？

不會。 區域端點將繼續適用于新的和現有的認知服務，而自訂子功能變數名稱稱則是選擇性的。 即使新增了自訂子功能變數名稱稱，區域端點仍會繼續使用資源。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>如果 SDK 向我詢問資源的區域，該怎麼做？

> [!WARNING]
> 語音服務目前**不**支援自訂子域。 使用語音服務和相關聯的 Sdk 時，請使用區域端點。

同時支援區域端點和自訂子功能變數名稱稱，並可交替使用。 不過，需要完整的端點。

您可以在[Azure 入口網站](https://portal.azure.com)中資源的 [**總覽**] 分頁取得區域資訊。 如需區域端點的完整清單，請參閱[是否有區域端點的清單？](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>自訂子功能變數名稱稱是否為區域？

是。 使用自訂子功能變數名稱稱並不會變更認知服務資源的任何區域層面。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>自訂子功能變數名稱稱的需求為何？

自訂子功能變數名稱稱對於您的資源而言是唯一的。 名稱只可包含英數位元和 `-` 字元; 長度必須介於2到64個字元之間，且結尾不能是 `-` 。

## <a name="can-i-change-a-custom-domain-name"></a>我可以變更自訂功能變數名稱嗎？

不會。 建立自訂子功能變數名稱稱並與資源相關聯之後，就無法變更它。

## <a name="can-i-reuse-a-custom-domain-name"></a>我可以重複使用自訂功能變數名稱嗎？

每個自訂子功能變數名稱稱都是唯一的，因此為了重複使用您指派給認知服務資源的自訂子功能變數名稱稱，您必須刪除現有的資源。 刪除資源之後，您可以重複使用自訂子功能變數名稱稱。

## <a name="is-there-a-list-of-regional-endpoints"></a>是否有區域端點的清單？

是。 這是您可以搭配 Azure 認知服務資源使用的區域端點清單。

> [!NOTE]
> Translator 服務和 Bing 搜尋 Api 會使用全域端點。

| 端點類型 | 區域 | 端點 |
|---------------|--------|----------|
| 公用 | 全域（Translator & Bing） | `https://api.cognitive.microsoft.com` |
| | 澳大利亞東部 | `https://australiaeast.api.cognitive.microsoft.com` |
| | 巴西南部 | `https://brazilsouth.api.cognitive.microsoft.com` |
| | 加拿大中部 | `https://canadacentral.api.cognitive.microsoft.com` |
| | 美國中部 | `https://centralus.api.cognitive.microsoft.com` |
| | 東亞 | `https://eastasia.api.cognitive.microsoft.com` |
| | 美國東部 | `https://eastus.api.cognitive.microsoft.com` |
| | 美國東部 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | 法國中部 | `https://francecentral.api.cognitive.microsoft.com` |
| | 印度中部 | `https://centralindia.api.cognitive.microsoft.com` |
| | 日本東部 | `https://japaneast.api.cognitive.microsoft.com` |
| | 南韓中部 | `https://koreacentral.api.cognitive.microsoft.com` |
| | 美國中北部 | `https://northcentralus.api.cognitive.microsoft.com` |
| | 北歐 | `https://northeurope.api.cognitive.microsoft.com` |
| | 南非北部 | `https://southafricanorth.api.cognitive.microsoft.com` |
| | 美國中南部 | `https://southcentralus.api.cognitive.microsoft.com` |
| | 東南亞 | `https://southeastasia.api.cognitive.microsoft.com` |
| | 英國南部 | `https://uksouth.api.cognitive.microsoft.com` |
| | 美國中西部 | `https://westcentralus.api.cognitive.microsoft.com` |
| | 西歐 | `https://westeurope.api.cognitive.microsoft.com` |
| | 美國西部 | `https://westus.api.cognitive.microsoft.com` |
| | 美國西部 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov 維吉尼亞州 | `https://virginia.api.cognitive.microsoft.us` |
| 中國 | 中國東部 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | 中國北部 | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>另請參閱

* [認知服務是什麼？](Welcome.md)
* [驗證](authentication.md)
