---
title: 動態 CRM |Azure 應用商店
description: 設定 Dynamics CRM 的潛在客戶管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 524ae203a311d538431205bf8c6498de45aeb4d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280298"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>設定 Dynamics CRM Online 的潛在客戶管理

此文章說明如何設定 Dynamics CRM Online 以處理銷售潛在客戶。

## <a name="prerequisites"></a>Prerequisites

需要有下列使用者權限才能完成此文章中的步驟：
- 您必須是 Dynamics CRM Online 執行個體上的系統管理員，才能安裝解決方案。
- 您必須是租用戶系統管理員，才能為潛在客戶服務建立新的服務帳戶。

<a name="install-the-solution"></a>安裝解決方案
--------------------

1.  下載 [Microsoft Marketplace 潛在客戶寫入器解決方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，並儲存在本機。

2.  開啟 Dynamics CRM Online，並前往 [設定]。
    >[!NOTE]
    >如果在下一個螢幕擷取畫面中沒有看到選項，則表示您沒有您所需的權限。
 
       ![Dynamics 安裝程式檢視](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  選取 [匯入]****，然後選取您在步驟 1 中下載的解決方案。
 
    ![Dynamics 匯入選項](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  完成安裝解決方案。

## <a name="configure-user-permissions"></a>設定使用者權限

要將潛在客戶寫入您的 Dynamics CRM 執行個體，您必須與我們共用服務帳戶，並設定該帳戶的權限。

使用下列步驟建立服務帳戶，並指派權限。 您可以使用 **Azure Active Directory** 或 **Office 365**。

### <a name="azure-active-directory"></a>Azure Active Directory

我們建議使用此選項，因為您可以獲得額外的好處，即永遠不需要更新您的使用者名稱/密碼以取得潛在客戶。 若要使用 Azure Active Directory 選項，請從 Active Directory 應用程式中提供應用程式識別碼、應用程式金鑰和目錄識別碼。

使用下列步驟為 Dynamics CRM 設定 Azure Active Directory。

1.  登入 [Azure 入口網站](https://portal.azure.com/)，然後選取 [Azure Active Directory 服務]。

2.  選擇**屬性**，然後複製**目錄 ID**。這是需要在雲合作夥伴門戶中使用的租戶帳戶標識。

    ![取得目錄識別碼](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  選擇**應用註冊**，然後選擇 **"新建應用程式註冊**"。
4.  輸入應用程式名稱。
5.  針對類型，選取 [Web 應用程式 / API]****。
6.  請提供 URL。 潛在客戶不需要此欄位，但建立應用程式時需要。
7. 選取 [建立]****。
8.  註冊應用程式後，選擇 **"屬性"，** 然後選擇**複製應用程式 ID**。您將在雲合作夥伴門戶中使用此連接資訊。
9.  在 [屬性] 中，將應用程式設定為 [多租用戶]，然後選取 [儲存]****。

10. 選取 [金鑰]**** 並建立新的金鑰，其持續時間設為 [永久有效]**。 選取 [儲存]**** 以建立金鑰。 
11. 在 [金鑰] 功能表中，選取 [複製金鑰值]****。 請儲存此值的複本，因為您需要為 Cloud Partner 入口網站提供此值。
    
    ![Dynamics 取得已註冊金鑰](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. 選取 [必要權限]****，然後選取 [新增]****。 
13. 選取 [Dynamics CRM Online]**** 作為新的 API，並檢查 [以組織使用者身分存取 CRM Online]** 的權限。

14. 在 Dynamics CRM 上，移至 [使用者] 並選取 [已啟用的使用者] 下拉式清單以切換到 [應用程式使用者]****。
    
    ![應用程式使用者](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. 選取 [新增]**** 以建立新的使用者。 選取 [使用者：應用程式使用者]**** 下拉式清單。
    
    ![加入新的應用程式使用者](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. 在 [新使用者]**** 中，提供要與此連線一起使用的名稱和電子郵件。 在 Azure 入口網站中，貼上您所建立之應用程式的**應用程式識別碼**。

     ![設定新的使用者](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. 移至此文章中的「安全性設定」以完成設定此使用者的連線。

### <a name="office-365"></a>Office 365

如果不想使用 Azure 活動目錄，可以在*Microsoft 365 管理中心*註冊新使用者。 您需要每 90 天更新一次使用者名稱/密碼，以繼續獲得潛在客戶。

使用下列步驟為 Dynamics CRM 設定 Office 365。

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。

2. 選擇 **"管理"** 磁貼。

    ![辦公室線上管理員](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. 選取 [新增使用者]****。

    ![新增使用者](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. 為潛在客戶寫入器服務建立新使用者。 設定下列設定：

    -   提供密碼，然後取消核取 [讓這名使用者，第一次登入時變更密碼] 選項。
    -   選取 [使用者 (沒有系統管理員存取權)] 作為使用者的角色。
    -   選取下一個螢幕擷取畫面中所示的產品授權。 您將需要支付您所選擇的授權。 該解決方案也適用於 Dynamics CRM Online 基本授權。
    
    ![設定使用者權限與授權](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>安全性設定

最後一個步驟是讓您所建立的 [使用者] 寫入潛在客戶。

1.  登入 Dynamics CRM Online。
2.  在 [設定]**** 上，選取 [安全性]****。
    
    ![安全性設定](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  在 [使用者權限]**** 中選取您建立的使用者，然後選取 [管理使用者角色]****。 核取 [Microsoft Marketplace 潛在客戶寫入器]**** 以指派角色。

    ![指派使用者角色](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶，以及追蹤解決方案版本以確保相容性的權限。

4.  在 [安全性] 中，選取 [安全性角色]**** 並尋找 Microsoft Marketplace 潛在客戶寫入器的角色。
    
    ![配置安全主管編寫器](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. 選擇 **"核心記錄**"選項卡。為使用者實體 UI 啟用創建/讀取/寫入。

    ![為使用者啟用建立/讀取/寫入](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>總結

通過將產生的帳戶資訊新增至 Cloud Partner 入口網站，完成為潛在客戶管理設定 Dynamics CRM。 例如：

-   **Azure Active Directory** - **應用程式識別碼** (範例：*23456052-aaaa-bbbb-8662-1234df56788f*)、**目錄識別碼** (範例：*12345678-8af1-4asf-1234-12234d01db47*)，以及**應用程式金鑰** (範例：*1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*)。
-   **Office 365** - **Url** (範例：*https://contoso.crm4.dynamics.com*)、**使用者名稱** (範例：*contoso\@contoso.onmicrosoft.com*)，以及**密碼** (範例：*P\@ssw0rd*)。
