---
title: 條件訪問服務依賴項 - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄條件訪問中使用條件來觸發策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380019"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure 活動目錄條件訪問中的服務依賴項是什麼？ 

使用條件訪問策略，您可以指定對網站和服務的訪問要求。 例如，您的訪問要求可以包括需要多重要素驗證 （MFA） 或[託管設備](require-managed-devices.md)。 

當您直接存取網站或服務時，相關策略的影響通常很容易評估。 例如，如果您有一個策略，需要為 SharePoint Online 配置 MFA，則對共用點 Web 門戶的每個登錄都強制實施 MFA。 但是，評估策略的影響並不總是直截了當的，因為有雲應用依賴于其他雲應用。 例如，Microsoft 團隊可以提供對 SharePoint 連線資源的訪問。 因此，當您在當前方案中訪問 Microsoft Teams 時，您還受 SharePoint MFA 策略的約束。   

## <a name="policy-enforcement"></a>強制執行原則 

如果配置了服務依賴項，則可以使用早期繫結或後期綁定強制應用策略。 

- **早期繫結策略實施**意味著使用者在訪問調用應用之前必須滿足從屬服務策略。 例如，使用者在登錄到 MS Teams 之前必須滿足 SharePoint 策略。 
- 在使用者登錄到調用應用後，將執行**後期繫結原則**。 調用應用請求時，強制將延遲到下游服務的權杖。 示例包括訪問規劃器的 MS 團隊和訪問 SharePoint Office.com。 

下圖說明瞭 MS Teams 服務依賴項。 實心箭頭指示早期繫結強制，規劃器的虛線箭頭指示後期綁定強制。 

![MS 團隊服務依賴項](./media/service-dependencies/01.png)

最佳做法是，應盡可能跨相關應用和服務設置通用策略。 具有一致的安全狀態為您提供最佳的使用者體驗。 例如，為業務設置跨 Exchange Online、SharePoint Online、Microsoft 團隊和 Skype 的通用策略可顯著減少因將不同策略應用於下游服務而可能產生的意外提示。 

下表列出了其他服務依賴項，其中用戶端應用必須滿足這些依賴項  

| 用戶端應用程式         | 下游服務                          | 強制執行 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | 微軟 Azure 管理（門戶和 API） | 早期繫結 |
| 微軟課堂 | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft Teams     | Exchange                                    | 早期繫結 |
|                     | MS 規劃師                                  | 後期綁定  |
|                     | SharePoint                                  | 早期繫結 |
|                     | 商務用 Skype Online                   | 早期繫結 |
| 辦公室門戶       | Exchange                                    | 後期綁定  |
|                     | SharePoint                                  | 後期綁定  |
| 展望組      | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| PowerApps           | 微軟 Azure 管理（門戶和 API） | 早期繫結 |
|                     | Windows Azure Active Directory              | 早期繫結 |
| 隨附此逐步解說的專案             | Dynamics CRM                                | 早期繫結 |
| 商務用 Skype  | Exchange                                    | 早期繫結 |
| Visual Studio       | 微軟 Azure 管理（門戶和 API） | 早期繫結 |
| Microsoft Forms     | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft To-Do     | Exchange                                    | 早期繫結 |

## <a name="next-steps"></a>後續步驟

若要了解如何在環境中實作條件式存取，請參閱[在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)。
