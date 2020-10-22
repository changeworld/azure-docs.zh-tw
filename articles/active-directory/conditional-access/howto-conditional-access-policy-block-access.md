---
title: 條件式存取-封鎖存取-Azure Active Directory
description: 建立自訂條件式存取原則
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
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366185"
---
# <a name="conditional-access-block-access"></a>條件式存取：封鎖存取

針對具有保守雲端遷移方法的組織，「封鎖所有」原則是可以使用的選項。 

> [!CAUTION]
> 封鎖原則的設定不正確可能會導致組織被鎖定 Azure 入口網站。

這類原則可能會有非預期的副作用。 在啟用之前，適當的測試和驗證是很重要的。 在進行變更時，系統管理員應該利用條件式存取中的工具，例如條件式 [存取僅限報表模式](concept-conditional-access-report-only.md) 和 [What If 工具](what-if-tool.md) 。

## <a name="user-exclusions"></a>使用者排除

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**急用**帳戶，以避免整個租用戶帳戶遭到鎖定。 雖然不太可能發生，但如果所有管理員都遭到鎖定而無法使用租用戶，緊急存取系統管理帳戶就可以用來登入租用戶，以採取存取權復原步驟。
   * 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取帳戶](../roles/security-emergency-access.md)一文。
* **服務帳戶**和**服務主體**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是未與任何特定使用者繫結的非互動式帳戶。 後端服務通常會使用這些帳戶，以透過程式設計方式存取應用程式，但也可用來登入系統以便進行管理。 請排除這類服務帳戶，因為您無法透過程式設計方式來完成 MFA。 條件式存取不會封鎖服務主體所進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將其取代為[受控識別](../managed-identities-azure-resources/overview.md)。 您暫時可以在基準原則中排除這些特定帳戶，來解決此問題。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，以封鎖除了 [Office 365](concept-conditional-access-cloud-apps.md#office-365) 以外的所有應用程式的存取權（如果使用者不在信任的網路上）。 這些原則會放入 [僅限報表模式](howto-conditional-access-insights-reporting.md) ，讓系統管理員可以判斷他們對現有使用者的影響。 當系統管理員想要套用原則時，可以將它們切換為 **開啟**。

第一個原則會封鎖除了 Microsoft 365 應用程式以外的所有應用程式存取權（如果不在信任的位置）。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派]  底下，選取 [使用者和群組]  。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [ **雲端應用程式] 或 [動作**] 下，選取下列選項：
   1. 在 [ **包含**] 底下，選取 [ **所有雲端應用程式**]。
   1. 在 [ **排除**] 底下選取 [ **Office 365**]，選取 [ **選取**]，然後選取 [ **完成**]。
1. 在 [條件] 底下：
   1. 在 [條件] > [位置] 底下。
      1. 將 [設定] 設定為 [是]
      1. 在 [ **包含**] 下，選取 **任何位置**。
      1. 在 [ **排除**] 下，選取 [ **所有信任的位置**]。
      1. 選取 [完成]。
   1. 在 [**用戶端應用程式] (預覽]) **中 **，將設定為** **[是]** **，然後選取**[**完成**]，然後選取
1. 在 [**存取控制**授與] 底下  >  ，選取 **[****封鎖存取**]，然後選取 [**選取**]。
1. 確認您的設定，並將 [啟用原則] 設為 [報告專用]。
1. 選取 [建立]，以建立並啟用您的原則。

以下會建立第二個原則，以針對 Microsoft 365 的使用者要求多重要素驗證或符合規範的裝置。

1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派]  底下，選取 [使用者和群組]  。
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式或動作**] 下  >  ** **，選取 [**選取應用程式**]，選擇 [ **Office 365**]，**然後**選取 [**選取**]，然後選取 [
1. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**]。
   1. 選取 [ **需要多重要素驗證** ，且 **需要裝置標示為符合規範** ] 選取 [ **選取**]。
   1. 確定已選取 **[需要所有選取的控制項** ]。
   1. 選取 [選取] 。
1. 確認您的設定，並將 [啟用原則] 設為 [報告專用]。
1. 選取 [建立]，以建立並啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-insights-reporting.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
