---
title: 條件式存取服務相依性-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 條件式存取中使用條件來觸發原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258603"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active Directory 條件式存取中的服務相依性為何？ 

使用條件式存取原則，您可以指定網站和服務的存取需求。 例如，您的存取需求可能包括要求 (MFA) 或 [受管理裝置](require-managed-devices.md)的多重要素驗證。 

當您直接存取網站或服務時，相關原則的影響通常很容易評估。 例如，如果您的原則需要多重要素驗證 (MFA) for SharePoint Online 設定，則會針對每次登入 SharePoint web 入口網站強制執行 MFA。 不過，它不一定會直接向前評估原則的影響，因為有與其他雲端應用程式有相依性的雲端應用程式。 例如，Microsoft 小組可以在 SharePoint Online 中提供資源的存取權。 因此，當您在目前的案例中存取 Microsoft 小組時，您也會受限於 SharePoint MFA 原則。 

> [!TIP]
> 使用 [office 365](concept-conditional-access-cloud-apps.md#office-365) 應用程式將以所有 office 應用程式為目標，以避免 office stack 中的服務相依性發生問題。

## <a name="policy-enforcement"></a>強制執行原則 

如果您已設定服務相依性，則可以使用早期繫結或晚期繫結強制套用原則。 

- **早期繫結的原則強制** 表示使用者必須滿足相依的服務原則，才能存取呼叫的應用程式。 例如，使用者必須在登入 MS 小組之前滿足 SharePoint 原則。 
- 當使用者登入呼叫的應用程式之後，就會**強制執行晚期繫結原則**。 在呼叫應用程式要求（下游服務的權杖）時，會延後強制執行。 範例包括存取 SharePoint 的 Planner 和 Office.com 的 MS 團隊。 

下圖說明 MS 小組服務相依性。 實心箭號表示早期繫結的強制執行規劃的虛線箭號表示晚期繫結強制執行。 

![MS 小組服務相關性](./media/service-dependencies/01.png)

最佳做法是盡可能在相關的應用程式和服務之間設定一般原則。 擁有一致的安全性狀態可為您提供最佳的使用者體驗。 例如，在 Exchange Online、SharePoint Online、Microsoft 小組和商務用 Skype 之間設定通用原則，可大幅減少套用至下游服務的不同原則可能產生的非預期的提示。 

在 Office stack 中使用應用程式完成這項工作的絕佳方法，就是使用 [office 365 應用](concept-conditional-access-cloud-apps.md#office-365) 程式，而不是以個別應用程式為目標。

下表列出用戶端應用程式必須滿足的其他服務相依性  

| 用戶端應用程式         | 下游服務                          | 強制執行 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure 管理 (入口網站和 API)  | 早期繫結 |
| Microsoft 課堂 | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft Teams     | Exchange                                    | 早期繫結 |
|                     | MS Planner                                  | 晚期繫結  |
|                     | Microsoft Stream                            | 晚期繫結  |
|                     | SharePoint                                  | 早期繫結 |
|                     | 商務用 Skype Online                   | 早期繫結 |
| Office 入口網站       | Exchange                                    | 晚期繫結  |
|                     | SharePoint                                  | 晚期繫結  |
| Outlook 群組      | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| PowerApps           | Microsoft Azure 管理 (入口網站和 API)  | 早期繫結 |
|                     | Windows Azure Active Directory              | 早期繫結 |
| 專案             | Dynamics CRM                                | 早期繫結 |
| 商務用 Skype  | Exchange                                    | 早期繫結 |
| Visual Studio       | Microsoft Azure 管理 (入口網站和 API)  | 早期繫結 |
| Microsoft Forms     | Exchange                                    | 早期繫結 |
|                     | SharePoint                                  | 早期繫結 |
| Microsoft To-Do     | Exchange                                    | 早期繫結 |

## <a name="next-steps"></a>後續步驟

若要了解如何在環境中實作條件式存取，請參閱[在 Azure Active Directory 中規劃條件式存取部署](plan-conditional-access.md)。
