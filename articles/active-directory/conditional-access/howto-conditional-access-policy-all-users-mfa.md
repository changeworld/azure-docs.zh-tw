---
title: 條件式存取-所有使用者都需要 MFA-Azure Active Directory
description: 建立自訂的條件式存取原則，以要求所有使用者執行多重要素驗證
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af1347ac57e1a5c5ae99744924ea04fe9757581
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995338"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>條件式存取：所有使用者都需要 MFA

身為 Alex Weinert，Microsoft 身分識別安全性的目錄在他的文章中提及[您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)：

> 您的密碼並不重要，但 MFA 的確沒問題！ 根據我們的研究，當您使用 MFA 時，您的帳戶會比99.9% 更不可能遭到入侵。

本文中的指導方針可協助您的組織為您的環境建立平衡的 MFA 原則。

## <a name="user-exclusions"></a>使用者排除

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**急用**帳戶，以避免整個租用戶帳戶遭到鎖定。 雖然不太可能發生，但如果所有管理員都遭到鎖定而無法使用租用戶，緊急存取系統管理帳戶就可以用來登入租用戶，以採取存取權復原步驟。
   * 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)一文。
* **服務帳戶**和**服務主體**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是未與任何特定使用者繫結的非互動式帳戶。 後端服務通常會使用這些帳戶，以透過程式設計方式存取應用程式，但也可用來登入系統以便進行管理。 請排除這類服務帳戶，因為您無法透過程式設計方式來完成 MFA。 條件式存取不會封鎖服務主體所進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將其取代為[受控識別](../managed-identities-azure-resources/overview.md)。 您暫時可以在基準原則中排除這些特定帳戶，來解決此問題。

## <a name="application-exclusions"></a>應用程式排除專案

組織可能會有許多使用中的雲端應用程式。 並非所有的應用程式都可能需要同等的安全性。 例如，薪資和出席應用程式可能需要 MFA，但該餐廳可能不會。 系統管理員可以選擇從其原則中排除特定的應用程式。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，以要求所有使用者執行多重要素驗證。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [**包含**] 底下，選取 [**所有使用者**]
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**] 底下  >  ** **，選取 [**所有雲端應用程式**]。
   1. 在 [**排除**] 底下，選取不需要多重要素驗證的任何應用程式。
1. 在**Conditions**  >  **[用戶端應用程式（預覽）**] 條件下，將 [設定] 設為 **[是** **Configure** 在 **[選取要套用此原則的用戶端應用程式**] 下，保留選取所有預設值，然後選取 [**完成**]。
1. 在 [存取控制] > [授與] 底下選取 [授與存取權] 和 [需要多重要素驗證]，然後選取 [選取]。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

### <a name="named-locations"></a>具名位置

組織可以選擇將已知的網路位置（稱為「**已命名位置**」）併入其條件式存取原則。 這些命名位置可能包括受信任的 IPv4 網路，例如總公司位置的。 如需有關設定命名位置的詳細資訊，請參閱[Azure Active Directory 條件式存取中的位置條件為何？](location-condition.md)一文。

在上述範例原則中，如果從公司網路存取雲端應用程式，組織可能會選擇不要求多重要素驗證。 在此情況下，他們可以將下列設定新增至原則：

1. 在 [**指派**] 底下，選取 [**條件**] [  >  **位置**]。
   1. 設定 [是]。
   1. 包含 [任何位置]。
   1. 排除 [所有信任的位置]。
   1. 選取 [完成] 。
1. 選取 [完成] 。
1. **儲存**原則變更。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
