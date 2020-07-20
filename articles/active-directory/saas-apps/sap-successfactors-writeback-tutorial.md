---
title: 教學課程：在 Azure Active Directory 中設定 SuccessFactors 回寫 |Microsoft Docs
description: 瞭解如何將屬性回寫設定為從 Azure AD SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77060043"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>教學課程：將屬性回寫從 Azure AD 設定為 SAP SuccessFactors （預覽）
本教學課程的目的是要說明從 Azure AD 到 SuccessFactors Employee Central 的回寫屬性所需執行的步驟。 目前唯一支援回寫的屬性是 [電子郵件] 屬性。 

## <a name="overview"></a>總覽

當您使用 SuccessFactors 將輸入布建整合設定[為內部部署 AD 布建](sap-successfactors-inbound-provisioning-tutorial.md)應用程式或[SuccessFactors 以 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)布建應用程式之後，您可以選擇性地設定 SuccessFactors 回寫應用程式，將電子郵件地址寫回 SuccessFactors。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>誰最適合使用此使用者佈建解決方案？

此 SuccessFactors 回寫使用者布建解決方案最適合下列情況：

* 使用 Office 365 的組織，想要將其所管理的授權屬性（例如電子郵件地址）回 SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>設定整合的 SuccessFactors

所有 SuccessFactors 布建連接器的常見需求是，它們需要具有適當許可權的 SuccessFactors 帳號憑證，才能叫用 SuccessFactors OData Api。 本節說明在 SuccessFactors 中建立服務帳戶，並授與適當許可權的步驟。 

* [在 SuccessFactors 中建立/識別 API 使用者帳戶](#createidentify-api-user-account-in-successfactors)
* [建立 API 許可權角色](#create-an-api-permissions-role)
* [為 API 使用者建立許可權群組](#create-a-permission-group-for-the-api-user)
* [將許可權角色授與許可權群組](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中建立/識別 API 使用者帳戶
請與您的 SuccessFactors 系統管理員小組或實施合作夥伴合作，在 SuccessFactors 中建立或識別將用來叫用 OData Api 的使用者帳戶。 在 Azure AD 中設定布建應用程式時，將需要此帳戶的使用者名稱和密碼認證。 

### <a name="create-an-api-permissions-role"></a>建立 API 許可權角色

* 使用可存取系統管理中心的使用者帳戶登入 SAP SuccessFactors。
* 搜尋 [*管理許可權角色*]，然後從搜尋結果中選取 [**管理許可權角色**]。
  ![管理許可權角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 從 [許可權角色] 清單中 **，按一下 [新建]**。
  > [!div class="mx-imgBorder"]
  > ![建立新的許可權角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 為新的許可權角色新增**角色名稱**和**描述**。 [名稱] 和 [描述] 應該表示角色是用於 API 許可權。
  > [!div class="mx-imgBorder"]
  > ![許可權角色詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 在 [許可權設定] 底下，按一下 [**許可權 ...**]，然後向下流覽許可權清單，再按一下 [**管理整合工具**]。 核取 [**允許系統管理員透過基本驗證存取 ODATA API**] 的核取方塊。
  > [!div class="mx-imgBorder"]
  > ![管理整合工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 在相同的方塊中向下滾動，然後選取 [**員工中心 API**]。 如下所示新增許可權，以閱讀使用 ODATA API 和使用 ODATA API 進行編輯。 如果您打算將相同的帳戶用於回寫至 SuccessFactors 案例，請選取 [編輯] 選項。 
  > [!div class="mx-imgBorder"]
  > ![讀取寫入權限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* 按一下 [完成]。 按一下 **[儲存變更]** 。

### <a name="create-a-permission-group-for-the-api-user"></a>為 API 使用者建立許可權群組

* 在 SuccessFactors 系統管理中心中，搜尋 [*管理許可權群組*]，然後從搜尋結果中選取 [**管理許可權群組**]。
  > [!div class="mx-imgBorder"]
  > ![管理許可權群組](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 從 [管理許可權群組] 視窗中 **，按一下 [新建]**。
  > [!div class="mx-imgBorder"]
  > ![Add new group](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 為新群組新增組名。 組名應該表示群組適用于 API 使用者。
  > [!div class="mx-imgBorder"]
  > ![許可權組名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 將成員新增至群組。 例如，您可以從 [人員集區] 下拉式功能表中選取 [使用者**名稱**]，然後輸入將用於整合之 API 帳戶的使用者名稱。 
  > [!div class="mx-imgBorder"]
  > ![新增群組成員](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* 按一下 [**完成**] 以完成建立許可權群組。

### <a name="grant-permission-role-to-the-permission-group"></a>將許可權角色授與許可權群組

* 在 SuccessFactors 系統管理中心中，搜尋 [*管理許可權角色*]，然後從搜尋結果中選取 [**管理許可權角色**]。
* 從 [**許可權角色] 清單**中，選取您為 [API 許可權] 建立的角色。
* 在 **[將此角色授與 ...**] 底下，按一下 [**新增 ...** ] 按鈕。
* 從下拉式功能表中選取 [**許可權群組 ...** ]，然後按一下 [**選取 ...** ] 開啟 [群組] 視窗，以搜尋並選取上方建立的群組。 
  > [!div class="mx-imgBorder"]
  > ![新增許可權群組](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 檢查許可權群組的許可權角色授與。 
  > [!div class="mx-imgBorder"]
  > ![許可權角色和群組詳細資料](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* 按一下 **[儲存變更]** 。

## <a name="configuring-successfactors-writeback"></a>設定 SuccessFactors 回寫

本節提供下列步驟 

* [新增布建連接器應用程式並設定 SuccessFactors 的連線能力](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [設定屬性對應](#part-2-configure-attribute-mappings)
* [啟用及啟動使用者佈建](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>第1部分：新增布建連接器應用程式並設定 SuccessFactors 的連線能力

**若要設定 SuccessFactors 回寫：**

1. 移至 <https://portal.azure.com> 。

2. 在左側導覽列中，選取 [Azure Active Directory]****

3. 依序選取 [企業應用程式] 和 [所有應用程式]。

4. 選取 [新增應用程式]，然後選取 [全部] 類別。

5. 搜尋**SuccessFactors 回寫**，並從資源庫新增該應用程式。

6. 新增應用程式並顯示 [應用程式詳細資料] 畫面之後，**請選取 [** 布建]

7. **將布**建**模式**變更為**自動**

8. 完成 [系統管理員認證] 區段，如下所示：

   * **管理員使用者名稱**–輸入 SuccessFactors API 使用者帳戶的使用者名稱，並附上公司識別碼。 其格式為： **username \@ companyID**

   * **管理員密碼–** 輸入 SuccessFactors API 使用者帳戶的密碼。 

   * **租使用者 URL –** 輸入 SuccessFactors OData API 服務端點的名稱。 僅輸入不含 HTTP 或 HTTPs 的伺服器主機名稱。 此值應如下所示： **api-server-name.successfactors.com**。

   * **通知電子郵件–** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。
    > [!NOTE]
    > 如果佈建作業進入[隔離](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)狀態，Azure AD 佈建服務會傳送電子郵件通知。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請再次檢查 SuccessFactors 認證和 URL 是否有效。
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 成功儲存認證後，[對應]**區段會**顯示預設的 [**同步處理 Azure Active Directory 使用者至 SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應

在本節中，您將設定使用者資料從 SuccessFactors 流動到 Active Directory 的方式。

1. 在 [對應] 底下的 [布建] 索引標籤**上，按一下**[**同步處理 Azure Active Directory 使用者 SuccessFactors**

1. 在 [**來源物件範圍**] 欄位中，您可以藉由定義一組以屬性為基礎的篩選，來選取應將 Azure AD 中的哪些使用者集合視為回寫。 預設範圍是「Azure AD 中的所有使用者」。 
   > [!TIP]
   > 第一次設定佈建應用程式時，您將需要測試及確認屬性對應和運算式，以確保它提供您所需的結果。 Microsoft 建議使用 [**來源物件範圍**] 底下的範圍篩選器，利用 Azure AD 的幾個測試使用者來測試對應。 確認對應能夠運作之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. [**目標物件動作**] 欄位僅支援**更新**作業。

1. 在 [**屬性**對應] 區段中，您只能變更用來連結 SuccessFactors 使用者設定檔與 Azure AD 使用者的相符識別碼，以及 Azure AD 中哪一個屬性作為電子郵件的來源。 
    >[!div class="mx-imgBorder"]
    >![Azure 入口網站](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors 回寫只支援電子郵件屬性。 請不要使用 [**加入新的對應**] 來加入新的屬性。 

1. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

完成屬性對應設定之後，您現在便可以[啟用及啟動使用者佈建服務](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>啟用及啟動使用者佈建

SuccessFactors 布建應用程式設定完成之後，您就可以在 Azure 入口網站中開啟布建服務。

> [!TIP]
> 根據預設，當您開啟佈建服務時，它會為範圍中的所有使用者起始佈建作業。 如果有對應錯誤或 Workday 資料問題，則佈建作業可能失敗並進入隔離狀態。 為了避免這種情況，我們建議您最好是先設定 [來源物件範圍] 篩選，並使用幾個測試使用者來測試您的屬性對應，然後才為所有使用者啟動完整同步處理。 確認對應能夠運作且提供您所需的結果之後，您便可以移除篩選，或逐漸擴大篩選來包含更多使用者。

1. 在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [檔案] 。

3. 這項作業會啟動初始同步處理，這可能會花費數小時的時間，視 SuccessFactors 租使用者中的使用者人數而定。 您可以檢查進度列以追蹤同步處理週期的進度。 

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 

5. 在初始同步完成之後，它會在 [佈建] 索引標籤中寫入稽核摘要報告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![布建進度列](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
* [瞭解如何設定 SuccessFactors 與 Azure Active Directory 之間的單一登入](successfactors-tutorial.md)
* [了解如何將其他 SaaS 應用程式與 Azure Active Directory 整合](tutorial-list.md)
* [瞭解如何匯出和匯入您的布建設定](../app-provisioning/export-import-provisioning-configuration.md)

