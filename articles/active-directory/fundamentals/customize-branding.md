---
title: 將商標新增至組織的登入頁面-Azure AD
description: 以下相關指示說明如何將組織的商標新增至 Azure Active Directory 登入頁面。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb138ade0f579a7545c0910646b6adfb7d5ac02a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650218"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>將商標新增至組織的 Azure Active Directory 登入頁面
使用您組織的標誌和自訂色彩配置，在 Azure Active Directory (Azure AD) 登入頁面上展現一致的外觀與風格。 登入頁面會在使用者登入您組織的 web 應用程式時出現，例如 Microsoft 365，其使用 Azure AD 作為身分識別提供者。

>[!NOTE]
>新增自訂商標需要 Azure Active Directory Premium 1 或 Premium 2 授權。 如需授權和版本的詳細資訊，請參閱[註冊 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>Azure AD Premium 版本適用于使用全球 Azure Active Directory 實例的中國客戶。 中國的世紀的 Azure 服務目前不支援 Azure AD Premium 版。 如需詳細資訊，請使用 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/) (英文) 與我們連絡。

## <a name="customize-your-azure-ad-sign-in-page"></a>自訂 Azure AD 登入頁面
您可以自訂 Azure AD 登入頁面，該頁面會在使用者登入您組織的租用戶特定應用程式 (例如 `https://outlook.com/contoso.com`) 時或傳遞網域變數 (例如 `https://passwordreset.microsoftonline.com/?whr=contoso.com`) 時出現。

當您的使用者前往 www office.com 之類的網站時，您的自訂商標不會立即顯示 \. 。 使用者必須先登入，您的自訂商標才會出現。 使用者登入之後，商標可能需要15分鐘或更長的時間才會出現。 

> [!NOTE]
> 所有商標元素均為選擇性使用。 例如，如果您指定的橫幅標誌沒有背景影像，登入頁面將會顯示您的標誌以及目的地網站的預設背景影像 (例如 Microsoft 365) 。<br><br>此外，登入頁面商標不會延續至個人 Microsoft 帳戶。 如果使用者或商務來賓使用個人 Microsoft 帳戶登入，其登入頁面將不會出現貴組織的商標。

### <a name="to-customize-your-branding"></a>自訂您的商標
1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [Azure Active Directory]，然後選取 [公司商標]，再選取 [設定]。

    ![[Contoso - 公司商標頁面，[設定] 選項反白顯示](media/customize-branding/company-branding-configure-button.png)

3. 請在 [設定公司商標] 頁面上，提供下列部分或全部資訊。

    >[!IMPORTANT]
    >在此頁面新增的所有自訂影像均有其影像大小 (像素) 限制，而且可能有檔案大小 (KB) 限制。 由於有所限制，很可能必須使用相片編輯程式來建立適當大小的影像。

    - **一般設定**

        ![設定公司商標頁面，一般設定已完成](media/customize-branding/configure-company-branding-general-settings.png)

        - **語言。** 語言會自動設為預設值，而且無法變更。
        
        - **登入頁面背景影像。** 請選取要做為登入頁面背景的 .png 或 .jpg 影像檔案。 影像將會錨定至瀏覽器的中心，並且會調整為可查看空間的大小。 您無法選取大於1920x1080 圖元大小或檔案大小超過 300 KB 的影像。
        
            建議您在不具強式主旨焦點的情況下使用影像，例如，畫面中央會出現不透明的白色方塊，並且可以根據可查看空間的維度來涵蓋影像的任何部分。

        - **橫幅標誌。** 請選取 .png 或 .jpg 格式的標誌，標誌會在使用輸入使用者名稱後出現在登入頁面上，也會出現在 **My Apps** 入口網站頁面上。
            
            影像的高度不能大於60圖元或超過280圖元。 建議使用透明影像，因為背景可能會不符合您標誌的背景。 也建議不要在影像周圍加入邊框間距，否則會讓標誌看起來變小。

        - **Username 提示。** 輸入使用者忘記使用者名稱時，就會向使用者顯示的提示文字。 此文字必須是 Unicode，不可含有任何連結或程式碼，而且不可超過 64 個字元。 如果來賓是登入您的應用程式，建議不要加入此提示。

        - **登入頁面文字和格式。** 請輸入會在登入頁面底部出現的文字。 您可以使用此文字來提供其他資訊，例如技術支援中心的電話號碼或法律聲明。 此文字必須是 Unicode，且不能超過1024個字元。

           您可以自訂您輸入的登入頁面文字。 若要開始新的段落，請使用 enter 鍵兩次。 您也可以變更文字格式，以包含粗體、斜體、底線或可點按的連結。 使用下列語法將格式設定新增至文字： 

          > 連結： ```[text](link)``` 
          
          > 粗體： ``` **text** ``` 或 ``` __text__ ``` 
          
          > 斜體： ``` *text* ``` 或 ``` _text_ ``` 
          
          > 強調： ``` ++text++ ``` 

    - **Advanced 設定**
            
        ![設定公司商標頁面，進階設定已完成](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **登入頁面背景色彩。** 請指定十六進位色彩 (例如，白色是 #FFFFFF)，此色彩會在低頻寬連線的情況下代替背景影像。 建議使用橫幅標誌的主要顏色或貴組織的代表顏色。

        - **方形標誌影像。** 選取 .png (慣用的) 或您組織標誌的 .jpg 影像，在新 Windows 10 企業版裝置的安裝程式期間對使用者顯示。 此影像只會用於 Windows 驗證，而且只有在租用戶使用 [Windows Autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) 部署或做為其他 Windows 10 使用體驗的密碼輸入頁面時，才會向此租用戶顯示。 在某些情況下，它可能也會出現在 [同意] 對話方塊中。
        
            影像的大小不能大於240x240 的圖元，且檔案大小必須小於 10 KB。 建議使用透明影像，因為背景可能會不符合您標誌的背景。 也建議不要在影像周圍加入邊框間距，否則會讓標誌看起來變小。
    
        - **方形標誌影像、暗色調佈景主題。** 與上述的方形標誌影像相同。 此標誌影像會取代深色背景下的方形標誌影像，例如，在全新體驗 (OOBE) 期間出現的 Windows 10 Azure AD 連結畫面。  如果您的標誌在白色、深藍色和黑色背景上看起來很好，您就不需要加入此影像。 
        
        - **顯示保持登入選項。** 您可以選擇讓使用者在明確登出之前保持登入 Azure AD。如果您選擇 [ **否**]，則會隱藏這個選項，而且使用者必須在每次關閉並重新開啟瀏覽器時登入。

            這項功能僅適用于預設商標物件，而不是任何特定語言的物件。 若要深入瞭解如何設定及疑難排解選項以保持登入狀態，請參閱 [設定 Azure AD 帳戶的 [保持登入？] 提示](keep-me-signed-in.md)
        
            >[!NOTE]
            >SharePoint Online 和 Office 2010 的某些功能取決於能夠選擇此選項以保持登入的使用者。 如果您將此選項設定為 [否]，使用者可能就會在登入時看見額外和非預期的提示。
   

3. 您加入自己的商標完成之後，請選取 [儲存]。

    如果此流程建立的是您第一個自訂商標設定，則會成為租用戶的預設值。 如果您有其他設定，則可以選擇預設設定。
    
    >[!IMPORTANT]
    >若要將更多公司商標設定新增給您的租用戶，必須選擇 [Contoso - 公司商標] 頁面上的 [新增語言]。 這會開啟 [設定公司商標] 頁面，可在此按照上述相同步驟進行。

## <a name="update-your-custom-branding"></a>更新您的自訂商標
建立自訂商標之後，可以返回並變更任何項目。

### <a name="to-edit-your-custom-branding"></a>編輯您的自訂商標
1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [Azure Active Directory]，然後選取 [公司商標]，再選取 [設定]。

    ![[Contoso - 公司商標] 頁面，會顯示預設設定](media/customize-branding/company-branding-default-config.png)

3. 請在 [設定公司商標] 頁面上，按照本文的[自訂您的 Azure AD 登入頁面](#customize-your-azure-ad-sign-in-page)一節的說明，新增、移除或變更任何資訊。

4. 選取 [儲存]。

   您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="add-language-specific-company-branding-to-your-directory"></a>將特定語言公司商標新增到您的目錄
無法將原本設定的語言更改為預設語言以外的語言。 不過如果需要不同語言的設定，可以建立新的設定。

### <a name="to-add-a-language-specific-branding-configuration"></a>新增特定語言的商標設定

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [Azure Active Directory]，然後選取 [公司商標]，再選取 [新增語言]。

    ![[Contoso - 公司商標] 頁面，反白顯示 [新增語言] 選項](media/customize-branding/company-branding-new-language.png)

3. 請在 [設定公司商標] 頁面上，選取您的語言 (例如，法文)，然後按照本文的[自訂您的 Azure AD 登入頁面](#customize-your-azure-ad-sign-in-page)一節的說明，新增經過翻譯的資訊。

4. 選取 [儲存]。

    [Contoso - 公司商標] 頁面會更新，以顯示新的法文設定。

    ![[Contoso-公司商標] 頁面，其中顯示新的語言設定](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>將您的自訂商標新增至頁面
以文字 `?whr=yourdomainname` 修改 URL 結尾，將自訂商標新增至頁面。 這項修改適用於多個頁面，包括 Multi-Factor Authentication (MFA) 的設定頁面、自助式密碼重設 (SSPR) 設定頁面和登入頁面。

**範例：**

**原始 URL：** https://aka.ms/MFASetup<br>
**自訂 URL：**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**原始 URL：** https://aka.ms/SSPR<br>
**自訂 URL：**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
