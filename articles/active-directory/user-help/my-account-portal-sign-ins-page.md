---
title: 從我的登入 (預覽) 頁面檢視與搜尋您最近的登入活動 - Azure Active Directory | Microsoft Docs
description: 有關如何從 [我的帳戶] 入口網站的 [我的登入] 頁面查看及搜尋最近登入活動的詳細資料。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744518"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>從 \[我的登入 \(預覽\)\] 頁面查看並搜尋您最近的登入活動

您可以從 [我的帳戶] 入口網站的 [我的登入] 頁面，檢視所有您最近的公司或學校帳戶登入活動。 檢閱您的登入歷程記錄可協助您查看下列各項，以檢查是否有不尋常的活動：

- 是否有人嘗試猜出您的密碼。

- 是否有攻擊者成功登入您的帳戶及從何處登入。

- 攻擊者嘗試存取哪些應用程式。

## <a name="view-your-recent-sign-in-activity"></a>檢視您最近的登入活動

1. 登入您的公司或學校帳戶，然後前往 https://myaccount.microsoft.com/ 頁面。

2. 從左瀏覽窗格中選取 \[我的登入 \(預覽\)\] ，或從 \[我的登入 \(預覽\)\] 區塊選取 \[檢閱最近活動\] 連結。

    ![[我的帳戶] 頁面，顯示醒目提示的 [最近的活動] 連結](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展開並檢閱每個登入項目，以確定您可識別出每個項目。 如果您發現不熟悉的登入項目，我們強烈建議您變更密碼，以協助保護您的帳戶 (如果已遭到入侵)。

    ![[最近的活動] 頁面中的登入詳細資料已展開](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果您看到 [成功] 登入

您應該將自己的活動辨識為正常。 不過，如果您發現 [成功] 登入來自奇怪的位置、瀏覽器或作業系統，可能表示攻擊者已取得您帳戶的存取權。 在此情況下，建議您立即變更密碼，然後移至[安全性資訊](https://mysignins.microsoft.com/security-info)頁面，以更新您的安全性設定。

在您判定有不正確的項目之前，請確定您沒有看到誤判為真 (亦即該項目看起來有問題，但實際上是沒問題的)。 例如，我們會根據您的 IP 位址來判斷您的大約位置和對應。 但是行動網路的位置會特別難以找出，因為它們有時會將流量路由至遠處的位置。 因此，如果您使用華盛頓州的行動裝置登入，位置可能會顯示登入是來自加州。 因此，強烈建議您檢查更多詳細資料，而不僅只是檢查位置。 您也應該確定作業系統、瀏覽器和應用程式都全是合理的。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果您看到 [失敗] 登入

一個沒有工作階段活動的失敗登入，表示您的主要驗證方法 (使用者名稱/密碼) 失敗。 這可能表示您輸入錯誤的使用者名稱或密碼，但也可能表示攻擊者嘗試猜出您的密碼。 如果您認為攻擊者未成功地猜出您的密碼，則不需要變更您的密碼，但強烈建議您註冊 Azure Multi-Factor Authentication (MFA)。 有了 MFA，即使駭客最後猜出您的密碼，也無法存取您的帳戶。

如果您看到失敗的登入，並在 [工作階段] 活動下方有一個附註表示 [額外驗證失敗，程式碼無效]，則表示您的主要驗證方法 (使用者名稱/密碼) 成功，但 MFA 失敗。 如果是攻擊者，這表示他們正確猜出了您的密碼，但仍無法通過 MFA 挑戰。 在此情況下，我們建議您仍要變更密碼，因為攻擊者已得到部分的權利，然後移至[安全性資訊](https://mysignins.microsoft.com/security-info)頁面來更新您的安全性設定。

## <a name="search-for-specific-sign-in-activity"></a>搜尋特定登入活動

您可以使用任何可用的資訊來搜尋最近的登入活動。 例如，您可以依作業系統、位置、應用程式等搜尋最近的登入活動。

1. 在 [檢閱最近的活動] 頁面上，在 [搜尋] 列中輸入您要搜尋的資訊。 例如，輸入 `My Account` 來搜尋 [我的帳戶] 應用程式所收集的所有活動。

2. 選取 [搜尋] 按鈕開始搜尋。

    ![[最近的活動] 頁面會顯示醒目提示的搜尋列、搜尋按鈕和結果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>後續步驟

在檢視最近的登入活動之後，您可以：

- 檢視或管理您的[安全性資訊](user-help-security-info-overview.md)。

- 檢視或管理您的已連線[裝置](my-account-portal-devices-page.md)。

- 檢視或管理您的[組織](my-account-portal-organizations-page.md)。

- 檢視您組織如何[使用您的隱私權相關資料](my-account-portal-privacy-page.md)。
