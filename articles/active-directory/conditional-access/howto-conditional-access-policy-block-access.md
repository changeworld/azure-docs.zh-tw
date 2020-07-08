---
title: 條件式存取-封鎖存取-Azure Active Directory
description: 建立自訂的條件式存取原則
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3ee7287f2a5cf9491ae91d434caf2f653c853a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995304"
---
# <a name="conditional-access-block-access"></a>條件式存取：封鎖存取

對於具有保守雲端遷移方法的組織而言，[封鎖所有原則] 是可以使用的選項。 

> [!CAUTION]
> 封鎖原則的設定不正確，可能會導致組織遭到鎖定而無法 Azure 入口網站。

這類原則可能會產生非預期的副作用。 在啟用之前，適當的測試和驗證非常重要。 系統管理員應該在進行變更時，利用條件式存取中的工具，例如[條件式存取報表專用模式](concept-conditional-access-report-only.md)和[What If 工具](what-if-tool.md)。

## <a name="user-exclusions"></a>使用者排除

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**急用**帳戶，以避免整個租用戶帳戶遭到鎖定。 雖然不太可能發生，但如果所有管理員都遭到鎖定而無法使用租用戶，緊急存取系統管理帳戶就可以用來登入租用戶，以採取存取權復原步驟。
   * 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)一文。
* **服務帳戶**和**服務主體**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是未與任何特定使用者繫結的非互動式帳戶。 後端服務通常會使用這些帳戶，以透過程式設計方式存取應用程式，但也可用來登入系統以便進行管理。 請排除這類服務帳戶，因為您無法透過程式設計方式來完成 MFA。 條件式存取不會封鎖服務主體所進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將其取代為[受控識別](../managed-identities-azure-resources/overview.md)。 您暫時可以在基準原則中排除這些特定帳戶，來解決此問題。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

如果使用者不在受信任的網路上，下列步驟將有助於建立條件式存取原則，以封鎖[Office 365](concept-conditional-access-cloud-apps.md#office-365-preview)以外的所有應用程式存取。 這些原則會放入[僅限報表模式](howto-conditional-access-report-only.md)來啟動，讓系統管理員可以判斷他們對現有使用者的影響。 當系統管理員認為原則符合預期時，可以將它們切換至**On**。

第一個原則會封鎖除了 Office 365 應用程式以外的所有應用程式存取（如果不在信任的位置）。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下選取 [使用者和群組]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**] 底下，選取下列選項：
   1. 在 [**包含**] 底下，選取 [**所有雲端應用程式**]。
   1. 在 [**排除**] 底下，選取 **[Office 365 （預覽）**]，選取 [**選取**]，然後選取 [**完成**]。
1. 在 [條件] 底下：
   1. 在 [條件] > [位置] 底下。
      1. 將 [設定] 設定為 [是]
      1. 在 [**包含**] 底下，選取 [**任何位置**]。
      1. 在 [**排除**] 底下，選取 [**所有信任的位置**]。
      1. 選取 [完成] 。
   1. 在 **[用戶端應用程式（預覽）**] 底下，將 **[設定] 設為** **[是]** **，然後選取**[**完成**]，
1. 在 [**存取控制**授與] 底下  >  ，選取 **[****封鎖存取**]，然後選取 [**選取**]。
1. 確認您的設定，並將 [啟用原則] 設為 [報告專用]。
1. 選取 [建立]，以建立並啟用您的原則。

以下會建立第二個原則，以要求使用多重要素驗證或適用于 Office 365 使用者的相容裝置。

1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下選取 [使用者和群組]。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**  >  **包括**] 底下，選取 [**選取應用程式**]，選擇 [ **Office 365 （預覽）**]，**然後**選取 [**選取**]，
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**]。
   1. 選取 [**需要多重要素驗證**並**要求裝置必須標記為符合規範**] 選取 [**選取**]。
   1. 確定已選取 **[要求所有選取的控制項**]。
   1. 選取 [選取] 。
1. 確認您的設定，並將 [啟用原則] 設為 [報告專用]。
1. 選取 [建立]，以建立並啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
