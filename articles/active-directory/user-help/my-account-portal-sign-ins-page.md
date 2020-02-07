---
title: 從 [我的登入（預覽）] 頁面中，查看及搜尋最近的登入活動-Azure Active Directory |Microsoft Docs
description: 有關如何從我的帳戶入口網站的 [我的登入] 頁面中，查看及搜尋最近登入活動的詳細資料。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064014"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>從 [我的登入（預覽）] 頁面中，查看並搜尋您最近的登入活動

您可以從 [**我的帳戶**] 入口網站的 [我的登**入**] 頁面，查看所有最近使用的工作或學校帳戶登入活動。 檢查您的登入歷程記錄可協助您查看下列各項，以檢查是否有不尋常的活動：

- 如果有人嘗試猜出您的密碼。

- 如果攻擊者成功登入您的帳戶，以及來自哪個位置。

- 攻擊者嘗試存取哪些應用程式。

## <a name="view-your-recent-sign-in-activity"></a>檢視您最近的登入活動

1. 登入您的公司或學校帳戶，然後前往 https://myprofile.microsoft.com/ 頁面。

2. 從左側流覽窗格中選取 [我的登**入（預覽）** ]，或從 [我的登**入（預覽）** ] 區塊選取 [**回顧最近的活動**] 連結。

    ![[我的帳戶] 頁面，顯示反白顯示的最近活動連結](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展開並檢閱每個登入項目，以確定您可識別出每個項目。 如果您發現不熟悉的登入專案，我們強烈建議您變更密碼，以協助保護您的帳戶（如果它遭到入侵）。

    ![[最近的活動] 頁面中的登入詳細資料已展開](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果您看到成功的登入

您應該將自己的活動辨識為正常。 不過，如果您發現從奇怪的位置、瀏覽器或作業系統成功登入，可能表示攻擊者已取得您帳戶的存取權。 在此情況下，建議您立即變更密碼，然後移至 [[安全性資訊](https://mysignins.microsoft.com/security-info)] 頁面來更新您的安全性設定。

在您判斷不正確的專案之前，請確定您沒有看到「誤報」（這是因為專案看起來有問題，但仍有疑問）。 例如，我們會根據您的 IP 位址來判斷您的大約位置和對應。 行動網路特別難以指出，因為它們有時會路由傳送流量到遠處的位置。 因此，如果您使用華盛頓州的行動裝置登入，此位置可能會顯示來自加州的登入。 因此，我們強烈建議您檢查更多詳細資料，但只限于位置。 您也應該確保作業系統、瀏覽器和應用程式都有意義。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果您看到不成功的登入

登入失敗，沒有會話活動，表示您的主要驗證方法（使用者名稱/密碼）失敗。 這可能表示您輸入的使用者名稱或密碼錯誤，也可能表示攻擊者嘗試猜出您的密碼。 如果您認為攻擊者嘗試不成功猜出您的密碼，則不需要變更您的密碼，但我們強烈建議您註冊 Azure 多重要素驗證（MFA）。 使用 MFA 時，即使駭客最後猜到您的密碼，也無法存取您的帳戶。

如果您看到不成功的登入，並在 [會話活動] 底下顯示 [**其他驗證失敗]、[無效**的程式碼]，表示您的主要驗證（使用者名稱/密碼）已成功，但 MFA 失敗。 如果這是攻擊者，他們會正確猜出您的密碼，但仍無法通過 MFA 挑戰。 在此情況下，我們建議您仍然變更密碼，因為攻擊者會獲得該部分許可權，然後前往 [[安全性資訊](https://mysignins.microsoft.com/security-info)] 頁面來更新您的安全性設定。

## <a name="search-for-specific-sign-in-activity"></a>搜尋特定登入活動

您可以使用任何可用的資訊來搜尋最近的登入活動。 例如，您可以依作業系統、位置、應用程式等搜尋最近的登入活動。

1. 在 [**審查最近的活動**] 頁面上，輸入您要在**搜尋**列中搜尋的資訊。 例如，輸入 `My Account` 來搜尋我的帳戶應用程式所收集的所有活動。

2. 選取 [**搜尋**] 按鈕以開始搜尋。

    ![最近的活動頁面，顯示醒目提示的搜尋列、搜尋按鈕和結果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>後續步驟

在觀看最近的登入活動之後，您可以：

- 查看或管理您的[安全性資訊](user-help-security-info-overview.md)。

- 查看或管理已連線的[裝置](my-account-portal-devices-page.md)。

- 查看或管理您的[組織](my-account-portal-organizations-page.md)。

- 瞭解您的組織如何[使用您的隱私權相關資料](my-account-portal-privacy-page.md)。
