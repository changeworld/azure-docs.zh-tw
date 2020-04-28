---
title: 使用 What If 工具進行條件式存取的疑難排解-Azure Active Directory
description: 哪裡可以找到已套用的條件式存取原則，以及為何
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73175786"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用 What If 工具進行條件式存取的疑難排解

條件式存取中的[What If 工具](what-if-tool.md)在嘗試瞭解為何原則在特定情況下或未套用至使用者，或原則會套用至已知狀態時，都很強大。

What If 工具位於**Azure 入口網站** > **Azure Active Directory** > **條件式存取** > **What If**中。

![預設狀態下的條件式存取 What If 工具](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 工具目前不會在僅限報表模式中評估原則。

## <a name="gathering-information"></a>正在收集資訊

What If 工具只需要**使用者**就可以開始使用。 

下列其他資訊是選擇性的，但有助於縮小特定案例的範圍。

* 雲端應用程式或動作
* IP 位址 
* Country
* 裝置平台
* 用戶端應用程式（預覽）
* 裝置狀態（預覽） 
* 登入風險

這項資訊可從使用者、裝置或 Azure AD 登入記錄檔收集。

## <a name="generating-results"></a>產生結果

輸入在上一節中收集的準則，然後選取 [ **What If** ] 以產生結果清單。 

您可以在任何時間點選取 [**重設**]，以清除任何準則輸入並返回預設狀態。

## <a name="evaluating-results"></a>評估結果

### <a name="policies-that-will-apply"></a>將套用的原則

此清單會顯示條件的條件式存取原則。 此清單會同時包含適用的授與和會話控制項。 範例包括需要多重要素驗證才能存取特定的應用程式。

### <a name="policies-that-will-not-apply"></a>將不適用的原則

此清單會顯示條件式存取原則，在套用條件時不適用。 此清單會包含任何原則，以及它們不適用的原因。 範例包括可以從原則中排除的使用者和群組。

## <a name="use-case"></a>使用案例

許多組織會根據網路位置建立原則，允許受信任的位置和封鎖不應發生存取的位置。

若要驗證是否已適當設定，系統管理員可以使用 What If 工具來模擬存取，從應該允許的位置，到應該拒絕的位置。

![What If 工具顯示具有封鎖存取權的結果](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

在此情況下，使用者會遭到封鎖而無法存取其前往北歐的任何雲端應用程式，因為 Contoso 已封鎖該位置的存取。

此測試可以擴充以納入其他資料點，以縮小範圍。

## <a name="next-steps"></a>後續步驟

* [何謂條件式存取？](overview.md)
* [Azure Active Directory Identity Protection 是什麼？](../identity-protection/overview-v2.md)
* [什麼是裝置身分識別？](../devices/overview.md)
* [運作方式：Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
