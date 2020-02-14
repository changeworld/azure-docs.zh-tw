---
title: Azure Active Directory 條件式存取常見問題 |Microsoft Docs
description: 取得 Azure Active Directory 中有關條件式存取常見問題的解答。
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
ms.openlocfilehash: baa44481c4641f69ead5335298316c837062d2c0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186056"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory 條件式存取常見問題

## <a name="which-applications-work-with-conditional-access-policies"></a>哪些應用程式會使用條件式存取原則？

如需使用條件式存取原則之應用程式的相關資訊，請參閱[在 Azure Active Directory 中使用條件式存取規則的應用程式和瀏覽器](concept-conditional-access-cloud-apps.md)。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>條件式存取原則是否會針對 B2B 共同作業和來賓使用者強制執行？

系統會針對企業對企業 (B2B) 共同作業使用者強制執行原則。 不過，在某些情況下，使用者可能無法符合原則需求。 例如，來賓使用者的組織可能不支援多重要素驗證。 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 原則也適用於商務用 OneDrive 嗎？

是的。 SharePoint Online 原則也會套用至商務用 OneDrive。

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>為什麼我無法直接在用戶端應用程式（例如 Word 或 Outlook）上設定原則？

條件式存取原則會設定存取服務的需求。 在向該服務進行驗證時會強制執行。 原則並不直接在用戶端應用程式上設定， 而是在用戶端呼叫服務時套用。 例如，在 SharePoint 上設定的原則會套用至呼叫 SharePoint 的用戶端。 在 Exchange 上設定的原則會套用至 Outlook。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>條件式存取原則是否適用于服務帳戶？

條件式存取原則適用于所有使用者帳戶。 這包括作為服務帳戶的使用者帳戶。 通常，自動執行的服務帳戶無法滿足條件式存取原則的需求。 例如，可能需要多重要素驗證。 您可以使用條件式存取原則管理設定，從原則中排除服務帳戶。 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>是否有可用來設定條件式存取原則的圖形 Api？

目前沒有。 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>對於不支援的裝置平台，預設的排除原則是什麼？

目前，條件式存取原則會在 iOS 和 Android 裝置的使用者上選擇性地強制執行。 根據預設，其他裝置平臺上的應用程式不會受到 iOS 和 Android 裝置的條件式存取原則影響。 租用戶管理員可選擇覆寫全域原則，以禁止不受支援平台上的使用者存取。

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>條件式存取原則適用于 Microsoft 小組嗎？

Microsoft Teams 在核心產能情節中非常依賴 Exchange Online 和 SharePoint Online，例如會議、行事曆和檔案共用。 當使用者直接登入 Microsoft 小組時，針對這些雲端應用程式所設定的條件式存取原則會套用至 Microsoft 小組。

在 Azure Active Directory 條件式存取原則中，Microsoft 團隊也會以雲端應用程式的形式分別支援。 針對雲端應用程式所設定的條件式存取原則，會在使用者登入時套用至 Microsoft 小組。 不過，如果其他應用程式 (例如 Exchange Online 和 SharePoint Online) 沒有正確的原則，使用者可能仍然可以直接存取這些資源。

適用於 Windows 和 Mac 的 Microsoft Teams 桌面用戶端支援新式驗證。 新式驗證以 Azure Active Directory 驗證程式庫 (ADAL) 為基礎，支援 Microsoft Office 用戶端應用程式跨平台登入。

## <a name="next-steps"></a>後續步驟

- 若要為您的環境設定條件式存取原則，請參閱[Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。 
