---
title: 自訂子網域
titleSuffix: Azure Cognitive Services
description: 每個認知服務資源的自訂子功能變數名稱稱都會透過 Azure 入口網站、Azure Cloud Shell 或 Azure CLI 建立。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: c7dd916b27cd8005162c09f7e6a090293e336719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83590633"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>認知服務的自訂子功能變數名稱稱

Azure 認知服務會針對透過 [Azure 入口網站](https://portal.azure.com)、 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)建立的每個資源，使用自訂子功能變數名稱稱。 不同于特定 Azure 區域中所有客戶常見的區域端點，自訂子功能變數名稱稱對於資源而言是唯一的。 需要自訂子功能變數名稱稱，才能啟用 Azure Active Directory (Azure AD) 進行驗證等功能。

## <a name="how-does-this-impact-existing-resources"></a>這會如何影響現有的資源？

在2019年7月1日前建立的認知服務資源，將會使用相關聯服務的區域端點。 這些端點將會使用現有和新的資源。

如果您想要遷移現有資源以利用自訂子功能變數名稱稱，讓您可以啟用如 Azure AD 的功能，請遵循下列指示：

1. 登入 Azure 入口網站，並找出您想要新增自訂子功能變數名稱稱的認知服務資源。
2. 在 [ **總覽** ] 分頁中，找出並選取 [ **產生自訂功能變數名稱**]。
3. 這會開啟一個面板，說明如何為您的資源建立唯一的自訂子域。
   > [!WARNING]
   > 在您建立自訂子功能變數名稱稱之後，便 **無法** 變更。

## <a name="do-i-need-to-update-my-existing-resources"></a>我是否需要更新現有資源？

否。 區域端點將繼續針對新的和現有的認知服務運作，而且自訂子功能變數名稱稱是選擇性的。 即使已新增自訂子功能變數名稱稱，區域端點仍將繼續使用該資源。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>如果 SDK 要求我提供資源的區域，該怎麼辦？

> [!WARNING]
> 語音服務目前 **不** 支援自訂子域。 使用語音服務和相關聯的 Sdk 時，請使用區域端點。

同時支援區域端點和自訂子功能變數名稱稱，而且可以交換使用。 但是，必須要有完整的端點。

您可以在[Azure 入口網站](https://portal.azure.com)中資源的**總覽**分頁中找到區域資訊。 如需區域端點的完整清單，請參閱 [是否有區域端點的清單？](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>自訂子功能變數名稱稱是否為區域？

是。 使用自訂的子功能變數名稱稱並不會變更認知服務資源的任何區域部分。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>自訂子功能變數名稱稱的需求為何？

自訂子功能變數名稱稱對您的資源而言是唯一的。 名稱只可包含英數位元和 `-` 字元; 長度必須介於2到64個字元之間，且不能以結尾 `-` 。

## <a name="can-i-change-a-custom-domain-name"></a>我可以變更自訂功能變數名稱嗎？

否。 自訂子功能變數名稱稱建立並與資源建立關聯之後，就無法變更。

## <a name="can-i-reuse-a-custom-domain-name"></a>我可以重複使用自訂功能變數名稱嗎？

每個自訂子功能變數名稱稱都是唯一的，因此為了重複使用您已指派給認知服務資源的自訂子功能變數名稱稱，您必須刪除現有的資源。 刪除資源之後，您可以重複使用自訂子功能變數名稱稱。

## <a name="is-there-a-list-of-regional-endpoints"></a>有區域端點的清單嗎？

是。 這是您可以搭配 Azure 認知服務資源使用的區域端點清單。

> [!NOTE]
> Translator 服務和 Bing 搜尋 Api 會使用全域端點。

| 端點類型 | 區域 | 端點 |
|---------------|--------|----------|
| 公用 | & Bing) 的全域 (Translator | `https://api.cognitive.microsoft.com` |
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

* [什麼是認知服務？](Welcome.md)
* [驗證](authentication.md)
