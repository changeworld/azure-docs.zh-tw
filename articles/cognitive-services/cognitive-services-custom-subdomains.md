---
title: 自訂子域
titleSuffix: Azure Cognitive Services
description: 每個認知服務資源的自訂子功能變數名稱通過 Azure 門戶、Azure 雲外殼或 Azure CLI 創建。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647683"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>認知服務的自訂子功能變數名稱

Azure 認知服務對通過[Azure 門戶](https://portal.azure.com)[、Azure 雲外殼](https://azure.microsoft.com/features/cloud-shell/)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)創建的每個資源使用自訂子功能變數名稱。 與特定 Azure 區域中所有客戶都通用的區域終結點不同，自訂子功能變數名稱對資源是唯一的。 啟用 Azure 活動目錄 （Azure AD） 等功能以進行身份驗證，需要自訂子功能變數名稱。

## <a name="how-does-this-impact-existing-resources"></a>這對現有資源有什麼影響？

2019 年 7 月 1 日之前創建的認知服務資源將使用相關服務的區域終結點。 這些終結點將使用現有和新的資源。

如果要遷移現有資源以利用自訂子功能變數名稱，以便啟用 Azure AD 等功能，請按照以下說明操作：

1. 登錄到 Azure 門戶並找到要向其添加自訂子功能變數名稱的認知服務資源。
2. 在 **"概述"** 邊欄選項卡中，找到並選擇 **"生成自訂功能變數名稱**"。
3. 這將打開一個面板，其中包含為資源創建唯一自訂子域的說明。
   > [!WARNING]
   > 創建自訂子功能變數名稱後，**無法**更改該子功能變數名稱。

## <a name="do-i-need-to-update-my-existing-resources"></a>我需要更新我的現有資源嗎？

否。 區域終結點將繼續為新和現有的認知服務工作，自訂子功能變數名稱是可選的。 即使添加了自訂子功能變數名稱，區域終結點仍將繼續使用資源。

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>如果 SDK 向我詢問資源區域，該怎麼辦？

> [!WARNING]
> 此時，語音服務**不支援**自訂子域。 請使用語音服務和關聯的 SDK 時使用區域終結點。

區域終結點和自訂子功能變數名稱都受支援，可以互換使用。 但是，需要完整的終結點。

區域資訊在[Azure 門戶](https://portal.azure.com)中的"**概述"** 邊欄選項卡中提供。 有關區域終結點的完整清單，請參閱[是否有區域終結點清單？](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>自訂子功能變數名稱是區域性的嗎？

是。 使用自訂子功能變數名稱不會更改認知服務資源的任何區域方面。

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>自訂子功能變數名稱有哪些要求？

自訂子功能變數名稱對資源是唯一的。 名稱只能包括字母數位字元和`-`字元;它必須的長度介於 2 到 64 個字元之間，不能以`-`結尾。

## <a name="can-i-change-a-custom-domain-name"></a>我可以更改自訂功能變數名稱嗎？

否。 創建自訂子功能變數名稱並與資源關聯後，無法更改該名稱。

## <a name="can-i-reuse-a-custom-domain-name"></a>我可以重複使用自訂功能變數名稱嗎？

每個自訂子功能變數名稱都是唯一的，因此為了重用分配給認知服務資源的自訂子功能變數名稱，您需要刪除現有資源。 刪除資源後，可以重用自訂子功能變數名稱。

## <a name="is-there-a-list-of-regional-endpoints"></a>是否有區域終結點清單？

是。 這是可用於 Azure 認知服務資源的區域終結點的清單。

> [!NOTE]
> 翻譯文本 API 和必應搜索 API 使用全域終結點。

| 端點類型 | 區域 | 端點 |
|---------------|--------|----------|
| 公開 | 全域（譯者&必應） | `https://api.cognitive.microsoft.com` |
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
* [驗證][](authentication.md)
