---
title: 針對 Azure Active Directory 中的密碼型單一登入進行疑難排解
description: 針對針對密碼型單一登入設定的 Azure AD 應用程式問題進行疑難排解。
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 1823de88597756ba3db9aee0dc29501b1ba914a4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646231"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>針對 Azure AD 中的密碼型單一登入進行疑難排解

若要在我的應用程式中使用以密碼為基礎的單一登入 (SSO) ，必須安裝瀏覽器擴充功能。 當您選取已設定密碼型 SSO 的應用程式時，會自動下載擴充功能。 若要瞭解如何從終端使用者的觀點來使用我的應用程式，請參閱 [我的應用程式入口網站](../user-help/my-apps-portal-end-user-access.md)說明。

## <a name="my-apps-browser-extension-not-installed"></a>未安裝我的應用程式瀏覽器延伸模組
請確定已安裝瀏覽器擴充功能。 若要深入瞭解，請參閱 [規劃 Azure Active Directory 我的應用程式部署](access-panel-deployment-plan.md)。 

## <a name="single-sign-on-not-configured"></a>未設定單一登入
確定已設定以密碼為基礎的單一登入。 若要深入瞭解，請參閱 [設定以密碼為基礎的單一登入](configure-password-single-sign-on-non-gallery-applications.md)。

## <a name="users-not-assigned"></a>未指派的使用者
請確定已將使用者指派給應用程式。 若要深入瞭解，請參閱 [將使用者或群組指派給應用程式](assign-user-or-group-access-portal.md)。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>認證會填入，但延伸模組不會提交它們

如果應用程式廠商最近有變更登入頁面，以新增欄位、變更用來偵測使用者名稱和密碼欄位的識別碼，或修改其應用程式的登入體驗運作方式，通常就會發生此問題。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題需要一些時間來修正。 萬一其中一項整合無法正常運作，請開啟支援案例，如此便能儘速修正問題。

**如果您與此應用程式的廠商有所聯繫，** 請將我們的連絡方式傳送給他們，讓 Microsoft 能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../develop/v2-howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>認證會填入並提交，但頁面指出認證不正確

若要解決此問題，請嘗試下列方式：

- 讓使用者先嘗試使用為他們儲存的認證，**直接登入應用程式網站**。

  * 如果登入運作正常，請讓使用者在 [我的應用程式](https://myapps.microsoft.com/)的 [應用程式 **] 區段中**，按一下 [**應用程式] 圖** 格上的 [**更新認證**] 按鈕，將其更新為最新的已知工作使用者名稱和密碼。

  * 如果您或其他系統管理員已為此使用者指派認證，請尋找使用者或群組的應用程式指派，方法是瀏覽至應用程式的 [使用者和群組] 索引標籤、選取指派，然後按一下 [更新認證] 按鈕。

- 如果使用者已指派他們自己的認證，讓使用者 **檢查以確定他們的密碼在應用程式中尚未過期**，如果過期，請直接登入應用程式，以 **更新他們過期的密碼**。

  * 在應用程式中更新密碼之後，要求使用者在 [我的應用程式](https://myapps.microsoft.com/)的 **[應用程式] 區段中**，按一下 [**應用程式] 圖** 格上的 [**更新認證**] 按鈕，將其更新為最新的已知工作使用者名稱和密碼。

  * 如果您或其他系統管理員已為此使用者指派認證，請尋找使用者或群組的應用程式指派，方法是瀏覽至應用程式的 [使用者和群組] 索引標籤、選取指派，然後按一下 [更新認證] 按鈕。

- 確定我的應用程式瀏覽器擴充功能在使用者的瀏覽器中執行且已啟用。

- 確定您的使用者不會在 **incognito、inPrivate 或私用模式** 中嘗試從我的應用程式登入應用程式。 在這些模式中不支援我的應用程式延伸模組。

萬一先前的建議不可行，可能是因為應用程式端發生了變更，暫時中斷了應用程式與 Azure AD 的整合。 例如，這可能發生在下列情況中：當應用程式廠商在其頁面上引進了一個指令碼，而其行為在手動與自動輸入時極為不同，這可能導致自動整合 (就像我們自己的) 中斷。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在應用程式整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題可能需要一些時間來修正。 若整合未正常運作，您可以開啟支援案例，以便儘快修正。 

此外，**如果您與此應用程式的廠商聯繫，** **請將我們的連絡方式傳送給他們**，讓我們能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../develop/v2-howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>檢查應用程式的登入頁面最近是否已變更或需要額外的欄位

如果應用程式的登入頁面已大幅變更，有時這會導致我們的整合中斷。 當應用程式廠商將登入欄位、Captcha 或多重要素驗證新增至他們的體驗時，即為一例。 幸運的是，在許多情況下，Microsoft 可以與應用程式廠商合作，快速解決這些問題。

雖然 Microsoft 有技術可在應用程式整合中斷時進行自動偵測，但無法立即找出這些問題，或者問題可能需要一些時間來修正。 若整合未正常運作，您可以開啟支援案例，以便儘快修正。 

此外，**如果您與此應用程式的廠商聯繫，** **請將我們的連絡方式傳送給他們**，讓我們能夠與他們合作，將其應用程式與 Azure Active Directory 進行原生整合。 您可以將廠商引導到[在 Azure Active Directory 應用程式庫中列出您的應用程式](../develop/v2-howto-app-gallery-listing.md)，讓他們可以立即開始。

## <a name="capture-sign-in-fields-for-an-app"></a>捕捉應用程式的登入欄位

只有具備 HTML 功能的登入頁面支援登入欄位捕捉。 非標準登入頁面（例如使用 Adobe Flash 或其他非 HTML 技術的頁面）不支援此功能。

有兩種方式可以為您的自訂應用程式捕獲登入欄位：

- **自動登入欄位捕捉** 適用于大部分具有 HTML 功能的登入頁面，如果這些頁面對使用者名稱和密碼欄位 *使用知名的 DIV id* 。 頁面上的 HTML 是剪輯來尋找符合特定準則的 DIV 識別碼。 該中繼資料會儲存，以便稍後可以重新執行至應用程式。

- 如果應用程式廠商 *未標示登入輸入欄位*，則會使用 **手動登入欄位捕捉**。 如果廠商轉譯 *多個無法自動偵測的欄位，* 也會使用手動捕獲。 Azure Active Directory (Azure AD) 可以在登入頁面上儲存與登入頁面相同的欄位資料，如果您知道這些欄位在頁面上的位置。

一般情況下，如果自動登入欄位無法運作，請嘗試使用手動選項。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自動為應用程式捕捉登入欄位

若要使用自動登入欄位捕捉設定密碼型 SSO，請遵循下列步驟：
1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 以全域管理員或共同管理員身分登入。
2. 在左側流覽窗格中，選取 [ **所有服務** ] 以開啟 Azure AD 延伸模組。
3. 在篩選搜尋方塊中輸入 **Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。
4. 在 Azure AD 流覽窗格中選取 [ **企業應用程式** ]。
5. 選取 [ **所有應用程式** ] 以查看您的應用程式清單。
   > [!NOTE]
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式**] 清單頂端的 [**篩選器**] 控制項。 將 [ **顯示** ] 選項設定為 [所有應用程式]。
6. 選取您要為 SSO 設定的應用程式。
7. 應用程式載入之後，請在左側流覽窗格中選取 [ **單一登入** ]。
8. 選取 [ **密碼型登入** ] 模式。
9. 輸入 [登入 **url**]，這是使用者輸入其使用者名稱和密碼來登入之頁面的 url。 *確定您提供的 URL 頁面上有顯示登入欄位*。
10. 選取 [儲存]。
    系統會自動剪輯使用者名稱和密碼輸入方塊的頁面。 您現在可以使用 Azure AD 我的應用程式瀏覽器延伸模組，將密碼安全地傳輸到該應用程式。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手動捕獲應用程式的登入欄位

若要手動捕獲登入欄位，您必須安裝我的應用程式瀏覽器擴充功能。 此外，您的瀏覽器無法在 *inPrivate*、 *incognito* 或 *私* 用模式中執行。

若要使用手動登入欄位取得為應用程式設定密碼型 SSO，請遵循下列步驟：
1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 以全域管理員或共同管理員身分登入。
2. 在左側流覽窗格中，選取 [ **所有服務** ] 以開啟 Azure AD 延伸模組。
3. 在篩選搜尋方塊中輸入 **Azure Active Directory** ，然後選取 [ **Azure Active Directory**]。
4. 在 Azure AD 流覽窗格中選取 [ **企業應用程式** ]。
5. 選取 [ **所有應用程式** ] 以查看您的應用程式清單。
   > [!NOTE] 
   > 如果您沒有看到想要的應用程式，請使用 [**所有應用程式**] 清單頂端的 [**篩選器**] 控制項。 將 [ **顯示** ] 選項設定為 [所有應用程式]。
6. 選取您要為 SSO 設定的應用程式。
7. 應用程式載入之後，請在左側流覽窗格中選取 [ **單一登入** ]。
8. 選取 [ **密碼型登入** ] 模式。
9. 輸入 [登入 **URL**]，這是使用者輸入其使用者名稱和密碼來登入的頁面。 *確定您提供的 URL 頁面上有顯示登入欄位*。
10. 選取 [**設定 *&lt; &gt; Appname* Password 單一登入設定**]。
11. 選取 [手動偵測登 **入欄位**]。
14. 選取 [確定]  。
15. 選取 [儲存]  。
16. 依照指示使用我的應用程式。


## <a name="troubleshoot-problems"></a>問題疑難排解

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到「我們在該 URL 找不到任何登入欄位」錯誤

當登入欄位的自動偵測失敗時，您會收到此錯誤訊息。 若要解決此問題，請嘗試手動登入欄位偵測。 請參閱本文的「 [手動捕捉應用程式的登入欄位](#manually-capture-sign-in-fields-for-an-app) 」一節。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到「無法儲存單一登入設定」錯誤

很少會更新 SSO 設定。 若要解決此問題，請再次嘗試儲存設定。

如果您持續收到錯誤，請開啟支援案例。 請包括 [View portal 通知詳細資料](#view-portal-notification-details) 中所述的資訊，並將 [通知詳細資料傳送給支援工程師，以取得](#send-notification-details-to-a-support-engineer-to-get-help) 本文的協助章節。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我無法手動偵測應用程式的登入欄位

當手動偵測無法運作時，您可能會發現下列行為：
- 手動捕獲程式似乎正常運作，但捕捉的欄位不正確。
- 當捕捉程式執行時，正確的欄位不會反白顯示。
- 捕捉程式會將您帶到應用程式的登入頁面（如預期），但不會發生任何事。
- 手動捕獲看似正常運作，但在使用者從我的應用程式流覽至應用程式時，不會發生 SSO。

如果您遇到上述任何問題，請執行下列動作：
- 確定已 *安裝並啟用* 最新版本的我的應用程式瀏覽器擴充功能。
- 在捕獲程式期間，請確定您的瀏覽器不是處於 *incognito*、 *inPrivate* 或 *私* 用模式。 在這些模式中不支援我的應用程式延伸模組。
- 請確定您的使用者不會在 *incognito*、 *inPrivate* 或 *私用模式* 中嘗試從我的應用程式登入應用程式。
- 再次嘗試手動捕獲程式。 請確定紅色標記超過正確的欄位。
- 如果手動捕獲程式似乎停止回應，或登入頁面沒有回應，請再次嘗試手動捕獲程式。 但這次在完成此程式之後，請按 F12 鍵以開啟瀏覽器的開發人員主控台。 選取 [**主控台**] 索引標籤。 [**位置 =] 您在設定 *&lt; 應用程式 &gt; 時所指定的登入 URL***]，然後按 enter。 這會強制執行頁面重新導向，以結束捕獲程式並儲存已捕捉的欄位。

## <a name="request-support"></a>要求支援 
如果您在設定 SSO 並指派使用者時收到錯誤訊息，請開啟支援票證。 盡可能包含下列最多資訊：

-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   發生錯誤時的時區和時間/時間範圍
-   Fiddler 追蹤

### <a name="view-portal-notification-details"></a>查看入口網站通知詳細資料

若要查看任何入口網站通知的詳細資料，請遵循下列步驟：
1. 選取 [ **通知** ] 圖示 (Azure 入口網站右上角的鐘) 。
2. 選取顯示 *錯誤* 狀態的任何通知。  (它們有紅色的 "！"。) 
   > [!NOTE]
   > 您無法選取處於 [ *成功* ] 或 [ *進行中* ] 狀態的通知。
3. [通知詳細資料] 窗格隨即開啟。 閱讀資訊以瞭解問題。
5. 如果您仍然需要協助，請與支援工程師或產品群組分享資訊。 選取 [**複製錯誤**] 方塊右邊的 **複製** 圖示，將通知詳細資料複製到共用。

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>將通知詳細資料傳送給支援工程師以取得協助

請務必將此區段中所列的 *所有* 詳細資料與支援共用，讓他們可以快速地協助您。 若要記錄它，您可以拍攝螢幕擷取畫面，或選取 [ **複製錯誤**]。

下列資訊說明每個通知專案的意義，並提供範例。

#### <a name="essential-notification-items"></a>基本通知專案

- **標題**：通知的描述性標題。

   範例： *應用程式 proxy 設定*

- **描述**：作業結果發生的情況。

   範例： *輸入的內部 URL 已由另一個應用程式使用中。*

- **通知識別碼**：通知的唯一識別碼。

    範例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **用戶端要求識別碼**：您的瀏覽器所提出的特定要求識別碼。

    範例： *302fd775-3329-4670-a9f3-bea37004f0bc*

- **時間戳記 UTC**：發生通知的時間戳記（utc）。

    範例： *2017-03-23T19：50： 43.7583681 z*

- **內部交易識別碼**：用來在系統中查閱錯誤的內部識別碼。

    範例： **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**：執行操作的使用者。

    範例： *tperkins \@ f128.info*

- **租使用者識別碼**：執行作業的使用者所屬之租使用者的唯一識別碼。

    範例： *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **使用者物件識別碼**：執行作業之使用者的唯一識別碼。

    範例： *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>詳細通知專案

- **顯示名稱**： (可以是空的) 錯誤的詳細顯示名稱。

    範例： *應用程式 proxy 設定*

- **狀態**：通知的特定狀態。

    範例： *Failed*

- **物件識別碼**： (可以是空的，) 用來執行作業的物件識別碼。

   範例： *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **詳細資料**：作業結果所發生的詳細描述。

    範例： *內部 url ' <https://bing.com/> ' 無效，因為它已在使用中。*

- **複製錯誤**：可讓您選取 [**複製錯誤**] 文字方塊右邊的 **複製圖示**，以複製通知詳細資料以協助支援。

    例子：   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>後續步驟
* [應用程式管理快速入門系列](view-applications-portal.md)
* [規劃我的應用程式部署](access-panel-deployment-plan.md)