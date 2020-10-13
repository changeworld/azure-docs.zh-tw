---
title: 在 Microsoft 商業市集中建立受控服務供應項目
description: 如何使用合作夥伴中心的商業市集入口網站，為 Azure Marketplace 中的清單建立新的受控服務供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 34af52b32d9e31ad003c5e95e288f88b157a944a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710612"
---
# <a name="create-a-managed-service-offer"></a>建立受控服務供應項目

受控服務供應項目可協助啟用 [Azure Lighthouse](../../lighthouse/overview.md) 案例。 當客戶接受受控服務供應項目時，其就可以將資源上線，以進行 [Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 開始之前，請先[在合作夥伴中心建立商業市集帳戶](create-account.md) \(部分機器翻譯\) (如果您尚未有帳戶)。 請確定您已在商業市集計畫中註冊帳戶。

您必須擁有[銀級或金級雲端平台專長認證等級](https://partner.microsoft.com/membership/cloud-platform-competency)，或必須是 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp)，才能發佈受控服務供應項目。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [受控服務]。

    ![說明左側導覽功能表。](./media/new-offer-managed-service.png)

>[!NOTE]
>供應專案發佈之後，在合作夥伴中心中進行的編輯，只會在重新發佈供應專案之後出現在線上商店中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

* 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
* 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
* 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

* 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
* 在您選取 [建立] 之後，就無法變更供應項目別名。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

根據[受控服務認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) \(英文\)，必須要有**潛在客戶目的地**。 這會在每次客戶部署您的供應項目時，於您的 CRM 系統中建立一筆記錄。

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別，以及支援供應項目的法律合約。

### <a name="category"></a>類別

選取至少一個類別 (最多五個類別)，這些類別可用來將您的供應項目放入適當的市集搜尋區域。 請務必在供應項目描述中凸顯您的供應項目如何支援這些類別。

### <a name="terms-and-conditions"></a>條款及條件

若要提供您自己的法律條款及條件，請在 [條款及條件] 欄位中輸入。 您也可以提供 URL，以便於找到條款及條件。 客戶將必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

此頁面可讓您定義供應項目的市集詳細資料 (例如供應項目名稱、描述和影像)。

> [!NOTE]
> 供應項目清單內容 (例如描述、文件、螢幕擷取畫面與使用規定) 不一定要使用英文，只要供應項目用以下句子描述開頭即可：「此應用程式僅適用於 [非英文語言]」。 也可利用不同於供應項目清單內容中所使用的語言，來提供供應項目內容的「有用連結 URL」。

以下是供應專案資訊出現在 Azure 入口網站中的範例：

:::image type="content" source="media/example-managed-services.png" alt-text="說明這項供應專案如何出現在 Azure 入口網站中。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標題
2. 描述
3. 實用連結
4. 螢幕擷取畫面

### <a name="name"></a>名稱

您在此處輸入的名稱會作為供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會預先填入您為**供應項目別名**輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

### <a name="search-results-summary"></a>搜尋結果摘要

提供您供應項目的簡短描述 (最多 100 個字元)，其可用於市集清單搜尋結果。

### <a name="long-summary"></a>完整摘要

提供較詳細的供應項目描述 (最多 256 個字元)。 這段完整摘要也可以用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織隱私權原則的 URL (裝載於您的網站上)。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

提供有關您解決方案的選擇性補充線上文件。 按一下 [+ 新增連結] 新增其他有用的連結。

### <a name="contact-information"></a>連絡資訊

在此區段中，您必須提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，且可能也會提供給雲端解決方案提供者合作夥伴。

### <a name="support-urls"></a>支援 URL

如果您擁有 **Azure 全球客戶**和/或 **Azure Government 客戶**的支援網站，請在此處提供那些 URL。

### <a name="marketplace-images"></a>Marketplace 映像

在此區段中，您可以提供向客戶顯示供應項目時所要使用的標誌和影像。 所有影像都必須是 .png 格式。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

提供 **大型** 大小標誌的 PNG 檔案。 合作夥伴中心將使用這個來建立 **小型** 和 **中型** 標誌。 您可以選擇性地使用不同的映射取代這些映射。

- 從 216 x 216 到 350 x 350 px 的**大型** (，需要) 
- **中型** (90 x 90 px，選擇性) 
- **Small** (48 x 48 px，選擇性) 

這些標誌可用於清單中的不同位置：

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個螢幕擷取畫面，以顯示供應項目的運作方式。 所有螢幕擷取畫面都必須是 1280 x 720 像素。

#### <a name="videos"></a>影片

您可以選擇新增最多五個示範您供應項目的影片。 這些影片應裝載於 YouTube 和/或 Vimeo 上。 針對每一個影片，輸入影片名稱、URL 和影片的縮圖影像 (1280 x 720 像素)。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

- [市集供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md)

選取 [儲存草稿] 後再繼續。

## <a name="preview"></a>預覽

將供應項目在更廣泛的市集供應項目上線之前，您必須先將其提供給有限的預覽對象。 這可讓您先確認您的供應項目會出現在 Azure Marketplace 中，才能讓客戶使用。 Microsoft 支援和工程小組也能在此預覽期間檢視您的供應項目。

您可以在 [預覽對象] 區段中輸入 Azure 訂用帳戶識別碼，以定義預覽對象。 您可以手動輸入最多 10 個訂用帳戶識別碼，或上傳包含最多 100 個訂用帳戶識別碼的 .csv 檔案。

與這些訂用帳戶相關聯的任何客戶，將能夠在供應項目於 Azure Marketplace 上線之前加以檢視。 請務必在此包含您自己的訂用帳戶，以便您可以預覽您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="plan-overview"></a>方案概觀

每個供應專案都必須有一或多個方案 (先前稱為 Sku) 。 您可以新增多個方案來支援設定為不同價格的不同功能，或為特定客戶的限定對象自訂特定方案。 客戶可以檢視父代供應項目下可供他們使用的方案。

您最多可以為每個供應專案建立100個方案：這些供應專案的最高45個都可以是私用。 深入瞭解 [Microsoft 商業市場中私](../private-offers.md)用供應專案的私人方案。

在 [方案概觀] 頁面上，選取 [+ 建立新方案]。 然後，輸入**方案識別碼**和**方案名稱**。 這兩個值都只能包含小寫英數字元、連字號與底線，且最多 50 個字元。 這些值可能會向客戶顯示，而且在您發佈供應項目之後，即無法變更。

當您輸入這些值以繼續處理您的方案時，請選取 [建立]。 有三個區段需要完成：**方案清單**、**定價和可用性**，以及**技術設定**。

### <a name="plan-listing"></a>方案清單

首先，提供方案的**搜尋結果摘要**。 這是您方案的簡短描述 (最多 100 個字元)，可用於市集搜尋結果。

接下來，輸入**描述**，以提供更詳細的方案說明。

### <a name="pricing-and-availability"></a>價格與可用性

目前，只有一個可用於受控服務供應項目的定價模型：**自備授權 (BYOL)** 。 這表示您會直接向您的客戶收取此供應項目的相關費用，而且 Microsoft 不會向您收取任何費用。

[方案可視性] 區段可讓您指出此方案是否應為[私人](../../marketplace/private-offers.md)。 如果您未核取 [這是私人方案] 方塊，則您的方案將不限於特定客戶 (或特定數目的客戶)。

> [!NOTE]
> 透過雲端解決方案提供者 (CSP) 計畫的轉銷商所建立的訂用帳戶不支援私人方案。

若要使此方案只有特定客戶才能取得，請選取 [是]。 當您這麼做時，必須提供訂用帳戶識別碼來識別客戶。 您可以逐一輸入 (最多 10 個訂用帳戶)，或上傳 .csv 檔案 (在所有方案中，上限為 10,000 個)。 請務必在此包含您自己的訂用帳戶，以便您可以測試及驗證供應項目。

> [!IMPORTANT]
> 當您將某個方案發佈為公用之後，便無法再將其變更為私人。 若要控制哪些客戶可以接受您的供應項目和委派資源，請使用私人方案。 有了公用方案，您無法將可用性限制為僅供某個客戶甚至是特定數目的客戶使用，不過您可以選擇完全停止銷售該方案。 客戶接受供應項目後，您可以[移除委託的存取權](../../lighthouse/how-to/remove-delegation.md) (僅在您發布該供應項目時包含 [角色定義] 設定為[受控服務註冊分配刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的 [授權])。 您也可以與客戶聯繫，並要求其[移除您的存取權](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技術設定

您方案的此區段會建立資訊清單，其中包含管理客戶資源的授權資訊。 需要此資訊才能啟用 [Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。

請務必檢閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解支援哪些角色，以及定義授權的最佳做法。

> [!NOTE]
> 如先前所述，您**授權**項目中的使用者和角色將適用於購買方案的每位客戶。 如果您想要限制對特定客戶的存取，則必須發佈私人方案以供其專屬使用。

#### <a name="manifest"></a>資訊清單

首先，提供資訊清單的 [版本]。 使用的格式為 *n.n.n* (例如，1.2.5)。

接下來，輸入您的 [租用戶識別碼]。 此 GUID 與您組織 (亦即您將會在其中存取客戶資源的管理租用戶) 的 Azure Active Directory (Azure AD) 租用戶識別碼相關聯。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄] 就能看到它。

如果您發佈新版本的供應項目，而且需要建立已更新的資訊清單，請選取 [+ 新增資訊清單]。 請務必增加前一個資訊清單版本的版本號碼。

#### <a name="authorization"></a>授權

授權會定義管理租用戶中哪些實體能存取購買方案客戶才能存取的資源與訂用帳戶。 每個實體都會指派一個內建角色，該角色授與特定層級的存取權。

您最多可以為每個方案建立二十個授權。

> [!TIP]
> 在多數情況下，建議您指派角色給 Azure AD 使用者群組或服務主體，而不是指派給一系列個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。 將角色指派給 Azure AD 群組時，[請確定 [群組類型] 是 [安全性]，而不是 [Office 365]](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 如需其他建議，請參閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md)。

您必須為每個**授權**提供下列項目。 接著，您要新增多少使用者和角色定義，就視需要選取多少次 [+ 新增授權]。

- **Azure AD 物件識別碼**：使用者、使用者群組或應用程式的 Azure AD 識別碼，系統將會授與其您客戶資源的特定權限 (按角色定義所定義)。
- **Azure AD 物件顯示名稱**：協助客戶了解此授權用途的易記名稱。 客戶會在委派資源時看到此名稱。
- **角色定義**：從清單中選取其中一個可用的 Azure AD 內建角色。 此角色將會決定 [Azure AD 物件識別碼] 欄位中的使用者，對於您的客戶資源會有那些權限。 如需這些角色的說明，請參閱 Azure Lighthouse 的 [內建角色](../../role-based-access-control/built-in-roles.md) 和 [角色支援](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)。
  > [!NOTE]
  > 當適用的新內建角色新增至 Azure 時，將會在這裡加以提供，雖然其出現之前可能會有所延遲。
- **可指派的角色**：如果您為此授權在 [角色定義] 中選取 [使用者存取系統管理員]，才會顯示此選項。 若是如此，您必須在此新增一或多個可指派的角色。 [Azure AD 物件識別碼] 欄位中的使用者將能夠將這些角色指派給[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，[部署可補救的原則](../../lighthouse/how-to/deploy-policy-remediation.md)時需要這些角色。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。

> [!TIP]
> 若要確保您可以[移除委派的存取權](../../lighthouse/how-to/remove-delegation.md) (如有需要)，請包含 [角色定義] 設定為[受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的 [授權]。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

當您完成方案的所有區段後，您可以視需要選取 [+ 建立新方案]，以建立其他方案。 完成時，選取 [儲存]。

選取 [儲存草稿] 後再繼續。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應項目以供預覽

當您完成供應項目的所有必要區段之後，請選取入口網站右上角的 [檢閱並發佈]。

如果這是您第一次發佈此供應項目，您可以：

- 查看供應項目每個區段的完成狀態。
  - **尚未開始** - 此區段需要已完成卻尚未觸及。
  - **不完整** - 此區段中有需要修正的錯誤，或是需要您提供更多詳細資訊。 回到上一區段並加以更新。
  - **完成** - 此區段已完成，並已提供所有必要的資料且沒有任何錯誤。 供應項目的所有區段皆需為完成狀態，您才能提交供應項目。
- 在 [認證注意事項] 區段中，請將測試指示提供給認證小組，以確保您的應用程式已正確測試，並加上有助於了解應用程式的任何補充注意事項。
- 若要提交供應項目以進行發佈，請選取 [提交]。 當供應項目的預覽版本可供檢閱及核准時，我們會向您傳送一封電子郵件。 若要將您的供應項目公開發佈 (如果是私人供應項目則向私人對象發佈)，請回到 [合作夥伴中心]，然後選取 [上線]。

### <a name="customer-experience-and-offer-management"></a>客戶體驗與供應項目管理

當客戶部署您的供應項目時，其將能夠委派訂用帳戶或資源群組，以進行 [Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 如需此程序的詳細資訊，請參閱[客戶上線程序](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

您可以隨時[發佈供應項目的更新版本](update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者](../../lighthouse/how-to/view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以檢閱變更，並決定是否要更新為新的版本。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
- [了解 Azure Lighthouse](../../lighthouse/overview.md)
