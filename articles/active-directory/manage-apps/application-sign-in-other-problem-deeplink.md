---
title: 使用深層連結登入應用程式的問題 | Microsoft Docs
description: 如何為使用 Azure AD 從深層連結 URL 存取應用程式的問題疑難排解
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "65825420"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>使用深層連結登入應用程式的問題

存取面板是網頁型入口網站，可讓在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 

在 Azure AD 入口網站中可代表使用者設定這些應用程式。 應用程式必須經過正確的設定並指派至使用者或使用者所屬的群組，才能在存取面板中顯示該應用程式。

深層連結或使用者存取 URL 是一種連結，您的使用者可使用此種連結直接從其瀏覽器網址列存取其密碼 SSO 應用程式。 透過瀏覽到此連結，使用者不必先移至存取面板，便會自動登入應用程式。 這是使用者用來從 Office 365 應用程式啟動程式存取這些應用程式的相同連結。

## <a name="general-issues-to-check-first"></a>首先檢查的一般問題

-   請確定您使用的**瀏覽器**符合存取面板的最低需求。

-   確定使用者的瀏覽器已將應用程式的 URL 新增至其**信任的網站**。

-   檢查應用程式以確認其**設定**正確。

-   確定使用者的帳戶**已啟用**可供登入。

-   確定使用者的帳戶**未鎖定**。

-   確定使用者的**密碼未過期或忘記**。

-   確定 **Multi-Factor Authentication** 未封鎖使用者存取。

-   確定**條件式存取原則**或**身分識別保護**原則未封鎖使用者存取。

-   確定使用者的**驗證連絡資訊**為最新版本，而可強制執行 Multi-Factor Authentication 或條件式存取原則。

-   也要確定嘗試清除瀏覽器的 Cookie，然後嘗試再次登入。

## <a name="checking-the-deeplink"></a>檢查深層連結

若要檢查您是否擁有正確的深層連結，請執行這些步驟：

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**或共同管理員身分登入 **。**

2. 按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5. 按一下 [所有應用程式]****，以檢視所有應用程式的清單。

   * 如果您在這裡看不到您想要顯示的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項，並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**或共同管理員身分登入 **。**

7. 按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

8. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

9. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

10. 按一下 [所有應用程式]****，以檢視所有應用程式的清單。

    * 如果您在這裡看不到您想要顯示的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項，並將 [**顯示**] 選項設定為 [**所有應用程式]。**

11. 選取您想要檢查深層連結的應用程式。

12. 尋找 [使用者存取 URL]**** 標籤。 您的深層連結應符合此 URL。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安裝存取面板的瀏覽器延伸模組

若要安裝存取面板的瀏覽器擴充功能，請遵循下列步驟：

1.  在其中一種支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在您的 Azure AD 中以**使用者**身分登入。

2.  按一下存取面板中的 [密碼-SSO 應用程式]****。

3.  在要求安裝軟體的提示中，選取 [立即安裝]****。

4.  系統會根據您的瀏覽器將您導向下載連結。 將延伸模組**新增**到瀏覽器中。

5.  如果您的瀏覽器要求，請選取 [啟用]**** 或 [允許]**** 該延伸模組。

6.  安裝之後，**重新啟動**瀏覽器工作階段。

7.  登入存取面板，並查看您是否可以**啟動**密碼-SSO 應用程式

您可能也會從以下直接連結中下載適用於 Chrome 和 Firefox 的延伸模組：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 存取面板延伸模組](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>如何為 Azure AD 資源庫應用程式設定密碼單一登入

若要設定 Azure AD 資源庫中的應用程式，您必須：

-   [從 Azure AD 資源庫新增應用程式](#add-an-application-from-the-azure-ad-gallery)

-   [設定應用程式使用密碼單一登入](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>從 Azure AD 資源庫新增應用程式

若要從 Azure AD 資源庫新增應用程式，請遵循下列步驟：

1.  開啟[Azure 入口網站](https://portal.azure.com)，並以**全域管理員**或**共同**管理員身分登入。

2.  按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4.  在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5.  按一下 [企業應用程式]**** 窗格右上角的 [新增]**** 按鈕。

6.  在 [從資源庫新增]**** 區段的 [輸入名稱]**** 文字方塊中，輸入應用程式名稱。

7.  選取您要設為單一登入的應用程式。

8.  新增應用程式之前，您可以從 [名稱]**** 文字方塊變更其名稱。

9.  若要新增應用程式，按一下 [新增]****。

稍候片刻，您便能看見應用程式的設定窗格。

### <a name="configure-the-application-for-password-single-sign-on"></a>設定應用程式使用密碼單一登入

若要設定應用程式使用單一登入，請遵循下列步驟：

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**或共同管理員身分登入 **。**

2. 按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5. 按一下 [所有應用程式]****，以檢視所有應用程式的清單。

   * 如果您在這裡看不到您想要顯示的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項，並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 選取您要設定單一登入的應用程式。

7. 應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]****。

8. 選取 [以密碼為基礎的登入]**** 模式。

9. [將使用者指派給應用程式](#how-to-assign-a-user-to-an-application-directly)。

10. 此外，您也可以選取使用者資料列，按一下 [更新認證]****，然後代表使用者輸入使用者名稱和密碼，以代表使用者提供認證。 否則，系統會提示使用者在啟動時自行輸入認證。

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何為不在資源庫內的應用程式設定密碼單一登入

若要設定 Azure AD 資源庫中的應用程式，您必須：

-   [新增不在資源庫內的應用程式](#add-a-non-gallery-application)

-   [設定應用程式使用密碼單一登入](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>新增不在資源庫內的應用程式

若要從 Azure AD 資源庫新增應用程式，請遵循下列步驟：

1.  開啟[Azure 入口網站](https://portal.azure.com)，並以**全域管理員**或**共同**管理員身分登入。

2.  按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4.  在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5.  按一下 [企業應用程式]**** 窗格右上角的 [新增]**** 按鈕。

6.  按一下 [不在資源庫內的應用程式]****。

7.  在 [名稱]**** 文字方塊中輸入應用程式的名稱。 選取 [**新增]。**

稍候片刻，您便能看見應用程式的設定窗格。

### <a name="configure-the-application-for-password-single-sign-on"></a>設定應用程式使用密碼單一登入

若要設定應用程式使用單一登入，請遵循下列步驟：

1.  開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**或共同管理員身分登入 **。**

2.  按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4.  在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5.  按一下 [所有應用程式]****，以檢視所有應用程式的清單。

    1.  如果您在這裡看不到您想要顯示的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項，並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6.  選取您要設定單一登入的應用程式。

7.  應用程式載入後，按一下應用程式的左側導覽功能表中的 [單一登入]****。

8.  選取 [以密碼為基礎的登入]**** 模式。

9.  輸入 [登入 URL]****，使用者將在該處輸入其使用者名稱和密碼進行登入。 請確保在此 URL 看得到登入欄位。

10. 將使用者指派給應用程式。

11. 此外，您也可以選取使用者資料列，按一下 [更新認證]****，然後代表使用者輸入使用者名稱和密碼，以代表使用者提供認證。 否則，系統會提示使用者在啟動時自行輸入認證。

## <a name="how-to-assign-a-user-to-an-application-directly"></a>如何將使用者直接指派至應用程式

若要直接將一或多個使用者指派給應用程式，請遵循下列步驟︰

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**身分登入。

2. 按一下左側主導覽功能表底部的 [所有服務]****，以開啟 [Azure Active Directory 延伸模組]****。

3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory]**** 項目。

4. 在 Azure Active Directory 左側導覽功能表中，按一下 [企業應用程式]****。

5. 按一下 [所有應用程式]****，以檢視所有應用程式的清單。

   * 如果您在這裡看不到您想要顯示的應用程式，請使用 [**所有應用程式] 清單**頂端的 [**篩選**] 控制項，並將 [**顯示**] 選項設定為 [**所有應用程式]。**

6. 從清單中選取您想要指派使用者的應用程式。

7. 應用程式載入之後，按一下應用程式左側導覽功能表中的 [使用者和群組]****。

8. 按一下 [使用者和群組]**** 清單頂端的 [新增]**** 按鈕，以開啟 [新增指派]**** 窗格。

9. 按一下 [新增指派]**** 窗格中的 [使用者和群組]**** 選取器。

10. 在 [依姓名或電子郵件地址搜尋]**** 搜尋方塊中，輸入您有興趣指派之使用者的**全名**或**電子郵件地址**。

11. 將滑鼠停留在清單中的**使用者**上方，以顯示**核取方塊**。 若要將您的使用者新增至 [已選取]**** 清單，按一下使用者設定檔照片或標誌旁邊的核取方塊。

12. **選擇性︰** 如果您想要**新增多位使用者**，請在 [依姓名或電子郵件地址搜尋]**** 搜尋方塊中，輸入另一個**全名**或**電子郵件地址**，然後按一下核取方塊，將此使用者新增至 [已選取]**** 清單。

13. 當您完成選取使用者時，按一下 [選取]**** 按鈕，將他們新增到要指派至應用程式的使用者和群組清單。

14. **選擇性︰** 按一下 [新增指派]**** 窗格中的 [選取角色]**** 選取器，以選取要指派給您已選取之使用者的角色。

15. 按一下 [**指派**] 按鈕，將應用程式指派給選取的使用者。

稍待片刻，您已選取的使用者便能在存取面板中啟動這些應用程式。

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>如果這些疑難排解步驟無法解決問題。 

使用下列資訊 (若有的話) 開啟支援票證︰

-   相互關聯錯誤 ID

-   UPN (使用者電子郵件地址)

-   TenantID

-   瀏覽器類型

-   錯誤發生期間的時區和時間/時間範圍

-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[使用應用程式 Proxy 提供單一登入應用程式](application-proxy-configure-single-sign-on-with-kcd.md)
