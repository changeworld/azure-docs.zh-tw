---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 3590b8fdb67b25e0d4389e814aefcc4d18b1193c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015415"
---
Azure 客戶每月可以解除鎖定 25,000 封免費電子郵件。 這些25000的每月免費電子郵件可讓您存取先進的報告和分析，以及 [所有 api][all APIs] (WEB、SMTP、事件、剖析，以及其他) 。 如需 SendGrid 所提供其他服務的相關資訊，請參閱 [SendGrid 解決方案 (英文)][SendGrid Solutions] 頁面。

### <a name="to-sign-up-for-a-sendgrid-account"></a>註冊 SendGrid 帳戶
1. 登入 [Azure 入口網站][Azure portal]。
2. 在 [Azure 入口網站] 功能表或 [首頁] 中，選取 [ **建立資源**]。

    ![已選取 [建立資源] 選項之 [Azure 入口網站] 功能表的螢幕擷取畫面。][command-bar-new]
3. 搜尋並選取 [ **SendGrid**]。

    ![Azure 入口網站 Marketplace 畫面的螢幕擷取畫面，其中顯示搜尋方塊中的 "SendGr" 和搜尋結果中選取的 SendGrid。][sendgrid-store]
4. 完成註冊表單，然後選取 [建立]。

    ![[建立新的 SendGrid 帳戶] 對話方塊的螢幕擷取畫面，其中已填入名稱、密碼、訂用帳戶和資源群組欄位。][sendgrid-create]
5. 在您的 Azure 設定中輸入可識別 SendGrid 服務的 **名稱**。 名稱的長度必須介於 1 到 100 個字元之間，而且只能包含英數字元、連字號、句點和底線。 此名稱在已訂用的 Azure 市集項目清單中必須是唯一的。
6. 輸入並確認您的 **密碼**。
7. 選擇您的 **訂用帳戶**。
8. 建立新的 **資源群組** 或使用現有的資源群組。
9. 在 [定價層] 區段中，選取您想要註冊的 SendGrid 方案。

    ![[建立新的 SendGrid 帳戶] 對話方塊的螢幕擷取畫面，其中已開啟 [選擇您的定價層] 區段，並選取免費定價層。][sendgrid-pricing]
10. 輸入 **促銷代碼** (如果有的話)。
11. 輸入您的 **連絡人資訊**。
12. 複習並接受 **法律條款**。
13. 確認購買之後，您會看到 **部署成功** 的快顯視窗，而且您會看到您的帳戶已列出。

    ![SendGrid 帳戶頁面的螢幕擷取畫面，其中顯示列出的新帳戶 ContosoSendGrid。][all-resources]

    當您完成購買，並按下 [管理] 按鈕以起始電子郵件驗證程序之後，將收到一封來自 SendGrid 的電子郵件，詢問您是否要驗證您的帳戶。 如果您未收到這封電子郵件，或驗證您的帳戶時發生問題，請參閱我們的常見問題。

    ![[ContosoSendGrid 帳戶] 頁面的螢幕擷取畫面，其中已醒目提示 [管理] 按鈕。][manage]

    **在您驗證帳戶之前，您每天最多只能傳送 100 封電子郵件**

    若要修改您的訂用計畫或查看 SendGrid 連絡人設定，請按一下您的 SendGrid 服務名稱，以開啟 SendGrid Marketplace 儀表板。

    ![螢幕擷取畫面，顯示已從 [ContosoSendGrid 帳戶] 頁面選取 [所有設定] 來開啟 ContosoSendGrid 帳戶的 [設定] 頁面。][settings]

    若要使用 SendGrid 傳送電子郵件，您必須提供您的 API 金鑰。

### <a name="to-find-your-sendgrid-api-key"></a>尋找您的 SendGrid API 金鑰
1. 按一下 [管理] 。

    ![[ContosoSendGrid 帳戶] 頁面的螢幕擷取畫面，其中已醒目提示 [管理] 按鈕。][manage]
2. 在 SendGrid 儀表板的左側功能表中，依序選取 [設定] 和 [API 金鑰]。

    ![SendGrid 儀表板的螢幕擷取畫面，其中已開啟 [設定] 下拉式清單和已選取的 API 金鑰。][api-keys]

3. 按一下 [建立 API 金鑰]。

    ![[API 金鑰] 畫面的螢幕擷取畫面，其中已選取 [建立 API 金鑰] 按鈕。][general-api-key]
4. 至少需要提供 **這個金鑰的名稱**，並提供 **郵件傳送** 的完整存取權，然後選取 [儲存]。

    ![[新增一般 API 金鑰] 畫面的螢幕擷取畫面，其中郵件傳送設定為 [完整存取]、[排程的傳送] 設定為 [無存取]，並醒目提示 [儲存] 按鈕。][access]
5. 您的 API 將會在此時顯示一次。 請務必安全地儲存它。

### <a name="to-find-your-sendgrid-credentials"></a>尋找您的 SendGrid 認證
1. 按一下金鑰圖示來尋找您的 **使用者名稱**。

    ![ContosoSendGrid 帳戶頁面的螢幕擷取畫面，其中已醒目提示 [金鑰] 圖示。][key]
2. 密碼是您在安裝期間所選擇的密碼。 您可以選取 [變更密碼] 或 [重設密碼] 來進行任何變更。

若要管理電子郵件傳遞能力設定，按一下 [管理] 按鈕。 這將會重新導向至您的 SendGrid 儀表板。

![[ContosoSendGrid 帳戶] 頁面的螢幕擷取畫面，其中已醒目提示 [管理] 按鈕。][manage]

如需透過 SendGrid 傳送電子郵件的詳細資訊，請造訪[電子郵件 API 概觀][Email API Overview]。

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
