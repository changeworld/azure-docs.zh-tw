---
title: 條件式存取 - 針對 Azure 管理要求 MFA - Azure Active Directory
description: 建立自訂的條件式存取原則，以針對 Azure 管理工作要求多重要素驗證
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
ms.openlocfilehash: 7ee4c7776dbddf089d290d760d6c0689ba9c0102
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283022"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>條件式存取：需要 Azure 管理的 MFA

組織會使用各種不同的 Azure 服務，並從下列 Azure Resource Manager 型工具管理這些服務：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

這些工具可提供高特殊權限的資源存取權，以改變整個訂用帳戶的設定、服務設定和訂用帳戶計費。 為了保護這些特殊權限資源，Microsoft 建議您針對存取這些資源的使用者要求多重要素驗證。

## <a name="user-exclusions"></a>使用者排除

條件式存取原則是功能強大的工具，建議您從原則中排除下列帳戶：

* **緊急存取**或**急用**帳戶，以避免整個租用戶帳戶遭到鎖定。 雖然不太可能發生，但如果所有管理員都遭到鎖定而無法使用租用戶，緊急存取系統管理帳戶就可以用來登入租用戶，以採取存取權復原步驟。
   * 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)一文。
* **服務帳戶**和**服務主體**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是未與任何特定使用者繫結的非互動式帳戶。 後端服務通常會使用這些帳戶，以透過程式設計方式存取應用程式，但也可用來登入系統以便進行管理。 請排除這類服務帳戶，因為您無法透過程式設計方式來完成 MFA。 條件式存取不會封鎖服務主體所進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將其取代為[受控識別](../managed-identities-azure-resources/overview.md)。 您暫時可以在基準原則中排除這些特定帳戶，來解決此問題。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟會協助您建立條件式存取原則，以要求可存取 [Microsoft Azure 管理](concept-conditional-access-cloud-apps.md#microsoft-azure-management)應用程式的使用者執行多重要素驗證。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [雲端應用程式或動作] > [包含] 底下選取 [選取應用程式]、選擇 [Microsoft Azure 管理]，然後依序選取 [選取] 和 [完成]。
1. 在**Conditions**  >  **[條件用戶端應用程式（預覽）**] 底下**的 [選取用戶端應用程式] 下，將套用此原則以**保留所有預設值並選取 [**完成**]。
1. 在 [存取控制] > [授與] 底下選取 [授與存取權] 和 [需要多重要素驗證]，然後選取 [選取]。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
