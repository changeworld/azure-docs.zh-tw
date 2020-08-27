---
title: 使用 What If 工具對條件式存取進行疑難排解 - Azure Active Directory
description: 要在哪裡尋找所套用的條件式存取原則及其原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb51e8590e2859a9fe8692f79c5b1238ed5a2098
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948176"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用 What If 工具對條件式存取進行疑難排解

條件式存取中的 [What If 工具](what-if-tool.md)是一個強大的工具，可協助了解系統為什麼會在特定情況下將原則套用至 (或是不套用至) 使用者，或是原則是否會在已知狀態下套用。

What If 工具位於 **Azure 入口網站** > [Azure Active Directory] > [條件式存取] > [What If]。

![處於預設狀態的條件式存取 What If 工具](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 工具目前並不會評估處於報告專用模式的原則。

## <a name="gathering-information"></a>收集資訊

What If 工具僅需要**使用者**便可以開始使用。 

下列其他資訊是選擇性的，但是可以協助縮小特定案例的範圍。

* 雲端應用程式或動作
* IP 位址 
* 國家/區域
* 裝置平台
* 用戶端應用程式 (預覽)
* 裝置狀態 (預覽) 
* 登入風險

此資訊可以從使用者、其裝置或 Azure AD 登入記錄中收集。

## <a name="generating-results"></a>產生結果

輸入在上一節中所收集的準則，然後選取 [What If] 來產生結果清單。 

您可以在任何時候選取 [重設] 以清除任何準則輸入，並返回預設狀態。

## <a name="evaluating-results"></a>評估結果

### <a name="policies-that-will-apply"></a>會套用的原則

此清單將會顯示在此條件下會套用哪些條件式存取原則。 此清單將會同時包含適用的授與和工作階段控制項。 範例包括要求使用多重要素驗證來存取特定應用程式。

### <a name="policies-that-will-not-apply"></a>不會套用的原則

此清單將會顯示在套用此條件時不會套用的條件式存取原則。 此清單將會包含任何原則及不會加以套用的原因。 範例包括可能會從原則中排除的使用者和群組。

## <a name="use-case"></a>使用案例

許多組織都會根據網路位置建立原則，允許信任的位置，同時封鎖不應該發生存取的位置。

若要確認已適當做出某個設定，系統管理員可以使用 What If 工具來模擬存取，並從應該允許及應該拒絕的位置嘗試進行存取。

[![What If 工具顯示具有封鎖存取權的結果](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

在此情況下，當使用者前往北韓時，系統會封鎖其存取任何雲端應用程式的能力，因為 Contoso 已封鎖來自該位置的存取。

此測試可以擴充以納入其他資料點來縮小範圍。

## <a name="next-steps"></a>後續步驟

* [何謂條件式存取？](overview.md)
* [Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview-identity-protection.md)
* [什麼是裝置身分識別？](../devices/overview.md)
* [運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)