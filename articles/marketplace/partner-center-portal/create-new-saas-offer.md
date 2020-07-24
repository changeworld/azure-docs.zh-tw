---
title: 建立 SaaS 供應專案、Azure Marketplace 和 Microsoft AppSource
description: 如何使用 Microsoft 合作夥伴中心的 Microsoft 商業 Marketplace 方案，建立軟體即服務（SaaS）供應專案，以在 Microsoft AppSource、Azure Marketplace 或雲端解決方案提供者（CSP）方案中進行列出或銷售。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 2c5394dce503a6fa00e2a3e6ff73a683d3d2e76f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012088"
---
# <a name="create-a-saas-offer"></a>建立 SaaS 供應項目

若要開始在商業市集中建立軟體即服務 (SaaS) 供應項目，請務必先[建立合作夥伴中心帳戶](./create-account.md)，開啟 [[商業市集儀表板]](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) 並選取 [概觀] 索引標籤。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [商業市集] > [概觀]。
3. 在 [概觀] 頁面上，選取 [+ 新增供應項目] > [軟體即服務]。

   ![說明左側導覽功能表。](./media/new-offer-saas.png)

> [!NOTE]
> 發佈供應項目之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應項目之後出現在店面中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您在此輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且不同於向客戶顯示的供應項目名稱及其他值。
- 在您選取 [建立] 之後，就無法變更供應項目別名。

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-overview"></a>供應項目概觀

[發佈狀態] 會以視覺方式呈現供應項目發佈的必經步驟，以及完成各步驟所需的時間。 未完成的發佈步驟將會呈現灰色圖示。

[供應項目概觀] 功能表內有此供應項目上執行作業的連結清單。 這份動作清單會依據您對供應項目所做的選擇而變更。  

- 如果供應項目為草稿 - 刪除草稿
- 如果供應項目已上線 - 停止銷售供應項目
- 如果供應專案處於預覽階段 - 上線
- 如果您尚未完成發行者登出，請取消發佈

## <a name="offer-setup"></a>供應項目設定

此頁面會要求下列資訊。

- **您想要透過 Microsoft 銷售嗎？** (是/否)
    - **是**，我想要透過 Microsoft 銷售，並且讓 Microsoft 代表我主控交易
    - **否**，我只想要透過市集列出我的供應項目，並獨立處理交易。

### <a name="sell-through-microsoft"></a>透過 Microsoft 銷售

若透過 Microsoft 銷售，能達到更好的客戶探索和收購效果，讓 Microsoft 代表您處理市集交易，並善加利用 Microsoft 在全球各地供應的貿易功能。

#### <a name="saas-offer-requirements"></a>SaaS 供應項目需求

若要在合作夥伴中心上以商業市集列出軟體即服務 (SaaS) 供應項目，必須符合下列準則：

- 您的供應項目必須使用 [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 進行身分識別管理和驗證。
- 您的供應項目必須使用 [SaaS 履行 API](pc-saas-fulfillment-api-v2.md)，才能與 Azure Marketplace 進行整合。

#### <a name="saas-pricing-and-billing-options"></a>SaaS 價格與計費選項

在發行者的 Azure 訂閱上執行 SaaS 解決方案，部署軟體的基礎結構費用即會包含在客戶支付的授權費用內。 Azure 基礎結構使用量是直接由合作夥伴管理及計費。 客戶不會看到實際的基礎結構使用量費用。 發行者需將 Azure 基礎結構使用費用計入軟體授權價格中。

SaaS 供應項目使用計量收費服務，支援以固定費用、每位使用者或使用量計費的按月或按年收費方式。 Microsoft 的商業市集會依代理商模型上運作，藉由這種模型，發行者可設定價格、Microsoft 可向客戶收費，而且 Microsoft 可將收益支付給發行者，同時扣除代理商費用。

這是成本和支出的範例細目，用以示範代理程式模型（任何列出的價格僅供範例之用，而不是為了反映實際成本）：

|**授權成本**|**每月 $100**|
|:---|:---|
|Azure 使用量成本 (D1/1 核心)|直接向發行者收費，不是向客戶收費|
|由 Microsoft 向客戶收取的費用|每月 $100.00 (發行者必須在授權費用中計入任何產生或轉嫁的基礎結構費用)|

|**Microsoft 收取的費用**|**每月 $100**|
|:---|:---|
|Microsoft 向您支付授權成本的 80% <br>**對於符合資格的 SaaS 應用程式，Microsoft 會支付 90% 授權成本*|每月 $80.00 <br>每月 *$* 90.00*|

- 在此範例中，Microsoft 會向客戶收取 $100.00 的軟體授權費用，並支付 $80.00 給發行者。

> [!NOTE]
> **降低 Marketplace 服務費用**–針對您已在商業 Marketplace 上發佈的特定 SaaS 供應專案，microsoft 會將其 Marketplace 服務費用從20% （如 Microsoft 發行者合約中所述）降到10%。 若您的供應專案符合資格，Microsoft 必須將您的供應專案指定為 Azure IP 共同銷售受到激勵。 在每個日曆月份結束前，至少必須符合五（5）個工作天的資格，才能收到該月份的縮減 Marketplace 服務費用。 降低的 Marketplace 服務費用也適用于 Azure IP 共同銷售受到激勵 Vm、受管理的應用程式，以及透過商業 Marketplace 提供的任何其他合格 transactable IaaS 供應專案。

### <a name="list-through-microsoft"></a>透過 Microsoft 列出清單

可藉由建立市集清單，透過 Microsoft 推廣您的業務。 若選擇僅是列出您的供應項目，而不透過 Microsoft 交易，即表示 Microsoft 不會直接參與軟體授權交易。 如此並無相關聯的交易費用，發行者可以 100% 保留向客戶收取的任何軟體授權費用。 不過，發行者要負責支援軟體授權交易的所有層面，包含但不限於：訂購、履行、計量、計費、發票開立、付款與收帳。

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>立即取得 (免費)

提供有效的位址（從*HTTP*或*HTTPs*開始），即可免費向客戶列出您的供應專案，其可透過[使用 Azure Active Directory （Azure AD）](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)）來取得試用版的單一按一下驗證。 例如： `https://contoso.com/saas-app` 。

#### <a name="free-trial-listing"></a>免費試用 (清單)

藉由提供有效的位址（從*HTTP*或*HTTPs*開始），向客戶列出您的供應專案，其可透過[使用 Azure Active Directory （Azure AD）](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)）來取得試用版的單一按一下驗證。 例如： `https://contoso.com/trial/saas-app` 。 供應項目清單免費試用是由您的服務所建立、管理及設定，且沒有由 Microsoft 管理的訂閱。

> [!NOTE]
> 您的應用程式透過試用連結所接收的權杖，只能經由 AD 取得使用者資訊，以便在應用程式中自動建立帳戶。 Microsoft 帳戶 (MSA) 不支援使用此權杖進行驗證。

#### <a name="contact-me"></a>與我連絡

透過連接客戶關係管理 (CRM) 系統收集客戶連絡人資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，連同他們在您的供應項目中找到的供應項目名稱、識別碼和市集來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[潛在客戶管理](#customer-leads)一節。

#### <a name="example-marketplace-offer-listing"></a>範例市集供應項目清單

<!-- ![Example marketplace offer listing with notes](./media/marketplace-offer.svg) -->

以下是如何在 Microsoft AppSource 中顯示供應專案資訊的範例：

:::image type="content" source="media/example-appsource-saas.png" alt-text="說明此供應專案在 Microsoft AppSource 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 大型標誌
2. Categories
3. 產業
4. 支援位址（連結）
5. 使用規定
6. 隱私權原則
7. 供應項目名稱
8. 摘要
9. 描述
10. 螢幕擷取畫面/影片
11. 文件

<br>以下是如何在 Azure 入口網站中顯示供應專案資訊的範例：

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="說明此供應專案在 Azure 入口網站中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 標題
2. 描述
3. 實用的連結
4. 螢幕擷取畫面

## <a name="enable-a-test-drive"></a>啟用試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 [深入了解試用產品](../what-is-test-drive.md)。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

如需詳細資訊，請參閱[在商業市集中提供供應項目試用產品](test-drive.md) (機器翻譯)。

### <a name="test-drive-resources"></a>試用產品資源

- [什麼是試用產品？](../what-is-test-drive.md)
- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概觀](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF；請確定您已關閉快顯封鎖程式)

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>額外的潛在客戶管理資源
- [潛在客戶管理常見問題](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)）
- [常見的潛在客戶設定錯誤](../lead-management-for-cloud-marketplace.md#publishing-config-errors)）
- [潛在客戶管理概觀一頁式巡覽區](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面會請您定義用來在市集上將供應項目分組的類別和產業、支援供應項目的法律合約，以及應用程式版本。

### <a name="category"></a>類別

您的供應專案將會發佈至 AppSource 或 Azure Marketplace，視與您的供應專案相關聯的交易功能和您的類別選擇而定。 如需詳細資訊，請參閱[比較 Microsoft AppSource 和 Azure Marketplace](../comparing-appsource-azure-marketplace.md) 。 選取最符合您供應專案和預期物件的分類和子類別。 選取：

- 至少一個和最多兩個類別，包括主要和次要類別（選擇性）。
- 針對每個主要和/或次要類別，最多可有兩個子類別。 如果您的供應專案沒有適用的子類別，請選取 [**不適用**]。

請參閱[供應專案清單最佳做法](../gtm-offer-listing-best-practices.md)中每個店面適用的類別和子類別的完整清單。

### <a name="industries"></a>產業

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

產業選擇僅適用于發佈至 AppSource 的供應專案。

### <a name="app-version"></a>應用程式版本

這是選填欄位，可在 AppSource 市集中用來識別供應項目的版本號碼。

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業市集的標準合約

Microsoft 提供標準合約範本。

- **是否使用 Microsoft 商業市集的標準合約？**

為了簡化客戶的採購程序，並降低軟體廠商的法律複雜度，Microsoft 會提供為 Microsoft 商業市集的標準合約，以利市集的交易順利進行。 您不必以商業市集發行者的身分製作自訂條款及條件，而可選擇依據標準合約來提供軟體，如此客戶只需審閱並接受一次即可。 您可以在 https://go.microsoft.com/fwlink/?linkid=2041178 找到標準合約。

您可以勾選 [使用商業市集的標準合約] 核取方塊選擇使用標準合約，而不是提供您自己的自訂條款及條件。

![使用 [標準合約] 核取方塊](./media/use-standard-contract.png)

> [!NOTE]
> 當您使用 Microsoft 商業市集的標準合約發佈供應項目之後，就無法使用自己的自訂條款與條件。 這屬於「擇一」的情況。 您可以根據標準合約**或**您自己的條款及條件來提供您的解決方案。 如果您想要修改標準合約的條款，則可透過標準合約增修條款的方式。

#### <a name="standard-contract-amendments"></a>標準合約增修條款

為簡單起見，標準合約增修條款可讓發行者挑選標準合約條款，再配合您的產品或企業自訂條款。 如果客戶已檢閱並接受了 Microsoft 標準合約，就只需要檢閱合約的增修條款。

商業市集發行者有兩種可用的增修條款：

- 通用增修條款：這些增修條款可普遍適用於所有客戶的標準合約。 在購買流程中，供應項目的每個客戶都會看到通用增修條款。 客戶必須先接受標準合約和增修條款，然後才能使用您的供應項目。
- 自訂增修條款：屬於標準合約的特殊增修條款，僅透過 Azure 租用戶識別碼以特定客戶作為目標。 發行者可以選擇想要設為目標的租用戶。 只有租用戶的客戶才會在供應項目的購買流程中看到自訂增修條款。  客戶必須先接受標準合約和增修條款，然後才能使用您的供應項目。

>[!NOTE]
> 這兩種類型的增修條款會彼此堆疊。 自訂增修條款的目標客戶，在購買時也會取得標準合約的通用增修條款。

**Microsoft 商業市集標準合約的通用增修條款** – 請在此方塊中輸入通用修訂條款。 您可以為每個供應項目提供一份通用增修條款。 您可以在此方塊中輸入不限數目的字元。 客戶會在探索和購買流程期間，於 AppSource、Azure Marketplace 和/或 Azure 入口網站看到這些條款。

**Microsoft 商業市集標準合約的自訂增修條款** – 請先選取 [新增自訂增修條款]。 您可以為每個供應項目最多提供 10 份自訂增修條款。

- **自訂增修條款** - 在 [自訂增修條款] 方塊中，輸入您的自訂增修條款。 您可以在此方塊中輸入不限數目的字元。 只有來自您為這些自訂條款指定之租用戶識別碼的客戶，這些條款以及自訂增修條款才會在 Azure 入口網站的供應項目購買流程中顯示。  
- **租用戶識別碼** (必要) – 每份自訂增修條款的目標最多為 20 個租用戶識別碼。 如果您新增自訂的增修條款，必須至少提供一個租用戶識別碼。 租用戶識別碼會識別 Azure 中的客戶。 您可以向客戶要求提供此識別碼，客戶瀏覽至 portal.azure.com > [Azure Active Directory] > [內容] 即可找到此識別碼。 目錄識別碼值是租用戶識別碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在 [[我的 Microsoft Azure 和 Office 365 租使用者識別碼是什麼？](https://www.whatismytenantid.com) ] 中，使用其功能變數名稱 URL 來查詢組織的租使用者識別碼。
- **描述** (選用) – 提供簡單好記的租用戶識別碼描述，有助於識別您設定為增修條款目標的客戶。

#### <a name="terms-and-conditions"></a>條款及條件

若要提供您自己的自訂條款及條件，您可以選擇在條款及條件欄位中加以輸入。 最多可以在此欄位中輸入 10,000 個字元的文字。 如果您的條款與條件需要較長的描述，請在此欄位中輸入連往您條款與條件的單一 URL 連結， 會向客戶顯示為有效連結。

客戶必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

此頁面會顯示您的供應項目可供使用的語言 (和市場)，目前的英文 (美國) 是唯一可用的位置。 此外，此頁面會顯示特定語言清單的狀態，以及新增清單的日期/時間。 您需要定義每個語言/市場的市集詳細資料 (供應項目名稱、描述、搜尋字詞等)。

> [!NOTE]
> 供應項目清單內容 (例如供應項目描述、文件、螢幕擷取畫面、使用條款和隱私權原則) 不一定要使用英文，只需用下列句子做為供應項目描述的開頭即可：「此應用程式僅適用於 [非英文語言]」。 也可以提供實用連結 URL，以此方式以不同於供應項目清單內容所用的語言來提供內容。

### <a name="offer-listings"></a>供應項目清單

提供要在市集中顯示的詳細資料，包括供應項目和行銷資產的說明。

- **名稱** (必要) - 此處定義的名稱，會顯示為您的供應項目在所選市場上的標題。 系統會根據您先前在 [新增供應項目] 的輸入內容，預先填入名稱。 名稱可能是商標， 不能包含表情符號 (除非屬於是商標和著作權符號)，且有 50 個字元的上限。
- **摘要** (必要) - 提供您供應項目的簡短描述，以用於市集清單搜尋結果。 最多可以在此欄位中輸入 100 個字元的文字。
- **描述** (必要) - 提供您供應項目的描述，以顯示在市集清單概觀中。 建議納入價值主張、主要優點、與任何類別或產業的關聯性、應用程式內購買機會、任何必要的公開資訊，以及可供深入瞭解的連結。 最多可以在此欄位中輸入 3,000 個字元的文字，包括標記。 如需其他秘訣，請參閱[撰寫一份出色的應用程式介紹](/windows/uwp/publish/write-a-great-app-description)。
- **搜尋關鍵字** – 輸入最多三個搜尋關鍵字，客戶可以使用這些關鍵字在市集中尋找您的供應項目。
- **使用者入門說明** (必要) - 向潛在客戶說明如何設定並開始使用您的應用程式。  可以在快速入門中加進更詳細的線上文件連結。 最多可以在此欄位中輸入 3,000 個字元的文字。

#### <a name="description"></a>描述

這是必填欄位。

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>連結

- **隱私權原則** (必要) - 連往貴組織的隱私權原則的連結。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則
- **CSP 方案行銷資料** (選擇性) - 如果您選擇將供應項目延伸至[雲端解決方案提供者 (CSP)](../cloud-solution-providers.md) 方案，請提供行銷資料的連結。 CSP 能讓 CSP 合作夥伴搭售、行銷及轉售您的供應項目，將您的供應項目擴大提供給更多符合條件的客戶。 這類轉銷商需求取得您供應項目的行銷資料。 如需詳細資訊，請參閱[進入市場服務](https://partner.microsoft.com/reach-customers/gtm)。
- **實用連結** (選填) - 列出應用程式或相關服務的選擇性補充線上文件，請提供**標題**和 **URL**。 按一下 [+ 新增 URL] 即可新增其他實用連結。

#### <a name="contact-information"></a>連絡人資訊

- **連絡人** – 為每位客戶連絡人提供員工**姓名**、**電話號碼**和**電子郵件**地址 (不會公開顯示)。 **支援連絡人**群組需要**支援 URL** (會公開顯示)。

    - **支援連絡人** (必要) - 適用於一般支援問題。
    - **工程連絡人** (必要) - 適用於技術問題。
    - **通路管理員連絡人** (必要) - 適用於 CSP 方案相關的轉售商問題。

#### <a name="files-and-images"></a>檔案和影像

- **檔**（必要）-為您的供應專案新增相關的行銷檔（PDF 格式），且每個供應專案至少須有三份檔。
- **影像** (選用) - 您供應項目的標誌影像可能會出現在市集各處，需提供以下像素大小的 PNG 格式影像：

    - **小** (48 x 48，必要)
    - **中** (90 x 90，必要)
    - **大** (216 x 216，必要)
    - **寬** (255 x 115)

- **螢幕擷取畫面**（必要）-新增最多五個螢幕擷取畫面，示範您的供應專案，大小為 1280 x 720 圖元。 所有影像都必須是 .PNG 格式。
- **影片** (選用) - 新增用於呈現供應項目的影片連結。 您可以使用 YouTube 和/或 Vimeo 影片的連結，這會隨著您的供應項目顯示給客戶。 您也需要輸入影片的縮圖影像，必須為 1280 x 720 像素的 PNG 格式影像。 每個供應項目最多可以顯示四支影片。

>[!NOTE]
>如果上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

- [市集供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md)

選取 [儲存草稿] 後再繼續。

## <a name="preview-audience"></a>預覽對象

此頁面可讓您定義有限的**預覽對象**，以便在供應項目向範圍更廣的市集客群上線發佈之前，先釋出供應項目。

> [!IMPORTANT]
> 檢查過您的預覽供應項目之後，請選取 [上線]，如此您的供應項目就會上線發佈給市集大眾。

請在每行新增一個 AAD/MSA 帳戶電子郵件，可一併加入選擇性的敘述。

對於既有的 Microsoft 帳戶 (MSA) 或 Azure Active Directory 帳戶，可手動新增最多 10 個電子郵件地址，如果上傳 CSV 檔案則可新增 20 個，以便在供應項目上線發佈前先行驗證。 您可以藉由新增帳戶的方式，在將供應項目發佈到市集前，先定義允許預覽存取供應項目的對象。 如果您的供應項目已上線，您仍然可以定義預覽對象，以測試您供應項目的任何變更或更新。

> [!NOTE]
> 預覽對象與私人對象不同。 您的供應項目上線發佈到市集之前，預覽對象就能取得您的供應項目。 您也可以選擇建立方案，僅供應給私人對象。 在 [方案清單] 索引標籤中，可使用 [這是私人方案] 核取方塊來定義私人對象。 接著可使用 Azure 租用戶識別碼，定義多達 20,000 位客戶的私人對象。

選取 [儲存草稿] 後再繼續。

## <a name="technical-configuration"></a>技術設定

[**技術**設定] 索引標籤會定義 marketplace 用來與您的 SaaS 服務通訊的技術詳細資料。 如果客戶選擇取得和管理您的供應專案，則此連線可讓我們為終端客戶布建供應專案。 

>[!Note]
>在供應專案的詳細資料中設定這些詳細資料之前，您必須先執行與[SaaS 履行 api](./pc-saas-fulfillment-api-v2.md)的整合。

說明收集欄位使用方式的圖表和詳細說明可在[api](./pc-saas-fulfillment-api-v2.md)的檔中取得。

- **登陸頁面 URL** （必要）–定義 SAAS 網站 url （例如： `https://contoso.com/signup` ），客戶會在從 marketplace 取得您的供應專案，並從新建立的 SaaS 訂用帳戶觸發設定程式後，將會居住在那裡。  將會使用 marketplace 購買識別權杖參數來呼叫此 URL，以唯一識別特定終端客戶的 SaaS 購買。  針對對應的 SaaS 訂用帳戶詳細資料，您必須使用[解析](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)API 來交換此權杖。  這些詳細資料和您想要收集的任何其他資訊，都應該用來作為您的體驗內建的客戶互動網頁，以完成終端客戶註冊並啟用其購買。  在此頁面上，使用者應該使用 Azure Active Directory （Azure AD），透過單鍵驗證來註冊。 <br> <br> 當終端客戶從 Azure 入口網站或 M365 系統管理中心啟動受控 SaaS 體驗時，也會呼叫具有 marketplace 購買識別權杖參數的 URL。 當您第一次購買新客戶之後，以及針對管理其 SaaS 的現有客戶提供的權杖時，您應該處理這兩個流程。 <br> <br> 您在此設定的登陸頁面應該啟動並執行24/7。 這是您在 marketplace 中進行新購買的 SaaS 供應專案，或提供之有效訂用帳戶的設定要求時，將會收到通知的唯一方法。

- 連線**webhook** （必要）–針對 Microsoft 需要傳送給您的所有非同步事件（例如，SaaS 訂用帳戶已取消），我們會要求您提供連線 webhook URL。 我們會呼叫此 URL，以在事件時通知您。 <br> <br> 您提供的 webhook 應該啟動並執行24/7，因為這是您將透過 marketplace 購買的客戶 SaaS 訂閱的相關更新通知的唯一方法。 如果您還沒有 webhook 系統，最簡單的設定就是讓 HTTP 端點邏輯應用程式接聽要張貼的任何事件，然後適當地處理（例如， `https://prod-1westus.logic.azure.com:443/work` ）。 如需詳細資訊，請參閱[在邏輯應用程式中透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](../../logic-apps/logic-apps-http-endpoint.md)。

- **Azure AD 租使用者識別碼**（必要）–在 Azure 入口網站內，我們會要求您[建立 Azure Active Directory （AD）應用程式](../../active-directory/develop/howto-create-service-principal-portal.md)，以便驗證這兩個服務之間的連線是在經過驗證的通訊之後。 若要尋找[租使用者識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)），請移至您的 Azure Active Directory 並選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 應用程式識別碼**（必要）–您也需要[應用程式識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要取得其值，請移至您的 Azure Active Directory 並選取 [**應用程式註冊**]，然後尋找列出的**應用程式識別碼**（例如， `50c464d3-4930-494c-963c-1e951d15360e` ）。

>[!Note]
>Azure AD 應用程式識別碼會與您的合作夥伴中心帳戶中的發行者識別碼相關聯。 請確定您在所有供應專案中使用相同的應用程式識別碼。

>[!Note]
>如果發行者在合作夥伴中心有兩個或多個不同的帳戶，則應使用兩個或多個不同的 Azure AD 應用程式識別碼，分別用於其中一個帳戶。 合作夥伴中心內的每個合作夥伴帳戶都應該針對透過此帳戶發佈的所有 SaaS 供應專案，使用唯一的 Azure AD 應用程式識別碼。

選取 [儲存草稿] 後再繼續。

## <a name="plan-overview"></a>方案概觀

此頁面可讓您提供相同供應項目的不同方案選項。 這些方案（先前稱為 Sku）的版本、營收或服務層級可能有所不同。 您必須至少設定一個方案，才能在 marketplace 中銷售您的供應專案。

建立之後，會看到您的方案名稱、識別碼、價格模型、供應狀態 (公用或私人)、目前發佈狀態，以及任何可用的動作。

**方案概觀**中的可用**動作**會依方案的目前狀態而有所不同，可能包括：

- 如果方案狀態為 [草稿] - [刪除草稿]
- 如果方案狀態為 [即時] - [停止銷售方案] 或 [同步私人對象]

**建立新方案** (若選擇經由 Microsoft 銷售，則至少須有一個方案)

- **方案識別碼：** 建立此供應項目中每個方案的專屬方案識別碼。 客戶可以在產品 URL 和 Azure Resource Manager 範本 (如果適用) 中看到此識別碼。 只能使用小寫英數字元、連字號或底線。 此方案識別碼最多允許 50 個字元。 選取 [建立] 之後，就無法修改此識別碼。
- **方案名稱：** 客戶在決定要選擇供應項目的哪一項方案時，會看到此名稱。 建立此供應項目中每個方案的專屬供應項目名稱。 方案名稱是用來區分可能屬於相同供應項目的軟體方案 (例如，供應項目名稱：Windows 伺服器；方案：Windows Server 2016、Windows Server 2019)。

### <a name="plan-listing"></a>方案清單

此頁面可讓您定義方案名稱和描述。 <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **名稱** – 根據您預覽的 [新增方案] 輸入內容預先填入，且會顯示為市集中所見的供應項目「軟體方案」標題。
- **描述** – 此敘述項目可供說明此軟體方案的獨特之處，以及和您供應項目中其他軟體方案的差異所在。 最多 500 個字元。

選取 [儲存草稿] 後再繼續。

#### <a name="pricing-and-availability"></a>價格與可用性

可在此頁面設定要提供此方案的市場、希望的營收模式、價格以及計費期限。 此外，您可以註明是否要讓人人都能看到此方案，或僅限特定客戶 (私人對象) 可見。

#### <a name="markets-optional"></a>市場 (選用)

每個方案必須至少於一個市場供應。 選取 [編輯市場]，然後勾選您想要供應此方案的市場位置核取方塊。 此頁面包含一個搜尋方塊和選項，可用來選取「稅金已匯款」的[國家/地區](tax-details-paid-transactions.md)，其中 Microsoft 免除銷售並代表您使用稅金。

如果您先用美元 (USD) 設定方案價格，然後再新增另一個市場位置，新市場的價格會根據目前的匯率計算。 在發佈之前，請先檢閱每個市場的價格。 儲存變更之後，請使用 [匯出價格 (.xlsx)] 連結查看定價。

再繼續進行前，請先選取 [儲存]。

#### <a name="pricing"></a>定價

##### <a name="pricing-model"></a>定價模式

**固定費率** - 能以每月或每年固定費率價格取得您的供應項目。 有時稱為網站型定價。 使用此計價模式時，您可以選擇定義使用市集計量服務 API 的計量付費方案，以根據非標準單位向客戶收費。  如需計量付費的詳細資訊，請參閱[使用市集計量服務的計量帳單](./saas-metered-billing.md)。  如果您的 SaaS 服務的使用行為是「高載」，您也應該使用此選項。  我們不建議客戶每日或每小時定期切換方案。

**依使用者計費** - 根據存取供應項目或使用基座的使用者人數，計算供應項目的使用費用。 這種以使用者為準的模型，可設定價格內所允許的使用者人數上限和下限。 如此即可設定多個方案，根據使用者人數來設定不同的價格點。  這些欄位為選擇性。 如果未選取此項目，則會將使用者人數視為無限制 (最小值為 1，最多則為系統可支援的人數上限)。 可以更新您的方案時編輯上述欄位。

發佈之後，就無法變更計費的價格模式選項。 此外，相同供應項目的所有方案都必須使用相同的定價模式。

##### <a name="user-limits"></a>使用者限制

在適用情況下，請選取並設定使用者的上限和下限。

##### <a name="billing-term-and-price"></a>計費期限和價格

請選取客戶必須依照所列價格付款的 [期間] 和 [價格]。 至少必須提供每個月或每年的價格，或同時向客戶提供這兩種選項。

以美元設定的價格會在儲存時使用目前的匯率，轉換為所有選定市場的本地貨幣。 在發佈之前，請先匯出價格試算表，並檢閱每個市場的價格，以驗證這些價格。 如果想要在個別市場設定自訂價格，請修改並匯入價格試算表。 您必須負責驗證此價格並擁有這些設定。
*\*您必須先儲存您的價格變更，才能匯出價格資料。*

發行之前，請先仔細檢查您的價格，因為在方案發佈後，所能變更的地方會受到限制：

- 方案發佈之後，就無法變更價格模式。
- 發佈方案的計費期限之後，就無法移除。
- 一旦發佈方案的市場價格發行之後，就無法再變更。

#### <a name="free-trial"></a>免費試用

透過商業 marketplace 提供的 SaaS 供應專案，可讓您在透過 Microsoft 銷售時提供一個月的免費試用版。 計量方案以外的所有帳單模型和條款，均可支援免費試用。 客戶可透過此選項取得一個月的免費存取權，降低入門門檻。  如果選擇在供應項目內啟用方案的免費試用，客戶將無法在第一個月結束前轉換為付費訂閱。  在這段期間，購買供應項目的客戶可以試用任何已啟用免費試用的支援方案，並在方案之間切換。  付費訂閱的轉換會在試用期間結束時自動完成。

>[!NOTE]
>如果客戶選擇轉換為無免費試用的方案，則會開始轉換，但免費試用權限會立即消失。 此外，一旦客戶開始支付方案費用，就無法於相同的訂閱上再次取得免費試用，即使他們轉換為支援免費試用的 SKU 也一樣。

您可以在這裡為供應項目中的每個方案設定免費試用。 選取核取方塊即可允許試用一個月。

![一個月免費試用核取方塊](./media/free-trial-enable.png)

>[!NOTE]
>可交易的供應相簿發佈後，若有免費試用，則無法停用該方案。 請確定第一次發佈時定正確，以免需要重新建立方案。

若要取得目前參與免費試用的客戶訂閱資訊，請使用新的 API 屬性 `isFreeTrial`，會將之標示為 true 或 false。 如需詳細資訊，請參閱 [SaaS Get Subscription API](pc-saas-fulfillment-api-v2.md#get-subscription)。

>[!NOTE]
>使用市集計量服務的方案不支援免費試用。

#### <a name="plan-visibility"></a>方案可視性

您可以將每個方案設定為每個人都可以看見，或僅供您選擇的特定對象使用。 您可以使用 Azure AD 的租用戶識別碼，為這些限制的對象指派成員資格。

##### <a name="privacy"></a>隱私權

選取 [這是私人方案] 會將您的方案設為私人，而且只有您選擇的限制對象可看到。 發佈為私人方案之後，您可以更新對象，也可以選擇將方案提供給所有人。 一旦發行每個人都可以看見的方案，則必須保持每個人都可看到的狀態 (無法再次設定為私人方案)。

##### <a name="restricted-audience-tenant-ids"></a>**限制的對象 (租用戶識別碼)**

指派可存取此私人方案的對象。 請使用租用戶識別碼來指派存取權，並使用選項納入指派給每個租用戶識別碼的描述。 若要匯入 .csv 試算表檔案，最多可以新增 10 個租用戶識別碼，或 20,000 個客戶租用戶識別碼。

租用戶是組織的標記法，其識別碼表示為 GUID (全域唯一識別碼，用來識別資源的128 位整數編號)。 這是組織或應用程式開發人員在與 Microsoft 建立關係時 (例如註冊 Azure、Microsoft Intune 或 Microsoft 365 時) 收到的 Azure AD 專屬執行個體。 每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。 如果要檢查租用戶，請使用您要用來管理應用程式的帳戶登入 Azure 入口網站。 如果您有租用戶，系統會自動將您登入，而且您會在您帳戶名稱正下方看到租用戶名稱。 將滑鼠停留在 Azure 入口網站右上方的帳戶名稱上，以查看您的姓名、電子郵件、目錄/租用戶識別碼 (一個 GUID) 與您的網域。 如果帳戶與多個租用戶相關聯，您可以選取帳戶名稱開啟功能表，以切換租用戶。 每個租用戶都有自己的租用戶識別碼。 您也可以在 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 中，使用網域名稱 URL 來查詢組織的租用戶識別碼。

雖然 SaaS 供應項目會使用租用戶識別碼來定義私人對象，但其他類型可能會使用 Azure 訂閱識別碼 (也會以 Guid 表示)。

> [!NOTE]
> 私人對象 (或有限對象) 與預覽對象不同。 您可以在 **[預覽](#preview-audience)** 頁面上定義預覽對象。 供應項目到市集之發佈上線前，預覽對象可取得您的供應項目。 雖然私人對象指定僅適用於特定方案，預覽對象可以查看所有方案 (私人或非私人)，但僅限在測試和驗證方案時的有限預覽期間。

選取 [儲存草稿] 後再繼續。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>市集供應項目中的方案清單範例

:::image type="content" source="media/marketplace-plan.png" alt-text="包含附注的範例 marketplace 方案清單。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 方案名稱
2. 方案描述

<br>

## <a name="cloud-solution-provider-csp-reseller-audience"></a>雲端解決方案提供者 (CSP) 轉銷商對象

選取在 CSP 方案中提供您的供應項目，讓雲端解決方案提供者銷售您的產品，做為其客戶配套解決方案的一部份。 如需詳細資訊，請參閱[雲端解決方案提供者](https://go.microsoft.com/fwlink/?linkid=2111109)。

## <a name="publish"></a>發佈

完成供應項目的所有必要區段之後，請選取入口網站右上角的 [檢閱並發佈]。

### <a name="submit-offer-to-preview"></a>提交供應項目以供預覽

如果這是您第一次發行此供應項目，您可以：

- 查看供應項目每個區段的完成狀態。
    - **尚未開始** – 此區段需要已完成卻尚未觸及。
    - **不完整** – 此區段中有需要修正的錯誤，或是需要您提供更多詳細資訊。 您將需要回到上一區段並加以更新。
    - **完成** – 此區段已完成，並已提供所有必要的資料且沒有任何錯誤。 供應項目的所有區段皆需要處於完成狀態，您才能提交供應項目。
- 將測試指示提供給認證小組，確保您的應用程式能夠正確地測試，以及有助於瞭解應用程式的任何補充注意事項。
- 若要提交供應項目以進行發佈，請選取 [提交]。 我們會向您傳送一封電子郵件，讓您知道供應項目的預覽版本何時可供檢閱及核准。 返回 [合作夥伴中心]，然後選取 [**上線**]，將您的供應專案發佈到公開（或私人供應專案給私人物件）。

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
