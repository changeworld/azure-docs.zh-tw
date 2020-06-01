---
title: 針對 Azure EA 入口網站存取進行疑難排解
description: 本文將說明 Azure EA 入口網站中可能發生的一些 Azure Enterprise 合約 (EA) 常見問題。
author: bandersmsft
ms.author: banders
ms.date: 05/07/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 97ba4e77c96859115c974b630a455f4bea62051a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654738"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>針對 Azure EA 入口網站存取進行疑難排解

本文將說明 Azure Enterprise 合約 (EA) 可能發生的一些常見問題。 Azure EA 入口網站可用來管理 Enterprise 合約使用者和成本。 當您設定或更新 Azure EA 入口網站的存取權時，可能會遇到這些問題。

## <a name="issues-adding-an-admin-to-an-enrollment"></a>將系統管理員新增至註冊的問題

企業註冊有不同類型的驗證層級。 如果未正確套用驗證層級，當您嘗試登入 Azure EA 入口網站時，可能會發生問題。

您可以使用 Azure EA 入口網站，將存取權授與不同驗證層級的使用者。 企業系統管理員可以更新驗證層級，以符合其組織的安全性需求。

### <a name="authentication-level-types"></a>驗證層級類型

- 僅限 Microsoft 帳戶 - 適用於想要透過 Microsoft 帳戶使用、建立和管理使用者的組織。
- 公司或學校帳戶 - 適用於已設定 Active Directory 與雲端同盟，且所有帳戶都在單一租用戶上的組織。
- 跨租用戶的公司或學校帳戶 - 適用於已設定 Active Directory 與雲端同盟，且在多個租用戶上有帳戶的組織。
- 混合帳戶 - 可讓您使用 Microsoft 帳戶和/或使用公司或學校帳戶新增使用者。

新增至註冊的第一個公司或學校帳戶會決定「預設」或「主要」網域。 若要以另一個租用戶新增公司或學校帳戶，您必須將註冊下的驗證層級變更為跨租用戶驗證。

若要更新驗證層級：

1. 以企業系統管理員身分登入 Azure EA 入口網站。
2. 從左方導覽面板按一下 [管理]。
3. 按一下 [註冊] 索引標籤。
4. 在 [註冊詳細資料] 底下，選取 [驗證層級]。
5. 按一下鉛筆符號。
6. 按一下 [檔案] 。

![顯示驗證層級的範例 ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft 帳戶必須在 [https://signup.live.com](https://signup.live.com/) 上建立相關聯的識別碼。

公司或學校帳戶適用於已設定 Active Directory 同盟，且所有帳戶都在單一租用戶上的組織。 如果公司的內部 Active Directory 是同盟的，則可以使用公司或學校的同盟使用者驗證來新增使用者。

如果您的組織未使用 Active Directory 同盟，您就無法使用公司或學校電子郵件地址。 請改為註冊或建立新的電子郵件地址，並將其註冊為 Microsoft 帳戶。

## <a name="unable-to-access-the-azure-ea-portal"></a>無法存取 Azure EA 入口網站

如果您在嘗試登入 Azure EA 入口網站時收到錯誤訊息，請使用下列疑難排解步驟：

- 請確定您是使用正確的 Azure EA 入口網站 URL，也就是 https://ea.azure.com 。
- 判斷您是否已將 Azure EA 入口網站的存取新增為公司或學校帳戶，或是 Microsoft 帳戶。
  - 如果您使用的是公司帳戶，請輸入您的公司電子郵件和公司密碼。 您的組織會提供您公司密碼。 如果您遇到密碼相關問題，可以向 IT 部門詢問如何重設密碼。
  - 如果您使用的是 Microsoft 帳戶，請輸入您的 Microsoft 帳戶電子郵件地址和密碼。 如果您忘記 Microsoft 帳戶密碼，可以在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 上重設密碼。
- 請使用私密或無痕瀏覽器工作階段進行登入，如此便不會保留先前或現有工作階段中的 Cookie 或快取資訊。 清除瀏覽器的快取，並使用私密或無痕視窗來開啟 https://ea.azure.com 。
- 如果您在使用 Microsoft 帳戶時收到「無效使用者」錯誤，可能是因為您有多個 Microsoft 帳戶。 您嘗試用來登入的使用者不是主要的電子郵件地址。
或者，如果您收到「無效使用者」錯誤，可能是因為使用者新增至註冊時使用了錯誤的帳戶類型。 例如，使用了公司或學校帳戶，而不是 Microsoft 帳戶。 在此範例中，您可以讓另一位 EA 系統管理員新增正確的帳戶，或您需要連絡[支援人員](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c)。
  - 如果您需要檢查主要別名，請移至 [https://account.live.com](https://account.live.com)。 然後，按一下 [您的資訊]，然後按一下 [管理登入 Microsoft 的方式]。 遵循提示以確認替代電子郵件地址，並取得代碼來存取機密資訊。 輸入安全性驗證碼。 如果您不想要設定雙重要素驗證，請選取 [稍後設定]。
  - 您會看到 [管理登入 Microsoft 的方式] 頁面，您可在其中檢視您的帳戶別名。 檢查主要別名是否為您用來登入 Azure EA 入口網站的別名。 如果不是，您可以將其設為您的主要別名。 或者，您可以改為將此主要別名用於 Azure EA 入口網站。

## <a name="azure-ea-activation-faq"></a>Azure EA 啟用常見問題集

本文的這一節概述有關 Azure EA 啟用的常見問題解決方案。

### <a name="i-didnt-receive-an-activation-email"></a>我未收到啟用電子郵件

來自 Azure EA 入口網站的啟用電子郵件會從 *waep@microsoft.com* 寄出。 如果您未收到啟用電子郵件，請檢查電子郵件的垃圾郵件資料夾。 郵件主旨是「檢視/管理 Microsoft Azure 服務的邀請_主題」。 該郵件會傳送給每位新增的 EA 系統管理員。

如果您確定您已設定為 EA 系統管理員，則不需要等待收到啟用電子郵件才能登入 Azure EA 入口網站。 相反地，您可以移至 https://ea.azure.com ，並使用您的電子郵件地址 (公司、學校或 Microsoft 帳戶) 和密碼登入。

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>我想要將新的 EA 系統管理員新增至我的註冊

現有的企業系統管理員可以新增企業系統管理員。 如果您是 EA 系統管理員，請登入 EA 入口網站，按一下 [管理]，然後按一下右上角的 [+ 新增系統管理員]，以新增 EA 系統管理員。 請確定您有其電子郵件地址和慣用的登入方法，例如透過工作/學校驗證或 Microsoft Live ID 來新增使用者。

如果您不是 EA 系統管理員，請與貴公司的 EA 系統管理員聯繫，要求他們將您新增至註冊。 當他們將您新增到註冊後，您將會收到啟用電子郵件。

不過，如果 EA 系統管理員無法協助您，只要您可將下列資訊提供給我們，我們即可代表他們將您加入：
- 註冊號碼。
- 要新增的電子郵件地址和驗證類型 (公司/學校/MS)。
- 來自 EA 系統管理員的電子郵件核准。

當您擁有所有必要的資訊之後，請在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交要求。

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>我想要更新註冊的第一個 EA 系統管理員

更新入口網站上的通知連絡人和線上系統管理員，即可在大量授權服務中心更新第一個 EA 系統管理員。 EA 入口網站需要約 24 小時的時間才會更新。 更新之後，新的 EA 系統管理員將會收到啟用電子郵件。

如果您沒有 VLSC 入口網站存取權，或是您的初始 EA 系統管理員無法再管理註冊，也無法存取 EA 入口網站，請在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交要求，以要求更新並提供下列資訊：
- 註冊號碼
- 要新增的電子郵件地址和驗證類型 (公司/學校/MS)
- 變更初始 EA 系統管理員的原因
- 來自初始 EA 系統管理員的電子郵件核准

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>我目前的 EA 系統管理員已離開公司

EA 註冊可以有多個 EA 系統管理員，您可與另一個 EA 系統管理員聯繫，以新增 EA 系統管理員/帳戶擁有者/部門管理員。 不過，如果您不清楚貴公司的 EA 系統管理員是誰，或註冊沒有其他可用的 EA 系統管理員，請使用下列資訊來與我們聯繫：
- 註冊號碼
- 要新增的電子郵件地址和驗證類型 (公司/學校/MS)
- 提供目前 EA 系統管理員已離開公司的資訊

請注意，如果註冊有其他 EA 系統管理員，我們會與 EA 系統管理員聯繫，要求核准進行註冊的系統管理變更。

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>我的註冊目前顯示擱置狀態。 如何啟用我的註冊？

如果初始 EA 系統管理員之前尚未登入註冊，則註冊會處於擱置狀態。 如果您是 EA 系統管理員，請登入 Azure EA 入口網站。 在具有所有註冊號碼的登陸頁面上，您可能看不到您的擱置註冊。 請取消核取 EA 入口網站右上角的 [作用中] 方塊，此動作會顯示擱置註冊。 請按一下註冊以存取訊號，一旦觸達註冊的 [管理] 頁面，狀態就會從 [擱置] 更新為 [作用中]。

### <a name="why-is-my-account-stuck-in-pending-status"></a>為何我的帳戶卡在擱置狀態？

當新的帳戶擁有者 (AO) 第一次新增至 Azure EA 註冊時，其狀態一律會顯示為「擱置」。 當 AO 收到用於啟用的歡迎電子郵件時，即可登入來啟用其帳戶。 登入會將帳戶狀態從「擱置」更新為「作用中」。

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>我在登入 Azure EA 入口網站時收到錯誤

登入期間 Azure EA 入口網站上出現錯誤訊息有幾個可能的原因，請遵循下列疑難排解步驟：

 1. 請確定您在 [https://ea.azure.com](https://ea.azure.com) 使用正確的 EA 入口網站 URL。
 1. 判斷您是否已將 Azure EA 入口網站的存取權新增為公司或學校帳戶或 Microsoft 帳戶。 如果您使用的是公司帳戶，請輸入您的公司電子郵件和公司密碼。 如果您使用 Microsoft Live ID，請輸入您的 Live ID 電子郵件和 Microsoft Live ID 密碼。 如果您忘了 Microsoft Live ID 密碼，請在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 重設。
 1. 建議您使用私人瀏覽器進行登入，才不會保留先前/現有工作階段的任何 cookie 或快取。 清除 [快取]，然後使用私人瀏覽模式/incognito 視窗開啟 [https://ea.azure.com](https://ea.azure.com)。
 1. 如果您在使用 Microsoft 帳戶時收到「無效使用者」錯誤，可能是因為您有多個 Microsoft 帳戶，而您嘗試用來登入的帳戶不是主要別名。 若要檢查主要別名，請移至 account.live.com：
    - 移至「您的資訊」>「管理您的登入電子郵件或電話號碼」。
    - 遵循畫面上的提示以確認替代電子郵件地址，並取得代碼來存取機密資訊。
    - 輸入安全性驗證碼。
    - 如果您想要稍後設定雙步驟驗證，請選取「稍後設定」。
    - 您會進入 [管理您的帳號別名] 頁面，您會在其中看到您具有的帳戶別名。 再次檢查主要別名是否為您用來登入 Azure EA 入口網站的別名。 如果不是，您可以將其設為您的主要別名，也可以改為使用 Azure EA 入口網站的主要別名。

如果上述疑難排解步驟失敗，請在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交要求，其中包含下列資訊：
- 使用的瀏覽器和版本。
- 錯誤訊息的螢幕擷取畫面。
- 顯示錯誤的頁面 URL。  
- 發生錯誤時的日期、時間和時區。
- 此外，您取得記錄檔將有所幫助。 以下是使用下列資訊來擷取網路追蹤的步驟：
  1. 開啟 Internet Explorer。
  1. 按 F12 鍵，這會開啟位於 IE 底部的方塊。
  1. 選取 [網路] 索引標籤。
  1. 按一下 [開始擷取]。
  1. 執行造成錯誤的動作。
  1. 一旦收到錯誤，請按一下 [停止解取]。
  1. 儲存檔案，並在支援要求中包含資訊。
  1. 請確定您在支援要求中提供您的註冊號碼和電子郵件地址。

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>公司/學校帳戶和 Microsoft 帳戶有什麼差別？

**Microsoft 帳戶：** 已與 [https://signup.live.com](https://signup.live.com) 上的 Live ID 相關聯的帳戶。

**公司/學校帳戶：** 僅適用於已設定 Active Directory 與雲端同盟，且所有帳戶都在單一租用戶上的公司。 如果公司的內部 Active Directory 與雲端同盟，則可以使用公司/學校授權類型來新增使用者。

  從 2016 年 9 月起，Microsoft 不再允許將公司或學校電子郵件註冊為 Microsoft 帳戶。 如需詳細資訊，請參考下列資料：[https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/)。

  如果貴組織未與雲端同盟，您將無法使用您的公司或學校電子郵件地址。 請改為註冊或建立新的電子郵件地址，並將其註冊為 Microsoft 帳戶。

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>我忘了我的 Azure EA 入口網站密碼

如果您忘了 Microsoft Live ID 密碼，請在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 重設。

如果您忘了公司密碼，請洽詢貴公司的 IT 系統管理員。

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>我有有效的公司或學校帳戶，但無法將其新增至 EA 入口網站

如果您在不同的租用戶下有公司或學校帳戶，請將註冊詳細資料頁面下的授權層級變更為「跨租用戶的公司或學校帳戶」，您就能夠新增帳戶。

## <a name="next-steps"></a>後續步驟

- Azure EA 入口網站系統管理員應閱讀 [ Azure EA 入口網站的系統管理](ea-portal-administration.md)，以了解常見的管理工作。
