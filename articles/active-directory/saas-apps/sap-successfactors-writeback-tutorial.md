---
title: 教學課程：在 Azure Active Directory 中設定 SAP SuccessFactors 回寫 |Microsoft Docs
description: 瞭解如何設定從 Azure AD 將屬性回寫至 SAP SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 52c71e06b33ef29c2ef0628d651c7f72e41b87ff
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671900"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>教學課程：設定從 Azure AD 將屬性回寫回 SAP SuccessFactors
本教學課程的目的是要示範將屬性從 Azure AD 回寫至 SAP SuccessFactors 員工中心的步驟。 

## <a name="overview"></a>概觀

您可以設定 SAP SuccessFactors 回寫應用程式，將特定的屬性從 Azure Active Directory 寫入至 SAP SuccessFactors 員工中心。 SuccessFactors 回寫布建應用程式支援將值指派給下列員工中部屬性：

* 公司電子郵件
* 使用者名稱
*  (的公司電話號碼，包括國家/地區代碼、區碼、數位和分機) 
* 公司電話號碼主要旗標
* 行動電話號碼 (包括國家/地區代碼、區碼、數位) 
* 行動電話主要旗標 
* 使用者 custom01-custom15 屬性
* loginMethod 屬性

> [!NOTE]
> 此應用程式與 SuccessFactors 的輸入使用者布建整合應用程式沒有任何相依性。 您可以將它設定 [為與內部部署 AD 布建](sap-successfactors-inbound-provisioning-tutorial.md) 應用程式或 SuccessFactors 的 SuccessFactors 無關， [以 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 布建應用程式。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此 SuccessFactors 回寫使用者布建解決方案最適合用於：

* 使用 Microsoft 365 想要寫回受 IT 管理之權威屬性的組織 (例如電子郵件地址、電話、使用者名稱) 回 SuccessFactors 員工中心。

## <a name="configuring-successfactors-for-the-integration"></a>設定整合的 SuccessFactors

所有 SuccessFactors 布建連接器都需要具有適當許可權的 SuccessFactors 帳號憑證，才能叫用員工中央 OData Api。 本節說明在 SuccessFactors 中建立服務帳戶，並授與適當許可權的步驟。 

* [在 SuccessFactors 中建立/識別 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [建立 API 許可權角色](#create-an-api-permissions-role)
* [建立 API 使用者的許可權群組](#create-a-permission-group-for-the-api-user)
* [將許可權角色授與許可權群組](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中建立/識別 API 使用者帳戶
請與您的 SuccessFactors 管理員小組或實施合作夥伴合作，在 SuccessFactors 中建立或識別將用來叫用 OData Api 的使用者帳戶。 在 Azure AD 中設定布建應用程式時，將需要此帳戶的使用者名稱和密碼認證。 

### <a name="create-an-api-permissions-role"></a>建立 API 許可權角色

1. 使用可存取系統管理中心的使用者帳戶登入 SAP SuccessFactors。
1. 搜尋 [ *管理] 許可權角色* ，然後從搜尋結果中選取 [ **管理許可權角色** ]。

   ![管理許可權角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. 從 [許可權角色] 清單中，按一下 [ **建立新** 的]。

   > [!div class="mx-imgBorder"]
   > ![建立新的許可權角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 為新的許可權角色新增 **角色名稱** 和 **描述** 。 名稱和描述應該指出角色是用於 API 使用方式的許可權。

   > [!div class="mx-imgBorder"]
   > ![許可權角色詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. 在 [許可權設定] 下，按一下 [ **許可權** ]，然後依序向下滾動許可權清單，再按一下 [ **管理整合工具** ]。 核取 [ **允許系統管理員透過基本驗證存取 ODATA API** ] 的核取方塊。

   > [!div class="mx-imgBorder"]
   > ![管理整合工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 在相同的方塊中向下移動，然後選取 [ **員工中心 API** ]。 新增如下所示的許可權，以使用 odata api 來讀取，並使用 ODATA API 進行編輯。 如果您打算使用相同的帳戶來進行回寫至 SuccessFactors 案例，請選取 [編輯] 選項。 

   > [!div class="mx-imgBorder"]
   > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. 按一下 [完成]。 按一下 **[儲存變更]** 。

### <a name="create-a-permission-group-for-the-api-user"></a>建立 API 使用者的許可權群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [ *管理許可權群組* ]，然後從搜尋結果中選取 [ **管理許可權群組** ]。

   > [!div class="mx-imgBorder"]
   > ![管理許可權群組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. 從 [管理許可權群組] 視窗中，按一下 [ **建立新** 的]。

   > [!div class="mx-imgBorder"]
   > ![Add new group](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 加入新群組的組名。 組名應該指出群組適用于 API 使用者。

   > [!div class="mx-imgBorder"]
   > ![許可權組名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. 將成員新增至群組。 例如，您可以從 [人員集區] 下拉式功能表中選取 [使用者 **名稱** ]，然後輸入要用於整合的 API 帳戶使用者名稱。 

   > [!div class="mx-imgBorder"]
   > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. 按一下 [ **完成** ] 以完成許可權群組的建立。

### <a name="grant-permission-role-to-the-permission-group"></a>將許可權角色授與許可權群組

1. 在 SuccessFactors 系統管理中心內，搜尋 [ *管理許可權角色* ]，然後從搜尋結果中選取 [ **管理許可權角色** ]。
1. 從 [ **許可權角色] 清單** 中，選取您為 API 使用方式許可權所建立的角色。
1. 在 **[將此角色授與** ] 底下，按一下 [ **新增** ] 按鈕。
1. 從下拉式功能表中選取 [ **許可權群組** ]，然後按一下 [ **選取 ...** ] 開啟 [群組] 視窗，以搜尋並選取上面建立的群組。 

   > [!div class="mx-imgBorder"]
   > ![新增許可權群組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. 檢查許可權群組的許可權角色授與。 
   > [!div class="mx-imgBorder"]
   > ![許可權角色和群組詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. 按一下 **[儲存變更]** 。

## <a name="preparing-for-successfactors-writeback"></a>準備 SuccessFactors 回寫

SuccessFactors 回寫布建應用程式會使用特定的程式 *代碼* 值，在員工中心設定電子郵件和電話號碼。 這些程式 *代碼* 值會設定為屬性對應表中的常數值，且每個 SuccessFactors 實例都是不同的。 本節提供捕獲這些程式 *代碼* 值的步驟。

   > [!NOTE]
   > 請與您的 SuccessFactors 管理員一起完成本節中的步驟。 

### <a name="identify-email-and-phone-number-picklist-names"></a>識別電子郵件和電話號碼挑選清單名稱 

在 SAP SuccessFactors 中，挑選 *清單* 是一組可設定的選項，可讓使用者進行選取。 不同類型的電子郵件和電話號碼 (例如商務、個人、其他) 使用挑選清單來表示。 在此步驟中，我們將識別您 SuccessFactors 租使用者中設定的挑選清單，以儲存電子郵件和電話號碼值。 
 
1. 在 SuccessFactors Admin Center 中，搜尋 [ *管理商務* 設定]。 

   > [!div class="mx-imgBorder"]
   > ![管理商務配置](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. 在 [ **HRIS 元素** ] 底下，選取 [ **emailInfo** ]，然後按一下 [ **電子郵件類型** ] 欄位的 *詳細資料* 。

   > [!div class="mx-imgBorder"]
   > ![取得電子郵件資訊](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. 在 [ **電子郵件類型** 詳細資料] 頁面上，記下與此欄位相關聯的挑選清單名稱。 依預設，它是 **ecEmailType** 。 不過，在您的租使用者中可能會有所不同。 

   > [!div class="mx-imgBorder"]
   > ![識別電子郵件挑選清單](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. 在 [ **HRIS 元素** ] 底下，選取 [ **所** ]，然後按一下 [ **電話類型** ] 欄位的 *詳細資料* 。

   > [!div class="mx-imgBorder"]
   > ![取得電話資訊](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. 在 [ **電話類型** 詳細資料] 頁面上，記下與此欄位相關聯的挑選清單名稱。 依預設，它是 **ecPhoneType** 。 不過，在您的租使用者中可能會有所不同。 

   > [!div class="mx-imgBorder"]
   > ![識別手機挑選清單](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>取得 emailType 的常數值

1. 在 SuccessFactors Admin Center 中，搜尋並開啟挑選 *清單中心* 。 
1. 使用上一節所捕獲的電子郵件挑選清單名稱 (例如 ecEmailType) 來尋找電子郵件挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![尋找電子郵件類型挑選清單](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. 開啟主動電子郵件挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![開啟活動電子郵件類型挑選清單](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. 在 [電子郵件類型挑選清單] 頁面上，選取 [ *企業* ] 電子郵件類型。

   > [!div class="mx-imgBorder"]
   > ![選取商務電子郵件類型](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. 記下與 *商務* 電子郵件相關聯的 **選項識別碼** 。 這是我們將在屬性對應表中搭配 *emailType* 使用的程式碼。

   > [!div class="mx-imgBorder"]
   > ![取得電子郵件類型代碼](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > 當您複製值時，請捨棄逗號字元。 例如，如果 **選項識別碼** 值為 *8448* ，則將 Azure AD 中的 *emailType* 設定為常數位 *8448* (，而不含逗號字元) 。 

### <a name="retrieve-constant-value-for-phonetype"></a>取得 phoneType 的常數值

1. 在 SuccessFactors Admin Center 中，搜尋並開啟挑選 *清單中心* 。 
1. 使用上一節所捕捉的電話挑選清單名稱來尋找電話挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![尋找電話類型挑選清單](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. 開啟主動電話挑選清單。 

   > [!div class="mx-imgBorder"]
   > ![開啟主動電話類型挑選清單](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. 在 [電話類型挑選清單] 頁面上，檢查 [挑選 **清單] 值** 下所列的不同電話類型。

   > [!div class="mx-imgBorder"]
   > ![審核電話類型](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. 記下與 *公司* 電話相關聯的 **選項識別碼** 。 這是我們將在屬性對應表中搭配 *businessPhoneType* 使用的程式碼。

   > [!div class="mx-imgBorder"]
   > ![取得公司電話代碼](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. 記下與 *移動* 電話相關聯的 **選項識別碼** 。 這是我們將在屬性對應表中搭配 *cellPhoneType* 使用的程式碼。

   > [!div class="mx-imgBorder"]
   > ![取得行動電話代碼](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > 當您複製值時，請捨棄逗號字元。 例如，如果 **選項識別碼** 值為 *10606* ，則將 Azure AD 中的 *cellPhoneType* 設定為常數位 *10606* (，而不含逗號字元) 。 


## <a name="configuring-successfactors-writeback-app"></a>設定 SuccessFactors 回寫應用程式

本節提供的步驟 

* [新增布建連接器應用程式並設定 SuccessFactors 的連線能力](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：新增布建連接器應用程式並設定 SuccessFactors 的連線能力

**若要設定 SuccessFactors 回寫：**

1. 移至 <https://portal.azure.com>

2. 在左側導覽列中，選取 [Azure Active Directory] 

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋 **SuccessFactors 回寫** ，並從資源庫新增該應用程式。

6. 新增應用程式並顯示應用程式詳細資料畫面之後， **請選取 [** 布建]

7. **將布** 建 **模式** 變更為 **自動**

8. 完成 [系統管理員認證] 區段，如下所示：

   * 系統 **管理員使用者名稱** –輸入 SuccessFactors API 使用者帳戶的使用者名稱，並附加公司識別碼。 格式如下： **username \@ companyID**

   * **管理員密碼–** 輸入 SuccessFactors API 使用者帳戶的密碼。 

   * **租使用者 URL –** 輸入 SuccessFactors OData API 服務端點的名稱。 只輸入伺服器的主機名稱（不含 HTTP 或 HTTPs）。 這個值看起來應該像這樣： `api4.successfactors.com` 。

   * **通知電子郵件** – 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](../app-provisioning/application-provisioning-quarantine-status.md)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 SuccessFactors 認證和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 一旦成功儲存認證，[對應] **區段就** 會顯示預設對應。 如果看不到屬性對應，請重新整理頁面。  

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您將設定使用者資料如何從 SuccessFactors 流至 Active Directory。

1. 在 [布建] 索引標籤的 [對應] **底下，按一下** [布建 **Azure Active Directory 使用者**

1. 在 [ **來源物件範圍** ] 欄位中，您可以藉由定義一組以屬性為基礎的篩選，來選取要將 Azure AD 中的哪些使用者集合視為回寫。 預設範圍是「Azure AD 中的所有使用者」。 
   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議使用 [ **來源物件範圍** ] 下的範圍篩選器，利用 Azure AD 的一些測試使用者來測試對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. [ **目標物件動作** ] 欄位只支援 **更新** 作業。

1. 在 [ **屬性** 對應] 區段下的 [對應] 資料表中，您可以將下列 Azure Active Directory 屬性對應至 SuccessFactors。 下表提供如何對應回寫屬性的指引。 

   | \# | Azure AD 屬性 | SuccessFactors 屬性 | 備註 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 根據預設，這個屬性是相符的識別碼。 您可以使用任何其他的 Azure AD 屬性，該屬性可能會儲存等於 SuccessFactors 中 personIdExternal 的值，而不是「員工 Id」。    |
   | 2 | mail | 電子郵件 | 地圖電子郵件屬性來源。 基於測試目的，您可以將 userPrincipalName 對應至電子郵件。 |
   | 3 | 8448 | emailType | 此常數值是與商務電子郵件相關聯的 SuccessFactors 識別碼值。 更新此值以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱 emailType 的「抓取 [常數值](#retrieve-constant-value-for-emailtype) 」一節。 |
   | 4 | true | emailIsPrimary | 您可以使用此屬性，將商務電子郵件設定為 SuccessFactors 中的主要電子郵件。 如果商務電子郵件不是主要的，請將此旗標設定為 false。 |
   | 5 | userPrincipalName | [custom01 – custom15] | 您可以使用 [ **加入新對應** ]，選擇性地將 userPrincipalName 或任何 Azure AD 屬性寫入 SuccessFactors 使用者物件中提供的自訂屬性。  |
   | 6 | 內部內部部署-samAccountName | username | 您可以使用 [ **加入新對應** ]，選擇性地將內部部署 samAccountName 對應至 SuccessFactors 使用者名稱屬性。 |
   | 7 | SSO | loginMethod | 如果已針對 [部分 SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)設定 SuccessFactors 租使用者，然後使用 [加入新對應]，您可以選擇性地將 loginMethod 設定為常數值 "SSO" 或 "PWD"。 |
   | 8 | telephoneNumber | businessPhoneNumber | 使用此對應將 *telephoneNumber* 從 Azure AD 流向 SuccessFactors 商務/公司電話號碼。 |
   | 9 | 10605 | businessPhoneType | 此常數值是與公司電話相關聯的 SuccessFactors 識別碼值。 更新此值以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱 phoneType 的「抓取 [常數值](#retrieve-constant-value-for-phonetype) 」一節。 |
   | 10 | true | businessPhoneIsPrimary | 使用此屬性可設定公司電話號碼的主要旗標。 有效值為 true 或 false。 |
   | 11 | mobile | cellPhoneNumber | 使用此對應將 *telephoneNumber* 從 Azure AD 流向 SuccessFactors 商務/公司電話號碼。 |
   | 12 | 10606 | cellPhoneType | 此常數值是與行動電話相關聯的 SuccessFactors 識別碼值。 更新此值以符合您的 SuccessFactors 環境。 如需設定此值的步驟，請參閱 phoneType 的「抓取 [常數值](#retrieve-constant-value-for-phonetype) 」一節。 |
   | 13 | false | cellPhoneIsPrimary | 您可以使用這個屬性來設定行動電話號碼的主要旗標。 有效值為 true 或 false。 |
 
1. 驗證和檢查您的屬性對應。 
 
    >[!div class="mx-imgBorder"]
    >![回寫屬性對應](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. 按一下 [ **儲存** ] 以儲存對應。 接下來，我們將更新 JSON 路徑 API 運算式，以在您的 SuccessFactors 實例中使用 phoneType 碼。 
1. 選取 [顯示進階選項]  。 

    >[!div class="mx-imgBorder"]
    >![顯示 advanced 選項](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. 按一下 [ **SuccessFactors 的編輯屬性清單** ]。 

   > [!NOTE] 
   > 如果 [SuccessFactors] 選項的 [ **編輯屬性清單** ] 未顯示在 Azure 入口網站中，請使用 URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 存取頁面。 

1. 此視圖中的 [ **API 運算式** ] 欄會顯示連接器使用的 JSON 路徑運算式。 
1. 更新商務電話和行動電話的 JSON 路徑運算式，以使用識別碼值 ( *businessPhoneType* 和 *cellPhoneType* ) 對應至您的環境。 

    >[!div class="mx-imgBorder"]
    >![手機 JSON 路徑變更](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. 按一下 [ **儲存** ] 以儲存對應。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

SuccessFactors 布建應用程式設定完成之後，您就可以在 Azure 入口網站中開啟布建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果對應或資料問題中有錯誤，則布建作業可能會失敗，並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

1. 選取 **範圍** 。 您可以選取下列其中一個選項： 
   * **同步所有使用者和群組** ：如果您計畫將所有使用者的對應屬性從 Azure AD 寫回至 SuccessFactors **（受限於** 對應  ->  **來源物件範圍** 下定義的範圍規則），請選取此選項。 
   * **僅同步指派的使用者和群組** ：如果您計畫只在 [ **應用程式**  ->  **管理**  ->  **使用者和群組** ] 功能表選項中，針對已指派給此應用程式的使用者，寫回對應的屬性，請選取此選項。 這些使用者也 **會受限於** 對應  ->  **來源物件範圍** 下定義的範圍規則。

   > [!div class="mx-imgBorder"]
   > ![選取回寫範圍](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors 回寫布建應用程式不支援「群組指派」。 只支援「使用者指派」。 

1. 按一下 [檔案]  。

1. 這項作業會啟動初始同步處理，視 Azure AD 租使用者中有多少使用者和為作業定義的範圍而定，可能會花費數小時的時間。 您可以檢查進度列，以追蹤同步處理週期的進度。 

1. 您可以隨時檢查 Azure 入口網站中的 [布建 **記錄** ] 索引標籤，查看布建服務已執行的動作。 布建記錄會列出布建服務所執行的所有個別同步處理事件。 

1. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![布建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>支援的案例、已知問題和限制

請參閱 SAP SuccessFactors 整合參考指南的 [回寫案例一節](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) 。 

## <a name="next-steps"></a>後續步驟

* [深入探討 Azure AD 與 SAP SuccessFactors 整合參考](../app-provisioning/sap-successfactors-integration-reference.md)
* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [瞭解如何設定 SuccessFactors 與 Azure Active Directory 之間的單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和匯入布建設定](../app-provisioning/export-import-provisioning-configuration.md)