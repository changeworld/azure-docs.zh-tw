---
title: 教學課程：在 Azure Active Directory 中設定 SAP SuccessFactors 回寫 | Microsoft Docs
description: 了解如何設定從 Azure AD 到 SAP SuccessFactors 的屬性回寫
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: d39e00a80ab167936a749c73867b4343e6ed9d76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006433"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>教學課程：設定從 Azure AD 到 SAP SuccessFactors 的屬性回寫
本教學課程旨在說明將屬性從 Azure AD 回寫到 SAP SuccessFactors 員工中心的步驟。 

## <a name="overview"></a>概觀

您可以設定 SAP SuccessFactors Writeback 應用程式，將特定屬性從 Azure Active Directory 寫入 SAP SuccessFactors 員工中心。 SuccessFactors Writeback 佈建應用程式支援將值指派給下列員工中心屬性：

* 工作電子郵件
* 使用者名稱
* 公司電話號碼 (包括國碼 (地區碼)、區碼、號碼和分機)
* 公司電話號碼主要旗標
* 行動電話號碼 (包括國碼 (地區碼)、區碼、號碼)
* 行動電話主要旗標 
* 使用者 custom01-custom15 屬性
* loginMethod 屬性

> [!NOTE]
> 此應用程式與 SuccessFactors 的輸入使用者佈建整合應用程式沒有任何相依性。 您可以將其設定為獨立於 [SuccessFactors 至內部部署 AD](sap-successfactors-inbound-provisioning-tutorial.md) 佈建應用程式或 [SuccessFactors 至 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 佈建應用程式。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此 SuccessFactors Writeback 使用者佈建解決方案最適合下列對象：

* 使用 Microsoft 365 的組織，想要將由 IT 管理的授權屬性 (例如電子郵件地址、電話、使用者名稱) 回寫到 SuccessFactors 員工中心。

## <a name="configuring-successfactors-for-the-integration"></a>設定用於整合的 SuccessFactors

所有 SuccessFactors 佈建連接器都需要具有適當權限的 SuccessFactors 帳戶認證，才能叫用員工中心 OData API。 本節說明在 SuccessFactors 中建立服務帳戶，並授與適當權限的步驟。 

* [在 SuccessFactors 中建立/識別 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [建立 API 權限角色](#create-an-api-permissions-role)
* [為 API 使用者建立權限群組](#create-a-permission-group-for-the-api-user)
* [將權限角色授與權限群組](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中建立/識別 API 使用者帳戶
請與您的 SuccessFactors 系統管理員小組或實作夥伴合作，在 SuccessFactors 中建立或識別使用者帳戶，以用於叫用 OData API。 在 Azure AD 中設定佈建應用程式時，需要用到此帳戶的使用者名稱和密碼認證。 

### <a name="create-an-api-permissions-role"></a>建立 API 權限角色

1. 使用可存取系統管理中心的使用者帳戶登入 SAP SuccessFactors。
1. 搜尋 [管理權限角色]，然後從搜尋結果中選取 [管理權限角色]。

   ![管理權限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. 從 [權限角色] 清單中，按一下 [新建]。

   > [!div class="mx-imgBorder"]
   > ![建立新的權限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 為新的權限角色新增 **角色名稱** 和 **描述**。 名稱和描述應該指出這是針對 API 使用權限設定的角色。

   > [!div class="mx-imgBorder"]
   > ![權限角色詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. 在 [權限設定] 底下，按一下 [權限...]，然後向下捲動權限清單，再按一下 [管理整合工具]。 核取 **允許系統管理員透過基本驗證存取 OData API** 方塊。

   > [!div class="mx-imgBorder"]
   > ![管理整合工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 在相同的方塊中向下捲動，然後選取 [員工中心 API]。 如下所示，新增使用 ODATA API 讀取和使用 ODATA API 編輯的權限。 如果您打算在回寫到 SuccessFactors 的案例中使用相同帳戶，請選取編輯選項。 

   > [!div class="mx-imgBorder"]
   > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. 按一下 [完成]。 按一下 **[儲存變更]** 。

### <a name="create-a-permission-group-for-the-api-user"></a>為 API 使用者建立權限群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [管理權限群組]，然後從搜尋結果中選取 [管理權限群組]。

   > [!div class="mx-imgBorder"]
   > ![管理權限群組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. 從 [管理權限群組] 視窗中，按一下 [新建]。

   > [!div class="mx-imgBorder"]
   > ![Add new group](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 為新群組新增群組名稱。 群組名稱應指出這是 API 使用者的群組。

   > [!div class="mx-imgBorder"]
   > ![權限群組名稱](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. 將成員新增至群組。 例如，您可以從 [人員集區] 下拉式功能表中選取 [使用者名稱]，然後輸入將用於整合的 API 帳戶使用者名稱。 

   > [!div class="mx-imgBorder"]
   > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. 按一下 [完成] 即可完成建立權限群組。

### <a name="grant-permission-role-to-the-permission-group"></a>將權限角色授與權限群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [管理權限角色]，然後從搜尋結果中選取 [管理權限角色]。
1. 從 **權限角色清單** 中，選取您為 API 使用權限建立的角色。
1. 在 [將此角色授與...] 底下，按一下 [新增...] 按鈕。
1. 從下拉式功能表中選取 [權限群組...]，然後按一下 [選取...] 來開啟 [群組] 視窗，搜尋並選取上方建立的群組。 

   > [!div class="mx-imgBorder"]
   > ![新增權限群組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. 檢閱授與權限群組的權限角色。 
   > [!div class="mx-imgBorder"]
   > ![權限角色和群組詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. 按一下 **[儲存變更]** 。

## <a name="preparing-for-successfactors-writeback"></a>準備 SuccessFactors Writeback

SuccessFactors Writeback 佈建應用程式會使用特定「代碼」值來設定員工中心內的電子郵件和電話號碼。 這些「代碼」值會設定為屬性對應資料表中的常數值，而且每個 SuccessFactors 執行個體都有不同的值。 本節提供用來擷取這些「代碼」值的步驟。

   > [!NOTE]
   > 請洽詢您的 SuccessFactors 系統管理員，以完成本節中的步驟。 

### <a name="identify-email-and-phone-number-picklist-names"></a>識別電子郵件和電話號碼的挑選清單名稱 

在 SAP SuccessFactors 中，「挑選清單」是一組可設定的選項，使用者可以從中進行選取。 不同類型的電子郵件和電話號碼 (例如公司、個人、其他) 都會使用挑選清單來表示。 在此步驟中，我們將識別 SuccessFactors 租用戶中設定的挑選清單，以儲存電子郵件和電話號碼值。 
 
1. 在 SuccessFactors 系統管理中心內，搜尋 [管理業務設定]。 

   > [!div class="mx-imgBorder"]
   > ![管理業務設定](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. 在 [HRIS 元素] 底下，選取 [emailInfo]，然後針對 [email-type] 欄位按一下 [詳細資料]。

   > [!div class="mx-imgBorder"]
   > ![取得電子郵件資訊](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. 在 [email-type] 詳細資料頁面上，記下與此欄位相關聯的挑選清單名稱。 根據預設，此名稱是 **ecEmailType**。 不過，您租用戶中的名稱可能會不同。 

   > [!div class="mx-imgBorder"]
   > ![識別電子郵件挑選清單](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. 在 [HRIS 元素] 底下，選取 [phoneInfo]，然後針對 [phone-type] 欄位按一下 [詳細資料]。

   > [!div class="mx-imgBorder"]
   > ![取得電話資訊](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. 在 [phone-type] 詳細資料頁面上，記下與此欄位相關聯的挑選清單名稱。 根據預設，此名稱是 **ecPhoneType**。 不過，您租用戶中的名稱可能會不同。 

   > [!div class="mx-imgBorder"]
   > ![識別電話挑選清單](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>取得 emailType 的常數值

1. 在 SuccessFactors 系統管理中心內，搜尋並開啟 [挑選清單中心]。 
1. 使用上一節所擷取的電子郵件挑選清單名稱 (例如 ecEmailType) 來尋找電子郵件挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![尋找電子郵件類型挑選清單](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. 開啟使用中的電子郵件挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![開啟使用中的電子郵件類型挑選清單](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. 在電子郵件類型挑選清單頁面上，選取 [公司] 電子郵件類型。

   > [!div class="mx-imgBorder"]
   > ![選取公司電子郵件類型](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. 記下與「公司」電子郵件相關聯的 **選項識別碼**。 這是將與屬性對應表中 emailType 搭配使用的代碼。

   > [!div class="mx-imgBorder"]
   > ![取得電子郵件類型代碼](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > 當您複製值時，請捨棄逗號字元。 例如，如果 **選項識別碼** 值是 8,448，請將 Azure AD 中的 emailType 設定為常數 8448 (不包含逗號字元)。 

### <a name="retrieve-constant-value-for-phonetype"></a>取得 phoneType 的常數值

1. 在 SuccessFactors 系統管理中心內，搜尋並開啟 [挑選清單中心]。 
1. 使用上一節所擷取的電話挑選清單名稱來尋找電話挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![尋找電話類型挑選清單](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. 開啟使用中的電話挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![開啟使用中的電話類型挑選清單](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. 在電話類型挑選清單頁面上，檢閱 [挑選清單值] 底下所列的不同電話類型。

   > [!div class="mx-imgBorder"]
   > ![檢閱電話類型](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. 記下與「公司」電話相關聯的 **選項識別碼**。 這是將與屬性對應表中 businessPhoneType 搭配使用的代碼。

   > [!div class="mx-imgBorder"]
   > ![取得公司電話代碼](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. 記下與「行動電話」相關聯的 **選項識別碼**。 這是將與屬性對應表中 cellPhoneType 搭配使用的代碼。

   > [!div class="mx-imgBorder"]
   > ![取得行動電話代碼](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > 當您複製值時，請捨棄逗號字元。 例如，如果 **選項識別碼** 值是 10,606，請將 Azure AD 中的 cellPhoneType 設定為常數 10606 (不包含逗號字元)。 


## <a name="configuring-successfactors-writeback-app"></a>設定 SuccessFactors Writeback 應用程式

本節提供執行下列作業的步驟 

* [新增佈建連接器應用程式並設定 SuccessFactors 的連線](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第 1 部分：新增佈建連接器應用程式並設定 SuccessFactors 的連線

**若要設定 SuccessFactors Writeback：**

1. 移至 <https://portal.azure.com>

2. 在左側導覽列中，選取 [Azure Active Directory]

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **SuccessFactors Writeback**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7. 將 [佈建模式]  設定為 [自動]

8. 完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 SuccessFactors API 使用者帳戶的使用者名稱，並附上公司識別碼。 其格式為：**username\@companyID**

   * **系統管理員密碼 –** 輸入 SuccessFactors API 使用者帳戶的密碼。 

   * **租用戶 URL –** 輸入 SuccessFactors OData API 服務端點的名稱。 僅輸入不含 http 或 https 的伺服器主機名稱。 此值看起來應該像這樣：`api4.successfactors.com`。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](../app-provisioning/application-provisioning-quarantine-status.md)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請再次檢查 SuccessFactors 認證和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 成功儲存認證之後，[對應] 區段就會顯示預設的對應。 如果看不到屬性對應，請重新整理頁面。  

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您會設定使用者資料從 SuccessFactors 流向 Active Directory 的方式。

1. 在 [佈建] 索引標籤的 [對應] 底下，按一下 [佈建 Azure Active Directory 使用者]。

1. 在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取 Azure AD 中應進行回寫的使用者集合。 預設範圍是「Azure AD 中的所有使用者」。 
   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議您使用 [來源物件範圍] 底下的範圍篩選，利用幾個來自 Azure AD 的測試使用者來測試您的對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. [目標物件動作] 欄位只支援 **更新** 作業。

1. 在 [屬性對應] 區段下的對應資料表中，您可以將下列 Azure Active Directory 屬性對應至 SuccessFactors。 下表提供如何對應回寫屬性的指引。 

   | \# | Azure AD 屬性 | SuccessFactors 屬性 | 備註 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 根據預設，此屬性是比對識別碼。 除了 employeeId，您也可以使用任何其他內含 SuccessFactors 中 personIdExternal 對等值的 Azure AD 屬性。    |
   | 2 | mail | 電子郵件 | 對應電子郵件屬性來源。 基於測試目的，您可以將 userPrincipalName 對應至電子郵件。 |
   | 3 | 8448 | emailType | 此常數值是與公司電子郵件相關聯的 SuccessFactors 識別碼值。 更新此值，以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱[取得 emailType 的常數值](#retrieve-constant-value-for-emailtype)一節。 |
   | 4 | true | emailIsPrimary | 使用此屬性將公司電子郵件設定為 SuccessFactors 中的主要項目。 如果公司電子郵件不是主要項目，請將此旗標設定為 false。 |
   | 5 | userPrincipalName | [custom01 – custom15] | 若使用 **新增對應**，您可以選擇性地將 userPrincipalName 或任何 Azure AD 屬性寫入 SuccessFactors 使用者物件中可用的自訂屬性。  |
   | 6 | on-prem-samAccountName | username | 若使用 **新增對應**，您可以選擇性地將內部部署 samAccountName 對應至 SuccessFactors 使用者名稱屬性。 |
   | 7 | SSO | loginMethod | 如果針對[部分 SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766) 設定 SuccessFactors 租用戶，則您可以使用 [新增對應] 選擇性地將 loginMethod 設定為常數值 "SSO" 或 "PWD"。 |
   | 8 | telephoneNumber | businessPhoneNumber | 使用此對應讓 telephoneNumber 從 Azure AD 流向 SuccessFactors 公司電話號碼。 |
   | 9 | 10605 | businessPhoneType | 此常數值是與公司電話相關聯的 SuccessFactors 識別碼值。 更新此值，以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱[取得 phoneType 的常數值](#retrieve-constant-value-for-phonetype)一節。 |
   | 10 | true | businessPhoneIsPrimary | 使用此屬性來設定公司電話號碼的主要旗標。 有效的值為 true 或 false。 |
   | 11 | mobile | cellPhoneNumber | 使用此對應讓 telephoneNumber 從 Azure AD 流向 SuccessFactors 公司電話號碼。 |
   | 12 | 10606 | cellPhoneType | 此常數值是與行動電話相關聯的 SuccessFactors 識別碼值。 更新此值，以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱[取得 phoneType 的常數值](#retrieve-constant-value-for-phonetype)一節。 |
   | 13 | false | cellPhoneIsPrimary | 使用此屬性來設定行動電話號碼的主要旗標。 有效的值為 true 或 false。 |
 
1. 驗證和檢閱您的屬性對應。 
 
    >[!div class="mx-imgBorder"]
    >![回寫屬性對應](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. 按一下 [儲存] 以儲存對應。 接下來，我們將更新 JSON 路徑 API 運算式，以在您的 SuccessFactors 執行個體中使用 phoneType 代碼。 
1. 選取 [顯示進階選項]。 

    >[!div class="mx-imgBorder"]
    >![顯示進階選項](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. 按一下 [編輯 SuccessFactors 的屬性清單]。 

   > [!NOTE] 
   > 如果 [編輯 SuccessFactors 的屬性清單] 選項未顯示在 Azure 入口網站中，請使用 URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 來存取頁面。 

1. 此視圖中的 [API 運算式] 欄會顯示連接器所使用的 JSON 路徑運算式。 
1. 更新公司電話和行動電話的 JSON 路徑運算式，以使用與您環境對應的識別碼值 (businessPhoneType 和 cellPhoneType)。 

    >[!div class="mx-imgBorder"]
    >![電話 JSON 路徑變更](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. 按一下 [儲存] 以儲存對應。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

在 SuccessFactors 佈建應用程式設定完成之後，您可以在 Azure 入口網站中開啟佈建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

1. 選取 [範圍]。 您可以選取下列其中一個選項： 
   * **同步所有使用者與群組**：如果您打算將所有使用者的對應屬性從 Azure AD 寫回 SuccessFactors，請選取此選項，但須遵循 [對應] -> [來源物件範圍] 底下所定義的範圍規則。 
   * **只同步已指派的使用者與群組**：如果您已在 [應用程式] -> [管理] -> [使用者與群組] 功能表選項中，將使用者指派給此應用程式，而您打算只寫回這些使用者的對應屬性，則選取此選項。 這些使用者也會受限於 [對應] -> [來源物件範圍] 底下所定義的範圍規則。

   > [!div class="mx-imgBorder"]
   > ![選取回寫範圍](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors Writeback 佈建應用程式不支援「群組指派」。 僅支援「使用者指派」。 

1. 按一下 **[儲存]** 。

1. 此作業會啟動初始同步，所需花費的時數會視 Azure AD 租用戶中的使用者人數及該作業定義的範圍而定。 您可以檢查進度列來追蹤同步週期的進度。 

1. 您可隨時檢查 Azure 入口網站中的 [佈建記錄] 索引標籤，查看佈建服務執行了哪些動作。 佈建記錄會列出佈建服務所執行的所有個別同步事件。 

1. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![佈建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>支援的案例、已知問題和限制

請參閱 SAP SuccessFactors 整合參考指南的[回寫案例區段](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios)。 

## <a name="next-steps"></a>後續步驟

* [深入探討 Azure AD 和 SAP SuccessFactors 整合的參考](../app-provisioning/sap-successfactors-integration-reference.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何設定 SuccessFactors 與 Azure Active Directory 之間的單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [了解如何匯出和匯入您的佈建設定](../app-provisioning/export-import-provisioning-configuration.md)