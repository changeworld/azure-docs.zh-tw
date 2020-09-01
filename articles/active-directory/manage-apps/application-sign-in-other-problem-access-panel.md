---
title: 針對從 Azure AD 我的應用程式登入應用程式的問題進行疑難排解
description: 針對從 Azure AD 我的應用程式登入應用程式的問題進行疑難排解
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9dd407ce2727582039089c0abe31b68bfe5d0f30
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070321"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>針對從 Azure AD 我的應用程式登入應用程式的問題進行疑難排解

我的應用程式是一個網頁型入口網站，可讓使用者在 Azure Active Directory (Azure AD) 中使用工作或學校帳戶，以查看並啟動 Azure AD 系統管理員已授與他們存取權的雲端式應用程式。 

若要深入瞭解如何使用 Azure AD 作為應用程式的身分識別提供者，請參閱 [Azure AD 中的應用程式管理](what-is-application-management.md)。 若要快速掌握速度，請參閱 [應用程式管理的快速入門系列](view-applications-portal.md)。

在 Azure AD 入口網站中可代表使用者設定這些應用程式。 必須正確設定應用程式，並將其指派給使用者或使用者所屬的群組，才能在我的應用程式中看到應用程式。 

使用者能看見的應用程式類型可分為以下類別：
-   Office 365 應用程式
-   已設定同盟 SSO 的 Microsoft 及第三方應用程式
-   密碼型 SSO 應用程式
-   含現有 SSO 解決方案的應用程式

## <a name="general-issues-to-check-first"></a>首先檢查的一般問題

-   請確定網頁瀏覽器符合需求，請參閱 [我的應用程式支援的瀏覽器](../user-help/my-apps-portal-end-user-access.md)。
-   確定使用者的瀏覽器已將應用程式的 URL 新增至其**信任的網站**。
-   檢查應用程式以確認其**設定**正確。
-   確定使用者的帳戶**已啟用**可供登入。
-   確定使用者的帳戶**未鎖定**。
-   確定使用者的**密碼未過期或忘記**。
-   確定 **Multi-Factor Authentication** 未封鎖使用者存取。
-   確定**條件式存取原則**或**身分識別保護**原則未封鎖使用者存取。
-   確定使用者的**驗證連絡資訊**為最新版本，而可強制執行 Multi-Factor Authentication 或條件式存取原則。
-   也要確定嘗試清除瀏覽器的 Cookie，然後嘗試再次登入。

## <a name="problems-with-the-users-account"></a>使用者帳戶的問題
由於使用者的帳戶發生問題，因此可以封鎖對我的應用程式的存取。 以下是針對使用者和其帳戶設定進行疑難排解的一些方法︰
-   [檢查 Azure Active Directory 中是否存在使用者帳戶](#check-if-a-user-account-exists-in-azure-active-directory)
-   [檢查使用者帳戶的狀態](#check-a-users-account-status)
-   [重設使用者的密碼](#reset-a-users-password)
-   [啟用自助式密碼重設](#enable-self-service-password-reset)
-   [檢查使用者的多重要素驗證狀態](#check-a-users-multi-factor-authentication-status)
-   [檢查使用者的驗證連絡資訊](#check-a-users-authentication-contact-info)
-   [檢查使用者的群組成員資格](#check-a-users-group-memberships)
-   [檢查使用者獲指派的授權](#check-a-users-assigned-licenses)
-   [指派授權給使用者](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>檢查 Azure Active Directory 中是否存在使用者帳戶
若要檢查使用者的帳戶是否存在，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  檢查使用者物件的屬性，確定符合您的預期，沒有遺失資料。

### <a name="check-a-users-account-status"></a>檢查使用者帳戶的狀態
若要檢查使用者的帳戶狀態，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 [ **設定檔**]。
8.  在 [設定]**** 下，確定 [封鎖登入]**** 設為 [否]****。

### <a name="reset-a-users-password"></a>重設使用者的密碼
若要重設使用者的密碼，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 [使用者] 窗格頂端的 [ **重設密碼** ] 按鈕。
8.  在出現的 [**重設密碼**] 窗格上，選取 [**重設密碼**] 按鈕。
9.  為使用者複製**暫時密碼**或**輸入新密碼**。
10. 將這個新的密碼告知使用者，他們下次登入 Azure Active Directory 時必須變更此密碼。

### <a name="enable-self-service-password-reset"></a>啟用自助式密碼重設
若要啟用自助式密碼重設，請遵循下列部署步驟︰
-   [讓使用者重設其 Azure Active Directory 密碼](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)
-   [讓使用者重設或變更其 Active Directory 內部部署密碼](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>檢查使用者的多重要素驗證狀態
若要檢查使用者的多重要素驗證狀態，請遵循下列步驟：
1. 開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2. 選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4. 選取導覽功能表中的 [ **使用者和群組** ]。
5. 選取 [所有使用者]。
6. 選取窗格頂端的 [ **Multi-Factor Authentication** ] 按鈕。
7. Multi-Factor Authentication 系統 **管理入口網站** 載入之後，請確定您位於 [ **使用者** ] 索引標籤上。
8. 在使用者清單中搜尋、篩選或排序來尋找使用者。
9. 從使用者清單中選取使用者，然後視需要 [啟用]****、[停用]**** 或 [強制執行]**** 多重要素驗證。
   >[!NOTE]
   >如果使用者處於 [已強制]**** 狀態，您可以暫時將他們設為 [已停用]****，讓他們回到各自的帳戶。 使用者退回之後，您可以再次將他們的狀態變更為 [已啟用]****，以要求他們在下次登入時重新註冊連絡資訊。 或者，您可以依照[檢查使用者的驗證連絡資訊](#check-a-users-authentication-contact-info)中的步驟，為他們確認或設定此資料。

### <a name="check-a-users-authentication-contact-info"></a>檢查使用者的驗證連絡資訊
若要檢查用於多重要素驗證、條件式存取、身分識別保護和密碼重設的使用者驗證連絡資訊，請遵循下列步驟︰
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 [ **設定檔**]。
8.  向下捲動至 [驗證連絡資訊]****。
9.  **檢閱**使用者註冊的資料，並視需要予以更新。

### <a name="check-a-users-group-memberships"></a>檢查使用者的群組成員資格
若要檢查使用者的群組成員資格，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 **群組** 以查看使用者為其成員的群組。

### <a name="check-a-users-assigned-licenses"></a>檢查使用者獲指派的授權
若要檢查使用者獲指派的授權，請遵循下列步驟：
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 **授權** 以查看使用者目前已指派的授權。

### <a name="assign-a-user-a-license"></a>指派授權至使用者 
若要指派授權至使用者，請依遵循下列步驟︰
1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。
2.  選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3.  在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4.  選取導覽功能表中的 [ **使用者和群組** ]。
5.  選取 [所有使用者]。
6.  **搜尋** 您感興趣的使用者，然後選取要選取 **的資料列** 。
7.  選取 **授權** 以查看使用者目前已指派的授權。
8.  選取 [ **指派** ] 按鈕。
9.  從可用產品清單中選取**一或多個產品**。
10. **選擇性** 地選取 [ **指派選項** ] 專案，以更精確地指派產品。 選取 [確定]  。
11. 選取 [ **指派** ] 按鈕，將這些授權指派給此使用者。

## <a name="troubleshooting-deep-links"></a>疑難排解深層連結
深層連結或使用者存取 URL 是一種連結，您的使用者可使用此種連結直接從其瀏覽器網址列存取其密碼 SSO 應用程式。 藉由流覽至此連結，使用者會自動登入應用程式，而不需要先移至我的應用程式。 此連結與使用者用來從 Office 365 應用程式啟動器存取這些應用程式的相同。

### <a name="checking-the-deep-link"></a>檢查深層連結

若要檢查您是否有正確的深層連結，請遵循下列步驟：
1. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以 **全域系統管理員** 或共同管理員身分登入 **。**
2. 選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
4. 從 Azure Active Directory 左側導覽功能表中選取 [ **企業應用程式** ]。
5. 選取 [ **所有應用程式** ] 以查看您所有應用程式的清單。
   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。
6. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以 **全域系統管理員** 或共同管理員身分登入 **。**
7. 選取主左側導覽功能表頂端的 [**所有服務**]，以開啟**Azure Active Directory 擴充**功能。
8. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。
9. 從 Azure Active Directory 左側導覽功能表中選取 [ **企業應用程式** ]。
10. 選取 [ **所有應用程式** ] 以查看您所有應用程式的清單。
    * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。
11. 選取您要檢查深層連結的應用程式。
12. 尋找 [使用者存取 URL]**** 標籤。 您的深層連結應符合此 URL。

## <a name="contact-support"></a>請連絡支援人員
開啟支援票證，並提供下列資訊（如果有的話）：
-   相互關聯錯誤 ID
-   UPN (使用者電子郵件地址)
-   TenantID
-   瀏覽器類型
-   錯誤發生期間的時區和時間/時間範圍
-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
- [應用程式管理快速入門系列](view-applications-portal.md)
