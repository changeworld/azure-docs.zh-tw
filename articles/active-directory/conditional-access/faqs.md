---
title: Azure 活動目錄條件訪問常見問題 |微軟文檔
description: 獲取有關 Azure 活動目錄中的條件訪問常見問題的解答。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368080"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure 活動目錄條件訪問常見問題解答

## <a name="which-applications-work-with-conditional-access-policies"></a>哪些應用程式適用于條件訪問策略？

有關使用條件訪問策略的應用程式的資訊，請參閱[在 Azure 活動目錄中使用條件訪問規則的應用程式和瀏覽器](concept-conditional-access-cloud-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>B2B 協作和來賓使用者強制實施條件訪問策略嗎？

系統會針對企業對企業 (B2B) 共同作業使用者強制執行原則。 不過，在某些情況下，使用者可能無法符合原則需求。 例如，來賓使用者的組織可能不支援多重要素驗證。 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 原則也適用於商務用 OneDrive 嗎？

是。 SharePoint Online 原則也會套用至商務用 OneDrive。

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>為什麼我不能直接在用戶端應用（如 Word 或 Outlook）上設置策略？

條件訪問策略設置訪問服務的要求。 在向該服務進行驗證時會強制執行。 原則並不直接在用戶端應用程式上設定， 而是在用戶端呼叫服務時套用。 例如，在 SharePoint 上設定的原則會套用至呼叫 SharePoint 的用戶端。 在 Exchange 上設定的原則會套用至 Outlook。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>條件訪問策略是否適用于服務帳戶？

條件訪問策略適用于所有使用者帳戶。 這包括作為服務帳戶的使用者帳戶。 通常，無人值守的服務帳戶無法滿足條件訪問策略的要求。 例如，可能需要多重要素驗證。 通過使用條件訪問策略管理設置，可以從策略中排除服務帳戶。 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Microsoft 圖形 API 是否可用於配置條件訪問策略？

目前沒有。 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>對於不支援的裝置平台，預設的排除原則是什麼？

目前，條件訪問策略有選擇地對 iOS 和 Android 設備的使用者強制執行。 預設情況下，其他設備平臺上的應用程式不受 iOS 和 Android 設備的條件訪問策略的影響。 租用戶管理員可選擇覆寫全域原則，以禁止不受支援平台上的使用者存取。

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>條件訪問策略如何適用于 Microsoft 團隊？

Microsoft Teams 在核心產能情節中非常依賴 Exchange Online 和 SharePoint Online，例如會議、行事曆和檔案共用。 為這些雲應用設置的條件訪問策略適用于 Microsoft 團隊，當使用者直接登錄到 Microsoft Teams 時。

在 Azure 活動目錄條件訪問策略中，Microsoft 團隊也單獨作為雲應用支援。 為雲應用設置的條件訪問策略在使用者登錄時應用於 Microsoft Teams。 不過，如果其他應用程式 (例如 Exchange Online 和 SharePoint Online) 沒有正確的原則，使用者可能仍然可以直接存取這些資源。

適用於 Windows 和 Mac 的 Microsoft Teams 桌面用戶端支援新式驗證。 新式驗證以 Azure Active Directory 驗證程式庫 (ADAL) 為基礎，支援 Microsoft Office 用戶端應用程式跨平台登入。

## <a name="next-steps"></a>後續步驟

- 要為環境配置條件訪問策略，請參閱[Azure 活動目錄中的條件訪問最佳做法](best-practices.md)。 
