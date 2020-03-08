---
title: 建立條件式存取原則-Azure Active Directory
description: 有哪些選項可用來建立條件式存取原則，以及它們的意義為何？
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2ebcc885b4018f4d9c3ff1b525ffc19b1abdda
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671933"
---
# <a name="building-a-conditional-access-policy"></a>建立條件式存取原則

如[什麼是條件式存取](overview.md)一文所述，條件式存取原則是**指派**和**存取控制**的 if then 語句。 條件式存取原則會將信號結合在一起，以做出決策，並強制執行組織原則。

組織如何建立這些原則？ 需要什麼？

![條件式存取（信號 + 決策 + 強制 = 原則）](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>指派

指派部分會控制條件式存取原則的人員、目標和位置。

### <a name="users-and-groups"></a>使用者和群組

[使用者和群組](concept-conditional-access-users-groups.md)會指派原則將包含或排除的人員。 此指派可包括所有使用者、特定使用者群組、目錄角色或外部來賓使用者。 

### <a name="cloud-apps-or-actions"></a>雲端應用程式或動作

[雲端應用程式或動作](concept-conditional-access-cloud-apps.md)可以包含或排除將受限於原則的雲端應用程式或使用者動作。

### <a name="conditions"></a>條件

原則可以包含多個[條件](concept-conditional-access-conditions.md)。

#### <a name="sign-in-risk"></a>登入風險

針對具有[Azure AD Identity Protection](../identity-protection/overview.md)的組織，產生的風險偵測可能會影響您的條件式存取原則。

#### <a name="device-platforms"></a>裝置平台

具有多個裝置作業系統平臺的組織可能會想要在不同的平臺上強制執行特定原則。 

用來計算裝置平臺的資訊來自未經驗證的來源，例如可以變更的使用者代理字串。

#### <a name="locations"></a>位置

位置資料是由 IP 地理位置資料所提供。 系統管理員可以選擇定義位置，並選擇將其標示為信任的部分，如同其組織的網路位置。

#### <a name="client-apps"></a>用戶端應用程式

根據預設，條件式存取原則適用于支援新式驗證的瀏覽器應用程式、行動應用程式和桌面用戶端。 

此指派條件可讓條件式存取原則以不使用新式驗證的特定用戶端應用程式為目標。 這些應用程式包括 Exchange ActiveSync 用戶端、未使用新式驗證的舊版 Office 應用程式，以及 IMAP、MAPI、POP 和 SMTP 等郵件通訊協定。

#### <a name="device-state"></a>裝置狀態

此控制項用來排除已加入混合式 Azure AD 或在 Intune 中標示為相容的裝置。 您可以執行此排除動作來封鎖未受管理的裝置。 

## <a name="access-controls"></a>存取控制

條件式存取原則的存取控制部分會控制強制執行原則的方式。

### <a name="grant"></a>授與

[Grant](concept-conditional-access-grant.md)提供系統管理員強制執行原則的方法，讓他們可以封鎖或授與存取權。

#### <a name="block-access"></a>封鎖存取

封鎖存取會這麼做，它會在指定的指派下封鎖存取。 封鎖控制項的功能強大，而且應該以適當的知識絕大部分是。

#### <a name="grant-access"></a>授與存取權

授與控制項可以觸發一個或多個控制項的強制執行。 

- 需要多重要素驗證（Azure 多重要素驗證）
- 需要裝置標記為相容（Intune）
- 需要已加入混合式 Azure AD 的裝置
- 需要已核准的用戶端應用程式
- 需要應用程式保護原則

系統管理員可以選擇使用下列選項來要求其中一個先前的控制項或所有選取的控制項。 多個控制項的預設值是 [全部要求]。

- 需要所有選取的控制項（控制項和控制項）
- 需要其中一個選取的控制項（控制項或控制項）

### <a name="session"></a>工作階段

[會話控制項](concept-conditional-access-session.md)可以限制體驗 

- 使用應用程式強制執行限制
   - 目前僅適用于 Exchange Online 和 SharePoint Online。
      - 會傳遞裝置資訊，以允許控制授與完整或有限存取權的體驗。
- 使用條件式存取應用程式控制
   - 使用來自 Microsoft Cloud App Security 的信號來執行如下動作： 
      - 封鎖下載、剪下、複製及列印敏感性檔。
      - 監視風險會話行為。
      - 需要標記敏感性檔案。
- 登入頻率
   - 能夠變更新式驗證的預設登入頻率。
- 持續性瀏覽器會話
   - 可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。

## <a name="simple-policies"></a>簡單原則

條件式存取原則至少必須包含下列各項才能強制執行：

- 原則的**名稱**。
- **指派**
   - 要套用原則的**使用者和/或群組**。
   - 要套用原則的**雲端應用程式或動作**。
- **存取控制**
   - **授**與或**封鎖**控制項

![空白的條件式存取原則](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

常見的[條件式存取原則](concept-conditional-access-policy-common.md)一文包含一些我們認為對大部分組織很有用的原則。

## <a name="next-steps"></a>後續步驟

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[規劃以雲端為基礎的 Azure 多因素驗證部署](../authentication/howto-mfa-getstarted.md)

[使用 Intune 管理裝置合規性](/intune/device-compliance-get-started)

[Microsoft Cloud App Security 和條件式存取](/cloud-app-security/proxy-intro-aad)
