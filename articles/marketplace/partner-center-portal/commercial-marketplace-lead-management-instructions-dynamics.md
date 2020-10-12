---
title: Dynamics 365 Customer Engagement 的潛在客戶管理 - Microsoft 商業市集
description: 了解如何設定 Dynamics 365 Customer Engagement，以管理 Microsoft AppSource 和 Azure Marketplace 的潛在客戶。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 31dba5489db3778d738fc2856cf6aacfd6987711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030747"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>為 Dynamics 365 Customer Engagement 設定潛在客戶管理

本文描述如何設定 Dynamics 365 Customer Engagement (前稱為 Dynamics CRM Online)。 深入了解[設定 Customer Engagement 和 SharePoint Online 的伺服器架構驗證](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)變更，以處理商業市集供應項目中的銷售潛在客戶。

>[!NOTE]
>這些指示特定於適用於 Dynamics 365 Customer Engagement 的 Microsoft 託管雲端環境。 目前不支援直接連線到 Dynamics 內部部署環境。 您還有其他選項可收到潛在客戶，例如設定 [HTTPS 端點](./commercial-marketplace-lead-management-instructions-https.md)或 [Azure 資料表](./commercial-marketplace-lead-management-instructions-azure-table.md)。

## <a name="prerequisites"></a>Prerequisites

需要有下列使用者權限才能完成本文中的步驟：

* Dynamics 365 Customer Engagement 執行個體上的系統管理員權限能夠安裝解決方案。
* 租用戶系統管理員權限可為潛在客戶服務建立新的服務帳戶，以用來從商業市集供應項目傳送潛在客戶。
* 存取系統管理員入口網站。
* Azure 入口網站的存取權。

## <a name="install-the-solution"></a>安裝解決方案

1. 下載 [Microsoft Marketplace 潛在客戶寫入器解決方案](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)，並儲存在電腦。

1. 前往 Dynamics 執行個體的 URL，例如 `https://tenant.crm.dynamics.com`，開啟 Dynamics 365 Customer Engagement。

1. 選取頂端列的齒輪圖示，然後選取 [進階設定]。
 
    ![Dynamics 365 [進階設定] 功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. 在 [設定] 頁面上，開啟頂端列的 [設定] 功能表，然後選取 [解決方案]。

    >[!NOTE]
    >如未看到下圖中的選項，則表示無權繼續作業。 請連絡 Dynamics 365 Customer Engagement 執行個體的系統管理員。

    ![Dynamics 365 [解決方案] 選項](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. 在 [解決方案] 頁面上，選取 [匯入]，然後移至在步驟 1 所下載 **Microsoft Marketplace 潛在客戶寫入器**解決方案的儲存位置。

    ![匯入按鈕](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. 遵循 [匯入解決方案精靈]，以完成解決方案的匯入工作。

## <a name="configure-user-permissions"></a>設定使用者權限

您必須與 Microsoft 共用服務帳戶並設定該帳戶的權限，才能將潛在客戶寫入 Dynamics 365 Customer Engagement 執行個體。

使用下列步驟建立服務帳戶，並指派權限。 您可使用 Azure Active Directory 或 Office 365。

>[!NOTE]
>根據所選的驗證選項，跳至對應的指示。 請參閱 [Azure Active Directory](#azure-active-directory) 或 [Office 365](#office-365)。

### <a name="azure-active-directory"></a>Azure Active Directory

建議使用此選項，因為您永遠不需要更新使用者名稱或密碼即可繼續取得潛在客戶。 若要使用 Azure Active Directory 選項，請提供 Active Directory 應用程式中的應用程式識別碼、應用程式金鑰和目錄識別碼。

為 Dynamics 365 Customer Engagement 設定 Azure Active Directory：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]。

1. 選取 [屬性]，然後複製 [目錄屬性] 頁面上的 [目錄識別碼] 值。 請儲存此值，因為必須在發佈入口網站中提供此值，才能收到市集供應項目的潛在客戶。

    ![[Azure Active Directory 屬性] 功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. 在 Azure Active Directory 的左窗格中選取 [應用程式註冊]，然後選取該頁面的 [新增註冊]。
1. 輸入有意義的應用程式名稱。
1. 在 [支援的帳戶類型] 下，選取 [任何組織目錄中的帳戶]。
1. 在 [重新導向 URI (選擇性)] 下，選取 [Web]，然後輸入 URI，例如 `https://contosoapp1/auth`。 
1. 選取 [註冊]。

    ![註冊應用程式的頁面](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. 現在應用程式已註冊完成，請存取應用程式的概觀頁面。 複製該頁面中的 [應用程式 (用戶端) 識別碼] 值。 請儲存此值，因為必須在發佈入口網站和 Dynamics 365 中提供此值，才能收到市集供應項目的潛在客戶。

    ![[應用程式 (用戶端) 識別碼] 方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. 從應用程式的左窗格中選取 [憑證和祕密]，然後選取 [新增用戶端密碼] 按鈕。 輸入有意義的用戶端密碼描述，然後選取 [到期] 下的 [永不] 選項。 選取 [新增] 以建立用戶端密碼。

    ![[憑證和祕密] 功能表項目](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. 請在成功建立用戶端密碼後，立即複製 [用戶端密碼] 值。 離開此頁面後即無法擷取此值。 請儲存此值，因為必須在發佈入口網站中提供此值，才能收到市集供應項目的潛在客戶。 
1. 從應用程式的左窗格中選取 [API 權限]，然後選取 [+ 新增權限]。
1. 選取 [Microsoft API]，然後選取 [Dynamics CRM] 作為 API。
1. 請務必在 [您應用程式需要何種類型的權限?] 下，選取 [委派的權限]。 
1. 在 [權限] 下，為 [Access Common Data Service as organization users] \(以組織使用者身分存取 Common Data Service\) 選取 [user_impersonation]。 然後選取 [新增權限]。

    ![[新增權限] 按鈕](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. 當在 Azure 入口網站中完成步驟 1 到 14 之後，請前往 Dynamics 365 Customer Engagement 執行個體的 URL，例如 `https://tenant.crm.dynamics.com`。
1. 選取頂端列的齒輪圖示，然後選取 [進階設定]。
1. 在 [設定] 頁面上，開啟頂端列的 [設定] 功能表，然後選取 [安全性]。
1. 在 [安全性] 頁面上，選取 [使用者]。 在 [使用者] 頁面上，選取 [啟用的使用者] 下拉式清單，然後選取 [應用程式使用者]。
1. 選取 [新增] 以建立新的使用者。 

    ![建立新的使用者](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. 在 [新增使用者] 窗格中，確認選取 [USER: APPLICATION USER] \(使用者:應用程式使用者\)。 提供想要搭配此連線使用的使用者其名稱、全名和電子郵件地址。 另在 Azure 入口網站中，貼上在步驟 8 所建立應用程式的**應用程式識別碼**。 選取 [儲存後關閉] 以結束新增使用者。

    ![[新增使用者] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. 移至本文的＜安全性設定＞一節，以完成設定此使用者的連線。

### <a name="office-365"></a>Office 365

如果不想使用 Azure Active Directory，則可在 Microsoft 365 系統管理中心註冊新的使用者。 您必須每 90 天更新一次使用者名稱和密碼，才能繼續獲得潛在客戶。

為 Dynamics 365 Customer Engagement 設定 Office 365：

1. 登入 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。

1. 選取 [新增使用者]。

    ![Microsoft 365 系統管理中心的 [新增使用者] 選項](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 為潛在客戶寫入器服務建立新使用者。 設定下列設定：

    * 輸入使用者名稱。
    * 輸入密碼，並清除 [讓此使用者在第一次登入時變更密碼] 選項。
    * 選取 [使用者 (沒有系統管理員存取權)] 作為使用者的角色。
    * 為產品授權選取 [Dynamics 365 Customer Engagement Plan]，如下列畫面所示。 您將需要支付您所選擇的授權。 

請儲存這些值，因為必須在發佈入口網站中提供 [使用者名稱] 和 [密碼] 值，才能收到市集供應項目的潛在客戶。

![Microsoft 365 系統管理中心的 [新增使用者] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>安全性設定

最後一個步驟是讓所建立的使用者寫入潛在客戶。

1. 前往 Dynamics 執行個體的 URL，例如 `https://tenant.crm.dynamics.com`，以開啟 Dynamics 365 Customer Engagement。
1. 選取頂端列的齒輪圖示，然後選取 [進階設定]。
1. 在 [設定] 頁面上，開啟頂端列的 [設定] 功能表，然後選取 [安全性]。
1. 在 [安全性] 頁面上，選取 [使用者]，然後選取在本文件＜設定使用者權限＞一節中建立的使用者。 然後選取 [管理角色]。 

    ![[管理角色] 索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. 搜尋角色名稱 **Microsoft Marketplace 潛在客戶寫入器**，然後選取此名稱以將角色指派給使用者。

    ![[管理使用者角色] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >此角色由您匯入的解決方案所建立，僅擁有寫入潛在客戶，以及追蹤解決方案版本以確保相容性的權限。

1. 返回 [安全性] 頁面，然後選取 [安全性角色]。 搜尋並選取 **Microsoft Marketplace 潛在客戶寫入器** 角色。

    ![[安全性角色] 窗格](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. 在 [安全性角色] 中，選取 [核心記錄] 索引標籤。搜尋 [使用者實體 UI 設定] 項目。 在每個對應的圓圈中按一次，以啟用該實體的使用者建立、讀取和寫入權限 (1/4 黃圈)。

    ![Microsoft Marketplace 潛在客戶寫入器的 [核心記錄] 索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. 在 [自訂] 索引標籤上，搜尋 [系統作業] 項目。 在每個對應的圓圈中按四次，以啟用該實體的組織讀取、寫入和附加至權限 (實心的綠圈)。

    ![Microsoft Marketplace 潛在客戶寫入器的 [自訂] 索引標籤](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. 選取 [儲存後關閉]。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>設定供應項目以將潛在客戶傳送至 Dynamics 365 Customer Engagement 

在發佈入口網站中設定供應項目的潛在客戶管理資訊：

1. 移至供應項目的 [供應項目設定] 頁面。
1. 在 [潛在客戶] 區段下，選取 [連線]。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="潛在客戶":::

1. 在 [連線詳細資料] 快顯視窗上，選取 [Dynamics 365 Customer Engagement] 作為潛在客戶目的地。

    ![[潛在客戶目的地] 方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. 輸入 Dynamics 365 執行個體的 **URL**，例如 `https://contoso.crm4.dynamics.com`。

1. 選取 Azure Active Directory 或 Office 365 的**驗證**方法。 
1. 如果選取 **Azure Active Directory**，請輸入 [應用程式 (用戶端) 識別碼] (例如 `23456052-aaaa-bbbb-8662-1234df56788f`)、[目錄識別碼] (例如 `12345678-8af1-4asf-1234-12234d01db47`)，以及 [用戶端密碼] (例如 `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`)。

    ![使用選取的 Azure Active Directory 進行驗證](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. 如果選取 **Office 365**，請輸入 [使用者名稱] (例如 `contoso@contoso.onmicrosoft.com`) 和 [密碼] (例如 `P@ssw0rd`)。

    ![Office 365 的 [使用者名稱]方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. 針對 [連絡人電子郵件]輸入在公司中收到新潛在客戶時會收到電子郵件通知的員工其電子郵件地址。 您可輸入多個以分號分隔的電子郵件地址。
1. 選取 [確定]。

為確定已成功連線到潛在客戶目的地，請選取 [驗證] 按鈕。 如果成功，即會在潛在客戶目的地看到測試潛在客戶。

![[連絡人電子郵件] 方塊](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>您必須完成供應項目的其餘設定並加以發佈，才能接收到該供應項目的潛在客戶。
