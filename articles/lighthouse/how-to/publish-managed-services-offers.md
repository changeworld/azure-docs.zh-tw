---
title: 向 Azure 應用商店發佈託管服務產品/服務
description: 了解如何發佈將客戶上線至 Azure 委派資源管理的受控服務。
ms.date: 04/08/2020
ms.topic: conceptual
ms.openlocfilehash: 4791b1d2ae233b0cc7aad33dd5b15b6ea94b2018
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984549"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>向 Azure 應用商店發佈託管服務產品/服務

> [!IMPORTANT]
> 從 2020 年 4 月 14 日起,我們將開始將託管服務優惠的管理轉移到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[創建新託管服務產品/服務產品/服務/服務中的](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)說明進行管理遷移的優惠。

在本文中,您將瞭解如何使用[雲合作夥伴門戶](https://cloudpartner.azure.com/)將公共或私有託管服務產品發佈到 Azure[應用商店](https://azuremarketplace.microsoft.com)。 然後,購買產品/服務的客戶將能夠將訂閱和資源組用於[Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)。

## <a name="publishing-requirements"></a>發佈需求

您需要[在合作夥伴中心](../../marketplace/partner-center-portal/create-account.md)中擁有有效的帳戶才能創建和發佈產品/服務。 如果您還沒有帳戶,[註冊過程](https://aka.ms/joinmarketplace)將引導您完成在合作夥伴中心創建帳戶並註冊商業市場計劃的步驟。

根據[託管服務提供認證要求](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements),您必須具有[銀雲或金雲平臺能力級別](https://docs.microsoft.com/partner-center/learn-about-competencies)或 Azure 專家[MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能發佈託管服務產品/服務。

您的 Microsoft 合作夥伴網路 (MPN) 識別碼將會與您發佈的供應項目[自動關聯](../../billing/billing-partner-admin-link-started.md) \(部分機器翻譯\)，以追蹤您對客戶參與整體的影響。

> [!NOTE]
> 如果您不想將供應項目發佈至 Azure Marketplace，您可以使用 Azure Resource Manager 範本手動讓客戶上線。 如需詳細資訊，請參閱[讓客戶在 Azure 委派的資源管理中上線](onboard-customer.md)。

發佈受控服務供應項目，類似於將任何其他類型供應項目發佈至 Azure Marketplace。 要瞭解常規發佈過程,請參閱[Azure 應用商店和 AppSource 發表指南](../../marketplace/marketplace-publishers-guide.md)。 您也應檢閱[商業市集認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies)，尤其是[受控服務](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)一節。

客戶新增產品/服務後,他們將能夠委派一個或多個訂閱或資源組,然後這些訂閱或資源組將[註冊用於 Azure 委派的資源管理](#the-customer-onboarding-process)。

> [!IMPORTANT]
> Each plan in a Managed service offer includes a **Manifest Details** section, where you define the Azure Active Directory (Azure AD) entities in your tenant that will have access to the delegated resource groups and/or subscriptions for customers who purchase that plan. 請務必注意,您包括的任何組(或使用者或服務主體)對於購買計劃的每個客戶都將具有相同的許可權。 要配置不同的群組以與每個客戶合作,您需要發表每個客戶獨有的單獨[私人計畫](../../marketplace/private-offers.md)。

## <a name="create-your-offer"></a>建立您的供應項目

1. 登入[雲合作夥伴門戶](https://cloudpartner.azure.com/)。
2. 從左側導覽功能表中，選取 [新增供應項目]****，然後選取 [受管理的服務]****。
3. 您將看到編輯**器**部份,包括要填寫的四個部份:**產品/ 服務設定**,**計劃**,**市場****與支援**。 請繼續閱讀以下內容，以了解如何完成這些區段。

### <a name="enter-offer-settings"></a>輸入供應項目設定

在 [供應項目設定]**** 區段中，提供下列項目：

|欄位  |描述  |
|---------|---------|
|**供應項目識別碼**     | 您供應項目的唯一識別碼 (您的發行者設定檔內)。 此識別碼只能包含小寫英數字元、連字號與底線，且最多 50 個字元。 請記住，客戶可能會在產品 URL 與帳單報表之類的地方看到供應項目識別碼。 一旦發佈供應項目，就無法變更此值。        |
|**發行者識別碼**     | 將會與供應項目相關聯的發行者識別碼。 如果您有多個發行者識別碼，您可以選取要用於此供應項目的識別碼。       |
|**名稱**     | 您的供應項目在 Azure Marketplace 和 Azure 入口網站中，客戶所看到的名稱 (最多 50 個字元)。 使用客戶能理解且容易識別的品牌名稱 - 如果要透過您自己的網站宣傳此供應項目，請務必在此使用完全相同的名稱。        |

完成之後，請選取 [儲存]****。 現在，您可以到 [方案]**** 區段繼續設定。

### <a name="create-plans"></a>建立方案

每個供應項目都必須有一或多個方案 (有時稱為 SKU)。 您可以新增多個方案來支援設定為不同價格的不同功能，或為特定客戶的限定對象自訂特定方案。 客戶可以檢視父代供應項目下可供他們使用的方案。

在 [方案] 區段中，選取 [新增方案]****。 然後輸入 [方案識別碼]****。 此識別碼只能包含小寫英數字元、連字號與底線，且最多 50 個字元。 客戶可能會在產品 URL 和帳單報表之類的地方看到方案識別碼。 一旦發佈供應項目，就無法變更此值。

#### <a name="plan-details"></a>方案詳細資料

完成 [方案詳細資料]**** 區段中的下列區段：

|欄位  |描述  |
|---------|---------|
|**標題**     | 方案所顯示的易記名稱。 長度上限為 50 個字元。        |
|**摘要**     | 顯示在標題下的方案簡短說明。 長度上限是 100 個字元。        |
|**說明**     | 描述文字，提供詳細的方案說明。         |
|**計費模式**     | 這裡顯示 2 個計費模型，但您必須為受控服務供應項目選擇 [自備授權]****。 這表示您會直接向您的客戶收取此供應項目的相關費用，而且 Microsoft 不會向您收取任何費用。   |
|**這是私人計劃嗎?**     | 表明 SKU 為私人或公用。 預設值為 [否]**** (公用)。 如果您不變更此選項，您的方案不會限於特定客戶 (或特定數目的客戶)；發佈公用方案之後，您無法再將它變更為私人方案。 若要使此方案只有特定客戶才能取得，請選取 [是]****。 當您這麼做時，必須提供訂用帳戶識別碼來識別客戶。 您可以逐一輸入 (最多 10 個訂用帳戶)，或上傳 .csv 檔案 (最多 20,000 個訂用帳戶)。 請務必在此包含您自己的訂用帳戶，以便您可以測試及驗證供應項目。 如需詳細資訊，請參閱[私人 SKU 和方案](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) \(部分機器翻譯\)。  |

> [!IMPORTANT]
> 計劃一旦公開發佈,您就無法將其更改為私有。 要控制哪些客戶可以接受您的報價並委派資源,請使用私人計劃。 使用公共計畫時,您無法將可用性限制為特定客戶,甚至限制特定數量的客戶(儘管如果您選擇這樣做,您可以完全停止銷售計劃)。 只有在發佈產品/服務時,在**角色定義**中包含「**授權」** 設定為[「託管服務註冊分配刪除角色」時](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role),才能在客戶接受產品/服務後取消對[委派的存取權限](onboard-customer.md#remove-access-to-a-delegation)。 您也可以聯絡客戶,要求他們[移除您的存取權限](view-manage-service-providers.md#add-or-remove-service-provider-offers)。

#### <a name="manifest-details"></a>資訊清單詳細資料

完成方案的 [資訊清單詳細資料]**** 區段。 這會建立資訊清單，其中包含管理客戶資源的授權資訊。 需要此資訊才能啟用 Azure 委派的資源管理。

> [!NOTE]
> 如先前所述，您**授權**項目中的使用者和角色將適用於購買方案的每位客戶。 如果您想要限制對特定客戶的存取，則必須發佈私人方案以供其專屬使用。

首先，提供資訊清單的 [版本]****。 使用的格式為 *n.n.n* (例如，1.2.5)。

接下來，輸入您的 [租用戶識別碼]****。 此 GUID 與您組織的 Azure Active Directory 租用戶識別碼 (亦即您將會在其中管理客戶資源的租用戶) 相關聯。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄]**** 就能看到它。

最後，將一或多個**授權**項目新增至您的方案。 授權會定義哪些實體能存取購買方案客戶才能存取的資源與訂用帳戶，並指派授與特定存取層級的角色。

> [!TIP]
> 在多數情況下，建議您指派權限給 Azure AD 使用者群組或服務主體，而不是指派給一系列個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。 如需其他建議，請參閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../concepts/tenants-users-roles.md)。

您必須為每個**授權**提供下列項目。 接著，您要新增多少使用者和角色定義，就視需要選取多少次 [新增授權]****。

- **Azure AD 物件 ID**:使用者、使用者組或應用程式的 Azure AD 識別符,這些許可權將被授予客戶資源的某些許可權(如角色定義所述)。
- **Azure AD 物件顯示名稱**:一個友好名稱,用於幫助客戶瞭解此授權的目的。 客戶會在委派資源時看到此名稱。
- **角色定義**:從清單中選擇一個可用的 Azure AD 內建角色。 此角色將會決定 [Azure AD 物件識別碼]**** 欄位中的使用者，對於您的客戶資源會有那些權限。 有關這些角色的說明,請參閱[Azure 委派資源管理的](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)[內建角色](../../role-based-access-control/built-in-roles.md)和角色支援。
  > [!NOTE]
  > 由於適用的新內置角色已添加到 Azure 中,它們將在此處可用,儘管在出現之前可能會有一些延遲。
- **可分配角色**:僅在您在此授權**的角色定義**中選擇「使用者存取管理員」時,才需要此角色。 若是如此，您必須在此新增一或多個可指派的角色。 [Azure AD 物件識別碼]**** 欄位中的使用者將能夠將這些**可指派的角色**指派給[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，[部署可補救的原則](deploy-policy-remediation.md)時需要這些角色。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。 如果您未在此選取一個或多個角色，您的提交將不會通過認證。 (如果未為此使用者的角色定義選擇"使用者存取管理員",則此欄位不起作用。

> [!TIP]
> 為確保在需要時可以[刪除對委派的存取](onboard-customer.md#remove-access-to-a-delegation),請包括授權 **,** 其中**的角色定義**設定為[託管服務註冊分配刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

當您完成這項資訊後，您可以視需要選取 [新增方案]****，以建立其他方案。 當您完成時，選取 [儲存]****，然後到 [Marketplace]**** 區段繼續設定。

### <a name="provide-marketplace-text-and-images"></a>提供 Marketplace 文字與影像

[Marketplace]**** 區段可讓您提供客戶會在 Azure Marketplace 與 Azure 入口網站中看到的文字與影像。

在 [概觀]**** 區段中完成下列欄位：

|欄位  |描述  |
|---------|---------|
|**標題**     |  供應項目的標題，通常是較長的正式名稱。 這個標題會醒目顯示在 Marketplace 中。 長度上限為 50 個字元。 在多數情況下，這應該與您在 [供應項目設定]**** 區段中所提供的 [名稱]**** 相同。       |
|**摘要**     | 您供應項目的用途或功能簡介。 此項目通常顯示在標題底下。 長度上限是 100 個字元。        |
|**完整摘要**     | 您供應項目用途或功能的較長摘要。 長度上限為 256 個字元。        |
|**說明**     | 關於供應項目的詳細資訊。 此欄位的最大長度為 3000 個字元，且支援簡單 HTML 格式設定。 您必須在描述中的某處包含「受控服務」這些字。       |
|**行銷識別碼**     | 適合 URL 的唯一識別碼。 此識別碼只能包含小寫字母數位字元和破折號。 它將用於此優惠的應用商店 URL 中。 例如，如果您的發行者識別碼為 *contoso*，而行銷識別碼是 *sampleApp*，則 Azure Marketplace 中供應項目的 URL 會是 *https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp*。        |
|**預覽訂用帳戶識別碼**     | 可新增一到 100 個訂用帳戶識別碼。 與這些訂用帳戶相關聯的客戶，將能夠在供應項目於 Azure Marketplace 上線之前檢視它。 我們建議您在此包含自己的訂用帳戶，以便您能在供應項目可供客戶取得之前，預覽供應項目在 Azure Marketplace 中的顯示方式。  (Microsoft 支援和工程小組也能在此預覽期間檢視您的供應項目。)   |
|**實用連結**     | 與您供應項目相關的 URL，例如文件、版本資訊、常見問題集等等。        |
|**建議的類別 (最多 5 個)**     | 適用於您供應項目的一或多個類別 (最多五個)。 這些類別可協助客戶在 Azure Marketplace 與 Azure 入口網站中探索您的供應項目。        |

在 [行銷成品]**** 區段中，您可以上傳標誌和要與您供應項目一起顯示的其他資產。 您可以選擇性地上傳螢幕擷取畫面或影片連結，以協助客戶了解您的供應項目。

需要四個徽標大小:**小 (40x40)**、**中等 (90x90)**、**大 (115x115)** 和 寬 **(255x115)**。 您的標誌應遵循這些指導方針：

- Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
- 入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。
- 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
- 標誌的外觀與風格應該是「一般」，且避免使用漸層。 請不要在標誌上使用漸層背景。
- 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。
- 確定標誌不會自動縮放。

**主圖 (815x290)** 標誌是選擇性的，但建議您使用。 如果您包含主圖標誌，請遵循這些指導方針：

- 請勿在主圖標誌中包含任何文字，並請在標誌右側保留 415 像素的空白空間。 這是將會以程式設計方式內嵌的文字元素所需的保留空間：您的發行者顯示名稱、方案標題、供應項目長摘要。
- 主圖標誌的背景不得為黑色、白色或透明。 請確保您的背景色彩不會太淺，因為內嵌文字會顯示為白色。
- 一旦您發佈包含主圖圖示的供應項目，就不能將它移除 (不過您可視需要將它更新為其他版本)。

在 [潛在客戶管理]**** 區段中，您可以選取將會儲存您潛在客戶的 CRM 系統。 請注意，根據[受控服務認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，必須要有**潛在客戶目的地**。

最後，在 [法律]**** 區段中，提供您的 [隱私權原則 URL]**** 與 [使用規定]****。 您也可以在這裡指定此供應項目是否要使用[標準合約](../../marketplace/standard-contract.md) \(部分機器翻譯\)。

請務必先儲存您的變更，再到 [支援]**** 區段繼續設定。

### <a name="add-support-info"></a>新增支援資訊

在 [支援]**** 區段中，提供工程連絡人與客戶支援連絡人的姓名、電子郵件與電話號碼。 您也必須提供支援 URL。 當 Microsoft 有商務和支援相關問題需要連絡您時，我們會使用此資訊。

新增此資訊之後，選取 [儲存]****。

## <a name="publish-your-offer"></a>發佈您的供應項目

完成所有區段之後，下一步是將供應項目發佈至 Azure Marketplace。 選取 [發行]**** 按鈕來起始供應項目上線程序。 如需有關此程序的詳細資訊，請參閱[發行 Azure Marketplace 和 AppSource 供應項目](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md) \(部分機器翻譯\)。

您可以隨時[發佈供應項目的更新版本](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者****](view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以[檢閱變更](view-manage-service-providers.md#update-service-provider-offers)，並決定是否要更新為新的版本。 

## <a name="the-customer-onboarding-process"></a>客戶上線程序

客戶新增您的供應項目之後，他們將能夠[委派一個或多個特定訂用帳戶或資源群組](view-manage-service-providers.md#delegate-resources)，然後讓這些項目上線，以進行 Azure 委派的資源管理。 如果客戶已接受供應項目，但未委派任何資源，則他們會在 Azure 入口網站中[**服務提供者**](view-manage-service-providers.md)頁面上的 [提供者供應項目]**** 區段頂端看到一個備註。

> [!IMPORTANT]
> 委派必須由客戶租戶中的非來賓帳戶完成,該帳戶具有已加入的訂閱[的所有者內置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)(或包含正在裝機的資源組)。 若要查看可委派訂用帳戶的所有使用者，客戶租用戶中的使用者可以在 Azure 入口網站中選取訂用帳戶並開啟 [存取控制 (IAM)]****，然後[查看所有具有「擁有者」角色的使用者](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客戶委派訂閱(或訂閱中的一個或多個資源組 **),Microsoft.託管服務**資源提供程式將註冊該訂閱,租戶中的使用者將能夠根據產品/服務中的授權訪問委派的資源。

## <a name="next-steps"></a>後續步驟

- 瞭解[商業市場](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 前往 Azure 入口網站中的 [我的客戶]****，以[檢視及管理客戶](view-manage-customers.md)。
