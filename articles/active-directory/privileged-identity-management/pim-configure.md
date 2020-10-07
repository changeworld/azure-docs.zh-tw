---
title: 什麼是 Privileged Identity Management? - Azure AD | Microsoft Docs
description: 提供 Azure AD Privileged Identity Management (PIM) 的概觀。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0b7bcfa6569edd93373f493cfaa7deff2976d35
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540824"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>什麼是 Azure AD Privileged Identity Management？

 Privileged Identity Management (PIM) 是 Azure Active Directory (Azure AD) 中的一項服務，可供您管理、控制和監視組織內重要資源的存取。 這些資源包括 Azure AD、Azure 與其他 Microsoft 線上服務 (如 Microsoft 365 或 Microsoft Intune) 中的資源。 下列影片將為您介紹重要的 PIM 概念和功能。
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>使用的理由

組織想要將能夠存取安全資訊或資源的人數降到最低，因為這樣可以降低惡意執行者取得該存取權，或者授權使用者無意中影響到敏感資源的機率。 不過，使用者仍然需要在 Azure AD、Azure、Microsoft 365 或 SaaS 應用程式中執行特殊權限作業。 組織可以將 Azure 資源和 Azure AD 的 Just-In-Time 特殊存取權限提供給使用者。 需要針對這些使用者使用其系統管理員權限的方式進行監督。

## <a name="what-does-it-do"></a>用途

Privileged Identity Management 提供以時間為基礎和以核准為基礎的角色啟用，可降低因重要資源上有過多、不必要或誤用的存取權限而帶來的風險。 以下是 Privileged Identity Management 的一些主要功能：

- 提供 Azure AD 和 Azure 資源的 **Just-In-Time** 特殊存取權限
- 使用開始和結束日期指派**有時限**的資源存取權
- 需要**核准**才能啟用特殊權限角色
- 強制**多重要素驗證**以啟用任何角色
- 使用**理由**來了解使用者啟用的原因
- 在特殊權限角色啟用時取得**通知**
- 進行**存取權檢閱**以確保使用者仍然需要角色
- 下載**稽核歷程記錄**以供內部或外部稽核

## <a name="what-can-i-do-with-it"></a>用途為何？

設定 Privileged Identity Management 後，您會在左側導覽功能表中看到 [工作]  、[管理]  和 [活動]  選項。 身為系統管理員，您將在選項之間做選擇，例如管理 **Azure AD 角色**、管理 **Azure 資源**角色或特殊權限存取群組。 當您選擇想要管理的內容時，您會看到該選項的適當選項組。

![Azure 入口網站中的 Privileged Identity Management 螢幕擷取畫面](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>角色與角色權限為何？

對於 Privileged Identity Management 中的 Azure AD 角色，只有特殊權限角色管理員或全域系統管理員角色的使用者可以管理其他系統管理員的指派。 您可以[授與其他系統管理員存取權以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。 全域管理員、安全性系統管理員、全域讀者和安全性讀取者也可以在 Privileged Identity Management 中檢視 Azure AD 角色指派。

對於 Privileged Identity Management 中的 Azure 資源角色，只有訂用帳戶管理員、資源擁有者、或資源使用者存取系統管理員可以管理其他系統管理員的指派。 作為特殊權限角色系統管理員、安全性系統管理員或安全性讀取者的使用者，依預設沒有存取權限可檢視 Privileged Identity Management 中的 Azure 資源角色指派。

## <a name="scenarios"></a>案例

Privileged Identity Management 支援下列案例：

### <a name="privileged-role-administrator-permissions"></a>特殊權限角色管理員的權限

- 啟用特定角色的核准
- 指定核准者使用者或群組來核准要求
- 檢視所有特殊權限角色的要求和核准歷程記錄

### <a name="approver-permissions"></a>核准者權限

- 檢視待決的核准 (要求)
- 核准或拒絕提高角色權限的要求 (單一和大量)
- 提供我的核准或拒絕理由

### <a name="eligible-role-user-permissions"></a>符合資格的角色使用者權限

- 要求啟用需要核准的角色
- 檢視要啟用之要求的狀態
- 如果已核准啟用，在 Azure AD 中完成您的工作

## <a name="terminology"></a>詞彙

若要深入了解 Privileged Identity Management 及其文件，請檢閱下列詞彙。

| 詞彙或概念 | 角色指派類別 | 描述 |
| --- | --- | --- |
| 合格 | 類型 | 需要使用者執行一或多個動作才能使用角色的角色指派。 如果使用者已有資格使用角色，即表示他們可以在需要執行特殊權限工作時啟用該角色。 使用者不論是具有永久角色指派還是合格角色指派，獲得的存取權並無差異。 唯一的差異在於有些使用者並不一直需要該存取權。 |
| 作用中 | 類型 | 不要求使用者執行任何動作即可使用角色的角色指派。 指派為有效的使用者具有指派給角色的權限。 |
| 啟用 |  | 此程序會執行一或多個動作，讓使用者使用有資格使用的角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。 |
| 已指派 | State | 具有作用中角色指派的使用者。 |
| 已啟用 | State | 具有合格角色指派、已執行動作來啟用角色，且目前為作用中的使用者。  啟動後，使用者便可在必須加以重新啟動之前，先為預先設定的期限使用該角色。 |
| 永久合格 | Duration | 使用者一律有資格啟用角色的角色指派。 |
| 永久有效 | Duration | 使用者一律可以使用角色而不需執行任何動作的角色指派。 |
| 合格過期 | Duration | 使用者有資格在指定的開始和結束日期內啟用角色的角色指派。 |
| 有效過期 | Duration | 使用者可以在指定的開始和結束日期內使用角色而不需執行任何動作的角色指派。 |
| Just-In-Time (JIT) 存取 |  | 一種模型，使用者會在其中獲得臨時權限以執行特殊權限的工作，這可防止惡意或未經授權的使用者在權限過期後取得存取權。 只有當使用者需要時才會獲得存取權。 |
| 最低權限存取的原則 |  | 建議的安全性做法，每位使用者只會獲得所需的最低權限，以便完成他們獲得授權而可執行的工作。 這種做法只需要最少量的全域管理員，並會改為針對特定案例使用特定的管理員角色。 |

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

如需使用者的授權相關資訊，請參閱[使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Privileged Identity Management 的授權要求](subscription-requirements.md)
- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
