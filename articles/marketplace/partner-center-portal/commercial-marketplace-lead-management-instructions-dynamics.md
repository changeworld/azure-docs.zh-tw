---
title: Dynamics 365 Customer Engagement 的潛在客戶管理 |Azure Marketplace
description: 為 Dynamics 365 Customer Engagement 設定潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: b9158f7b2e3fc73a2fe2a9b20ead2558b7467f6f
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131046"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>為 Dynamics 365 Customer Engagement 設定潛在客戶管理

本文說明如何設定 Dynamics 365 Customer Engagement （先前名為 Dynamics CRM Online）。 深入瞭解[使用 Customer Engagement 和 SharePoint Online 設定以伺服器為基礎的驗證](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)中的變更，以處理來自您的商業 Marketplace 供應專案的銷售潛在客戶。

>[!NOTE]
>這些指示專屬於適用于 Dynamics 365 Customer Engagement 的 Microsoft 託管雲端環境。 目前不支援直接連接到 Dynamics 內部部署環境。 還有其他選項可讓您接收潛在客戶，例如設定[HTTPS 端點](./commercial-marketplace-lead-management-instructions-https.md)或[Azure 資料表](./commercial-marketplace-lead-management-instructions-azure-table.md)。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，需要下列使用者權限。 您必須：

* 請是 Dynamics 365 Customer Engagement 實例上的系統管理員，才能安裝解決方案並遵循這些指示。
* 成為租使用者系統管理員，為用來從商業 marketplace 供應專案傳送潛在客戶的潛在客戶服務建立新的服務帳戶。
* 具有 Office 365 管理員入口網站的存取權。
* 具有 Azure 入口網站的存取權。

## <a name="install-the-solution"></a>安裝解決方案

1. 下載[Microsoft Marketplace 潛在客戶撰寫者解決方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，並將它儲存在本機電腦上。

1. 前往 Dynamics 實例的 URL （例如），以開啟 Dynamics 365 Customer Engagement `https://tenant.crm.dynamics.com`。

1. 選取頂端列的齒輪圖示，然後選取 [ **Advanced Settings**]。
 
    ![Dynamics 365 Advanced Settings 功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. 在 [**設定**] 頁面上，開啟頂端列的 [**設定**] 功能表，然後選取 [**方案**]。

    >[!NOTE]
    >如果您在下列畫面中看不到選項，表示您沒有需要繼續進行的許可權。 請洽詢 Dynamics 365 Customer Engagement 實例上的系統管理員。

    ![Dynamics 365 解決方案選項](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. 在 [**方案**] 頁面上，選取 [匯**入**]，然後移至您在步驟1中所下載**Microsoft Marketplace 潛在客戶寫入器**解決方案的儲存位置。

    ![匯入按鈕](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 遵循 [匯入解決方案嚮導]，完成匯入解決方案的工作。

## <a name="configure-user-permissions"></a>設定使用者權限

若要將潛在客戶寫入 Dynamics 365 Customer Engagement 實例，您必須與 Microsoft 共用服務帳戶，並設定帳戶的許可權。

使用下列步驟建立服務帳戶，並指派權限。 您可以使用 Azure Active Directory 或 Office 365。

>[!NOTE]
>根據您選取的驗證選項，跳至對應的指示。 請參閱[Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)或[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

我們建議這個選項，因為您永遠不需要更新您的使用者名稱或密碼，就能繼續獲得潛在客戶。 若要使用 [Azure Active Directory] 選項，您可以從 Active Directory 應用程式提供應用程式識別碼、應用程式金鑰和目錄識別碼。

若要設定 Dynamics 365 Customer Engagement 的 Azure Active Directory：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]****。

1. 選取 [**屬性**]，然後複製 [**目錄屬性**] 頁面上的 [**目錄識別碼**] 值。 請儲存此值，因為您必須在發佈入口網站中提供它，才能接收 marketplace 供應專案的潛在客戶。

    ![Azure Active Directory 屬性功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. 從 Azure Active Directory 左窗格中選取 [**應用程式註冊**]，然後在該頁面上選取 [**新增註冊**]。
1. 為應用程式名稱輸入有意義的名稱。
1. 在 [**支援的帳戶類型**] 底下，選取 [**任何組織目錄中的帳戶**]。
1. 在 **[重新導向 URI （選用）**] 底下，選取 [ **Web** ] 並`https://contosoapp1/auth`輸入 URI，例如。 
1. 選取 [註冊]  。

    ![註冊應用程式頁面](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 現在，您的應用程式已註冊，請存取應用程式的 [總覽] 頁面。 複製該頁面上的 [**應用程式（用戶端）識別碼**] 值。 請儲存此值，因為您必須在發佈入口網站和 Dynamics 365 中提供它，才能接收 marketplace 供應專案的潛在客戶。

    ![[應用程式（用戶端）識別碼] 方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 從應用程式的左窗格中選取 [**憑證 & 秘密**]，然後選取 [**新增用戶端密碼**] 按鈕。 為用戶端密碼輸入有意義的描述，然後選取 [**到期**] 底下的 [**永不**] 選項。 選取 [**新增**] 以建立用戶端密碼。

    ![憑證 & 秘密功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 一旦成功建立用戶端密碼，請複製**用戶端密碼**值。 離開頁面之後，您將無法取得此值。 請儲存此值，因為您必須在發佈入口網站中提供它，才能接收 marketplace 供應專案的潛在客戶。 
1. 從應用程式的左窗格中選取 [ **API 許可權**]，然後選取 [ **+ 新增許可權**]。
1. 選取 [ **Microsoft api**]，然後選取 [ **Dynamics CRM** ] 作為 API。
1. 在 [**您的應用程式需要何種類型的許可權？**] 下，確定已選取 [**委派的許可權**]。 
1. 在 [**許可權**] 底下，選取 [**以組織使用者身分存取 Common Data Service**的 [ **user_impersonation** ] 核取方塊。 然後選取 [新增權限]****。

    ![[新增許可權] 按鈕](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. 當您完成 Azure 入口網站中的步驟1到14之後，請前往 URL （例如），移至您的 Dynamics 365 Customer Engagement `https://tenant.crm.dynamics.com`實例。
1. 選取頂端列的齒輪圖示，然後選取 [ **Advanced Settings**]。
1. 在 [**設定**] 頁面上，開啟頂端列的 [**設定**] 功能表，然後選取 [**安全性**]。
1. 在 [**安全性**] 頁面上，選取 [**使用者**]。 在 [**使用者**] 頁面上，選取 [**啟用的使用者**] 下拉式選單，然後選取 [**應用程式使用者**]。
1. 選取 [新增]**** 以建立新的使用者。 

    ![建立新的使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. 在 [**新增使用者**] 窗格中，確認已選取 [**使用者：應用程式使用者**]。 針對您要與此連線搭配使用的使用者，提供使用者名稱、全名和電子郵件地址。 此外，請貼上您在步驟8的 Azure 入口網站中所建立應用程式的**應用程式識別碼**。 選取 [**儲存] & [關閉**] 以完成新增使用者。

    ![新增使用者窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 請移至本文中的「安全性設定」一節，以完成設定此使用者的連線。

### <a name="office-365"></a>Office 365

如果您不想要使用 Azure Active Directory，您可以在 Microsoft 365 系統管理中心註冊新的使用者。 您每隔90天就需要更新您的使用者名稱和密碼，才能繼續獲得潛在客戶。

若要設定 Office 365 以進行 Dynamics 365 Customer Engagement：

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。

1. 選取 [新增使用者]****。

    ![Microsoft 365 系統管理中心新增使用者選項](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 為潛在客戶寫入器服務建立新使用者。 設定下列設定：

    * 輸入使用者名稱。
    * 輸入密碼，並清除 [**讓此使用者在第一次登入時變更密碼**] 選項。
    * 選取 [**使用者（無系統管理員存取權）** ] 做為使用者的角色。
    * 選取 [ **Dynamics 365 Customer Engagement 方案**] 作為 [產品授權]，如下列畫面所示。 您將需要支付您所選擇的授權。 

儲存這些值，因為您必須在發佈入口網站中提供使用者**名稱**和**密碼**值，才能接收 marketplace 供應專案的潛在客戶。

![Microsoft 365 管理中心的 [新增使用者] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全性設定

最後一個步驟是讓您建立的使用者能夠撰寫潛在客戶。

1. 前往 Dynamics 實例的 URL （例如），以開啟 Dynamics 365 Customer Engagement `https://tenant.crm.dynamics.com`。
1. 選取頂端列的齒輪圖示，然後選取 [ **Advanced Settings**]。
1. 在 [**設定**] 頁面上，開啟頂端列的 [**設定**] 功能表，然後選取 [**安全性**]。
1. 在 [**安全性**] 頁面上，選取 [**使用者**]，然後選取您在本檔的「設定使用者權限」一節中建立的使用者。 然後選取 [**管理角色**]。 

    ![[管理角色] 索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 搜尋 [角色名稱] **Microsoft Marketplace [潛在客戶作者**]，然後選取它來指派角色給使用者。

    ![[管理使用者角色] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶，以及追蹤解決方案版本以確保相容性的權限。

1. 返回 [**安全性**] 頁面，然後選取 [**安全性角色**]。 搜尋角色**Microsoft Marketplace 潛在客戶作者**，並加以選取。

    ![[安全性角色] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 在 [安全性] 角色中，選取 [**核心記錄**] 索引標籤。搜尋 [**使用者實體 UI 設定**] 專案。 針對該實體啟用 [建立]、[讀取] 和 [寫入] 1/4 許可權，方法是在每個對應的圓形中按一下 [一次]。

    ![Microsoft Marketplace 潛在客戶寫入器核心記錄索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. 在 [**自訂**] 索引標籤上，搜尋**系統作業**專案。 針對該實體啟用 [讀取]、[寫入] 和 [AppendTo] 許可權，其方式是在每個對應的圓形中按一下四次。

    ![Microsoft Marketplace 潛在客戶寫入器自訂索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. 選取 [**儲存並關閉**]。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>設定您的供應專案以將潛在客戶傳送至 Dynamics 365 Customer Engagement 

若要在發佈入口網站中為您的供應專案設定潛在客戶管理資訊：

1. 移至供應專案的**供應專案設定**頁面。
1. 選取 [**潛在客戶管理**] 區段下的 **[連接]** 。

    ![潛在客戶管理區段 [連線] 按鈕](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. 在 [連線詳細資料] 快顯視窗中，針對 [潛在客戶目的地] 選取 [ **Dynamics 365 Customer Engagement** ]。

    ![潛在客戶目的地方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. 輸入 Dynamics 365 實例的**URL** ，例如`https://contoso.crm4.dynamics.com`。

1. 選取**驗證**方法，Azure Active Directory 或 Office 365。 
1. 如果您選取 [ **Azure Active Directory**]，請輸入**應用程式（用戶端）識別碼**（例如`23456052-aaaa-bbbb-8662-1234df56788f`）、**目錄識別碼**（例如`12345678-8af1-4asf-1234-12234d01db47`）和**用戶端密碼**（例如`1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`）。

    ![已選取 Azure Active Directory 的驗證](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. 如果您選取**了 Office 365**，請輸入**使用者名稱**（ `contoso@contoso.onmicrosoft.com`例如）和**密碼**（例如`P@ssw0rd`）。

    ![Office 365 使用者名稱方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. 針對 [**連絡人電子郵件**]，輸入公司中的人員電子郵件地址，當收到新的潛在客戶時，應該會收到電子郵件通知。 您可以輸入多個電子郵件地址，並以分號分隔。
1. 選取 [確定]  。

若要確定您已成功連線到潛在客戶目的地，請選取 [**驗證**] 按鈕。 如果成功，您會在潛在客戶目的地中擁有測試潛在客戶。

![連絡人電子郵件箱](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。
