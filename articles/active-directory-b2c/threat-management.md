---
title: 管理對資源和資料的威脅
titleSuffix: Azure AD B2C
description: 了解 Azure Active Directory B2C 中針對拒絕服務攻擊和密碼攻擊的偵測和風險降低技術。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384969"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>管理對 Azure Active Directory B2C 中的資源與資料的威脅

Azure Active Directory B2C （Azure AD B2C）有內建功能，可協助您防範資源和資料的威脅。 這些威脅包括拒絕服務的攻擊和密碼攻擊。 拒絕服務的攻擊可能會造成預定使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。

## <a name="denial-of-service-attacks"></a>拒絕服務的攻擊

Azure AD B2C 可對抗使用 SYN cookie 進行的 SYN 洪水攻擊。 Azure AD B2C 也可以透過限制速率和連線數來防止拒絕服務的攻擊。

## <a name="password-attacks"></a>密碼攻擊

使用者所設定的密碼必須達到適當的複雜性。 Azure AD B2C 備有降低風險的技術，可防範密碼攻擊。 緩和措施包括偵測暴力密碼破解攻擊和字典密碼攻擊。 Azure AD B2C 會利用不同的訊號來分析要求的完整性。 Azure AD B2C 的設計會很聰明地區分預定使用者與駭客和殭屍網路。

Azure AD B2C 使用複雜的策略來鎖定帳戶。 帳戶會依據要求的 IP 和所輸入的密碼進行鎖定。 鎖定持續期間也會根據它是攻擊的可能性而加長。 在密碼嘗試10次失敗（預設的嘗試閾值）之後，就會發生一分鐘的鎖定。 當帳戶解除鎖定之後，下次登入失敗（也就是，服務在鎖定期間到期後自動解除鎖定之後），會發生另一分鐘的鎖定，每次登入失敗時就會繼續進行。 重複輸入相同密碼不會計算為多次登入失敗。

前 10 次鎖定期間長度為一分鐘。 接下來的 10 次鎖定期間會略微加長，而且每達到 10 次鎖定期間之後，鎖定持續時間都會繼續增加。 在帳戶未遭到鎖定之前登入成功，鎖定計數器會重設為零。 鎖定期間最長會持續五個小時。

## <a name="manage-password-protection-settings"></a>管理密碼保護設定

若要管理密碼保護設定，包括鎖定閾值：

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 使用上方功能表中的 [**目錄 + 訂**用帳戶] 篩選，選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [**安全性**] 底下，選取 **[驗證方法（預覽）**]，然後選取 [**密碼保護**]。
1. 輸入您想要的密碼保護設定，然後選取 [**儲存**]。

    ![Azure AD 設定中的 Azure 入口網站密碼保護] 頁面](./media/threat-management/portal-02-password-protection.png)
    <br />*在 [**密碼保護**設定] 中將 [鎖定閾值] 設定為 [5*]。

## <a name="view-locked-out-accounts"></a>查看鎖定的帳戶

若要取得鎖定帳戶的相關資訊，您可以檢查 Active Directory 登[入活動報告](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。 在 [**狀態**] 底下，選取 [**失敗**]。 嘗試登入失敗，登入**錯誤碼**為， `50053` 指出已鎖定的帳戶：

![顯示已鎖定帳戶的 Azure AD 登入報告區段](./media/threat-management/portal-01-locked-account.png)

若要瞭解如何在 Azure Active Directory 中查看登入活動報告，請參閱登[入活動報告錯誤代碼](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。
