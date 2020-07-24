---
title: 在 Azure Marketplace 上建立 Azure 虛擬機器供應項目
description: 瞭解如何使用所需的方案，在 Azure Marketplace 上建立虛擬機器供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: mingshen
ms.date: 07/17/2020
ms.openlocfilehash: 6df1620f2d1097ac23e5dfa3903fefc74d13fa65
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012020"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>在 Azure Marketplace 上建立 Azure 虛擬機器供應項目

此文章描述如何建立 Azure 虛擬機器供應項目，並將其發佈至 [Azure Marketplace](https://azuremarketplace.microsoft.com/)。 其將同時處理 Windows 型和 Linux 型虛擬機器，其中包含作業系統、虛擬硬碟 (VHD)，以及多達 16 個資料磁碟。 

在開始之前，請[在合作夥伴中心建立商業 Marketplace 帳戶](create-account.md) \(部分機器翻譯\)。 請確定您的帳戶已在商業 Marketplace 計畫中註冊。

## <a name="introduction"></a>簡介

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>發佈至 Azure Marketplace 的優點

當您在 Azure Marketplace 上發佈供應項目時，可以：

- 利用 Microsoft 品牌來推廣您的公司。
- 接觸超過 1 億個 Office 365 和 Dynamics 365 使用者以及超過 20 萬個組織。
- 從這些市集取得高品質潛在客戶。
- 讓 Microsoft 現場銷售和電話銷售團隊推廣您的服務。

### <a name="before-you-begin"></a>開始之前

如果您還未這麼做，請檢閱[虛擬機器供應項目發佈指南](../marketplace-virtual-machines.md) \(部分機器翻譯\) 和此 Azure 虛擬機器資料：

- 快速入門指南
  - [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入門範本](https://github.com/azure/azure-quickstart-templates) \(英文\)
- 教學課程
  - [Linux VM](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../../virtual-machines/windows/tutorial-manage-vm.md)
- 範例
  - [適用於 Linux 虛擬機器的 Azure CLI 範例](../../virtual-machines/linux/cli-samples.md) \(部分機器翻譯\)
  - [適用於 Linux 虛擬機器的 Azure PowerShell](../../virtual-machines/linux/powershell-samples.md) \(部分機器翻譯\)
  - [適用於 Windows 虛擬機器的 Azure CLI 範例](../../virtual-machines/windows/cli-samples.md) \(部分機器翻譯\)
  - [適用於 Windows 虛擬機器的 Azure PowerShell](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>技術知識的基本概念

設計、建置和測試供應項目的程序需要一些時間，而且同時需要建置供應項目所使用之 Azure 平台和技術方面的專業知識。

您的工程小組應具備下列 Microsoft 技術的基本了解和運用知識：

- [Azure 服務](https://azure.microsoft.com/services/)
- [Azure 應用程式的設計和架構](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左窗格上，選取 [商業 Marketplace] > [概觀]。
3. 在 [概觀] 頁面上，選取 [新增供應項目] > [Azure 虛擬機器]。

    ![顯示左窗格功能表選項和 [新增供應項目] 按鈕的螢幕擷取畫面。](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 供應項目發佈之後，您在合作夥伴中心對其進行的任何編輯，只有在您重新發佈供應項目之後，才會出現在 Azure Marketplace 上。 變更供應項目之後，請務必一律重新發佈該供應項目。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在 Azure Marketplace 供應項目和 Azure PowerShell 與 Azure CLI 的網址中看到此識別碼 (如果適用)。
- 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 供應項目別名是用於合作夥伴中心內供應項目的名稱。

- Azure Marketplace 上不使用此名稱。 其與向客戶顯示的供應項目名稱和其他值不同。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

### <a name="test-drive"></a>試用產品

*試用產品*是向潛在客戶展示供應項目的絕佳方式。 其可讓潛在客戶選擇「購買前先試用」，這有助於增加轉換並產生最適合的潛在客戶。 如需詳細資訊，請參閱[什麼是試用產品？](../what-is-test-drive.md)\(部分機器翻譯\)。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

其他試用產品資源：

- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [行銷最佳做法](../what-is-test-drive.md)
- [下載試用產品概觀](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF 檔案 (請確定您的快顯封鎖程式已關閉)。

### <a name="customer-leads"></a>潛在客戶

當您使用合作夥伴中心將供應項目發佈到商業 Marketplace 時，將其連線到客戶關係管理 (CRM) 系統。 這可讓您在有人對您的產品感興趣或使用您的產品時，立即收到客戶連絡人資訊。 如果您想要啟用試用產品 (請參閱上一節)，則需要連線到 CRM。 否則，可以選擇連線到 CRM。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > 如果此處未列出您的 CRM 系統，請使用 [Azure 資料表儲存體](commercial-marketplace-lead-management-instructions-azure-table.md)或 [HTTPS 端點](commercial-marketplace-lead-management-instructions-https.md)儲存您的潛在客戶資料。 接著，將資料匯出至您的 CRM 系統。

1. 在合作夥伴中心內發佈時，將供應項目連線到潛在客戶目的地。
1. 確認已正確設定與潛在客戶目的地的連線。 在合作夥伴中心發佈之後，Microsoft 會驗證連線並將測試潛在客戶傳送給您。 在上架前預覽供應項目時，您也可以嘗試在預覽環境中自行部署供應項目，藉此測試您的潛在客戶連線。
1. 確認潛在客戶目的地的連線保持在最新狀態，您就不會失去任何潛在客戶。

1. 選取 [儲存草稿]，然後再繼續。

## <a name="properties"></a>屬性

在 [**屬性**] 頁面上，您可以定義用來將供應專案分組的類別，Azure Marketplace、應用程式版本，以及支援供應專案的法律合約。

### <a name="category"></a>類別

選取 [類別] 和 [子類別]，將您的供應專案放在適當的 marketplace 搜尋區域。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和最多兩個類別，包括主要和次要類別（選擇性）。
- 針對每個主要和/或次要類別，最多可有兩個子類別。 如果您的供應專案沒有適用的子類別，請選取 [**不適用**]。

請參閱[供應專案清單最佳作法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。 虛擬機器供應專案一律會出現在 Azure Marketplace 上的 [**計算**] 類別之下。

### <a name="legal"></a>法律

您必須為您的客戶提供供應項目條款及條件。 您有兩個選擇：

- **使用您自己的條款及條件**

   若要提供您自己的自訂條款及條件，請在 [條款及條件] 方塊中輸入最多 10,000 個字元的文字。 如果您需要更長的描述，請針對您的條款及條件輸入單一網址。 這將會向客戶顯示為作用中連結。

   客戶必須先接受這些條款，才能試用您的供應項目。

- **使用 Microsoft 商業 Marketplace 的標準合約**

   為了簡化客戶的採購程序，並降低軟體廠商的法律複雜度，Microsoft 為商業 Marketplace 提供了標準合約。 當您根據標準合約提供軟體時，客戶只需要閱讀並接受一次，而且您不需要建立自訂的條款及條件。

   若要使用標準合約，請選取 [使用 Microsoft 商業 Marketplace 的標準合約] 核取方塊，然後在快顯視窗中選取 [接受] (您可能必須向上捲動才能加以檢視)。

   ![顯示 [合作夥伴中心] 內 [法律] 窗格，以及 [使用 Microsoft 商業 Marketplace 的標準合約] 核取方塊的螢幕擷取畫面。](media/use-standard-contract.png)

  > [!NOTE]
  > 當您使用商業 Marketplace 的標準合約發佈供應項目之後，就無法使用自己的自訂條款及條件。 您可以根據標準合約或您自己的條款及條件來提供解決方案。

  如需詳細資訊，請參閱 [Microsoft 商業 Marketplace 的標準合約](../standard-contract.md) \(部分機器翻譯\)。 下載[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF 檔案 (請確定您的快顯封鎖程式已關閉)。

  **標準合約增修條款**

  為簡單起見，標準合約增修條款可讓您選取標準合約條款，並為您的產品或企業建立條款。 只有在客戶已經檢閱並接受 Microsoft 標準合約之後，才需要檢閱合約的增修條款。 增修條款有兩種類型：

  * **通用增修條款**：這些增修條款可普遍適用於所有客戶的標準合約。 在購買流程中，供應項目的每個客戶都會看到通用增修。 客戶必須先接受標準合約和增修條款，然後才能使用您的供應項目。 您可以為每個供應項目提供一個通用增修。 您可以在此方塊中輸入不限數目的字元。 客戶會在探索和購買流程期間，於 AppSource、Azure Marketplace 和/或 Azure 入口網站看到這些條款。

  * **自訂增修條款**：標準合約的這些特殊增修條款是透過 Azure 租用戶識別碼，以特定客戶作為目標。 您可以選擇想要設定為目標的租用戶。 只有租用戶的客戶才會在供應項目的購買流程中看到自訂增修條款。 客戶必須先接受標準合約和增修條款，然後才能使用您的供應項目。

    1. 從選取 [新增自訂增修條款 (最多 10 個)] 開始。 您可以為每個供應項目最多提供 10 個自訂增修條款。 執行下列動作：

       a. 在 [自訂增修條款] 方塊中，輸入您自己的增修條款。 您可以輸入不限數目的字元。 只有來自您為這些自訂條款指定之租用戶識別碼的客戶，才會在 Azure 入口網站的供應項目購買流程中看到這些條款。

       b. (必要) 提供**租用戶識別碼**。 每個自訂增修的目標最多可達 20 個租用戶識別碼。 如果您新增自訂增修，必須至少提供一個租用戶識別碼，以便在 Azure 中識別您的客戶。 您的客戶可以在 Azure 中選取 [Azure Active Directory] > [屬性]，以便為您找到此功能。 目錄識別碼值是租用戶識別碼 (例如，50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在[我的 Microsoft Azure 和 Office 365 租用戶識別碼是什麼？](https://www.whatismytenantid.com/) \(英文\) 中，使用其網域名稱網址，尋找客戶的組織租用戶識別碼。

       c. (選用) 為租用戶識別碼提供易記的**描述**，其可協助您識別您設定為增修目標的客戶。

        > [!NOTE]
        > 這兩種類型的增修條款會彼此配對。 以自訂增修條款為目標的客戶也會在購買期間取得標準合約的通用增修條款。

    1. 選取 [儲存草稿]，然後再繼續。

## <a name="offer-listing"></a>供應項目清單

在 [供應項目清單] 頁面上，您可以定義供應項目詳細資料，例如供應項目名稱、描述、連結和連絡人。

> [!NOTE]
> 您的供應專案清單內容（例如描述、檔、螢幕擷取畫面和使用規定）不需要英文，只要供應專案的描述開頭為「此應用程式僅適用于」片語 \<non-English language> 。 您也可以針對以不同於供應項目清單內容中所使用的語言提供內容的網站，提供連結 URL。

### <a name="marketplace-details"></a>Marketplace 詳細資料

#### <a name="name"></a>名稱

您在此處輸入的名稱會當作供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會自動填入您在 [供應項目別名] 方塊中輸入的名稱。 您稍後可以變更此名稱。 名稱：

- 可以是商標。 您可以包含商標和著作權符號。
- 不得包含超過 50 個字元。
- 不可包含表情圖示。

#### <a name="search-results-summary"></a>搜尋結果摘要

提供供應項目的簡短描述，以顯示在 Azure Marketplace 搜尋結果中。 簡短描述最多可以包含 100 個字元。

#### <a name="long-summary"></a>完整摘要

提供供應項目的完整描述，以顯示在 Azure Marketplace 搜尋結果中。 完整描述最多可以包含 256 個字元。

#### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織隱私權原則的網址 (URL)。 確保您的供應項目符合隱私權法律及規定。 您也必須在網站上張貼有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

提供有關您供應項目的補充線上文件。 若要新增連結，請選取 [新增連結]，然後完成下列欄位：

- **Name**：客戶將會在 [詳細資料] 頁面上看到名稱。
- **連結 (URL)** ：輸入可讓客戶檢視線上文件的連結。

### <a name="customer-support-links"></a>客戶支援連結

提供支援網站，讓客戶可以連絡您的支援小組。

- Azure 全球支援網站
- Azure Government 支援網站

### <a name="partner-support-contact"></a>合作夥伴支援連絡人

提供連絡人資訊，讓 Microsoft 合作夥伴在您的客戶開立支援票證時使用。 Azure Marketplace 上不會列出此資訊。

- 名稱
- 電子郵件
- 電話

### <a name="engineering-contact"></a>工程連絡人

提供當您的供應項目有問題 (包括認證的問題) 時，Microsoft 所要使用的連絡人資訊。 Azure Marketplace 上不會列出此資訊。

- 名稱
- 電子郵件
- 電話

### <a name="azure-marketplace-media"></a>Azure Marketplace 媒體

提供要搭配您供應項目使用的標誌和影像。 所有影像都必須採用 PNG 格式。 模糊影像將會導致您的提交遭到拒絕。

>[!NOTE]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="azure-marketplace-logos"></a>Azure Marketplace 標誌

請以下列四個影像尺寸，提供供應項目標誌的 PNG 檔案：

- **小** (48 &times; 48 像素)
- **中** (90 &times; 90 像素)
- **大** (216 &times; 216 像素)
- **寬** (255 &times; 115 像素)

全部四個標誌都是必要的，而且會顯示在各種 Azure Marketplace 清單中。

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個顯示供應項目運作方式的螢幕擷取畫面。 每個螢幕擷取畫面的大小都必須為 1280 &times; 720 像素且採用 PNG 格式。 每個螢幕擷取畫面都必須包含標題。

#### <a name="videos"></a>影片

新增最多五個示範您供應項目的影片。 影片應該在外部影片服務上託管。 輸入影片的名稱、網址，以及影片的縮圖 PNG 影像 (大小為 1280 &times; 720 像素)。

如需其他 Marketplace 清單資源，請參閱 [Marketplace 供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md) \(部分機器翻譯\)。

選取 [儲存草稿]，然後再繼續。

## <a name="preview"></a>預覽

選取 [預覽] 索引標籤，然後選取有限的 [預覽對象] 來驗證您的供應項目，再將其即時發佈給更廣泛的商用 Marketplace 對象。

> [!IMPORTANT]
> 在 [預覽] 窗格上檢查供應項目之後，選取 [上線]，為商業 Marketplace 公開對象發佈您的供應項目。

您的預覽對象是以 Azure 訂用帳戶識別碼 GUID 識別，且每個都有一個選擇性描述。 客戶無法看到這些欄位。 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到 Azure 訂用帳戶識別碼。

分別 (最多 10 個識別碼) 或藉由上傳 CSV 檔案 (最多 100 個識別碼)，新增至少一個 Azure 訂用帳戶識別碼。 您可以藉由新增這些訂用帳戶識別碼，定義哪些人員可以在即時發佈之前預覽您的供應項目。 如果您的供應項目已上線，您仍然可以定義預覽對象，以測試您供應項目的變更或更新。

> [!NOTE]
> 預覽對象與私人對象不同。 預覽對象可以在供應項目即時發佈到 Azure Marketplace 上_之前_先存取。 預覽對象可以查看並驗證所有方案，包括只有在您的供應項目完全發佈到 Azure Marketplace 之後，才供私人對象使用的方案。 私人對象 (在方案的 [定價和可用性] 窗格上定義) 對特定方案具有獨佔式存取權。

選取 [儲存草稿]，再繼續進行下一節。

## <a name="plan-overview"></a>方案概觀

您可以在合作夥伴中心的相同供應項目中提供各種方案選項。 供應專案至少需要一個方案（先前稱為 SKU），這可能會因營收物件、Azure 區域、功能或 VM 映射而異。

建立方案之後，選取 [方案概觀] 索引標籤以顯示：

- 方案名稱
- 授權模型
- 對象 (公開或私人)
- 目前的發佈狀態
- 可用動作

[方案概觀] 窗格上可用的動作會根據方案的目前狀態而有所不同。

- 如果方案狀態為 [草稿]，選取 [刪除草稿]。
- 如果方案狀態是 [即時發佈]，選取 [停止銷售方案] 或 [同步私人對象]。

### <a name="create-a-new-plan"></a>建立新的方案

選取最上方的 [建立新方案]。 [新增方案] 對話方塊隨即出現。

在 [方案識別碼] 方塊中，為此供應項目中的每個方案建立唯一的方案識別碼。 客戶將可在產品網址中看到此識別碼。 僅使用小寫字母和數字、連字號或底線，以及最多 50 個字元。

> [!NOTE]
> 選取 [建立] 之後，即無法變更方案識別碼。

在 [方案名稱] 方塊中，輸入此方案的名稱。 當客戶決定要在您的供應項目內選取哪一個方案時，會看到此名稱。 建立可清楚指出方案之間差異的唯一名稱。 例如，您可以使用 [隨用隨付]、[BYOL]、[進階] 和 [企業] 方案，進入 **Windows Server**。

選取 [建立]。

### <a name="plan-setup"></a>方案設定

設定方案類型的高階設定、指定是否重複使用另一個方案中的技術設定，以及識別應該提供方案的 Azure 區域。 您在這裡所做的選擇會決定要在相同方案的其他窗格上顯示哪些欄位。

#### <a name="reuse-a-technical-configuration"></a>重複使用技術設定

如果您有多個相同類型的方案，而且這些方案之間的套件相同，則可以選取 [此方案重複使用另一個方案的技術設定]。 此選項可讓您為此供應項目選取另一個相同類型方案，並可讓您重複使用其技術設定。

> [!NOTE]
> 當您重複使用另一個方案的技術設定時，整個 [技術設定] 索引標籤都會從此方案中消失。 另一個方案中的技術設定詳細資料 (包括您在之後所做的任何更新) 也會用於此方案。 方案發佈之後即無法變更此設定。

#### <a name="azure-regions"></a>Azure 區域

您的方案必須至少在一個 Azure 區域中提供。

選取 [Azure Global] 選項，讓您的方案可供所有 Azure 全球區域中具有商業 Marketplace 整合的客戶使用。 如需詳細資訊，請參閱[地理可用性和貨幣支援](../marketplace-geo-availability-currencies.md) \(部分機器翻譯\)。

選取 [Azure Government] 選項，讓您的方案可用於 [Azure Government](../../azure-government/documentation-government-welcome.md) \(英文\) 區域。 此區域為美國聯邦、州、地方或部落實體的客戶，以及符合資格可以為這些實體提供服務的合作夥伴，提供控管權限。 身為發行者的您必須負責任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。

在您發佈至 [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md) 之前，請先在環境中測試並驗證您的方案，因為部分某些可能會有所不同。 若要設定並測試您的方案，請從 [Microsoft Azure 政府機構試用版](https://azure.microsoft.com/global-infrastructure/government/request/)網頁要求試用帳戶。

> [!NOTE]
> 在您的方案發佈並可在特定 Azure 區域中使用之後，您就無法移除該區域。

#### <a name="azure-government-certifications"></a>Azure Government 認證

只有當您在上一節中選取 [Azure Government] 作為 [Azure 區域] 時，才會看到此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以是直接指向程式清單的連結，或是指向您自己網站上相關合規性描述的連結。 只有 Azure Government 客戶可以看到這些連結。

選取 [儲存草稿]，然後再繼續。

### <a name="plan-listing"></a>方案清單

在此節中，您要設定方案的清單詳細資料。 此窗格會顯示特定的資訊，這可能會與相同供應項目中的其他方案不同。

#### <a name="plan-name"></a>方案名稱

此欄位會自動填入您在建立方案時提供給您方案的名稱。 這個名稱在 Azure Marketplace 上會顯示為此方案的標題。 其限制為 100 個字元。

#### <a name="plan-summary"></a>方案摘要

提供您方案 (非供應項目) 的簡短摘要。 此摘要限制為 100 個字元。

#### <a name="plan-description"></a>方案描述

描述此軟體方案獨一無二之處，以及供應項目內方案之間的任何差異。 僅描述方案，而非供應項目。 方案描述最多可包含 2,000 個字元。

選取 [儲存草稿]，然後再繼續。

### <a name="pricing-and-availability"></a>價格與可用性

在此窗格上，您可以設定：

- 此方案可供使用的市場。
- 每小時的價格。
- 是否要讓每個人都能看到此方案，或僅特定客戶 (私人對象) 看得到。

#### <a name="markets"></a>市場

每個方案必須至少可用於一個市場。 選取此方案應可供購買所在每個市場位置的核取方塊 (這些市場中的使用者仍然可以將供應項目部署到您在[「方案設定」](#plan-setup)一節中所選取的所有 Azure 區域)。[已繳交稅金] 按鈕會顯示 Microsoft 代您繳納銷售與使用稅的國家/地區。 「發佈至中國」僅限於*免費*或*自備授權* (BYOL) 的方案。

如果您已經用美元 (USD) 貨幣設定方案價格，並新增另一個市場位置，則新市場的價格會根據目前的匯率計算。 在您發佈之前，請務必先檢閱每個市場的價格。 在儲存變更之後，選取 [匯出價格 (xlsx)] 以檢閱您的定價。

當您移除市場時，該市場中使用作用中部署的客戶將無法建立新的部署，或擴大其現有的部署。 現有的部署不會受到影響。

#### <a name="pricing"></a>定價

針對 [授權模型]，選取 [依使用量計算的每月計費方案]，以設定此方案的定價，或選取 [自備授權]，讓客戶搭配其現有的授權使用此方案。

針對依使用量計算的每月計費方案，請使用下列三個定價輸入選項之一：

- **每個核心**：提供每個核心的定價 (美元)。 Microsoft 會計算每個核心大小的定價，並使用目前的匯率，將其轉換為當地貨幣。
- **每個核心大小**：提供每個核心大小的定價 (美元)。 Microsoft 會計算定價，並使用目前的匯率，將其轉換為當地貨幣。
- **每個市場和核心大小**：為所有市場提供每個核心大小的定價。 您可以從試算表匯入價格。

> [!NOTE]
> 儲存定價變更，以啟用匯出的定價資料。 在您的方案中發佈市場價格之後，之後就無法進行變更。 若要在發佈之前確保價格正確，請匯出定價試算表，並檢閱每個市場的價格。

#### <a name="free-trial"></a>免費試用

您可以為您的客戶提供一個月或三個月的*免費試用*。

#### <a name="visibility"></a>可見性

您可以設計每個方案，讓每個人都可以看到，或只有預先選取的對象看得到。 使用 Azure 訂用帳戶識別碼指派此有限對象中的成員資格。

**公用**：每個人都可以看到您的方案。

**私人對象**：讓您的方案變成只有預先選取的對象看得到。 將其發佈為私人方案之後，您可以更新對象或將其變更為公用。 在您將方案設定為公用之後，就必須保持公用。 無法將其變更回私人方案。

> [!NOTE]
> 私人對象或有限對象不同於您在 [預覽] 窗格上定義的預覽對象。 預覽對象可以在供應項目即時發佈到 Azure Marketplace 上_之前_先存取。 雖然私人對象選擇僅適用於特定方案，但預覽對象可以基於驗證目的，檢視所有私人和公用方案。

**有限對象 (Azure 訂用帳戶識別碼)** ：使用 Azure 訂用帳戶識別碼指派可存取此私人方案的對象。 選擇性地加上您已指派的每個 Azure 訂用帳戶識別碼的描述。 如果您要匯入 CSV 試算表，請手動新增最多 10 個訂用帳戶識別碼，或最多 20,000 個識別碼。 Azure 訂用帳戶識別碼會以 GUID 表示，而且所有字母都必須是小寫。

>[!Note]
>透過雲端解決方案提供者方案（CSP）的轉銷商所建立的 Azure 訂用帳戶，不支援私用供應專案。

#### <a name="hide-a-plan"></a>隱藏方案

如果您的虛擬機器只會在透過另一個解決方案範本或受控應用程式參考時間接使用，請選取此核取方塊以發佈虛擬機器，但對可能直接加以搜尋或瀏覽的客戶則隱藏。

> [!NOTE]
> 隱藏的方案不支援預覽連結。

選取 [儲存草稿]，然後再繼續。

### <a name="technical-configuration"></a>技術設定

提供與此方案相關聯的影像和其他技術屬性。 如需詳細資訊，請參閱[建立 Azure VM 技術資產](create-azure-container-technical-assets.md) \(部分機器翻譯\)。

> [!NOTE]
> 如果您已將此方案設定為重複使用 [方案設定] 索引標籤上另一個方案的套件，則不會顯示 [技術設定] 索引標籤。

#### <a name="operating-system"></a>作業系統

在 [作業系統] 窗格上，執行下列動作：

- 針對 [作業系統系列]，選取 **Windows** 或 **Linux** 作業系統。
- 針對 [版本] 或 [廠商]，選取 [Windows 版本] 或 [Linux 廠商]。
- 針對 [作業系統易記名稱]，輸入易記的作業系統名稱。 客戶可以看到此名稱。

#### <a name="recommended-vm-sizes"></a>建議的 VM 大小

最多選取六個要顯示在 Azure Marketplace 上的建議虛擬機器大小。

#### <a name="open-ports"></a>開啟連接埠

在已部署的虛擬機器上開啟公用或私用連接埠。

#### <a name="storage-option-for-deployment"></a>用於部署的儲存選項

針對 [磁碟部署選項]，選取您客戶可以用於虛擬機器的磁碟部署類型。 Microsoft 建議將部署限制為僅 [受控磁碟部署]。

#### <a name="properties"></a>屬性

針對 [支援加速網路]，選取您的 VM 是否支援[加速網路](https://go.microsoft.com/fwlink/?linkid=2124513)。

#### <a name="vm-images"></a>VM 映像

提供虛擬機器映像的磁碟版本和共用存取簽章 (SAS) URI。 為每個 VM 映像新增最多 16 個資料磁碟。 在指定的提交項目中，每個方案只提供一個新的映像版本。 映像發佈之後，您就無法進行編輯，但可以將其刪除。 刪除版本可防止新的和現有的使用者部署已刪除版本的新執行個體。

- **光碟版本**：您所提供之映像的版本。
- **SAS URI**：您的 Azure 儲存體帳戶中儲存作業系統 VHD 的位置。 若要瞭解如何取得 SAS URI，請參閱[取得 VM 映射的共用存取](get-sas-uri.md)簽章 URI。
- 資料磁碟映像也是儲存在其 Azure 儲存體帳戶中的 VHD 共用存取簽章 URI。
- 在一個方案中每次提交時僅加入一個映像。

無論您使用哪一種作業系統，只能加入解決方案所需的最少數目資料磁碟。 在部署期間，客戶無法移除屬於映像一部分的磁碟，但在部署期間或之後可隨時加入磁碟。

在繼續前，選取 [儲存草稿]，然後返回 [方案概觀]。

## <a name="resell-through-csps"></a>透過 CSP 轉售

在[雲端解決方案提供者 (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) 方案中，將供應項目提供給合作夥伴，以延伸您供應項目的觸角。 所有自備授權 (BYOL) 方案都會自動加入方案中。 您也可以選擇加入非 BYOL 方案。

選取 [儲存草稿]，然後再繼續。

## <a name="test-drive"></a>試用產品

設定示範或*試用產品*，讓客戶在購買之前，先試用您的供應項目一段固定的時間。 若要為您的客戶建立示範環境，請參閱[商用 Marketplace 中的試用產品供應項目](test-drive.md) \(部分機器翻譯\)。

若要啟用試用產品，請在 [供應項目設定] 窗格上，選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

其他試用產品資源：

- [行銷最佳做法](../what-is-test-drive.md)
- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試用產品概觀](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF 檔案 (請確定您的快顯封鎖程式已關閉)

選取 [儲存草稿]，然後再繼續。

## <a name="review-and-publish"></a>檢閱並發佈

完成供應項目的所有必要區段之後，您可加以提交以檢閱並發佈。

在右上方，選取 [檢閱並發佈]。

在此窗格上，您可以：

- 檢視供應項目每個區段的完成狀態：

  - **未啟動**：區段尚未啟動且必須完成。
  - **不完整**：區段中有必須修正的錯誤，或是需要您提供詳細資訊。 如需更新不完整資訊的相關指導方針，請參閱此文章稍早的小節。
  - **完整**︰區段已經完成，而且沒有任何錯誤。 您必須完成供應項目的所有區段，才能提交供應項目。
- 為認證小組提供**認證注意事項**，以協助確保您的應用程式已正確測試。 加上可協助小組了解應用程式的測試指示和任何補充注意事項。

若要提交供應項目進行發佈，請選取 [檢閱並發佈]。

Microsoft 將會傳送一封電子郵件訊息，讓您知道供應項目的預覽版本何時可供檢閱及核准。 若要將您的供應項目發佈至公用 (或者，如果是私人供應項目，則將其發佈給私人對象)，請依序移至 [合作夥伴中心] 和供應項目的 [概觀] 頁面，然後選取 [上線]。 當發佈正在進行時，您的供應項目狀態會出現在頁面最上方。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈程序中的**手動驗證**步驟代表您的供應項目及其相關聯技術資產的廣泛檢閱。 如果此程序發現您的供應項目發生錯誤，您將會收到詳細說明問題的認證報告。 只要進行必要的修正，然後重新發佈您的供應項目即可。

## <a name="offer-overview"></a>供應項目概觀

[供應項目概觀] 頁面會顯示發佈供應項目 (已完成和進行中) 所需步驟的視覺表示法，以及完成每個步驟所需的時間長度。

此頁面也包含可協助您處理供應項目的連結，端視其狀態而定：

- 如果供應項目為草稿：[刪除草稿供應項目](update-existing-offer.md#delete-a-draft-offer) \(部分機器翻譯\)
- 如果供應項目已上線：[停止銷售供應項目](update-existing-offer.md#stop-selling-an-offer-or-plan) \(部分機器翻譯\)
- 如果供應項目處於預覽階段：[上線](publishing-status.md#publisher-approval) \(部分機器翻譯\)
- 如果您尚未完成發行者登出：[取消發佈](update-existing-offer.md#cancel-publishing) \(部分機器翻譯\)

## <a name="marketplace-examples"></a>Marketplace 範例

以下是如何在 Azure Marketplace 中顯示供應專案資訊的範例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="說明此供應專案在 Azure Marketplace 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 大型標誌
2. 價格
3. Categories
4. 條款及條件
5. 隱私權原則位址（連結）
6. 供應項目名稱
7. 描述
8. 實用的連結
9. 螢幕擷取畫面/影片

<br>以下是如何在 Azure Marketplace 搜尋結果中顯示供應專案資訊的範例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="說明這項供應專案在 Azure Marketplace 搜尋結果中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 小型標誌
2. 供應項目名稱
3. 搜尋結果摘要
4. 試用版

<br>以下是 Azure Marketplace 計畫詳細資料的範例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="說明 Azure Marketplace 方案詳細資料。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 計畫名稱和摘要
2. 建議 VM 大小
3. 規劃定價

<br>以下是如何在 Azure 入口網站中顯示供應專案資訊的範例：

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="說明此供應專案在 Azure 入口網站中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 名稱
2. 描述
3. 實用的連結
4. 螢幕擷取畫面/影片

<br>以下是如何在 Azure 入口網站搜尋結果中顯示供應專案資訊的範例：

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="說明此供應專案在 Azure 入口網站搜尋結果中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 小型標誌
2. 供應項目名稱
3. 搜尋結果摘要

<br>以下是 Azure 入口網站方案詳細資料的範例：

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="說明 Azure 入口網站計畫的詳細資料。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 方案名稱
2. 方案描述

## <a name="next-step"></a>後續步驟

- [更新商業市集中的現有供應項目](update-existing-offer.md)
