---
title: 條件式存取-需要適用于系統管理員的 MFA-Azure Active Directory
description: 建立自訂的條件式存取原則，以要求系統管理員執行多重要素驗證
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90e8a8b0926575b5a40a8c0ca7820e31827434ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755204"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>條件式存取：系統管理員需要 MFA

指派系統管理許可權的帳戶是攻擊者的目標。 在這些帳戶上要求多重要素驗證（MFA），是降低那些帳戶遭到入侵風險的簡單方法。

Microsoft 建議您至少要求下列角色的 MFA：

* 計費管理員
* 條件式存取系統管理員
* Exchange 系統管理員
* 全域管理員
* 技術服務人員（密碼）系統管理員
* 密碼管理員
* 安全性系統管理員
* SharePoint 管理員
* 使用者管理員

組織可以選擇包含或排除角色（如其所示）。

## <a name="user-exclusions"></a>使用者排除專案

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**中斷玻璃**帳戶，以避免整個租使用者帳戶鎖定。 在不太可能發生的情況下，所有系統管理員都會被鎖定在您的租使用者中，您的緊急存取系統管理帳戶可以用來登入租使用者，採取復原存取的步驟。
   * 如需詳細資訊，請參閱[Azure AD 中的管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)一文。
* **服務帳戶**和**服務主體**，例如 Azure AD Connect 同步帳戶。 服務帳戶是不會與任何特定使用者系結的非互動式帳戶。 後端服務通常會使用它們，以便以程式設計方式存取應用程式，但也會用來登入系統以供管理之用。 您應該排除這類服務帳戶，因為無法以程式設計方式完成 MFA。 條件式存取不會封鎖服務主體所進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將它們取代為[受管理的身分識別](../managed-identities-azure-resources/overview.md)。 暫時的因應措施是，您可以從基準原則中排除這些特定帳戶。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，要求這些指派的系統管理角色執行多重要素驗證。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入**Azure 入口網站**。
1. 流覽至**Azure Active Directory** > **安全性** > **條件式存取**。
1. 選取 [新增原則]****。
1. 提供您的原則名稱。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [**指派**] 底下，選取 [**使用者和群組**]
   1. 在 [**包含**] 底下，選取 [**目錄角色（預覽）** ]，然後至少選擇下列角色：
      * 驗證系統管理員
      * 計費管理員
      * 條件式存取系統管理員
      * Exchange 系統管理員
      * 全域管理員
      * 服務台管理員
      * 密碼管理員
      * 安全性系統管理員
      * SharePoint 管理員
      * 使用者管理員
   1. 在 [**排除**] 底下，選取 [**使用者和群組**]，然後選擇貴組織的 [緊急存取] 或 [中斷玻璃帳戶]。 
   1. 選取 [完成]  。
1. 在 [**雲端應用程式或動作** > ]**底下，選取**[**所有雲端應用程式**]，然後選取 [**完成**]。
1. 在**Conditions** > **[條件用戶端應用程式（預覽）**] 底下，將 [設定]**設為** **[是]**，**然後選取**[
1. 在 **[存取控制** > **授**與] 底下，選取 **[授與存取權**，**需要多重要素驗證**]，然後選取 [**選取**]。
1. 確認您的設定，並將 [**啟用原則**] 設為 [**開啟**]。
1. 選取 [**建立**] 以建立以啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取的一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
