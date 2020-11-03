---
title: 使用 Azure Active Directory B2C 設定 N8 身分識別的教學課程
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 來設定 TheAccessHub 管理工具的教學課程，以解決客戶帳戶遷移和客戶服務要求 (CSR) 管理。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a6d6ca825a556ea3c98fb94d4becbb75b8f2a7d7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294248"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 TheAccessHub 管理工具的教學課程

在此範例教學課程中，我們會提供指引，說明如何整合 Azure Active Directory (AD) B2C 與 N8 身分識別的 [TheAccessHub 管理工具](https://n8id.com/products/theaccesshub-admintool/) 。 此解決方案會解決客戶帳戶遷移和客戶服務要求 (CSR) 管理。  

如果您有下列一或多個需求，此解決方案就適用于您：

- 您有現有的網站，而您想要遷移至 Azure AD B2C。 不過，您會面臨客戶帳戶（包括密碼）的遷移工作

- 您需要一種工具，讓您的 CSR 管理 Azure AD B2C 帳戶。

- 您必須針對您的 Csr 使用委派的系統管理。

- 您想要同步處理您的資料，並將其從許多存放庫合併至 Azure AD B2C。

## <a name="pre-requisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- [Azure AD B2C 的租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。 租使用者必須連結到您的 Azure 訂用帳戶。

- TheAccessHub 管理工具環境：聯絡 [N8 身分識別](https://n8id.com/contact/) 以布建新環境。

- 參數任何資料庫或輕量型目錄存取協定的連線和認證資訊 (LDAPs) 您想要從中遷移客戶資料。

- 參數如果您想要將 TheAccessHub 管理工具整合到您的註冊原則流程中，請設定使用 [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)的 Azure AD B2C 環境。

## <a name="scenario-description"></a>案例描述

TheAccessHub 管理工具的執行方式就像 Azure 中的任何其他應用程式。 它可以在 N8 身分識別的 Azure 訂用帳戶或客戶的訂用帳戶中執行。 下列架構圖顯示執行。

![顯示 n8identity 架構圖表的影像](./media/partner-n8identity/n8identity-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取 [註冊] 以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫 TheAccessHub 管理工具並傳遞使用者屬性
| 3. | TheAccessHub 管理工具會檢查現有的資料庫中是否有目前的使用者資訊。  
| 4. | 使用者記錄是從資料庫同步處理到 TheAccessHub 管理工具。
| 5. | TheAccessHub 管理工具會與委派的 CSR/技術服務人員管理員共用資料。
| 6. | TheAccessHub 管理工具會將使用者記錄與 Azure AD B2C 同步。
| 7. |根據 TheAccessHub 管理工具的成功/失敗回應，Azure AD B2C 將自訂的歡迎電子郵件傳送給使用者。

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>在您的 Azure AD B2C 租使用者中建立全域管理員

TheAccessHub 管理工具需要代表全域系統管理員的許可權，才能讀取使用者資訊並在 Azure AD B2C 租使用者中進行變更。 定期管理員的變更獲勝;t 影響 TheAccessHub 管理工具與租使用者互動的能力。

若要建立全域管理員，請遵循下列步驟：

1. 在 Azure 入口網站中，以系統管理員身分登入您的 Azure AD B2C 租使用者。 流覽至 **Azure Active Directory**  >  **使用者**
2. 選取 [ **新增使用者** ]
3. 選擇 [ **建立使用者** ] 以建立一般目錄使用者，而非客戶
4. 完成身分識別資訊表單

   a. 輸入使用者名稱，例如 ' theaccesshub '

   b. 輸入名稱，例如 ' TheAccessHub Service Account '

5. 選取 [ **顯示密碼** ] 並複製初始密碼以供稍後使用
6. 指派全域管理員角色

   a. 選取使用者目前的角色 **使用者** 加以變更

   b. 檢查全域管理員的記錄

   c. **選取**  > **建立**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>將 TheAccessHub 管理工具與您的 Azure AD B2C 租使用者連線

TheAccessHub 管理工具會使用 Microsoft 的圖形 API 來讀取及變更您的目錄。 它會作為您租使用者中的全域管理員。 TheAccessHub 管理工具需要額外的許可權，您可以在此工具中授與。

若要授權 TheAccessHub 管理工具以存取您的目錄，請遵循下列步驟：

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理員**  >  **Azure AD B2C Config**

3. 選取 **授權連接**

4. 在新的視窗中，使用您的全域管理員帳戶登入。 如果您是第一次使用新的服務帳戶登入，系統可能會要求您重設密碼。

5. 遵循提示，然後選取 [ **接受** ]，授與 TheAccessHub 系統管理員工具所要求的許可權。

## <a name="configure-a-new-csrhelpdesk-user-using-your-enterprise-identity"></a>使用您的企業身分識別設定新的 CSR/技術服務人員使用者

建立 CSR/技術服務人員，以使用其現有的企業 Azure Active Directory 認證來存取 TheAccessHub 管理工具。

若要使用單一登入 (SSO) 來設定 CSR/技術支援使用者，建議您執行下列步驟：

1. 使用 N8 Identity 提供的認證登入 TheAccessHub 管理工具。

2. 流覽至管理同事的 **管理員工具**  >  **Manage Colleagues**

3. 選取 [ **新增同事** ]

4. 選取 **同事類型 Azure 系統管理員**

5. 輸入同事的設定檔資訊

   a. 選擇主要組織將會控制誰擁有管理此使用者的許可權。

   b. 針對登入識別碼/Azure AD 使用者名稱，請從使用者的 Azure Active Directory 帳戶提供使用者主體名稱。

   c. 在 [TheAccessHub 角色] 索引標籤上，檢查管理的角色技術服務人員。 它可讓使用者存取 [管理同事] 視圖。 使用者仍然必須放在群組中，或讓組織擁有者對客戶採取行動。

6. 選取 [提交]  。

## <a name="configure-a-new-csrhelpdesk-user-using-a-new-identity"></a>使用新的身分識別設定新的 CSR/技術服務人員使用者

建立 CSR/技術服務人員的使用者，該使用者將以 TheAccessHub 管理工具特有的新本機認證來存取 TheAccessHub 管理工具。 這主要是供不使用企業 Azure AD 的組織使用。

若要在沒有 SSO 的情況下 [設定 CSR/技術支援](https://youtu.be/iOpOI2OpnLI) 使用者，請遵循下列步驟：

1. 使用 N8 Identity 提供的認證登入 TheAccessHub 管理工具

2. 流覽至管理同事的 **管理員工具**  >  **Manage Colleagues**

3. 選取 [ **新增同事** ]

4. 選取 [ **同事類型] 本機系統管理員**

5. 輸入同事的設定檔資訊

   a. 選擇主要組織將會控制誰擁有管理此使用者的許可權。

   b. 在 [ **TheAccessHub 角色** ] 索引標籤上，選取受管理的角色 **技術服務人員** 。 它可讓使用者存取 [管理同事] 視圖。 使用者仍然必須放在群組中，或讓組織擁有者對客戶採取行動。

6. 複製 **登入識別碼/電子郵件** 和 **一次性密碼** 屬性。 將它提供給新的使用者。 他們將會使用這些認證來登入 TheAccessHub 管理工具。 系統會提示使用者在第一次登入時輸入新密碼。

7. 選取 [ **提交** ]

## <a name="configure-partitioned-csrhelpdesk-administration"></a>設定資料分割 CSR/服務台管理

在 TheAccessHub 管理工具中管理客戶和 CSR/技術支援使用者的許可權，是使用組織階層進行管理。 所有同事和客戶都有其所在的主要組織。 您可以將特定的同事或同事群組指派為組織的擁有者。  組織擁有者可以管理 (對組織中的) 同事和客戶，以及他們擁有的 suborganizations 進行變更。 若要允許多個同事管理一組使用者，可以建立具有許多成員的群組。 然後，您可以將群組指派為組織擁有者，而該群組的所有成員都可以管理組織中的同事和客戶。

### <a name="create-a-new-group"></a>建立新群組

1. 使用 N8 身分識別提供給您的 **認證** 登入 TheAccessHub 管理工具

2. 流覽至 **組織 > 管理群組**

3. 選取 > **新增群組**

4. 輸入 **組名** 、 **群組描述** 和 **群組擁有** 者

5. 搜尋並核取您想要成為群組成員的同事的方塊，然後選取 [ **新增** ] >

6. 在頁面底部，您可以看到群組的所有成員。

7. 如果您可以選取資料列結尾的 **x** 來移除需要的成員

8. 選取 [ **提交** ]

### <a name="create-a-new-organization"></a>建立新的組織

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至組織 > **管理組織**

3. 選取 > **新增組織**

4. 提供 **組織名稱** 、 **組織擁有** 者和 **父組織** 。

    a. 組織名稱最好是對應至客戶資料的值。 當您在載入同事和客戶資料時，如果您在負載中提供組織的名稱，則可以將同事自動放到組織中。

    b. 擁有者代表將在此組織中管理客戶和同事的人員或群組，以及內的任何 suborganization。

    c. 父組織會指出哪些其他組織原本也負責此組織。

5. 選取 [提交]  。

### <a name="modify-the-hierarchy-via-the-tree-view"></a>透過樹狀檢視修改階層

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **管理員工具**  >  **樹狀檢視**

3. 在此標記法中，您可以將可管理哪些組織的同事視覺化。

4. 您可以藉由將您想要其成為父系的組織 overtop，來修改階層。

5. 當您完成變更階層時，請選取 [ **儲存** ]。

## <a name="customize-welcome-notification"></a>自訂歡迎通知

當您使用 TheAccessHub 將使用者從先前的驗證解決方案遷移至 Azure AD B2C 時，您可能會想要自訂使用者歡迎通知，其會在遷移期間透過 TheAccessHub 傳送給使用者。 此訊息通常會包含客戶的連結，以在 Azure AD B2C 目錄中設定新密碼。

若要自訂通知：

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub

2. 流覽至 **系統管理員**  >  **通知**

3. 選取 [ **建立同事] 範本**

4. 選取 [編輯]

5. 視需要修改訊息和範本欄位。 範本欄位是 HTML 感知的，可將 HTML 格式的通知傳送給客戶電子郵件。

6. 選取 [完成時 **儲存** ]。

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>將外部資料源中的資料移轉至 Azure AD B2C

您可以使用 TheAccessHub 管理員工具，從各種資料庫、LDAPs 和 CSV 檔案匯入資料，然後將該資料推送至 Azure AD B2C 的租使用者。 它是藉由將資料載入 TheAccessHub 管理工具中的 Azure AD B2C 使用者同事類型來完成。  如果資料來源不是 Azure 本身，資料會放在 TheAccessHub 管理工具和 Azure AD B2C 中。 如果外部資料的來源不是您電腦上的簡單 .csv 檔案，請先設定資料來源，再執行資料載入。 下列步驟說明如何建立資料來源並執行資料載入。

### <a name="configure-a-new-data-source"></a>設定新的資料來源

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理**  >  **資料來源**

3. 選取 [ **加入資料來源** ]

4. 提供此資料來源的 **名稱** 和 **類型**

5. 填寫表單資料，視您的資料來源類型而定：

   **針對資料庫**

   a. **類型** –資料庫

   b. **資料庫類型** –從其中一個支援的資料庫類型中選取資料庫。

   c. **連接 URL** –輸入格式正確的 JDBC 連接字串。 例如：``jdbc:postgresql://myhost.com:5432/databasename``

   d. 使用者 **名稱** -輸入存取資料庫的使用者名稱

   e. **密碼** –輸入用來存取資料庫的密碼

   f. **查詢** –輸入 SQL 查詢以解壓縮客戶詳細資料。 例如：``SELECT * FROM mytable;``

   g. 選取 [ **測試連接** ]，您會看到資料的範例，以確保連接可以正常運作。

   **針對 LDAPs**

   a. **類型** – LDAP

   b. **主機** –輸入執行 LDAP 伺服器之電腦的主機名稱或 IP。 例如：``mysite.com``

   c. **埠** –輸入 LDAP 伺服器正在接聽的埠號碼。

   d. **SSL** –如果 TheAccessHub Admin Tool 應使用 SSL 安全地與 LDAP 通訊，請核取此方塊。 強烈建議使用 SSL。

   e. **登入 DN** –輸入使用者帳戶的 dn 以登入，並進行 LDAP 搜尋

   f. **密碼** –輸入使用者的密碼

   g. **基底 dn** –在您要搜尋的階層頂端輸入 DN

   h. **篩選** –輸入將取得客戶記錄的 LDAP 篩選字串

   i. **屬性** –輸入您的客戶記錄中以逗號分隔的屬性清單，以傳遞給 TheAccessHub 管理工具

   j. 選取 **測試連接** ，您會看到資料的範例，以確保連接可以正常運作。

   **針對 OneDrive**

   a. **類型** -商務用 OneDrive

   b. 選取 **授權連接**

   c. 新視窗會提示您登入 **onedrive** ，並以具有 OneDrive 帳戶讀取權限的使用者身分登入。 TheAccessHub 管理工具，將會為此使用者提供讀取 CSV 載入檔案的動作。

   d. 遵循提示，然後選取 [ **接受** ]，授與 TheAccessHub 系統管理員工具所要求的許可權。

6. 選取 [完成時 **儲存** ]。  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>將資料來源中的資料同步處理至 Azure AD B2C

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理員**  >  **資料同步** 處理

3. 選取 **新的負載**

4. 選取 [ **同事] 類型** Azure AD B2C 使用者

5. 選取 [ **來源** ]，在快顯對話方塊中，選取您的資料來源。 如果您已建立 OneDrive 資料來源，也請選取該檔案。

6. 如果您不想要使用這項負載來建立新的客戶帳戶，請變更第一個原則： **如果在 TheAccessHub 中找不到同事，則****不執行任何動作**

7. 如果您不想要使用此負載來更新現有的客戶帳戶，請在 **來源和 TheAccessHub 資料不相符時** 變更第二個原則，然後 **不執行任何動作**

8. 選取 [ **下一步** ]

9. 在 **搜尋對應** 設定中，我們會識別如何將載入記錄與已載入 TheAccessHub 管理工具的客戶相互關聯。 選擇來源中的一或多個識別屬性。 將屬性與 TheAccessHub 管理工具中保存相同值的屬性相符。 如果找到相符的結果，則會覆寫現有的記錄;否則，將會建立新的客戶。 您可以排序許多這些檢查。 例如，您可以先檢查電子郵件，然後再檢查姓氏和名字。

10. 在左側功能表中，選取 [ **資料對應** ]。

11. 在 Data-Mapping 設定中，指派應從您的來源屬性填入哪些 TheAccessHub 管理工具屬性。 不需要對應所有屬性。 現有客戶的未對應屬性將保持不變。

12. 如果您將 org_name 的屬性對應至現有組織名稱的值，則會將新建立的客戶放在該組織中。

13. 選取 [ **下一步** ]

14. 如果應該重複此負載，可以指定每日/每週或每月排程。 否則，請 **立即** 保留預設值。

15. 選取 [ **提交** ]

16. 如果選取 [ **現在] 排程** ，則會立即將新的記錄新增至 [資料同步處理] 畫面。 一旦驗證階段達到100%，請選取 **新記錄** 以查看負載的預期結果。 針對已排程的載入，這些記錄只會在排程的時間之後顯示。

17. 如果沒有任何錯誤，請選取 [ **執行** ] 以認可變更。 否則，請選取 [ **更多** ] 功能表中的 [ **移除** ] 以移除載入。 然後，您可以更正來源資料或載入對應，然後再試一次。 相反地，如果錯誤數目很小，您可以手動更新記錄，並在每筆記錄上選取 [ **更新** ] 以進行更正。 最後，您可以繼續進行任何錯誤，稍後再以 TheAccessHub 管理工具中的 **支援介入** 來解決問題。

18. 當 **資料同步** 處理記錄在載入階段變成100% 時，就會起始載入所產生的所有變更。 客戶應該會開始出現或接收 Azure AD B2C 中的變更。

## <a name="synchronize-azure-ad-b2c-customer-data-into-theaccesshub-admin-tool"></a>將 Azure AD B2C 客戶資料同步處理到 TheAccessHub 管理工具

TheAccessHub Admin Tool 是一次性或進行中的作業，可將 Azure AD B2C 中的所有客戶資訊同步處理到 TheAccessHub 管理工具中。 這可確保 CSR/技術服務人員的系統管理員可以看到最新的客戶資訊。

若要將 Azure AD B2C 的資料同步處理到 TheAccessHub 管理工具：

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理員**  >  **資料同步** 處理

3. 選取 **新的負載**

4. 選取 [ **同事] 類型** Azure AD B2C 使用者

5. 針對 [ **選項** ] 步驟，保留預設值。

6. 選取 [ **下一步** ]

7. 針對 **& 搜尋步驟的資料對應** ，保留預設值。 除非您將與現有組織名稱的值對應至屬性 **org_name** ，否則建立的新客戶將會放在該組織中。

8. 選取 [ **下一步** ]

9. 如果應該重複此負載，可以指定每日/每週或每月排程。 否則保留預設值： **Now** 。 建議您定期從 Azure AD B2C 進行同步處理。

10. 選取 [ **提交** ]

11. 如果選取 [ **現在** ] 排程，則會立即將新的記錄新增至 [資料同步處理] 畫面。 一旦驗證階段達到100%，請選取新記錄以查看負載的預期結果。 針對已排程的載入，這些記錄只會在排程的時間之後顯示。

12. 如果沒有任何錯誤，請選取 [ **執行** ] 以認可變更。 否則，請選取 [更多] 功能表中的 [ **移除** ] 以移除載入。 然後，您可以更正來源資料或載入對應，然後再試一次。 相反地，如果錯誤數目很小，您可以手動更新記錄，並在每筆記錄上選取 [ **更新** ] 以進行更正。 最後，您可以繼續進行任何錯誤，稍後再以 TheAccessHub 管理工具中的支援介入來解決問題。

13. 當 **資料同步** 處理記錄在載入階段變成100% 時，就會起始載入所產生的所有變更。

## <a name="configure-azure-ad-b2c-policies-to-call-theaccesshub-admin-tool"></a>設定 Azure AD B2C 原則以呼叫 TheAccessHub 管理工具

有時候，同步處理 TheAccessHub 管理工具的能力，可以讓 Azure AD B2C 保持最新狀態。 我們可以利用 TheAccessHub 管理工具的 API 和 Azure AD B2C 原則，在發生變更時通知 TheAccessHub 管理工具。 此解決方案需要 [Azure AD B2C 自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started#:~:text=%20Get%20started%20with%20custom%20policies%20in%20Azure,Experience%20Framework%20applications.%20Azure%20AD%20B2C...%20More%20)的技術知識。 在下一節中，我們將為您提供範例原則步驟和安全憑證，以在您 Sign-Up 自訂原則中通知新帳戶的 TheAccessHub 管理工具。

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>建立安全的認證來叫用 TheAccessHub 管理工具的 API

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理員**  >  **管理工具**  >  **API 安全性**

3. 選取 [ **產生** ]

4. 複製 **憑證密碼**

5. 選取 [ **下載** ] 以取得用戶端憑證。

6. 遵循此 [教學](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#https-client-certificate-authentication ) 課程，將用戶端憑證新增至 Azure AD B2C。

### <a name="retrieve-your-custom-policy-examples"></a>取出您的自訂原則範例

1. 使用 N8 身分識別提供給您的認證登入 TheAccessHub 管理工具

2. 流覽至 **系統管理員**  >  **管理工具**  >  **Azure B2C 原則**

3. 提供您的 Azure AD B2C 租使用者網域和 Identity Experience Framework 設定的兩個 Identity Experience Framework 識別碼

4. 選取 [儲存]。

5. 選取 [ **下載** ] 以取得具有基本原則的 zip 檔案，以在客戶註冊時將客戶新增至 TheAccessHub 管理工具。

6. 遵循本 [教學](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) 課程，以開始在 Azure AD B2C 中設計自訂原則。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)