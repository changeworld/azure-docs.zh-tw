---
title: Azure 活動目錄報告 API 的先決條件 |微軟文件
description: 了解存取 Azure AD 報告 API 的必要條件
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991257"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>存取 Azure Active Directory 報告 API 的必要條件

[Azure Active Directory (Azure AD) 報告 API](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) 透過一組以 REST 為基礎的 API 為您提供資料的程式設計方式存取。 可以從程式設計語言和工具呼叫這些 API。

報告 API 會使用 [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) 授權存取 Web API。

若要準備存取報告 API，您必須︰

1. [指派角色](#assign-roles)
2. [授權要求](#license-requirements)
3. [註冊應用程式](#register-an-application)
4. [授予權限](#grant-permissions)
5. [收集組態設定](#gather-configuration-settings)

## <a name="assign-roles"></a>指派角色

若要透過 API 來存取報告資料，您必須已具備下列其中一個指派的角色︰

- 安全性讀取者

- 安全性系統管理員

- 全域管理員

## <a name="license-requirements"></a>授權要求

為了造訪租戶的登錄報告,Azure AD 租戶必須具有關聯的 Azure AD 高級許可證。 Azure AD AdadP1(或以上)許可證需要存取任何 Azure AD 租戶的登入報告。 或者,如果目錄類型是 Azure AD B2C,則登錄報告可通過 API 訪問,而無需任何其他許可證要求。 


## <a name="register-an-application"></a>註冊應用程式

即使您使用腳本訪問報告 API,也需要註冊。 註冊為您提供**一個應用程式 ID**,這是授權調用所必需的,並且使代碼能夠接收權杖。

若要設定您的目錄以存取 Azure AD 報告 API，您必須以 Azure 管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)，而且該帳戶同時也是 Azure AD 租用戶**全域管理員**目錄角色的成員。

> [!IMPORTANT]
> 在具有管理員權限的認證之下執行的應用程式會非常強大，請務必將應用程式的識別碼和祕密認證保存在安全的位置。
> 

**若要註冊 Azure AD 應用程式：**

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格中，選取 [Azure Active Directory]****。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在 [Azure Active Directory]**** 頁面中，選取 [應用程式註冊]****。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 在 **"應用註冊"頁中**,選擇 **"新建註冊**"。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **註冊應用程式**頁面:

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在 [名稱]**** 文字方塊中，輸入 `Reporting API application`。

    b. 對於**受支援的帳戶類型**,選擇**僅在此組織中的帳戶**。

    c. 在**重定向網址**中選擇`https://localhost`**Web**文字框 ,鍵入 。

    d. 選取 [註冊]  。 


## <a name="grant-permissions"></a>授與權限 

根據您想要存取的 API，您需要授與應用程式下列權限：  

| API | 權限 |
| --- | --- |
| Windows Azure Active Directory | 讀取目錄資料 |
| Microsoft Graph | 讀取所有稽核記錄資料 |


![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/36.png)

下節列出適用於這兩個 API 的步驟。 如果您不想存取其中一個 API，可以跳過相關步驟。

**若要對應用程式授與 API 使用權限：**


1. 選擇**API 權限**,然後**新增權限**。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. 在**要求 API 權限頁上**,找到**支援舊 API** Azure**的目錄圖**。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. 在 **「必需權限」** 頁上,**選擇「應用程式權限**」,展開**目錄**複選框**目錄。**  選取 [新增權限]****。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. 在**報告 API 應用程式 - API 許可權**頁上,選擇 **「授予管理員同意**」。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 注意:**預設情況下**在 API 註冊期間添加 Microsoft 圖形。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>收集組態設定 

本節說明如何從您的目錄取得下列設定︰

- 網域名稱
- 用戶端識別碼
- 用戶端密碼

您在設定報告 API 的呼叫時需要這些值。 

### <a name="get-your-domain-name"></a>取得您的網域名稱

**若要取得網域名稱：**

1. 在 [Azure 入口網站](https://portal.azure.com) 的左導覽窗格上，選取 [Azure Active Directory]****。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在 [Azure Active Directory]**** 頁面中，選取 [自訂網域名稱]****。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 從網域清單中複製網域名稱。


### <a name="get-your-applications-client-id"></a>取得應用程式的用戶端識別碼

**若要取得應用程式的用戶端識別碼：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 從 [應用程式註冊]**** 頁面中選取您的應用程式。

3. 從應用程式頁面，瀏覽至 [應用程式識別碼]**** 並選取 [按一下以複製]****。

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>取得應用程式的用戶端祕密
 避免嘗試使用 API 訪問審核日誌或登錄的錯誤。

**若要取得應用程式的用戶端祕密：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，按一下 [Azure Active Directory]****。
   
    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  從 [應用程式註冊]**** 頁面中選取您的應用程式。

3.  在**API 應用程式**頁面上選擇**證書和機密**,在 **「用戶端機密**」部分中,按下 **「新用戶端機密**」。 

    ![註冊應用程式](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. 在「**新增用戶端機密**」 頁上,新增:

    a. 在 [描述]**** 文字方塊中，輸入 `Reporting API`。

    b. 選取 [2 年後]**** 來作為 [到期]****。

    c. 按一下 [檔案]  。

    d. 複製金鑰值。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>針對報告 API 中的錯誤進行疑難排解

本節列出了在使用 Microsoft 圖形 API 訪問活動報告時可能會遇到的常見錯誤消息及其解決方法的步驟。

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>錯誤:無法從 Microsoft 圖形取得使用者角色

 使用圖形資源管理員 UI 中的兩個登入按鈕登錄您的帳戶,以避免在嘗試使用圖形資源管理器登錄時出錯。 

![Graph 總管](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>錯誤:無法從 Microsoft 圖形執行進階授權檢查 

如果您在嘗試使用 Graph 總管存取登入時，遇到這個錯誤訊息，請在您帳戶左側導覽列底下選擇 [修改權限]****，然後選取 [Tasks.ReadWrite]**** 和 [Directory.Read.All]****。 

![修改權限 UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>錯誤:租戶不是 B2C,或者租戶沒有高級許可證

存取登入報表需要 Azure Active Directory 進階 1 (P1) 授權。 如果您在存取登入時看到這個錯誤訊息，請確定您的租用戶已獲得 Azure AD P1 授權。

### <a name="error-the-allowed-roles-does-not-include-user"></a>錯誤:允許的角色不包括使用者。 

 避免嘗試使用 API 訪問審核日誌或登錄的錯誤。 確保帳戶是 Azure 活動目錄租戶中**的安全讀取器**或**報表讀取器**角色的一部分。

### <a name="error-application-missing-aad-read-directory-data-permission"></a>錯誤：應用程式遺漏 AAD「讀取目錄資料」權限 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>錯誤... 應用程式缺少 Microsoft 圖形 API 讀取所有稽核紀錄資料"許可權

依先決條件中的步驟[訪問 Azure 活動目錄報告 API,](howto-configure-prerequisites-for-reporting-api.md)以確保應用程式使用正確的許可權集運行。 

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory 報告 API 搭配憑證來取得資料](tutorial-access-api-with-certificates.md)
* [稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
