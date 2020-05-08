---
title: 條件式存取服務相依性-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 條件式存取中使用條件來觸發原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b0d7816dc83a7c3536e44ff2461d85ea6178ff1
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778476"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取中的服務相依性為何？ 

透過條件式存取原則，您可以指定對網站和服務的存取需求。 例如，您的存取需求可能包括需要多重要素驗證（MFA）或[受管理的裝置](require-managed-devices.md)。 

當您直接存取網站或服務時，相關原則的影響通常很容易評估。 例如，如果您的原則需要設定 SharePoint Online 的多重要素驗證（MFA），則每次登入 SharePoint web 入口網站時，都會強制執行 MFA。 不過，因為有與其他雲端應用程式有相依性的雲端應用程式，所以不一定能夠直接進行評估原則的影響。 例如，Microsoft 小組可以提供 SharePoint Online 中資源的存取權。 因此，當您在我們目前的案例中存取 Microsoft 小組時，您也會受限於 SharePoint MFA 原則。 

## <a name="policy-enforcement"></a>強制執行原則 

如果您已設定服務相依性，則可以使用早期繫結或晚期繫結強制來套用原則。 

- **早期繫結的原則強制執行**表示使用者必須滿足相依的服務原則，才能存取呼叫的應用程式。 例如，使用者必須先滿足 SharePoint 原則，才能登入 MS 小組。 
- 在使用者登入呼叫應用程式之後，會進行**晚期繫結的原則強制執行**。 在呼叫應用程式要求時，強制會延後到下游服務的權杖。 範例包括存取 Planner 的 MS 小組，以及存取 SharePoint 的 Office.com。 

下圖說明 MS 小組服務相依性。 實心箭號表示早期繫結強制執行 Planner 的虛線箭號表示晚期繫結強制。 

![MS 小組服務相依性](./media/service-dependencies/01.png)

最佳做法是，您應該盡可能在相關的應用程式和服務中設定一般原則。 擁有一致的安全性狀態，可為您提供最佳的使用者體驗。 例如，在 Exchange Online、SharePoint Online、Microsoft 團隊和商務用 Skype 之間設定通用原則，可大幅減少可能因不同原則套用至下游服務而產生的非預期提示。 

使用 office stack 中的應用程式來完成這項操作的絕佳方式，是使用[office 365 （預覽）](concept-conditional-access-cloud-apps.md#office-365-preview) ，而不是以個別應用程式為目標。

下表列出額外的服務相依性，用戶端應用程式必須滿足這些相關性  

| 用戶端應用程式         | 下游服務                          | 強制執行 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure 管理（入口網站和 API） | 早期繫結 |
| Microsoft 課堂 | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft Teams     | Exchange                                    | 早期繫結 |
|                     | MS Planner                                  | 晚期繫結  |
|                     | SharePoint                                  | 早期繫結 |
|                     | 商務用 Skype Online                   | 早期繫結 |
| Office 入口網站       | Exchange                                    | 晚期繫結  |
|                     | SharePoint                                  | 晚期繫結  |
| Outlook 群組      | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| PowerApps           | Microsoft Azure 管理（入口網站和 API） | 早期繫結 |
|                     | Windows Azure Active Directory              | 早期繫結 |
| 專案             | Dynamics CRM                                | 早期繫結 |
| 商務用 Skype  | Exchange                                    | 早期繫結 |
| Visual Studio       | Microsoft Azure 管理（入口網站和 API） | 早期繫結 |
| Microsoft Forms     | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft To-Do     | Exchange                                    | 早期繫結 |

## <a name="next-steps"></a>後續步驟

若要了解如何在環境中實作條件式存取，請參閱[在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)。
