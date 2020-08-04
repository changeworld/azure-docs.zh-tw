---
title: 從 [我的登入] 頁面中，查看並搜尋最近的登入活動-Azure Active Directory |Microsoft Docs
description: 有關如何從 [我的帳戶] 入口網站的 [我的登入] 頁面查看及搜尋最近登入活動的詳細資料。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: 1816170da0fb6e5120f8cec6b6dda68d2ea1c678
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543168"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>從 [我的登入] 頁面中，查看並搜尋您最近的登入活動

您可以從 [我的帳戶] 入口網站的 [我的登入] 頁面，檢視所有您最近的公司或學校帳戶登入活動。 檢閱您的登入歷程記錄可協助您查看下列各項，以檢查是否有不尋常的活動：

- 是否有人嘗試猜出您的密碼。
- 是否有攻擊者成功登入您的帳戶及從何處登入。
- 攻擊者嘗試存取哪些應用程式。

## <a name="view-your-recent-sign-in-activity"></a>檢視您最近的登入活動

1. 登入您的公司或學校帳戶，然後前往 https://myaccount.microsoft.com/ 頁面。

2. 從左側流覽窗格中選取 [我的登**入**]，或從 [我的登**入**] 區塊選取 [**複習最近的活動**] 連結。

    ![[我的帳戶] 頁面，顯示醒目提示的 [最近的活動] 連結](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展開並檢閱每個登入項目，以確定您可識別出每個項目。 如果您發現不熟悉的登入專案，請變更您的密碼以保護帳戶以防遭到入侵。

    ![[最近的活動] 頁面中的登入詳細資料已展開](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果您看到成功的登入

有時候，在檢查您自己的一般登入活動時，您可能會看到從不熟悉的位置、瀏覽器或作業系統成功登入。 不熟悉的登入可能表示攻擊者已取得您帳戶的存取權。 如果您看到未授權的活動，建議您立即變更密碼，然後移至 [[安全性資訊](https://mysignins.microsoft.com/security-info)] 以更新您的安全性設定。

在您判定有不正確的項目之前，請確定您沒有看到誤判為真 (亦即該項目看起來有問題，但實際上是沒問題的)。 例如，我們會根據您的 IP 位址來判斷您的大約位置和對應。 但是行動網路的位置會特別難以找出，因為它們有時會將流量路由至遠處的位置。 即使您是使用華盛頓州的行動裝置登入，位置可能還是會顯示來自加州的登入。 我們強烈建議您只檢查位置以外的詳細資料。 請確定作業系統、瀏覽器和應用程式都有意義。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果您看到不成功的登入

如果您看到登入失敗，可能表示您輸入的認證不成功。 這也表示攻擊者嘗試猜出您的密碼。 若要回應此風險，您不需要變更您的密碼，但我們建議您註冊 Azure 多重要素驗證（MFA）。 使用多重要素驗證時，即使駭客猜測您的密碼，也無法存取帳戶。

![不成功的登入磚](media/my-account-portal-sign-ins-page/unsuccessful.png)

如果您看到不成功的登入，並顯示 [**會話活動**] 底下的附注，表示 `Additional verification failed, invalid code` 您的主要驗證認證已成功，但多重要素驗證失敗。 這種情況可能表示攻擊者已正確猜出您的密碼，但卻無法通過多重要素驗證挑戰。 我們建議您仍然變更密碼，因為攻擊者可能已經這麼做，並移至 [[安全性資訊](https://mysignins.microsoft.com/security-info)] 頁面來更新您的安全性設定。

## <a name="search-for-specific-sign-in-activity"></a>搜尋特定登入活動

您可以使用任何可用的資訊來搜尋最近的登入活動。 例如，您可以依作業系統、位置、應用程式等搜尋最近的登入活動。

1. 在 [檢閱最近的活動] 頁面上，在 [搜尋] 列中輸入您要搜尋的資訊。 例如，輸入 `Unsuccessful` 以搜尋我的帳戶應用程式所收集的所有失敗登入活動。

2. 選取 [搜尋] 按鈕開始搜尋。

    ![[最近的活動] 頁面會顯示醒目提示的搜尋列、搜尋按鈕和結果](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>確認不尋常的活動

在 [我的登**入**] 頁面中，您可以在該活動的磚中，確認標示為不尋常活動的登入。

![不尋常的登入磚，可確認您已執行或未嘗試登入](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>後續步驟

在檢視最近的登入活動之後，您可以：

- 檢視或管理您的[安全性資訊](user-help-security-info-overview.md)。

- 檢視或管理您的已連線[裝置](my-account-portal-devices-page.md)。

- 檢視或管理您的[組織](my-account-portal-organizations-page.md)。

- 檢視您組織如何[使用您的隱私權相關資料](my-account-portal-privacy-page.md)。

- 變更 [我的帳戶入口網站設定](my-account-portal-settings.md)
