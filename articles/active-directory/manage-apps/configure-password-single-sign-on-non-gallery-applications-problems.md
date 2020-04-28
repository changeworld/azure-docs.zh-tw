---
title: 設定非資源庫應用程式的密碼 SSO 時發生問題
description: 當您針對不在 Azure AD 應用程式庫中的自訂應用程式設定密碼單一登入（SSO）時，所發生的常見問題。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274140"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>為不在資源庫中的應用程式設定密碼單一登入時所發生的問題

本文說明當您為非資源庫應用程式設定*密碼單一登入*（SSO）時，可能會發生的常見問題。

## <a name="capture-sign-in-fields-for-an-app"></a>捕獲應用程式的登入欄位

只有具備 HTML 功能的登入頁面才支援登入欄位捕捉。 不支援非標準的登入頁面，像是使用 Adobe Flash 或其他非 HTML 功能的技術。

有兩種方式可為您的自訂應用程式捕捉登入欄位：

- **自動登入欄位捕捉**適用于大部分具備 HTML 功能的登入頁面，如果他們對使用者名稱和密碼欄位*使用知名的 DIV id* 。 頁面上的 HTML 是剪輯來尋找符合特定準則的 DIV 識別碼。 該中繼資料會被儲存，以便稍後可以在應用程式中重新執行。

- 如果應用程式廠商*未標示登入輸入欄位*，則會使用**手動登入欄位捕捉**。 如果廠商*呈現多個無法自動偵測的欄位，* 也會使用手動捕獲。 Azure Active Directory （Azure AD）可以在登入頁面上儲存多個欄位的資料，如果您告訴它欄位在頁面上的位置。

一般而言，如果自動登入欄位捕捉無效，請嘗試手動選項。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自動捕獲應用程式的登入欄位

若要使用自動登入欄位 capture 來設定密碼型 SSO，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 以全域管理員或共同管理員身分登入。

2. 在左側導覽窗格中，選取 [**所有服務**] 以開啟 [Azure AD] 延伸模組。

3. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。

4. 在 Azure AD 流覽窗格中選取 [**企業應用程式**]。

5. 選取 [**所有應用程式**] 以查看您應用程式的清單。

   > [!NOTE]
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式**] 清單頂端的 [**篩選**] 控制項。 將 [**顯示**] 選項設定為 [所有應用程式]。

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請在左側導覽窗格中選取 [**單一登入**]。

8. 選取 [**密碼型登入**模式]。

9. 輸入 [登入**url**]，這是使用者輸入其使用者名稱和密碼以進行登入的頁面 url。 *請確定在您提供的 URL 頁面上可以看到登入欄位*。

10. 選取 [儲存]  。

    系統會自動剪輯 [使用者名稱] 和 [密碼] 輸入方塊的頁面。 您現在可以使用 Azure AD，使用存取面板瀏覽器擴充功能將密碼安全地傳輸到該應用程式。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手動捕獲應用程式的登入欄位

若要手動捕獲登入欄位，您必須安裝存取面板的瀏覽器延伸模組。 此外，您的瀏覽器不能在*inPrivate*、 *incognito*或*私*用模式下執行。

若要安裝延伸模組，請參閱本文的[安裝存取面板瀏覽器延伸](#install-the-access-panel-browser-extension)模組一節。

若要使用手動登入欄位 capture 為應用程式設定密碼型 SSO，請遵循下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 以全域管理員或共同管理員身分登入。

2. 在左側導覽窗格中，選取 [**所有服務**] 以開啟 [Azure AD] 延伸模組。

3. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。

4. 在 Azure AD 流覽窗格中選取 [**企業應用程式**]。

5. 選取 [**所有應用程式**] 以查看您應用程式的清單。

   > [!NOTE] 
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式**] 清單頂端的 [**篩選**] 控制項。 將 [**顯示**] 選項設定為 [所有應用程式]。

6. 選取您想要為 SSO 設定的應用程式。

7. 應用程式載入之後，請在左側導覽窗格中選取 [**單一登入**]。

8. 選取 [**密碼型登入**模式]。

9. 輸入 [登入**URL**]，這是使用者輸入其使用者名稱和密碼以進行登入的頁面。 *請確定在您提供的 URL 頁面上可以看到登入欄位*。

10. 選取 **[ * &lt;設定&gt; appname*密碼單一登入設定**]。

11. 選取 [手動偵測登**入欄位**]。

14. 選取 **[確定]**。

15. 選取 [儲存]  。

16. 依照指示使用存取面板。

## <a name="troubleshoot-problems"></a>疑難排解問題

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到「在該 URL 找不到任何登入欄位」錯誤

當登入欄位的自動偵測失敗時，您會收到此錯誤訊息。 若要解決此問題，請嘗試手動登入欄位偵測。 請參閱本文的[手動捕獲應用程式的登入欄位](#manually-capture-sign-in-fields-for-an-app)一節。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到「無法儲存單一登入設定」錯誤

很少，更新 SSO 設定會失敗。 若要解決此問題，請嘗試再次儲存設定。

如果您持續收到錯誤，請開啟支援案例。 請在「[觀看入口網站通知詳細資料](#view-portal-notification-details)」中包含相關資訊，並將[通知詳細資料傳送給支援工程師，以取得](#send-notification-details-to-a-support-engineer-to-get-help)本文的說明章節。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我無法手動偵測應用程式的登入欄位

當手動偵測無法運作時，您可能會發現下列行為：

- 手動捕捉程式似乎正常執行，但已捕捉的欄位不正確。

- 當 capture 進程執行時，正確的欄位不會反白顯示。

- 捕捉程式會如預期般帶您前往應用程式的登入頁面，但不會發生任何事。

- 手動捕獲似乎正常執行，但當使用者從存取面板流覽至應用程式時，不會發生 SSO。

如果您遇到上述任何問題，請執行下列動作：

- 請確定您已*安裝並啟用*存取面板瀏覽器延伸模組的最新版本。 請參閱本文的[安裝存取面板瀏覽器延伸](#install-the-access-panel-browser-extension)模組一節。

- 在 capture 程式期間，請確定您的瀏覽器不是處於*incognito*、 *inPrivate*或*私*用模式。 這些模式不支援存取面板延伸模組。

- 請確定您的使用者不會在*incognito*、 *inPrivate*或*私用模式*中，嘗試從存取面板登入應用程式。

- 再次嘗試手動抓取程式。 請確定紅色標記不在正確的欄位上。

- 如果手動捕捉程式似乎停止回應，或登入頁面沒有回應，請再次嘗試手動捕捉程式。 但這次在完成此程式之後，請按 F12 鍵以開啟瀏覽器的開發人員主控台。 選取 [**主控台**] 索引標籤。輸入**window. location = "*&lt;您在設定應用程式&gt;時所指定的登入 URL*"**，然後按 enter。 這會強制執行頁面重新導向，以結束捕捉進程並儲存已捕捉的欄位。

### <a name="contact-support"></a>請連絡支援人員

如果您仍有問題，請使用 Microsoft 支援服務開啟案例。 描述您所嘗試的內容。 請在「[觀看入口網站通知詳細資料](#view-portal-notification-details)」中包含詳細資料，並將[通知詳細資料傳送給支援工程師，以取得](#send-notification-details-to-a-support-engineer-to-get-help)本文的說明章節（如果適用）。

## <a name="install-the-access-panel-browser-extension"></a>安裝存取面板的瀏覽器擴充功能

請遵循下列步驟：

1. 在支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)。 以*使用者*身分登入 Azure AD。

2. 在存取面板中選取 [**密碼-SSO 應用程式**]。

3. 當系統提示您安裝軟體時，請選取 [**立即安裝**]。

4. 系統會將您導向瀏覽器的下載頁面。 選擇 [**新增**] 延伸模組。

5. 如果出現提示，請選取 [**啟用**] 或 [**允許**]。

6. 安裝之後，請重新開機您的瀏覽器。

7. 登入存取面板。 查看您是否可以開啟已啟用密碼 SSO 的應用程式。

您也可以透過下列連結直接下載適用于 Chrome 和 Firefox 的瀏覽器延伸模組：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>觀看入口網站通知詳細資料

若要查看任何入口網站通知的詳細資料，請遵循下列步驟：

1. 選取 Azure 入口網站右上角的 [**通知**] 圖示（鐘）。

2. 選取顯示*錯誤*狀態的任何通知。 （它們有紅色的 "！"）。

   > [!NOTE]
   > 您無法選取處於 [*成功*] 或 [*進行中*] 狀態的通知。

3. [通知詳細資料]**** 窗格隨即開啟。 閱讀資訊以瞭解問題。

5. 如果您仍然需要協助，請與支援工程師或產品小組分享資訊。 選取 [**複製錯誤**] 方塊右邊的**複製**圖示，將通知詳細資料複製到共用。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>將通知詳細資料傳送給支援工程師以取得協助

請務必共用本節中所列的*所有*詳細資料，並提供支援，讓他們能夠快速地協助您。 若要記錄它，您可以採取螢幕擷取畫面或選取 [**複製錯誤**]。

下列資訊說明每個通知專案的意義，並提供範例。

### <a name="essential-notification-items"></a>基本通知專案

- **標題**：通知的描述性標題。

   範例：*應用程式 proxy 設定*

- **描述**：作業所發生的結果。

   範例：*輸入的內部 URL 已由另一個應用程式使用中。*

- **通知識別碼**：通知的唯一識別碼。

    範例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **用戶端要求識別碼**：您的瀏覽器所建立的特定要求識別碼。

    範例： *302fd775-3329-4670-a9f3-bea37004f0bc*

- **時間戳記 UTC**：發生通知的時間戳記（utc 格式）。

    範例： *2017-03-23T19：50： 43.7583681 z*

- **內部交易識別碼**：用來在系統中查閱錯誤的內部識別碼。

    範例： **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**：執行作業的使用者。

    範例： *tperkins\@f128.info*

- **租使用者識別碼**：執行作業的使用者所屬之租使用者的唯一識別碼。

    範例： *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **使用者物件識別碼**：執行作業之使用者的唯一識別碼。

    範例： *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>詳細通知專案

- **顯示名稱**：（可以是空白）錯誤的更詳細顯示名稱。

    範例：*應用程式 proxy 設定*

- **狀態**：通知的特定狀態。

    範例：*失敗*

- **物件識別碼**：（可以是空的）執行作業所針對的物件識別碼。

   範例： *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **詳細資料**：作業結果所發生的詳細描述。

    範例：*內部 url '<https://bing.com/>' 無效，因為它已在使用中。*

- **複製錯誤**：可讓您選取 [**複製錯誤**] 文字方塊右邊的**複製圖示**，以複製通知詳細資料以協助支援。

    實例```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
