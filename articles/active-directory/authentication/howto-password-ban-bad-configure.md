---
title: 如何在 Azure AD Azure Active Directory 中禁止弱式密碼
description: 使用 Azure AD 動態禁用密碼在環境中禁用弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155047"
---
# <a name="configuring-the-custom-banned-password-list"></a>設定自訂禁用密碼清單

許多組織發現，其使用者會使用常見的當地用語 (例如，學校、球隊或知名人士) 建立密碼，因此很容易讓人猜到。 當使用者和系統管理員嘗試變更或重設密碼時，Microsoft 的自訂禁用密碼清單可讓組織在全域禁用密碼清單外，再新增要評估和封鎖的字串。

## <a name="add-to-the-custom-list"></a>新增自訂清單

要設定自訂禁用密碼清單，需要有 Azure Active Directory Premium P1 或 P2 授權。 如需 Azure Active Directory 授權的詳細資訊，請參閱[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至**Azure Active Directory** > 的**安全性** > **驗證方法** > **密碼保護**]。
1. 將 [強制使用自訂清單] 選項設定為 [是]。
1. 在 [自訂禁用密碼清單] 中新增字串，每行一個字串
   * 自訂禁用密碼清單最多可包含1000個詞彙。
   * 自訂禁用密碼清單不會區分大小寫。
   * 自訂禁用密碼清單會考量常見字元替代。
      * 範例："o" 和 "0"，或是 "a" 和 "\@"
   * 最小字串長度為 4 個字元，最大長度為 16 個字元。
1. 新增完所有字串時，按一下 [儲存]。

> [!NOTE]
> 可能需要數小時才會套用自訂禁用密碼清單的更新。

> [!NOTE]
> 自訂的禁用密碼清單限制為最多1000個詞彙。 它不是為了封鎖非常大的密碼清單而設計的。 為了充分利用自訂禁用密碼清單的優點，Microsoft 建議您先複習並瞭解自訂禁用密碼清單的設計和使用方式（請參閱[自訂禁用密碼清單](concept-password-ban-bad.md#custom-banned-password-list)），以及密碼評估演算法（請參閱[如何評估密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)）。

![在 Azure 入口網站中的 [驗證方法] 下修改自訂禁用密碼清單](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>運作方式

每次有使用者或系統管理員重設或變更 Azure AD 密碼時，密碼都會流經禁用密碼清單以確認其不在清單上。 使用 Azure AD 所設定或變更的密碼中，都會包含這項檢查。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會被禁用的內容時，他們會看到下列其中一個錯誤訊息：

* 不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。
* 可惜的是，您無法使用該密碼，因為它包含系統管理員已封鎖的文字或字元。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

[禁用密碼清單的概念性概觀](concept-password-ban-bad.md)

[Azure AD 密碼保護的概念性概觀](concept-password-ban-bad-on-premises.md)

[啟用內部部署與禁用密碼清單的整合](howto-password-ban-bad-on-premises.md)
