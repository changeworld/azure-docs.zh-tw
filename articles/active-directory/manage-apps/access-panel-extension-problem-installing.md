---
title: 安裝應用程式訪問面板瀏覽器延伸 - Azure AD
description: 修正在安裝存取面板的瀏覽器擴充功能時所遇到的常見錯誤。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275865"
---
# <a name="install-the-access-panel-browser-extension"></a>安裝存取面板的瀏覽器擴充功能

存取面板是網頁型入口網站。 如果您在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶，可以使用存取面板來檢視和啟動 Azure AD 系統管理員已授權您存取的雲端式應用程式。 

如果您是使用 Azure AD 版本，也可以透過存取面板使用自助群組和應用程式管理功能。 

存取面板會與 Azure 入口網站分開。 您不需要具有 Azure 訂用帳戶。

## <a name="web-browser-requirements"></a>網頁瀏覽器需求

存取面板至少需要有支援 JavaScript 且啟用 CSS 的瀏覽器。 若要在存取面板中透過密碼型 SSO 登入應用程式，您必須在瀏覽器中安裝存取面板擴充功能。 當您選取已設定密碼型 SSO 的應用程式時，系統就會自動下載此擴充功能。

對於密碼型 SSO，您可以使用下列任何瀏覽器：

- **Microsoft Edge**：在 Windows 10 Anniversary Edition 或更新版本上。 
- **Chrome**：在 Windows 7 或更新版本，和在 MacOS X 或更新版本上。
- **Firefox 26.0 或更新版本**：在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上。

## <a name="install-the-access-panel-browser-extension"></a>安裝存取面板的瀏覽器擴充功能

若要安裝存取面板的瀏覽器擴充功能，請執行下列作業：

1.  在其中一種支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在您的 Azure AD 中，以使用者身分登入。

2.  選取密碼型 SSO 應用程式。

3.  當系統提示您時，選取 [立即安裝]****。  
    系統會將您導向至所選取瀏覽器的下載連結。 
    
4.  選取 [加入]****。

5.  如果您看到系統提示，請 [啟用]**** 或 [允許]**** 擴充功能。

6.  在安裝完成後，請重新啟動瀏覽器。

7.  登入存取面板，並查看您是否可以啟動密碼型 SSO 應用程式。

您也可以直接從下列網站下載適用於 Chrome 和 Microsoft Edge 的延伸模組：

- [Chrome 擴充功能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge 延伸模組](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [火狐擴展](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>使用 My Apps 安全登入擴充功能
* 如果您是使用 `https://myapps.microsoft.com` 以外的 My Apps URL，請執行下列作業來設定您的預設 URL：
   1. 在尚未** 登入擴充功能時，以滑鼠右鍵按一下擴充功能圖示。
   2. 在功能表上，選取 [My Apps URL]****。
   3. 選取預設 URL。
   4. 選取擴充功能圖示。
   5. 若要登入擴充功能，請選取 [登入以開始使用]****。

* 若要直接從瀏覽器登入應用程式，請執行下列作業：
   1. 安裝擴充功能後，請選取 [登入以開始使用]**** 來登入擴充功能。
   2. 使用登入 URL 來登入應用程式。  
       登入 URL 通常是顯示登入表單之應用程式的 URL。
      該擴充功能應該會變更狀態，並讓您知道有密碼可用。
   3. 若要登入，請選取擴充功能圖示。

* 若要從擴充功能啟動應用程式，請執行下列動作：
   1. 安裝擴充功能後，請選取 [登入以開始使用]**** 來登入擴充功能。
   2. 選取擴充功能圖示將其功能表開啟。
   3. 在 My Apps 入口網站中搜尋可用的應用程式。
   4. 在搜尋結果清單中，選取應用程式。  
       您所使用的最後三個應用程式會顯示在 [最近使用的]**** 捷徑清單中。
       
* 若要遠端使用公司內部 URL，請執行下列作業：
    1. [在租戶上配置應用程式代理](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)
    2. 通過[應用程式代理髮布應用程式](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)和 URL
    3. 安裝延伸模組，然後藉由選取 [登入以開始使用] 來登入該延伸模組
    4. 即使在遠端，您還是可以立即瀏覽至公司內部 URL

> [!NOTE]
> 上述選項僅適用於 Microsoft Edge、Chrome 和 Firefox。

## <a name="set-up-a-group-policy-for-internet-explorer"></a>設定適用於 Internet Explorer 的群組原則

您可以設定一個群組原則，以允許您在使用者電腦上遠端安裝適用於 Internet Explorer 的存取面板擴充功能。

設定群組原則之前，請確認：

-   您已設定了 [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，並且已將使用者的電腦加入網域。

-   若要編輯群組原則物件 (GPO)，您必須擁有「編輯設定」** 權限。 根據預設，系統會將此權限授與下列安全性群組的成員：網域系統管理員、企業系統管理員及群組原則建立者擁有者。

如需設定群組原則並將它部署到使用者的逐步指示，請參閱[使用群組原則部署適用於 Internet Explorer 的存取面板擴充功能](deploy-access-panel-browser-extension.md)。

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>針對 Internet Explorer 中的存取面板擴充功能進行疑難排解

如需存取診斷工具和設定 Internet Explorer 擴充功能的相關資訊，請參閱[針對 Internet Explorer 中的存取面板擴充功能進行疑難排解](manage-access-panel-browser-extension.md)。

> [!NOTE]
> Internet Explorer 的支援有限，且不會再收到新的軟體更新。 Microsoft Edge 是建議使用的瀏覽器。

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>如果上述步驟無法解決問題

使用下列資訊 (若有的話) 開啟支援票證︰

-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   發生錯誤時的時區和時間或時間範圍
-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](what-is-single-sign-on.md)
