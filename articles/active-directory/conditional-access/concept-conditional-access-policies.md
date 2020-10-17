---
title: 建立條件式存取原則-Azure Active Directory
description: 有哪些選項可用來建立條件式存取原則，以及它們的意義為何？
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5361460f7816dd4a3b2b53deecd9d360f98ad1d3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145369"
---
# <a name="building-a-conditional-access-policy"></a>建立條件式存取原則

如同 [條件式存取](overview.md)文章中所述，條件式存取原則是 if-then 語句、 **指派** 和 **存取控制**。 條件式存取原則會將信號結合在一起，以進行決策並強制執行組織原則。

組織如何建立這些原則？ 需要什麼？ 它們的套用方式為何？

![條件式存取 (信號 + 決策 + 強制 = 原則) ](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

您可以隨時將多個條件式存取原則套用至個別使用者。 在此情況下，您必須滿足所有套用的原則。 例如，如果某個原則需要 (MFA) 的多重要素驗證，而另一個原則需要符合規範的裝置，您必須完成 MFA，並使用符合規範的裝置。 所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，必須滿足所有的指派才能觸發原則。

所有的原則都會在兩個階段強制執行：

- 第1階段：收集會話詳細資料 
   - 收集需要進行原則評估的會話詳細資料，例如網路位置和裝置身分識別。 
   - 原則評估的第1階段是在 [僅限報表模式下](concept-conditional-access-report-only.md)啟用的原則和原則進行。
- 第2階段：強制 
   - 使用階段1中收集的會話詳細資料，以找出任何未符合的需求。 
   - 如果有設定為封鎖存取權的原則，則會在此處停止強制執行，而且會封鎖使用者。 
   - 系統會提示使用者完成下列順序的第1階段未滿足的額外授與控制需求，直到符合原則為止：  
      - Multi-Factor Authentication 
      - 核准的用戶端應用程式/應用程式保護原則 
      - 受管理的裝置 (符合規範或混合式 Azure AD 聯結)  
      - 使用規定 
      - 自訂控制項  
   - 滿足所有 grant 控制項之後，請將會話控制項套用 (應用程式強制執行、Microsoft Cloud App Security 和權杖存留期)  
   - 原則評估的第2階段會針對所有啟用的原則進行。 

## <a name="assignments"></a>指派

指派部分可控制條件式存取原則的使用者、內容和位置。

### <a name="users-and-groups"></a>使用者和群組

[使用者和群組](concept-conditional-access-users-groups.md) 會指派原則將包含或排除的人員。 此指派可以包括所有使用者、特定使用者群組、目錄角色或外部來賓使用者。 

### <a name="cloud-apps-or-actions"></a>雲端應用程式或動作

[雲端應用程式或動作](concept-conditional-access-cloud-apps.md) 可以包含或排除雲端應用程式，或會受限於原則的使用者動作。

### <a name="conditions"></a>條件

原則可以包含多個 [條件](concept-conditional-access-conditions.md)。

#### <a name="sign-in-risk"></a>登入風險

如果組織有 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)，則產生的風險偵測可能會影響您的條件式存取原則。

#### <a name="device-platforms"></a>裝置平台

具有多個裝置作業系統平臺的組織，可能會想要在不同的平臺上強制執行特定原則。 

用來計算裝置平臺的資訊來自未驗證的來源，例如可變更的使用者代理程式字串。

#### <a name="locations"></a>位置

位置資料是由 IP 地理位置資料提供。 系統管理員可以選擇定義位置，並選擇將某些位置標示為信任，就像組織的網路位置一樣。

#### <a name="client-apps"></a>用戶端應用程式

依預設，條件式存取原則適用于支援新式驗證的瀏覽器應用程式、行動應用程式和桌面用戶端。 

此指派條件可讓條件式存取原則以不使用新式驗證的特定用戶端應用程式為目標。 這些應用程式包括 Exchange ActiveSync 用戶端、未使用新式驗證的舊版 Office 應用程式，以及 IMAP、MAPI、POP 和 SMTP 等郵件通訊協定。

#### <a name="device-state"></a>裝置狀態

此控制項可用來排除混合式 Azure AD 聯結或在 Intune 中標示為符合規範的裝置。 您可以執行此排除以封鎖未受管理的裝置。 

## <a name="access-controls"></a>存取控制

條件式存取原則的 [存取控制] 部分可控制原則的強制執行方式。

### <a name="grant"></a>授與

[Grant](concept-conditional-access-grant.md) 提供系統管理員一種強制執行原則的方法，讓系統管理員可以封鎖或授與存取權。

#### <a name="block-access"></a>封鎖存取

封鎖存取會這樣做，它會封鎖指定指派下的存取。 區塊控制項的功能強大，而且應該以適當的知識絕大部分是。

#### <a name="grant-access"></a>授與存取權

Grant 控制項可以觸發一或多個控制項的強制執行。 

-  (Azure Multi-Factor Authentication) 需要多重要素驗證
- 要求裝置必須標示為符合規範 (Intune) 
- 需要已加入混合式 Azure AD 的裝置
- 需要已核准的用戶端應用程式
- 需要應用程式保護原則

系統管理員可以選擇使用下列選項來要求其中一個先前的控制項或所有選取的控制項。 多個控制項的預設值是全部都需要。

- 需要 (控制項和控制項的所有選取控制項) 
- 需要其中一個選取的控制項 (控制項或控制) 

### <a name="session"></a>工作階段

[會話控制項](concept-conditional-access-session.md) 可以限制體驗 

- 使用應用程式強制執行限制
   - 目前僅適用于 Exchange Online 和 SharePoint Online。
      - 傳遞裝置資訊，以允許控制授與完整或有限存取權的體驗。
- 使用條件式存取應用程式控制
   - 使用來自 Microsoft Cloud App Security 的信號來執行下列作業： 
      - 封鎖下載、剪下、複製和印表機密檔。
      - 監視具風險的會話行為。
      - 需要標記機密檔。
- 登入頻率
   - 針對新式驗證變更預設登入頻率的能力。
- 持續性瀏覽器會話
   - 可讓使用者在關閉並重新開啟其瀏覽器視窗之後，保持登入狀態。

## <a name="simple-policies"></a>簡單原則

條件式存取原則必須至少包含下列各項才能強制執行：

- 原則的**名稱**。
- **指派**
   - 要套用原則的**使用者和/或群組**。
   - 要套用原則的**雲端應用程式或動作**。
- **存取控制**
   - **Grant** 或 **Block** 控制項

![空白的條件式存取原則](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

本文 [常用的條件式存取原則](concept-conditional-access-policy-common.md) 包含一些我們認為對大部分組織很有用的原則。

## <a name="next-steps"></a>後續步驟

[建立條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json#create-a-conditional-access-policy)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[規劃雲端式 Azure Multi-Factor Authentication 部署](../authentication/howto-mfa-getstarted.md) \(部分機器翻譯\)

[使用 Intune 管理裝置合規性](/intune/device-compliance-get-started)

[Microsoft Cloud App Security 和條件式存取](/cloud-app-security/proxy-intro-aad)
