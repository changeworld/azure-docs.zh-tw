---
title: 如何為 Azure AD 應用配置密碼單一登入 |微軟文檔
description: 如何在 Microsoft 標識平臺 （Azure AD） 中配置密碼單一登入 （SSO） 到 Azure AD 企業應用程式
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063521"
---
# <a name="configure-password-single-sign-on"></a>設定密碼單一登入

將[庫應用](add-gallery-app.md)[或非庫 Web 應用](add-non-gallery-app.md)添加到 Azure AD 企業應用程式時，可用的單一登入選項之一是基於[密碼的單一登入](what-is-single-sign-on.md#password-based-sso)。 此選項可用於任何具有 HTML 登錄頁的 Web。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 對於多個使用者需要共用單個帳戶（例如，到組織的社交媒體應用帳戶）的方案，這非常有用。 

基於密碼的 SSO 是快速開始將應用程式集成到 Azure AD 中的絕佳方式，並允許您：

-   針對已經與 Azure AD 整合的應用程式，安全地儲存和重播使用者名稱和密碼，以啟用**使用者的單一登入**

-   針對需要使用者名稱和密碼以外的更多欄位才能登入的應用程式，**支援需要多個登入欄位的應用程式**

-   針對使用者在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)輸入認證時所看到的使用者名稱和密碼輸入欄位，**自訂標籤**

-   針對**使用者**在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)手動輸入的任何現有應用程式帳戶，允許他們提供自己的使用者名稱和密碼

-   允許**商務群組的成員**使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能，指定要指派給使用者的使用者名稱和密碼

-   允許**管理員**使用"更新憑據"功能指定個人或組在登錄應用程式時使用的使用者名和密碼 

## <a name="before-you-begin"></a>開始之前

如果尚未將應用程式新增至您的 Azure AD 租用戶，請參閱[新增資源庫應用程式](add-gallery-app.md)或[新增非資源庫應用程式](add-non-gallery-app.md)。

## <a name="open-the-app-and-select-password-single-sign-on"></a>打開應用並選擇密碼單一登入

1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 導航到**Azure 活動目錄** > **企業應用程式**。 Azure AD 租用戶中應用程式的隨機樣本隨即出現。 

3. 在 [應用程式類型]**** 功能表中，選取 [所有應用程式]****，然後選取 [套用]****。

4. 在搜尋方塊中輸入應用程式的名稱，然後從結果中選取應用程式。

5. 在 [管理]**** 區段中，選取 [單一登入]****。 

6. 選擇**基於密碼**的 。

7. 輸入應用程式的基於 Web 的登錄頁的 URL。 此字串必須是包含使用者名輸入欄位的頁面。

   ![密碼單一登入](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. 選取 [儲存]****。 Azure AD 嘗試分析使用者名輸入和密碼輸入的登錄頁。 如果嘗試成功，您就完成了。 
 
> [!NOTE]
> 下一步是[將使用者或組分配給應用程式](methods-for-assigning-users-and-groups.md)。 分配使用者和組後，可以提供在使用者登錄到應用程式時代表使用者使用的憑據。 選擇 **"使用者和組**"，選擇使用者或組行的核取方塊，然後按一下"**更新憑據**"。 然後，輸入代表使用者或組使用的使用者名和密碼。 否則，系統將提示使用者在啟動時自己輸入憑據。
 

## <a name="manual-configuration"></a>手動設定

如果 Azure AD 的解析嘗試失敗，您可以手動設定登錄。

1. 在**\<"配置>"下**，**選擇\<"配置應用程式名稱>密碼單一登入設置**"以顯示 **"配置登錄"** 頁。 

2. 選擇 **"手動檢測登錄欄位**"。 將顯示描述手動檢測登錄欄位的其他說明。

   ![手動設定基於密碼的單一登入](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 選擇 **"捕獲登錄欄位**"。 捕獲狀態頁將在新選項卡中打開，顯示消息**中繼資料捕獲當前正在進行**。

4. 如果 **"訪問面板擴展必需"** 框出現在新選項卡中，請選擇"**立即安裝**"以安裝 **"我的應用安全登錄擴展"瀏覽器延伸**。 （瀏覽器延伸需要微軟邊緣、Chrome 或 Firefox。然後安裝、啟動和啟用擴展，然後刷新捕獲狀態頁。

   然後，瀏覽器延伸將打開另一個顯示輸入 URL 的選項卡。
5. 在包含輸入 URL 的選項卡中，完成登錄過程。 填寫使用者名和密碼欄位，並嘗試登錄。 （您不必提供正確的密碼。

   提示要求您保存捕獲的登錄欄位。
6. 選取 [確定]****。 瀏覽器延伸更新捕獲狀態頁與消息**中繼資料已更新的應用程式**。 瀏覽器選項卡關閉。

7. 在 Azure AD**配置登錄頁上**，選擇 **"確定"，我能夠成功登錄到應用**。

8. 選取 [確定]****。

捕獲登錄頁後，您可以分配使用者和組，也可以設置憑據策略，就像常規[密碼 SSO 應用程式](what-is-single-sign-on.md)一樣。

> [!NOTE]
> 您可以在應用程式的 [設定]**** 索引標籤上使用 [上傳標誌]**** 按鈕，來上傳應用程式的圖格標誌。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [配置自動使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
