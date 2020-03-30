---
title: 從"我的登錄（預覽）"頁面查看和搜索最近的登錄活動 - Azure 活動目錄 |微軟文檔
description: 有關如何從"我的帳戶"門戶的"我的登錄"頁面查看和搜索您最近的登錄活動的詳細資訊。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064014"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>從"我的登錄（預覽）"頁面查看和搜索您最近的登錄活動

您可以在 **"我的帳戶"** 門戶的"**我的登錄"** 頁面查看所有最近的工作或學校帳戶登錄活動。 查看登錄歷史記錄可説明您查看異常活動，從而説明您查看：

- 如果有人試圖猜測您的密碼。

- 如果攻擊者成功登錄到您的帳戶，以及從什麼位置登錄。

- 攻擊者嘗試訪問哪些應用。

## <a name="view-your-recent-sign-in-activity"></a>檢視您最近的登入活動

1. 登入您的公司或學校帳戶，然後前往 https://myprofile.microsoft.com/ 頁面。

2. 從左側功能窗格中選擇 **"我的登錄（預覽）"，** 或從"**我的登錄（預覽）"** 塊中選擇 **"查看最近的活動**"連結。

    ![我的帳戶頁面，顯示突出顯示的"最近活動"連結](media/my-account-portal/my-account-portal-sign-ins.png)

3. 展開並檢閱每個登入項目，以確定您可識別出每個項目。 如果您發現一個看起來不熟悉的登錄專案，我們強烈建議您更改密碼，以説明保護您的帳戶，如果它被洩露。

    ![[最近的活動] 頁面中的登入詳細資料已展開](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>如果您看到成功登錄

你應該認識到自己的活動是正常的。 但是，如果您注意到來自陌生位置、瀏覽器或作業系統的成功登錄，則可能意味著攻擊者已訪問您的帳戶。 在此情況下，我們建議您立即更改密碼，然後轉到["安全資訊](https://mysignins.microsoft.com/security-info)"頁面以更新安全設置。

在確定某些內容不正確之前，請確保您沒有看到誤報（專案看起來有問題，但可以）。 例如，我們會根據您的 IP 位址確定您的大致位置和地圖。 移動網路尤其難以精確定位，因為它們有時會將流量路由到遙遠的位置。 因此，如果您在華盛頓州使用行動裝置登錄，則該位置可能會顯示來自加利福尼亞州的登錄。 因此，我們強烈建議您查看更多詳細資訊，而不僅僅是位置。 您還應確保作業系統、瀏覽器和應用程式都有意義。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>如果您看到登錄失敗

登錄失敗，沒有會話活動，意味著您的主要驗證方法（使用者名/密碼）失敗。 這可能意味著您鍵入的使用者名或密碼錯誤，但也可能意味著攻擊者正在嘗試猜測您的密碼。 如果您認為攻擊者試圖猜測您的密碼失敗，則不必更改密碼，但我們強烈建議您註冊 Azure 多重要素驗證 （MFA）。 使用 MFA，即使駭客最終猜到您的密碼，也不足以訪問您的帳戶。

如果您看到登錄失敗，在"會話活動"下有注釋，上面寫著"**其他驗證失敗，代碼無效**"，則意味著主身份驗證（使用者名/密碼）成功，但 MFA 失敗。 如果這是攻擊者，他們正確猜到了您的密碼，但仍無法通過 MFA 質詢。 在這種情況下，我們建議您仍更改密碼，因為攻擊者正確獲取了該部分，然後轉到["安全資訊](https://mysignins.microsoft.com/security-info)"頁以更新您的安全設置。

## <a name="search-for-specific-sign-in-activity"></a>搜索特定的登錄活動

您可以通過任何可用資訊搜索最近的登錄活動。 例如，您可以按作業系統、位置、應用等搜索最近的登錄活動。

1. 在"**查看最近活動"** 頁上，在 **"搜索"** 欄中鍵入要搜索的資訊。 例如，鍵入`My Account`以搜索"我的帳戶"應用收集的所有活動。

2. 選擇 **"搜索"** 按鈕以開始搜索。

    ![最近活動頁面，顯示突出顯示的搜索欄、搜尋按鈕和結果](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>後續步驟

查看最近的登錄活動後，您可以：

- 查看或管理[您的安全資訊](user-help-security-info-overview.md)。

- 查看或管理連接[的設備](my-account-portal-devices-page.md)。

- 查看或[管理您的組織。](my-account-portal-organizations-page.md)

- 查看您的組織如何使用[您的隱私相關資料](my-account-portal-privacy-page.md)。
