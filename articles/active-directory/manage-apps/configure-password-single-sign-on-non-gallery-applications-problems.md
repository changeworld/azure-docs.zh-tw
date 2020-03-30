---
title: 為非庫應用配置密碼 SSO 時出現問題
description: 為 Azure AD 應用程式庫中未使用的自訂應用配置密碼單一登入 （SSO） 時出現的常見問題。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274140"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>為非庫應用程式佈建密碼單一登入時遇到問題

本文介紹了為非庫應用配置*密碼單一登入*（SSO） 時可能出現的常見問題。

## <a name="capture-sign-in-fields-for-an-app"></a>捕獲應用的登錄欄位

登錄欄位捕獲僅支援啟用 HTML 的登錄頁。 不支援非標準登錄頁，例如使用 Adobe Flash 或其他非 HTML 支援的技術的登錄頁。

有兩種方法可以捕獲自訂應用的登錄欄位：

- **如果自動登入欄位捕獲**對使用者名和密碼欄位*使用眾所周知的 DIV ID，* 則適用于大多數啟用 HTML 的登錄頁。 頁面上的 HTML 被刮去以查找符合特定條件的 DIV ID。 該中繼資料將被保存，以便以後可以重播到應用。

- 如果應用供應商*未標記登錄輸入欄位*，則使用**手動登錄欄位捕獲**。 如果供應商*呈現多個無法自動檢測到的欄位，* 則也使用手動捕獲。 Azure 活動目錄 （Azure AD） 可以存儲與登錄頁上相同的欄位的資料（如果告訴它這些欄位在頁面上的位置）。

通常，如果自動登入欄位捕獲不起作用，請嘗試手動選項。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自動捕獲應用的登錄欄位

要使用自動登入欄位捕獲配置基於密碼的 SSO，請按照以下步驟操作：

1. 打開[Azure 門戶](https://portal.azure.com/)。 以全域管理員或共同管理員身份登錄。

2. 在左側的功能窗格中，選擇 **"所有服務**"以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE]
   > 如果看不到所需的應用，請使用 **"所有應用程式**"清單頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為 SSO 配置的應用。

7. 載入應用後，在左側的功能窗格中選擇 **"單一登入**"。

8. 選擇**基於密碼的登錄**模式。

9. 輸入**登錄 URL**，這是使用者輸入其使用者名和密碼登錄的頁面的 URL。 *確保登錄欄位在提供的 URL 的頁面上可見*。

10. 選取 [儲存]****。

    使用者名和密碼輸入框會自動刮擦頁面。 現在，您可以使用 Azure AD 使用訪問面板瀏覽器延伸安全地將密碼傳輸到該應用。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手動捕獲應用的登錄欄位

要手動捕獲登錄欄位，必須安裝訪問面板瀏覽器延伸。 此外，您的瀏覽器不能*以"私人*"、*隱身*或*私有*模式運行。

要安裝擴展，請參閱本文[的"安裝面板瀏覽器延伸](#install-the-access-panel-browser-extension)"部分。

要使用手動登錄欄位捕獲為應用配置基於密碼的 SSO，請按照以下步驟操作：

1. 打開[Azure 門戶](https://portal.azure.com/)。 以全域管理員或共同管理員身份登錄。

2. 在左側的功能窗格中，選擇 **"所有服務**"以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE] 
   > 如果看不到所需的應用，請使用 **"所有應用程式**"清單頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為 SSO 配置的應用。

7. 載入應用後，在左側的功能窗格中選擇 **"單一登入**"。

8. 選擇**基於密碼的登錄**模式。

9. 輸入**登錄 URL**，這是使用者輸入其使用者名和密碼登錄的頁面。 *確保登錄欄位在提供的 URL 的頁面上可見*。

10. 選擇 **"*&lt;配置應用程式&gt;名稱*密碼單一登入設置**"。

11. 選擇 **"手動檢測登錄欄位**"。

14. 選擇 **"確定**"。

15. 選取 [儲存]****。

16. 按照說明使用訪問面板。

## <a name="troubleshoot-problems"></a>疑難排解問題

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我得到一個"我們找不到任何登錄欄位在該 URL"錯誤

當自動檢測登錄欄位失敗時，您會收到此錯誤訊息。 要解決此問題，請嘗試手動登錄欄位檢測。 請參閱本文[的應用程式部分的手動捕獲登錄欄位](#manually-capture-sign-in-fields-for-an-app)。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到"無法保存單一登入配置"錯誤

更新 SSO 配置很少失敗。 要解決此問題，請嘗試再次保存配置。

如果繼續收到錯誤，請打開一個支援案例。 包括["查看門戶通知詳細資訊](#view-portal-notification-details)"中描述的資訊，並將[通知詳細資訊發送給支援工程師，以獲得本文的説明](#send-notification-details-to-a-support-engineer-to-get-help)部分。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>無法手動檢測應用的登錄欄位

當手動檢測不起作用時，您可能會觀察到以下行為：

- 手動捕獲過程似乎工作正常，但捕獲的欄位不正確。

- 捕獲過程運行時，正確的欄位不會突出顯示。

- 捕獲過程將按預期的方式訪問應用的登錄頁面，但沒有任何反應。

- 手動捕獲似乎工作，但當使用者從訪問面板導航到應用時，不會發生 SSO。

如果您遇到上述任何問題，可以執行以下操作：

- 請確保您*已安裝並啟用*了訪問面板瀏覽器延伸的最新版本。 請參閱本文[的"安裝訪問面板"瀏覽器延伸](#install-the-access-panel-browser-extension)部分。

- 確保您的瀏覽器在捕獲過程中未處於*隱身**、Private*或*私有*模式。 這些模式下不支援訪問面板擴展。

- 確保使用者在*隱身**、Private*或*私有模式下*不會嘗試從訪問面板登錄到應用。

- 請重試手動捕獲過程。 確保紅色標記位於正確的欄位上。

- 如果手動捕獲過程似乎停止回應或登錄頁未回應，請重試手動捕獲過程。 但這次，在完成此過程後，按 F12 鍵打開瀏覽器的開發人員主控台。 選擇**主控台**選項卡.鍵入**視窗.location_"*&lt;配置應用&gt;時指定的登錄 URL"，*** 然後按 Enter。 這將強制頁面重定向，該頁面將結束捕獲過程並存儲捕獲的欄位。

### <a name="contact-support"></a>請連絡支援人員

如果仍有問題，則使用 Microsoft 支援打開案例。 描述你嘗試過什麼。 包括["查看門戶通知詳細資訊](#view-portal-notification-details)"中描述的詳細資訊，並將[通知詳細資訊發送給支援工程師，以獲得本文的説明](#send-notification-details-to-a-support-engineer-to-get-help)部分（如果適用）。

## <a name="install-the-access-panel-browser-extension"></a>安裝訪問面板瀏覽器延伸

請遵循下列步驟：

1. 在受支援的瀏覽器中打開[訪問面板](https://myapps.microsoft.com)。 以*使用者*身份登錄到 Azure AD。

2. 在訪問面板中選擇**密碼 SSO 應用程式**。

3. 當系統提示您安裝軟體時，選擇"**立即安裝**"。

4. 您將被引導到瀏覽器的下載頁面。 選擇**添加**副檔名。

5. 如果系統提示您，請選擇"**啟用**"或 **"允許**"。

6. 安裝後，重新開機瀏覽器。

7. 登錄到訪問面板。 查看是否可以打開啟用密碼 SSO 的應用。

您也可以通過以下連結直接下載 Chrome 和 Firefox 的瀏覽器延伸：

-   [鉻接入面板擴展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [火狐接入面板擴展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>查看門戶通知詳細資訊

要查看任何門戶通知的詳細資訊，請按照以下步驟操作：

1. 選擇 Azure 門戶右上角**的通知**圖示（鈴聲）。

2. 選擇顯示*錯誤*狀態的任何通知。 （他們有一個紅色的"！

   > [!NOTE]
   > 不能選擇處於 *"成功*"或"*正在"* 狀態的通知。

3. [通知詳細資料]**** 窗格隨即開啟。 閱讀資訊以瞭解問題。

5. 如果您仍需要幫助，則與支援工程師或產品組共用資訊。 選擇 **"複製"錯誤**框右側的**複製**圖示，以複製要共用的通知詳細資訊。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>向支援工程師發送通知詳細資訊以獲取有關説明

請務必與支援部門共用本節中列出的*所有*詳細資訊，以便它們能夠快速説明您。 要錄製它，您可以截屏或選擇 **"複製錯誤**"。

以下資訊說明了每個通知項的含義，並提供了示例。

### <a name="essential-notification-items"></a>基本通知項

- **標題**：通知的描述性標題。

   示例：*應用程式代理設置*

- **說明**：操作的結果。

   示例：*輸入的內部 URL 已被另一個應用程式使用。*

- **通知 ID**：通知的唯一 ID。

    示例：*用戶端通知-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **用戶端請求 ID**：您的瀏覽器提供的特定請求 ID。

    示例 *：302fd775-3329-4670-a9f3-bea37004f0bc*

- **時間戳記 UTC**：發生通知時的時間戳記，以 UTC 表示。

    示例： *2017-03-23T19：50：43.7583681Z*

- **內部事務 ID**：用於查找系統中錯誤的內部 ID。

    示例 **：71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**： 運行該操作的使用者。

    示例 *：tperkinsf128.info\@ *

- **租戶 ID**：運行操作的使用者是 其成員的租戶的唯一 ID。

    示例 *：7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **使用者物件識別碼**：運行操作的使用者的唯一 ID。

    示例 *：17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>詳細的通知項

- **顯示名稱**：（可以為空）錯誤更詳細的顯示名稱。

    示例：*應用程式代理設置*

- **狀態**：通知的特定狀態。

    示例：*失敗*

- **物件識別碼**：（可以為空）運行操作的物件識別碼。

   示例 *：8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **詳細資訊**：操作結果的詳細描述。

    示例：*內部 url<https://bing.com/>' ' 無效，因為它已在使用中。*

- **複製錯誤**：允許您選擇 **"複製錯誤**文字方塊"右側的**複製圖示**，以複製通知詳細資訊以説明提供支援。

    例子：```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
