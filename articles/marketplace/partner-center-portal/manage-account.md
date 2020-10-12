---
title: 如何在 Microsoft 合作夥伴中心管理商業市集帳戶
description: 了解如何在 Microsoft 合作夥伴中心管理商業市集帳戶。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 08/27/2020
ms.openlocfilehash: a2d2c4d29a6af073e3e4e6a74c257cb864b8a78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400684"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>在合作夥伴中心管理您的商業市集帳戶

[建立合作夥伴中心帳戶](./create-account.md)之後，您可以使用[商業市集儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)管理帳戶和供應項目。

在本文中，我們將深入探討如何管理您的合作夥伴中心帳戶，包括如何：

- [存取您的合作夥伴中心帳戶設定](#access-your-account-settings)
- [尋找您的發行者識別碼、Symantec 識別碼、賣方識別碼、使用者識別碼、MPN 識別碼和 Azure AD 租用戶](#account-details)
- [更新連絡人資訊](#contact-info)
- [設定用於監視客戶使用情況的追蹤 GUID](#tracking-guids)
- [管理使用者](#manage-users)
- [管理群組](#manage-groups)
- [管理 Azure AD 應用程式](#manage-azure-ad-applications)
- [定義使用者角色和權限](#define-user-roles-and-permissions)
- [管理 Azure AD 租用戶 (公司帳戶)](#manage-tenants)
- [管理合作夥伴中心合約](#agreements)

## <a name="access-your-account-settings"></a>存取您的帳戶設定

如果您尚未這麼做，您 (或您組織的系統管理員) 應該存取合作夥伴中心帳戶的[帳戶設定](https://partner.microsoft.com/dashboard/account/management)來執行下列動作：
- 檢查您公司的帳戶驗證狀態
- 確認您的 Symantec 識別碼、賣方識別碼、Microsoft 合作夥伴網路 (MPN) 識別碼、發行者識別碼，以及連絡人資訊 (包括公司核准者和賣方連絡人)
- 為將在合作夥伴中心使用您商務帳戶的人，建立使用者帳戶

### <a name="open-developer-settings"></a>開啟開發人員設定

[帳戶設定] 位於合作夥伴中心內[商業市集儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace)的右上角。 選取齒輪圖示 (位於儀表板右上角附近)，然後選取 [開發人員設定] **** 。

![合作夥伴中心的 [帳戶設定] 功能表](./media/dashboard-developer-settings.png)

在 [帳戶設定] 內，您將能夠檢視：
- **帳戶詳細資料**：帳戶類型和帳戶狀態
- **發行者識別碼**：賣方識別碼、使用者識別碼、發行者識別碼、Azure AD 租用戶等
- **連絡人資訊**：發行者顯示名稱、賣方連絡人名稱、電子郵件、電話和地址
- **追蹤 GUID**：與您的帳戶相關聯的任何追蹤 GUID

### <a name="account-details"></a>帳戶詳細資料

在 [帳戶詳細資料] 區段中，您可以看到基本資訊，例如您的 [帳戶類型] (公司或個人)，以及您帳戶的 [驗證狀態]。 在您的帳戶驗證過程中，這些設定會顯示所需的每個步驟，包括電子郵件驗證、雇用驗證和商務驗證。 您也可以在這裡更新電子郵件，並視需要重新傳送驗證。

### <a name="publisher-ids"></a>發行者識別碼

在 [發行者識別碼] 區段中，您可以看到 [Symantec 識別碼]、[賣方識別碼]、[使用者識別碼]、[MPN 識別碼] 和 [Azure AD 租用戶]。 Microsoft 會指派這些值來唯一識別您的開發人員帳戶，而且無法加以編輯。

### <a name="contact-info"></a>連絡資訊

在 [連絡人資訊] 區段中，您可以看到 [發行者顯示名稱]、[賣方連絡人資訊] (公司賣方的連絡人名稱、電子郵件、電話號碼和地址)，以及 [公司核准者] (有權核准公司決策的人員姓名、電子郵件和電話號碼)。

#### <a name="payout-account"></a>支出帳戶

支出帳戶是發送您的銷售收入款所用的銀行帳戶。 此銀行帳戶必須與您註冊合作夥伴中心帳戶的國家/地區相同。

若要設定您的支出帳戶：

1. 請前往合作夥伴中心的[商業市集概觀頁面](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 在 [設定檔] 區段下的 [Payout Profile] \(支出設定檔\) 旁，選取 [更新]。
3. **選擇付款方式**：銀行帳戶或 PayPal。
4. **新增付款資訊**：這可能包括選擇帳戶類型 (支票或儲蓄)、輸入帳戶持有人姓名、帳號和匯款選路編號、帳單地址、電話號碼，或 PayPal 電子郵件地址。 *如需使用 PayPal 作為帳戶付款方式的詳細資訊，並想要了解您的市場區域是否支援該方式，請參閱 [PayPal 資訊](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)。

> [!IMPORTANT]
> 變更您的付款帳戶會延遲您的付款最多一次付款週期。 會發生此延遲是因為我們必須確認帳戶變更，就像我們在第一次設定支出帳戶時所做的一樣。 您仍然會在帳戶確認之後收到全額款項。當期付款週期未付的款項會新增至下一期。  

#### <a name="tax-profile"></a>稅金設定檔

檢閱您目前的稅金設定檔狀態，確認顯示正確的 [實體類型] 和 [稅金憑證資訊]。 選取 [編輯] 以更新或完成所有必要的表單。

若要建立您的稅金狀態，您必須指定居住國家/地區和國籍，並填妥與您國家/地區相關聯的適當稅務表單。

無論您的居住國家/地區或國籍為何，都必須填寫美國稅務表單，才能透過 Microsoft 銷售任何供應項目。 符合特定美國居住規定的合作夥伴必須填寫 IRS W-9 表單。 美國境外的其他合作夥伴必須填寫 IRS W-8 表單。 您可以在完成稅金設定檔時，於線上填寫這些表單。

不需要美國個人納稅人識別編號 (ITIN)，即可從 Microsoft 收到款項或申請租稅協定減免優惠。

您可以在合作夥伴中心以電子方式填妥並提交您的稅務表單；在大部分的情況下，您不需要列印和郵寄任何表單。

不同的國家和地區有不同的稅務規定。 您必須支付的確切金額取決於您銷售供應項目的國家和地區。 在某些國家/地區，會由 Microsoft 代您繳納銷售與使用稅。 在列出您供應項目的過程中會識別這些國家/地區。 在其他國家/地區，視您註冊的位置而定，您可能需要針對銷售項目直接向當地稅務機關繳納銷售與使用稅。 此外，您收到的銷售收入款可能會視為收入而予以課稅。 我們強烈建議您洽詢您所在國家或地區的相關主管機關，主管機關最能協助您判斷您 Microsoft 銷售交易的正確稅務資訊。

##### <a name="withholding-rates"></a>扣繳率

您在稅務表單中提交的資訊會決定適當的扣繳稅率。 此扣繳率僅適用於您在美國所進行的銷售，在非美國位置進行的銷售，扣繳規範則不適用。 扣繳率不盡相同，但對於大部分在美國境外註冊的開發人員，預設扣繳率為 30%。 如果您的國家/地區與美國達成所得稅協定，您可以選擇降低此扣繳率。

##### <a name="tax-treaty-benefits"></a>租稅協定減免優惠

如果您居住在美國境外，則可利用租稅協定減免優惠。 這些減免優惠會因國家/地區而異，並可讓您減少 Microsoft 扣繳的稅額。 您可以填妥 W-8BEN 表單的第二部分，來申請租稅協定減免優惠。 我們建議您與您所在國家或地區的適當資源進行通訊，以判斷您是否適用這些減免優惠。

[深入了解 Windows 應用程式/遊戲開發人員和 Azure Marketplace 發行者的稅務詳細資料](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)。

#### <a name="payout-hold-status"></a>支出保留狀態

根據預設，Microsoft 會每月匯款。 不過，您可以選擇性地將支出保留，以防止將付款傳送至您的帳戶。 如果您選擇保留支出，我們將繼續記錄您所獲得的所有收入，並在您的 [支出摘要] 中提供詳細資料。 在您解除保留之前，我們不會將任何款項匯到您的帳戶。

若要保留您的付款，請移至 [帳戶設定]。 在 [財務詳細資料] 下的 [Payout hold status] \(支出保留狀態\) 區段中，將滑桿切換至 [開啟]。 您可以隨時變更支出保留狀態，但請注意，您的決定會影響下一個每月支出。 例如，如果您想要保留 4 月的支出，請務必在 3 月底前將支出保留狀態設定為 [開啟]。

一旦您將支出保留狀態設定為 [開啟]，則會保留所有支出，直到您將滑桿切換回 [關閉] 為止。 當您這麼做時，系統會將您包含在下一個月支出週期中 (前提是已達到任何適用的付款閾值)。 例如，如果您已保留支出，但想要在 6 月產生支出，則請務必在 5 月底前將支出保留狀態切換至 [關閉]。

> [!NOTE]
> 您的 [Payout hold status] \(支出保留狀態\) 選項適用於**所有**透過 Microsoft 合作夥伴中心支付的收入來源，包括 Azure Marketplace、AppSource、Microsoft Store、廣告等。 您無法為每個收入來源選取不同的保留狀態。

### <a name="devices"></a>裝置

裝置管理設定僅適用於 UWP 發行。 [深入了解](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)。

### <a name="tracking-guids"></a>追蹤 GUID

全域唯一識別碼 (GUID) 是可用於追蹤 Azure 使用情況的唯一參考編號 (具有 32 個十六進位數字)。 

若要建立 GUID 來進行追蹤，您應該使用 GUID 產生器。 Azure 儲存體小組已建立 [GUID 產生器表單](https://aka.ms/StoragePartners)，其會透過電子郵件傳送正確格式的 GUID 給您，且可跨不同追蹤系統重複使用。

建議您為每個產品的每個供應項目與散發通道建立唯一的 GUID。 如果您不想要分割報告，您可以讓產品的多個散發通道使用單一 GUID。

若您使用範本部署產品，且該產品在 Azure Marketplace 與 GitHub 上都有提供，則您可以建立並註冊兩個相異的 GUID：

- Azure Marketplace 中的產品 A
- GitHub 上的產品 A

報告是由合作夥伴值 (Microsoft 合作夥伴 ID) 與 GUID 所完成。 您也能以與供應項目中每個方案一致的更細微層級來追蹤 GUID。

如需詳細資訊，請參閱 [使用 Guid 追蹤 Azure 客戶使用量的常見問題](../azure-partner-customer-usage-attribution.md#faq)) 。

## <a name="create-a-billing-profile"></a>建立帳單設定檔

如果您要發行 [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) 或 [Dynamics 365 for Operations](./create-new-operations-offer.md) 供應項目，您需要完成**帳單設定檔**。

系統會預先填入您法律實體的帳單地址，稍後可以更新此地址。 [稅金] 和 [加值稅識別碼] 欄位為選擇性。  無法編輯國家/地區名稱和公司名稱。

## <a name="multi-user-account-management"></a>多使用者帳戶管理

合作夥伴中心使用 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 進行多重使用者帳戶存取和管理。 您組織的 Azure AD 會在註冊過程中，自動與您的合作夥伴中心帳戶建立關聯。

## <a name="manage-users"></a>管理使用者

[合作夥伴中心] 的 [使用者] 區段 (在 [帳戶設定] 下) 可讓您使用 Azure AD 管理能存取合作夥伴中心帳戶的使用者、群組和 Azure AD 應用程式。 您的帳戶必須具有要新增或編輯使用者之[公司帳戶 (Azure AD 租用戶)](./company-work-accounts.md) 的[**管理員層級**](#define-user-roles-and-permissions)權限。 若要管理不同公司帳戶/租用戶中的使用者，您必須登出，然後以具有該公司帳戶/租用戶**管理員**權限的使用者身分重新登入。

使用您的公司帳戶 (Azure AD 租用戶) 登入之後，您可以：

- [新增或移除使用者](#add-existing-users)
- [變更使用者密碼](#change-a-user-password)
- [新增或移除群組](#manage-groups)
- [新增或移除 Azure AD 應用程式](#add-new-azure-ad-applications)
- [管理 Azure AD 應用程式的金鑰](#manage-keys-for-an-azure-ad-application)
- [定義使用者角色和權限](#define-user-roles-and-permissions)

請記住，所有合作夥伴中心使用者 (包括群組和 Azure AD 應用程式) 都必須在與合作夥伴中心帳戶相關聯的 [Azure AD 租用戶](#manage-tenants)中，具有作用中的公司帳戶。

### <a name="add-existing-users"></a>新增現有的使用者

若要將使用者新增至您[公司帳戶 (Azure AD 租用戶)](./company-work-accounts.md) 中已存在的合作夥伴中心帳戶：

1. 移至 [使用者] (在 [帳戶設定] 下)，然後選取 [新增使用者]。
2. 從顯示的清單中選取一或多個使用者。 您可以使用搜尋方塊來搜尋特定使用者。
*如果您要選取多個使用者新增至您的合作夥伴中心帳戶，您必須為其指派相同的角色或一組相同的自訂權限。 若要新增具有不同角色/權限的多個使用者，請針對每個角色或每組自訂權限重複這些步驟。
3. 當您完成選擇使用者時，請選取 [ **新增選取**的]。
4. 在 [角色] 區段中，指定所選使用者的角色或自訂權限。
5. 選取 [儲存]。

### <a name="create-new-users"></a>建立新的使用者

若要建立全新的使用者帳戶，您必須擁有具備[**全域管理員**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)權限的帳戶。

1. 移至 [使用者] (在 [帳戶設定] 下)，選取 [新增使用者]，然後選擇 [建立新的使用者]。
1. 輸入每個新使用者的名字、姓氏和使用者名稱。 
1. 如果您想要讓新使用者擁有您組織目錄中的全域管理員帳戶，請核取標示為 [Make this user a Global administrator in your Azure AD, with full control over all directory resources] \(將此使用者設為 Azure AD 中可完整控制所有目錄資源的全域管理員\) 的方塊。 這可讓使用者完整存取您公司 Azure AD 中的所有系統管理功能。 他們將能夠在您組織的公司帳戶 (Azure AD 租用戶) 中新增和管理使用者，但無法在合作夥伴中心進行，除非您將適當的角色/權限授與該帳戶。
1. 如果您核取 [Make this user a Global administrator] \(將此使用者設為全域管理員\) 方塊，您就必須提供**密碼復原電子郵件**，以便使用者能夠在必要時復原其密碼。
1. 在 [群組成員資格] 區段中，選取新使用者所屬的任意群組。
1. 在 [角色] 區段中，指定使用者的角色或自訂權限。
1. 選取 [儲存]。

在合作夥伴中心建立新的使用者，也會在您登入的公司帳戶 (Azure AD 租用戶) 中建立該使用者的帳戶。 對合作夥伴中心內的使用者名稱進行變更，將會在您組織的公司帳戶 (Azure AD 租用戶) 中進行相同的變更。

### <a name="invite-new-users-by-email"></a>透過電子郵件邀請新的使用者

若要透過電子郵件邀請目前不屬於您公司帳戶 (Azure AD 租用戶) 的使用者，您必須擁有具備[**全域管理員**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)權限的帳戶。

1. 移至 [使用者] (在 [帳戶設定] 下)，選取 [新增使用者]，然後選擇 [Invite users by email] \(透過電子郵件邀請使用者\)。
2. 輸入一或多個電子郵件地址， (最多10個) （以逗號或分號分隔）。
3. 在 [角色] 區段中，指定使用者的角色或自訂權限。
4. 選取 [儲存]。

您邀請的使用者將會收到電子郵件邀請，以加入您的合作夥伴中心帳戶。 這會在您的公司帳戶 (Azure AD 租用戶) 中建立新的來賓使用者帳戶。 每個使用者都必須接受其邀請，才能存取您的帳戶。

如果您需要重新傳送邀請，請前往 [使用者] 頁面，在使用者清單中尋找邀請，然後選取其電子郵件地址 (或顯示「待決邀請」的文字)。 然後，選取頁面底部的 [重新傳送邀請]。

> [!NOTE]
> 如果您的組織使用[目錄整合](https://go.microsoft.com/fwlink/p/?LinkID=724033)來同步內部部署目錄服務與您的 Azure AD，您將無法在合作夥伴中心建立新的使用者、群組或 Azure AD 應用程式。 您 (或您內部部署目錄中的其他系統管理員) 必須直接在內部部署目錄中建立這些項目，才能在合作夥伴中心看到並新增這些項目。

### <a name="remove-a-user"></a>移除使用者

若要從您的公司帳戶 (Azure AD 租用戶) 中移除使用者，請移至 [使用者] (在 [帳戶設定] 下)，使用最右欄中的核取方塊來選取您想要移除的使用者，然後從可用的動作中選擇 [移除]。 隨即會出現一個快顯視窗，供您確認是否要移除選取的使用者。

### <a name="change-a-user-password"></a>變更使用者密碼

如果有使用者需要變更其密碼，而且您在建立使用者帳戶時已提供**密碼復原電子郵件**，則該使用者可以自行執行這項操作。 您也可以依照下列步驟來更新使用者的密碼。 若要變更您公司帳戶 (Azure AD 租用戶) 的使用者密碼，您必須使用[**全域管理員**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)權限登入帳戶。 這會變更您 Azure AD 租使用者中的使用者密碼，以及他們用來存取合作夥伴中心的密碼。

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取您要編輯的使用者帳戶名稱。
2. 選取頁面底部的 [重設密碼] 按鈕。
3. [確認] 頁面會出現，顯示使用者的登入資訊，包括暫時的密碼。 請務必列印或複製此資訊，並提供給使用者，因為離開此頁面之後，就無法存取臨時密碼。

## <a name="manage-groups"></a>管理群組

群組可讓您同時控制多個使用者角色和權限。

### <a name="add-an-existing-group"></a>新增現有的群組

若要將您組織公司帳戶 (Azure AD 租用戶) 中已存在的群組新增至您的合作夥伴中心帳戶：

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取 [新增群組]。
2. 從顯示的清單中選取一或多個群組。 您可以使用搜尋方塊來搜尋特定群組。
如果您要選取多個群組新增至您的合作夥伴中心帳戶，您必須為其指派相同的角色或一組相同的自訂權限。 若要新增具有不同角色/權限的多個群組，請對每個角色或每組自訂權限重複這些步驟。
3. 當您完成選擇群組時，請選取 [ **新增選取**的]。
4. 在 [角色] 區段中，指定所選群組的角色或自訂權限。 群組的所有成員都能以您套用至群組的權限，存取您的合作夥伴中心帳戶，而不論與其個別帳戶相關聯的角色和權限為何。
5. 選取 [儲存]。

當您新增現有的群組時，屬於該群組成員的每個使用者都能以與群組獲指派角色相關聯的權限，存取您的合作夥伴中心帳戶。

### <a name="add-a-new-group"></a>新增群組

若要將全新的群組新增至您的合作夥伴中心帳戶：

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取 [新增群組]。
2. 在下一個頁面上，選取 [新增群組]。
3. 輸入新群組的顯示名稱。
4. 指定群組的角色或自訂權限。 群組的所有成員都能以您在這裡套用的權限，存取您的合作夥伴中心帳戶，而不論與其個別帳戶相關聯的角色/權限為何。
5. 從顯示的清單中選取新群組的使用者。 您可以使用搜尋方塊來搜尋特定使用者。
6. 當您完成選取使用者時，請選取 [新增 **選取** 專案] 將其新增至新群組。
7. 選取 [儲存]****。

這個新群組也會在您組織的工作帳戶中建立 (Azure AD 租使用者) ，而不只是在您的合作夥伴中心帳戶中。

### <a name="remove-a-group"></a>移除群組

若要從您的公司帳戶 (Azure AD 租用戶) 中移除群組，請移至 [使用者] (在 [帳戶設定] 下)，使用最右欄中的核取方塊來選取您想要移除的群組，然後從可用的動作中選擇 [移除]。 隨即會出現一個快顯視窗，供您確認是否要移除選取的群組。

## <a name="manage-azure-ad-applications"></a>管理 Azure AD 應用程式

您可以允許屬於公司 Azure AD 的應用程式或服務存取您的合作夥伴中心帳戶。

### <a name="add-existing-azure-ad-applications"></a>新增現有的 Azure AD 應用程式

若要新增您公司 Azure Active Directory 中已存在的應用程式：

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取 [新增 Azure AD 應用程式]。
2. 從顯示的清單中選取一或多個 Azure AD 應用程式。 您可以使用搜尋方塊來搜尋特定 Azure AD 應用程式。 如果您要選取多個 Azure AD 應用程式新增至您的合作夥伴中心帳戶，您必須為其指派相同的角色或一組相同的自訂權限。 若要新增具有不同角色/權限的多個 Azure AD 應用程式，請對每個角色或每組自訂權限重複這些步驟。
3. 當您完成選取 Azure AD 的應用程式時，請選取 [ **新增選取**的]。
4. 在 [角色] 區段中，指定所選 Azure AD 應用程式的角色或自訂權限。
5. 選取 [儲存]。

### <a name="add-new-azure-ad-applications"></a>新增 Azure AD 應用程式

如果您想要將合作夥伴中心存取權授與全新的 Azure AD 應用程式帳戶，您可以在 [使用者] 區段中建立一個帳戶。 這將會在您的公司工作帳戶中建立新的帳戶 (Azure AD 租使用者) ，而不是只在您的合作夥伴中心帳戶中。 如果您主要是使用此 Azure AD 應用程式進行合作夥伴中心驗證，而且不需要使用者直接進行存取，您可以在 [回覆 URL] 和 [應用程式識別碼 URI] 輸入任意有效的位址，前提是您目錄中的其他 Azure AD 應用程式都不會使用這些值。

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取 [新增 Azure AD 應用程式]。
2. 在下一個頁面上，選取 [新增 Azure AD 應用程式]。
3. 輸入新 Azure AD 應用程式的 [回覆 URL]。 這是使用者可以登入並使用您 Azure AD 應用程式的 URL (有時也稱為「應用程式 URL」或「登入 URL」)。 [回覆 URL] 的長度不能超過 256 個字元，而且在您的目錄中必須是唯一的。
4. 輸入新 Azure AD 應用程式的 [應用程式識別碼 URI]。 這是將單一登入要求傳送至 Azure AD 時，所顯示 Azure AD 應用程式的邏輯識別碼。 **應用程式識別碼 URI**必須是目錄中每個 Azure AD 應用程式的唯一識別碼。 此識別碼的長度不能超過 256 個字元。 如需應用程式識別碼 URI 的詳細資訊，請參閱將 [應用程式與 Azure Active Directory) 整合](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts) 。
5. 在 [角色] 區段中，指定 Azure AD 應用程式的角色或自訂權限。
6. 選取 [儲存]。

新增或建立 Azure AD 應用程式之後，您可以返回 [使用者] 區段，然後選取應用程式名稱以檢閱應用程式的設定，包括租用戶識別碼、用戶端識別碼、回覆 URL 和應用程式識別碼 URI。

### <a name="remove-an-azure-ad-application"></a>移除 Azure AD 應用程式

若要從您的公司帳戶 (Azure AD 租用戶) 中移除應用程式，請移至 [使用者] (在 [帳戶設定] 下)，使用最右欄中的核取方塊來選取您想要移除的應用程式，然後從可用的動作中選擇 [移除]。 隨即會出現一個快顯視窗，供您確認是否要移除選取的應用程式。

### <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 應用程式的金鑰

如果您的 Azure AD 應用程式會在 Microsoft Azure AD 中讀取和寫入資料，則需要金鑰。 您可以為 Azure AD 應用程式建立金鑰，方法是在合作夥伴中心編輯資訊。 您也可以移除不再需要的金鑰。

1. 從 [使用者] 頁面 (在 [帳戶設定] 下)，選取 Azure AD 應用程式的名稱。 您將會看到 Azure AD 應用程式的所有作用中金鑰，包括建立金鑰的日期，以及將于50到期的時間。
2. 若要移除不再需要的金鑰，請選取 [移除]。
3. 若要新增金鑰，請選取 [新增金鑰]。
4. 您會看到一個畫面，其中顯示 [用戶端識別碼] 和 [金鑰值]。 請務必列印或複製此資訊，因為離開此頁面之後，就再也無法進行存取。
5. 如果您想要建立更多金鑰，請選取 [Add another key] \(新增其他金鑰\)。

## <a name="define-user-roles-and-permissions"></a>定義使用者角色和權限

您公司的使用者可以在合作夥伴中心上指派下列角色和許可權給商業 marketplace 方案：

- **擁有者**
  - 「這是第一次建立帳戶並具有完整存取權的人員，包括建立和編輯所有帳戶使用者，以及變更所有財務和帳戶設定的能力。 帳戶通常只有一個擁有者。」
- **管理員**
  - 可存取所有 Microsoft 帳戶功能，但稅金和支出設定除外
  - 可管理使用者、角色和公司帳戶 (租用戶)
- **開發人員**
  - 可管理和發行供應項目
  - 可檢視一些發行者報表

> [!NOTE]
> 在商業市集計畫，不會使用全域管理員、商務參與者、財務參與者和行銷人員角色。 指派這些角色給使用者不會有任何作用。 只有管理員和開發人員角色可授與權限給使用者。

如需在合作夥伴中心的其他區域中管理角色和權限的詳細資訊，例如 Azure Active Directory (AD)、雲端解決方案提供者 (CSP)、控制台廠商 (CPV)、來賓使用者或 Microsoft 合作夥伴網路 (MPN)，請參閱[在合作夥伴中心指派使用者角色和權限](https://docs.microsoft.com/partner-center/permissions-overview)。

## <a name="manage-tenants"></a>管理租用戶

Azure Active Directory (AD) 租用戶 (在此文件中也稱為您的「公司帳戶」) 代表您在 Azure 入口網站中設定的組織，可協助您為內部與外部使用者管理 Microsoft 雲端服務的特定執行個體。 如果您的組織已訂閱 Microsoft 雲端服務（例如 Azure、Microsoft Intune 或 Microsoft 365），就會為您建立 Azure AD 租使用者。

您可以設定多個租用戶來搭配合作夥伴中心使用。 在合作夥伴中心帳戶內，所有具有**管理員**角色的使用者，都可以選擇從帳戶新增和移除 Azure AD 租用戶。  

### <a name="add-an-existing-tenant"></a>新增現有的租用戶

若要將另一個 Azure AD 租用戶與您的合作夥伴中心帳戶建立關聯：

1. 從 [租用戶] 頁面 (在 [帳戶設定] 下)，選取 [Associate another Azure AD tenant] \(與其他 Azure AD 租用戶建立關聯\)。
2. 為您要建立關聯的租用戶輸入 Azure AD 認證。
3. 檢閱您 Azure AD 租用戶的組織和網域名稱。 若要完成關聯，請選取 [確認]。

如果關聯成功，您可以接著在合作夥伴中心的 [使用者] 區段中，新增和管理帳戶使用者。

### <a name="create-a-new-tenant"></a>建立新的租用戶

若要使用您的合作夥伴中心帳戶建立全新的 Azure AD 租用戶：

1. 從 [租用戶] 頁面 (在 [帳戶設定] 下)，選取 [建立新的 Azure AD 租用戶]。
2. 輸入新 Azure AD 的目錄資訊：
    - **網域名稱**：我們將用於 Azure AD 網域的唯一名稱，後面接著 "onmicrosoft.com"。 例如，如果您輸入 "example"，您的 Azure AD 網域會是 "example.onmicrosoft.com"。
    - **連絡人電子郵件**：我們可以在必要時就帳戶相關問題連絡您的電子郵件地址。
    - **全域管理員使用者帳戶資訊**：您想要用於新全域管理員帳戶的名字、姓氏、使用者名稱和密碼。
3. 選取 [建立]，確認新的網域和帳戶資訊。
4. 使用新的 Azure AD 全域管理員使用者名稱和密碼登入，開始[新增和管理使用者](#manage-users)。

如需在 Azure 入口網站內 (而不是透過合作夥伴中心入口網站) 建立新租用戶的詳細資訊，請參閱[在 Azure Active Directory 中建立新的租用戶](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文。

### <a name="remove-a-tenant"></a>移除租用戶

若要從您的合作夥伴中心帳戶中移除租用戶，請在 [租用戶] 頁面 (在 [帳戶設定] 下) 尋找其名稱，然後選取 [移除]。 系統會提示您確認是否要移除租用戶。 一旦您這麼做，該租用戶中的使用者將無法登入合作夥伴中心帳戶，且會移除您為這些使用者設定的任何權限。

當您移除租用戶時，從該租用戶新增至合作夥伴中心帳戶的所有使用者將無法再登入該帳戶。

> [!TIP]
> 如果您目前使用相同租用戶中的帳戶登入合作夥伴中心，則無法移除租用戶。 若要移除租用戶，您必須以另一個租用戶 (與帳戶相關聯) 的**管理員**身分登入合作夥伴中心。 如果只有一個租用戶與此帳戶相關聯，則只有在用以開啟帳戶的 Microsoft 帳戶登入之後，才能移除該租用戶。

## <a name="agreements"></a>合約

[**帳戶) 設定**] 下合作夥伴中心 (的 [**協定**] 區段可讓您查看已授權的發行合約清單。 這些合約會根據名稱和版本號碼列出，包括接受日期和接受合約的使用者名稱。

如果有需要注意的合約更新，此頁面頂端可能會出現 [需要採取動作]。 若要接受更新的合約，請先閱讀連結的合約版本，再選取 [接受合約]。
