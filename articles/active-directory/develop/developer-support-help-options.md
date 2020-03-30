---
title: Azure AD 應用程式開發人員的支援和協助選項 | Microsoft Docs
description: 了解如何在建立應用程式以與 Microsoft 身分識別 (Azure Active Directory 和 Microsoft 帳戶) 整合時，取得開發相關疑難和問題的協助和支援
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160962"
---
# <a name="support-and-help-options-for-developers"></a>開發人員的支援和協助選項

如果您是剛開始整合 Azure Active Directory (Azure AD)、Microsoft 身分識別或 Microsoft Graph API，還是在您將新功能實作到應用程式時，您有時會需要從社群取得協助，或是需要了解您身為開發人員所擁有的支援選項。 本文會協助您了解這些選項，包括：

> [!div class="checklist"]
> * 如何搜尋社群是否尚未回答您的問題，或您嘗試實作的功能是否已有現存文件
> * 在某些情況下，您只想使用我們的支援工具來協助您進行特定問題的偵錯
> * 如果您找不到所需的解答，就可以在「Stack Overflow」** 上發問
> * 如果您發現我們的驗證程式庫有問題，請提出 GitHub** 問題
> * 最後，如果您需要與人對談，您可以開啟支援要求

## <a name="search"></a>搜尋

如果您有開發相關問題，您可以在文件、[GitHub 範例](https://github.com/azure-samples)，或 [Stack Overflow](https://www.stackoverflow.com) 問題的解答中，找到您需要的答案。

### <a name="scoped-search"></a>限域搜尋

為了更快獲得結果，請在您慣用的搜尋引擎中使用下列查詢，將搜尋範圍限制在 Stack Overflow、文件和程式碼範例：

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

其中，*{Your Search Terms}* 對應您的搜尋關鍵字。

## <a name="use-the-development-support-tools"></a>使用開發支援工具

| 工具  | 描述  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | 貼上識別碼或存取權杖以將宣告名稱和值解碼。 |
| [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer)| 此工具可讓您針對 Microsoft Graph API 提出要求並查看回應。 |

## <a name="post-a-question-to-stack-overflow"></a>在 Stack Overflow 張貼問題

Stack Overflow 是適用於開發相關問題的慣用管道。 開發人員社群成員和 Microsoft 小組成員會在這裡直接參與以協助您解決問題。

若無法透過搜尋找到問題的解答，請將新問題提交至 Stack Overflow。 在提出問題時使用下列其中一個標記，以協助社群更快速地識別和回答您的問題：

|元件/區域  | Tags |
|---------|---------|
| ADAL 程式庫 | [[阿達爾]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL 程式庫     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN 中介軟體  | [[azure 活動目錄]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[微軟圖]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| 與驗證或授權主題相關的其他任何區域 | [[azure 活動目錄]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Stack Overflow 的下列貼文包含如何提出問題以及如何新增原始程式碼的提示。 遵循這些指導方針以增加社群成員快速評估並回應您問題的機會：

* [要如何問一個好問題](https://stackoverflow.com/help/how-to-ask)
* [如何建立最小、完成且可驗證的範例](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>建立 GitHub 問題

如果您發現與我們的程式庫有關的錯誤或問題，請在我們的 GitHub 存放庫上提出問題。 我們的程式庫屬於開放原始碼，因此您也可以提交提取要求。

有關庫及其 GitHub 存儲庫的清單，請參閱以下內容：

* [Azure 活動目錄身份驗證庫 （ADAL）](../azuread-dev/active-directory-authentication-libraries.md)庫和 GitHub 存儲庫
* [微軟身份驗證庫 （MSAL）](reference-v2-libraries.md)庫和 GitHub 存儲庫

## <a name="open-a-support-request"></a>開啟支援要求

如果您需要與人對談，您可以開啟支援要求。 如果您是 Azure 的客戶，則有幾個支援選項可供您使用。 若要比較方案，請參閱[本頁](https://azure.microsoft.com/support/plans/)。 Azure 的客戶也可以使用開發人員支援。 如需如何購買開發人員支援方案的相關資訊，請參閱[本頁](https://azure.microsoft.com/support/plans/developer/)。

* 如果您已經有 Azure 支援方案，請[在此開啟支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* 如果您不是 Azure 的客戶，您也可以透過[我們的商業支援](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)，向 Microsoft 開啟支援要求。

您也可以嘗試[虛擬專員](https://support.microsoft.com/contactus/?ws=support)，以取得支援或提出問題。
