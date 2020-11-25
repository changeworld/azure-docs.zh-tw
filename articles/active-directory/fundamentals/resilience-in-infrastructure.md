---
title: 使用 Azure Active Directory 在您的 IAM 基礎結構中建立恢復功能
description: 架構設計人員和 IT 系統管理員的指南，未為其 IAM 基礎結構中斷的能力建立恢復功能。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ad97a822aaa6477616a6661a579df6c4ec82729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919423"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>在您的身分識別與存取管理基礎結構中建立恢復功能

Azure Active Directory 是全域雲端身分識別和存取管理系統，可為貴組織的資源提供驗證和授權等重要服務。 本檔提供指引，說明如何針對依賴 Azure Active Directory (Azure AD) 的資源，瞭解、包含和降低驗證或授權服務中斷的風險。 

檔集的設計目的是

* 身分識別架構設計人員

* 身分識別服務擁有者

* 身分識別作業小組

另請參閱 [應用程式開發人員](https://aka.ms/azureadresilience/developer) 和 [Azure AD B2C 系統](resilience-b2c.md)的檔。

## <a name="what-is-resilience"></a>什麼是復原？

在您的身分識別基礎結構內容中，復原能力是忍受服務中斷的能力，例如驗證和授權，或其他元件的失敗，對您的業務、使用者和作業的影響最少或沒有影響。 中斷的影響可能很嚴重，而復原需要進行用心規劃。

## <a name="why-worry-about-disruption"></a>為什麼要擔心中斷？

如果 Azure AD 的呼叫鏈中有任何元件失敗，則每次呼叫驗證系統都會受到中斷的影響。 這表示，如果您的基礎結構有任何部分發生問題，可能會中斷，因為使用者無法存取他們所需的應用程式。 因此，減少驗證呼叫數目以及這些呼叫中的相依性數量，對您的復原能力很重要。 應用程式開發人員可以對權杖的要求頻率進行某些控制。 例如，與開發人員合作，以確保他們盡可能使用 Azure AD 受控識別來處理其應用程式。 

在權杖型驗證系統（例如 Azure AD）中，使用者的應用程式 (用戶端) 必須先從身分識別系統取得安全性權杖，才能存取應用程式或其他資源。 在有效期間內，用戶端可以多次呈現相同的權杖，以存取應用程式。

當呈現給應用程式的權杖到期時，應用程式會拒絕權杖，而且用戶端必須從 Azure AD 取得新權杖。 取得新權杖可能需要使用者互動，例如認證提示。 使用較長的權杖減少驗證呼叫的頻率，可減少這項風險。 不過，您必須使用較少的原則評估所建立的風險來平衡權杖生命週期。 如需有關管理權杖存留期的詳細資訊，請參閱這篇有關將重新 [驗證提示優化](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)的文章。

## <a name="ways-to-increase-resilience"></a>提高復原能力的方法
下圖顯示可提高復原能力的六個具體方式。 本文的後續步驟部分中所連結的文章會詳細說明每個方法。
  
![顯示系統管理員恢復功能總覽的圖表](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>後續步驟
系統管理員和架構設計人員的復原能力資源
 
* [使用認證管理建立恢復功能](resilience-in-credentials.md)

* [以裝置狀態建立恢復功能](resilience-with-device-states.md)

* [使用連續存取評估來建立恢復功能 (CAE) ](resilience-with-continuous-access-evaluation.md)

* [在外部使用者驗證中建立恢復功能](resilience-b2b-authentication.md)

* [打造混合式驗證的復原能力](resilience-in-hybrid.md)

* [使用應用程式 Proxy 來建立應用程式存取的復原能力](resilience-on-premises-access.md)

開發人員的復原能力資源

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)
