---
title: 管理對資源和資料的威脅
titleSuffix: Azure AD B2C
description: 了解 Azure Active Directory B2C 中針對拒絕服務攻擊和密碼攻擊的偵測和風險降低技術。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183596"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>管理對 Azure Active Directory B2C 中的資源與資料的威脅

Azure 活動目錄 B2C（Azure AD B2C） 具有內置功能，可説明您抵禦資源和資料的威脅。 這些威脅包括拒絕服務的攻擊和密碼攻擊。 拒絕服務的攻擊可能會造成預定使用者無法使用資源。 密碼攻擊可導致未經授權的資源存取。

## <a name="denial-of-service-attacks"></a>拒絕服務的攻擊

Azure AD B2C 可對抗使用 SYN cookie 進行的 SYN 洪水攻擊。 Azure AD B2C 也可以透過限制速率和連線數來防止拒絕服務的攻擊。

## <a name="password-attacks"></a>密碼攻擊

使用者所設定的密碼必須達到適當的複雜性。 Azure AD B2C 備有降低風險的技術，可防範密碼攻擊。 緩解措施包括檢測暴力密碼攻擊和字典密碼攻擊。 Azure AD B2C 會利用不同的訊號來分析要求的完整性。 Azure AD B2C 的設計會很聰明地區分預定使用者與駭客和殭屍網路。

Azure AD B2C 使用複雜的策略來鎖定帳戶。 帳戶會依據要求的 IP 和所輸入的密碼進行鎖定。 鎖定持續期間也會根據它是攻擊的可能性而加長。 嘗試密碼 10 次未成功（預設嘗試閾值）後，將發生一分鐘的鎖定。 下次在帳戶解鎖後登錄失敗時（即，在鎖定期到期後，服務自動解鎖帳戶後），將發生另一次一分鐘的鎖定，並針對每次不成功的登錄繼續。 重複輸入相同密碼不會計算為多次登入失敗。

前 10 次鎖定期間長度為一分鐘。 接下來的 10 次鎖定期間會略微加長，而且每達到 10 次鎖定期間之後，鎖定持續時間都會繼續增加。 在帳戶未遭到鎖定之前登入成功，鎖定計數器會重設為零。 鎖定期間最長會持續五個小時。

## <a name="manage-password-protection-settings"></a>管理密碼保護設置

要管理密碼保護設置，包括鎖定閾值：

1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 使用頂部功能表中的**目錄 + 訂閱**篩選器選擇包含 Azure AD B2C 租戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 **"安全"** 下，選擇**身份驗證方法（預覽），** 然後選擇**密碼保護**。
1. 輸入所需的密碼保護設置，然後選擇 **"保存**"。

    ![Azure 門戶密碼保護頁在 Azure AD 設置中](./media/threat-management/portal-02-password-protection.png)
    <br />*在**密碼保護**設置中將鎖定閾值設置為 5。*

## <a name="view-locked-out-accounts"></a>查看鎖定帳戶

要獲取有關鎖定帳戶的資訊，可以檢查活動目錄[登錄活動報表](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。 在 **"狀態**"下，選擇 **"失敗**"。 使用`50053`**登錄錯誤代碼**的登錄嘗試失敗，指示帳戶鎖定：

![顯示鎖定帳戶的 Azure AD 登錄報表的部分](./media/threat-management/portal-01-locked-account.png)

要瞭解如何在 Azure 活動目錄中查看登錄活動報告，請參閱[登錄活動報告錯誤代碼](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。
