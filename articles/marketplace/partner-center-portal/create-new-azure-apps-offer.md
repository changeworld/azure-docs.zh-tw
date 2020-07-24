---
title: 建立 Azure 應用程式供應項目 - Microsoft 商業 Marketplace
description: 了解在合作夥伴中心的商業 Marketplace 入口網站中建立新 Azure 應用程式供應項目的步驟和考量。 您可以在 Azure Marketplace 中或透過雲端解決方案提供者 (CSP) 計畫，列出或銷售您的 Azure 應用程式供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: qianw211
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: 6789e32cbbb1257a668e7be87a8b73a0511b6fb0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87127429"
---
# <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

此文章說明在商業 Marketplace 中建立新 Azure 應用程式供應項目的步驟和考量。 建立新的 Azure 應用程式供應項目之前，您應該先熟悉這些概念。

發佈新的 Azure 應用程式供應項目之前，請[在合作夥伴中心建立商業 Marketplace 帳戶](create-account.md)，並確定您的帳戶已在商業 Marketplace 方案中註冊。

## <a name="before-you-begin"></a>開始之前

設計、建置和測試這些 Azure 應用程式供應項目需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。 您的工程小組應具備下列 Microsoft 技術的知識：

* 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解。
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)。
* 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的運用知識。
* 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識。
* 具備 [JSON](https://www.json.org/) 的運用知識。

### <a name="technical-documentation-and-resources"></a>技術文件和資源

當您準備好用於商業 Marketplace 的 Azure 應用程式供應項目時，請檢閱下列資源。

* [了解 Azure Resource Manager 範本](../../azure-resource-manager/resource-group-authoring-templates.md)

* 快速入門：

    * [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
    * [Azure 範本最佳做法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [發佈應用程式定義](../../managed-applications/publish-service-catalog-app.md)
    * [部署服務目錄應用程式](../../managed-applications/deploy-service-catalog-quickstart.md)

* 教學課程：

    * [建立定義檔案](../../managed-applications/publish-service-catalog-app.md)
    * [發佈 Marketplace 應用程式](../../managed-applications/publish-marketplace-app.md)

* 範例：

    * [Azure CLI](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [受控應用程式解決方案](../../managed-applications/sample-projects.md)

[為 Azure Marketplace 建置解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603) \(英文\) 這部影片可針對 Azure 應用程式供應項目類型提供完整的介紹：

* 有哪些可用的供應項目類型；
* 需要哪些技術資產；
* 如何撰寫 Azure Resource Manager 範本；
* 開發與測試應用程式 UI；
* 如何發佈應用程式供應項目；
* 應用程式檢閱程序。

### <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

建議您將下列工具新增至開發環境：

* [Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：
    * 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
    * 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
    * 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

您可以在 [Azure 開發人員工具](https://azure.microsoft.com/tools/)頁面中檢閱可用的工具。 此外，如果您使用的是 Visual Studio，則為 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="types-of-azure-application-plans"></a>Azure 應用程式方案類型

Azure 應用程式方法有兩種：解決方案範本和受控應用程式。

* **解決方案範本**是在 Marketplace 中發佈解決方案的其中一個主要方法。 當您的解決方案除了單一虛擬機器 (VM) 之外，還需要以自動化方式進行額外的部署和設定時，請使用此方案類型。 透過解決方案範本，您可以自動提供多個資源，包括 VM、網路和儲存體資源，以提供複雜的 IaaS 解決方案。  如需有關建置解決方案範本的詳細資訊，請參閱 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)。

* **受控應用程式**與解決方案範本類似，只有一個主要差異。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用受控應用程式可輕鬆建置完全受控的周全應用程式並提供給您的客戶。  如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](../../managed-applications/overview.md)。

## <a name="technical-requirements"></a>技術需求

所有 Azure 應用程式在 `.zip` 封存的根資料夾中都至少包含兩個檔案：

* Resource Manager 範本檔案，名為 [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)。  此範本會定義要部署到客戶 Azure 訂用帳戶中的資源。 如需 Resource Manager 範本的範例，請參閱 [Azure 快速入門範本資源庫](https://azure.microsoft.com/resources/templates/)或對應的 [GitHub：Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)存放庫。

* Azure 應用程式建立體驗的使用者介面定義，名為 [createUiDefinition.json](../../managed-applications/create-uidefinition-overview.md)。  在使用者介面中，您可以指定讓取用者提供參數值的項目。

所有新的 Azure 應用程式供應項目都必須包含一個 [Azure 合作夥伴客戶使用方式屬性 GUID](../azure-partner-customer-usage-attribution.md) \(部分機器翻譯\)。 

若要了解每個應用程式方案的發佈需求，請參閱[解決方案範本供應項目發佈需求](../marketplace-solution-templates.md)和[受控應用程式供應項目發佈需求](../marketplace-managed-apps.md)。

## <a name="create-a-new-offer"></a>建立新的供應項目

>[!NOTE]
>發佈供應項目之後，在合作夥伴中心對其進行的編輯只有在重新發佈供應項目之後才會出現在店面中。 進行變更之後，請務必一律重新發佈該供應項目。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左側功能表中，選取 [商業 Marketplace] > [概觀]。

1. 在 [概觀] 頁面上，選取 [+新增供應項目] > [Azure 應用程式]。

    ![說明左側導覽功能表。](./media/new-offer-azure-app.png)

1. 在 [新增供應項目] 頁面上，輸入 [供應項目識別碼]。 這是您帳戶中每個供應項目的唯一識別碼。

     * 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
     * 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
     * 選取 [建立] 之後，即無法變更供應項目識別碼。

1. 輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

     * 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
     * 在您選取 [建立] 之後，就無法變更供應項目別名。

1. 選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

[供應項目設定] 頁面可讓您為供應項目設定試用產品和潛在客戶管理。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 [深入了解試用產品](../what-is-test-drive.md)。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。 如何設定試用產品環境會在本主題稍後的[試用產品技術設定](#test-drive-technical-configuration)一節中提及。

如需詳細資訊，請參閱[試用商業 Marketplace 中的供應項目](test-drive.md) \(部分機器翻譯\)。 您也可以閱讀[試用產品最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) \(英文\)，並下載[試用產品概觀 PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) \(英文\) (請確定您的快顯封鎖程式已關閉)

>[!Note]
>所有 Azure 應用程式都是使用 Azure Resource Manager 範本實作的，因此 Azure 應用程式唯一可用的試用產品類型是 [Azure Resource Manager 試用產品](../azure-resource-manager-test-drive.md) \(部分機器翻譯\)。

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

[**屬性**] 頁面可讓您定義用來在 marketplace 上將供應專案分組的類別、應用程式版本，以及支援供應專案的法律合約。

### <a name="category"></a>類別

選取 [類別] 和 [子類別]，將您的供應專案放在適當的 marketplace 搜尋區域。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和最多兩個類別，包括主要和次要類別（選擇性）。
- 針對每個主要和/或次要類別，最多可有兩個子類別。 如果您的供應專案沒有適用的子類別，請選取 [**不適用**]。

請參閱[供應專案清單最佳作法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。

### <a name="legal"></a>法律

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>供應項目清單

此頁面可讓您管理商業 Marketplace 供應項目的複本和映像。

### <a name="marketplace-details"></a>Marketplace 詳細資料

> [!NOTE]
> 供應項目清單內容 (例如描述、文件、螢幕擷取畫面與使用規定) 不一定要使用英文，只要供應項目用以下句子描述開頭即可：「此應用程式僅適用於 [非英文語言]」。 也可利用不同於供應項目清單內容中所使用的語言，來提供供應項目內容的*有用連結 URL*。

以下範例說明如何在 Azure Marketplace 中顯示供應專案資訊（任何列出的價格僅供範例之用，而非用於反映實際成本）：

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="說明此供應專案在 Azure Marketplace 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 標誌
2. Categories
3. 支援位址（連結）
4. 使用規定
5. 隱私權原則位址（連結）
6. 供應項目名稱
7. 總結
8. 說明
9. 螢幕擷取畫面/影片

<br>以下是如何在 Azure 入口網站中顯示供應專案資訊的範例：

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="說明此供應專案在 Azure 入口網站中的顯示方式。":::

#### <a name="call-out-descriptions"></a>向外撥說明

1. 標題
2. 說明
3. 實用的連結
4. 螢幕擷取畫面

#### <a name="name"></a>名稱

您在此處輸入的名稱會作為供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會預先填入您為**供應項目別名**輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

#### <a name="search-results-summary"></a>搜尋結果摘要

提供供應項目的簡短描述，最多 100 個字元。 此描述可用於搜尋結果。

#### <a name="long-summary"></a>完整摘要

提供更詳細的供應項目描述，最多 256 個字元。 此描述可用於搜尋結果。

#### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜尋關鍵字，協助客戶在 Marketplace 中尋找您的供應項目。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織隱私權原則的 URL。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

選取 [+ 新增連結]，為您的解決方案新增選擇性補充線上文件的連結。

### <a name="contact-information"></a>連絡人資訊

提供 [支援連絡人]、[工程連絡人] 與 [CSP 計畫連絡人] 的姓名、電子郵件與電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，可能也會提供給 CSP 合作夥伴。 有些連絡人可能需要額外的資訊。

### <a name="marketplace-media"></a>Marketplace 媒體

提供要搭配您供應項目使用的標誌和映像。 所有影像都必須採用 PNG 格式。 模糊影像將會導致您的提交遭到拒絕。

>[!Note]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

#### <a name="store-logos"></a>儲存標誌

請以下列三個像素大小提供供應項目標誌的 PNG 檔案：

- **小型** (48 x 48)
- **中型** (90 x 90)
- **大型** (216 x 216)
- **寬型** (255 x 115)

全部三個標誌都是必要的，且在清單中會用於不同位置。

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個顯示供應項目運作方式的螢幕擷取畫面。 每個螢幕擷取畫面的大小都必須為 1280 x 720 像素且採用 PNG 格式。 每個螢幕擷取畫面都必須包含標題。

#### <a name="videos"></a>影片

新增最多五個示範您供應項目的影片。 這些影片應該在外部影片服務上託管。 輸入影片的名稱、網址，以及影片的縮圖 PNG 影像 (大小為 1280 x 720 像素)。

#### <a name="additional-marketplace-listing-resources"></a>其他 Marketplace 清單資源

- [市集供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md)

選取 **[儲存草稿]** 後再繼續。

## <a name="preview-audience"></a>預覽對象

在 [預覽] 索引標籤上，選擇有限的 [預覽對象] 來驗證您的供應項目，再將其即時發佈給更廣泛的 Marketplace 對象。

> [!IMPORTANT]
> 在 [預覽] 中檢查供應項目之後，選取 [上線]，為商業 Marketplace 公開對象發佈您的供應項目。

您的預覽對象是以 Azure 訂用帳戶識別碼 GUID 識別，且每個都有一個選擇性描述。 客戶無法看到這些欄位。 您可以在 Azure 入口網站的 [訂用帳戶] 頁面上找到 Azure 訂用帳戶識別碼。

藉由分別 (最多 10 個) 或上傳 CSV 檔案 (最多 100 個)，新增至少一個 Azure 訂用帳戶識別碼。 您可以藉由新增這些訂用帳戶識別碼，定義哪些人員可以在即時發佈之前預覽您的供應項目。 如果您的供應項目已上線，您仍然可以定義預覽對象，以測試您供應項目的變更或更新。

> [!NOTE]
> 預覽對象與私人對象不同。 預覽對象可以在供應項目即時發佈到 Marketplace 上_之前_先存取。 預覽對象可以查看並驗證所有方案，包括只有在您的供應項目完全發佈到 Marketplace 之後，才供私人對象使用的方案。 私人對象 (在方案的 [定價和可用性] 索引標籤中定義) 對特定方案具有獨佔式存取權。

選取 **[儲存草稿]** 後再繼續。

## <a name="technical-configuration"></a>技術設定

只有在您的供應項目包含將使用 Marketplace 計量服務 API 發出計量事件的受控應用程式時，才完成此節。 輸入您的服務將在發出計量事件時使用的 **Azure Active Directory 租用戶識別碼**和 **Azure Active Directory 應用程式識別碼**。

選取 [儲存草稿] 後再繼續。

## <a name="technical-configuration-offer-level"></a>技術設定 (供應項目層級)

>[!Note]
>供應項目層級的技術詳細資料是選擇性的。  只有在您要發佈具有計量付費計費的受控應用程式，且擁有將使用 Azure AD 安全性權杖進行驗證的服務時，才需要設定這些詳細資料。 如需詳細資訊，請參閱不同驗證選項的[驗證策略](./marketplace-metering-service-authentication.md)。

技術設定會定義用來識別服務的詳細資料 (租用戶識別碼和應用程式識別碼)，這將使用 [Marketplace 計量服務 API](./marketplace-metering-service-apis.md) 發出受控應用程式的計量事件。  輸入您的服務將在發出計量事件時使用的身分識別。

* **Azure AD 租使用者識別碼**（必要）：在 Azure 入口網站內部，您必須[建立 Azure Active Directory （AD）應用程式](../../active-directory/develop/howto-create-service-principal-portal.md)，以便驗證這兩個服務之間的連線是在驗證通訊之後。 若要尋找[租使用者識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)），請移至您的 Azure Active Directory 並選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure AD 應用程式識別碼**（必要）：您也需要[應用程式識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）和驗證金鑰。 若要取得那些值，請前往 Azure Active Directory，並選取 [應用程式註冊]，然後尋找列出的 [應用程式識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 若要尋找驗證金鑰，請移至 [設定] 並選取 [金鑰]。 您將必須提供描述與持續時間，然後才會提供數值。

>[!Note]
>Azure 應用程式識別碼將與您的發行者識別碼相關聯，而且只能在此發行者帳戶內重複使用。

>[!Note]
>如果您想要使用[批次使用事件](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)，則需要此設定。  如果您想要提交[使用事件](marketplace-metering-service-apis.md#metered-billing-single-usage-event)），您也可以使用[實例中繼資料服務](../../active-directory/managed-identities-azure-resources/overview.md)來取得[JSON web 權杖（JWT）持有人權杖](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

## <a name="plan-overview"></a>方案概觀

此索引標籤可讓您在相同供應項目中提供不同的方案選項。 這些方案（先前稱為 Sku）在計畫類型（解決方案範本與受控應用程式）、營收或物件方面可能有所不同。 請至少設定一個方案，才能在 Marketplace 中列出您的供應項目。

建立之後，您將會在此索引標籤上看到您的方案名稱、識別碼、方案類型、可用性 (公用或私人)、目前發佈狀態，以及任何可用的動作。

[方案概觀] 中的可用 [動作] 會根據方案的目前狀態而有所不同，而且可能包括：

* 如果方案狀態為 [草稿] – [刪除草稿]。
* 如果方案狀態為 [即時] – [停止銷售方案] 或 [同步私人對象]。

### <a name="create-new-plan"></a>建立新的方案

***方案識別碼*** – 為此供應項目中的每個方案建立唯一的方案識別碼。 客戶將可在產品 URL 中看到此識別碼。  只能使用小寫英數字元、連字號或底線。 針對此方案識別碼，最多允許 50 個字元。 選取 [建立] 之後，就無法修改此識別碼。

***方案名稱*** - 當客戶決定要在您的供應項目內選取哪一個方案時，會看到此名稱。 為此供應項目中的每個方案建立唯一的供應項目名稱。 方案名稱是用來區分可能屬於相同供應項目的軟體方案 (例如，供應項目名稱：Windows 伺服器；方案：Windows Server 2016、Windows Server 2019)。

### <a name="plan-setup"></a>方案設定

此索引標籤可讓您設定方案類型的高階設定、是否重複使用來自另一個方案的套件，以及該方案應該用於哪些雲端。 您在此索引標籤上的答案將會影響要顯示在相同方案的其他索引標籤上的欄位。

#### <a name="plan-type"></a>方案類型
選取供應項目的方案類型。 **解決方案範本**方案完全由客戶所管理。 **受控應用程式**方案可讓發行者代表客戶管理應用程式。 如需詳細資訊，請參閱 [Azure 應用程式方案類型](#types-of-azure-application-plans)。

#### <a name="re-use-technical-configuration"></a>重複使用技術設定

如果您有多個相同類型的方案，而且這些方案之間的套件相同，則可以選取 [此方案重複使用另一個方案的套件]。  當您選取此選項時，將能夠選取其中一個相同類型的其他方案，讓此供應項目重複使用其中的套件。 

>[!Note]
>當您重複使用另一個方案的套件時，整個 [技術設定] 索引標籤都會從此方案中消失。 另一個方案中的技術設定詳細資料 (包括您在之後所做的任何更新) 也會用於此方案。<br><br>這個方案發佈之後即無法變更此設定。

#### <a name="azure-regions-cloud"></a>Azure 區域 (雲端)

您的方案必須至少在一個 Azure 區域中提供。

選取 [Azure Global] 選項，讓您的方案可供所有 Azure 全球區域中具有商業 Marketplace 整合的客戶使用。 如需詳細資料，請參閱[地理可用性和貨幣支援](../marketplace-geo-availability-currencies.md) \(部分機器翻譯\)。

選取 [Azure Government] 選項，讓您的方案可用於 [Azure Government](../../azure-government/documentation-government-welcome.md) \(英文\) 區域。 此區域為美國聯邦、州、地方或部落實體的客戶，以及符合資格可以為這些實體提供服務的合作夥伴，提供控管權限。 身為發佈者的您必須負責任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。

發佈至 [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md) 之前，請先在環境中測試並驗證您的方案，因為部分端點可能會有所不同。 若要設定並測試您的方案，請從 [Microsoft Azure 政府機構試用版](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

>[!NOTE]
>在您的方案發佈並可在特定 Azure 區域中使用之後，您就無法移除該區域。

#### <a name="azure-government-certifications"></a>Azure Government 認證

只有當您選取 [Azure Government] 時，才會看到此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以是直接指向程式清單的連結，或是指向您自己網站上相關合規性描述的連結。 只有 Azure Government 客戶可以看到這些連結。

選取 [儲存草稿] 後再繼續。

### <a name="plan-listing"></a>方案清單

您可以在此設定方案的清單詳細資料。 此索引標籤會顯示相同供應項目中的不同方案間可能有所差異的特定資訊。

#### <a name="plan-name"></a>方案名稱

這會預先填入您在建立方案時提供給您方案的名稱。 此名稱會在 Marketplace 中顯示為此方案的標題，而且限制為 100 個字元。

#### <a name="plan-summary"></a>方案摘要

提供您方案 (非供應項目) 的簡短摘要。 此摘要限制為 100 個字元。

#### <a name="plan-description"></a>方案描述

描述此軟體方案獨一無二之處，以及供應項目內方案之間的差異。 請不要描述供應項目，而只描述方案。 方案描述最多可包含 2,000 個字元。

選取 [儲存草稿] 後再繼續。

### <a name="availability-solution-template-plans-only"></a>可用性 (僅限解決方案範本方案)

您可以讓所有人、只有特定客戶 (私人對象) 看到此方案，以及是否要隱藏方法以供其他解決方案範本或受控應用程式使用。

#### <a name="plan-visibility"></a>方案可視性

您可以將每個方案設定為每個人都可以看到，或僅供您選擇的特定對象看到。 您可以使用 Azure 訂用帳戶識別碼，為這個限制的對象指派成員資格。

選取 [這是私人方案]，將您的方案設定為私人，而且只有您選擇的限制對象可以看到。 發佈為私人方案之後，您可以更新對象，也可以選擇將方案提供給所有人使用。 一旦發佈每個人都可以看到的方案之後，必須保持每個人都可以看到的狀態 (無法重新設定為私人方案)。

如果您將方案設定為私人，請輸入 **Azure 訂用帳戶識別碼**及其描述。 每個都是可以存取此私人方案的對象。 存取權是使用 Azure 訂用帳戶識別碼指派的，而且可以選擇包含指派給每個 Azure 訂用帳戶識別碼的描述。 個別新增最多 10 個客戶訂用帳戶識別碼；透過匯入 CSV 檔案，則最多可新增 20,000 個客戶訂用帳戶識別碼。 Azure 訂用帳戶識別碼會以 GUID 表示，而且字母必須是小寫。

>[!Note]
>私人對象或有限對象不同於您在 [預覽] 索引標籤上定義的預覽對象。預覽對象可以在供應項目即時發佈到 Marketplace 上_之前_先存取。 雖然私人對象選擇僅適用於特定方案，但預覽對象可以基於驗證目的，檢視所有方案 (無論是否為私人方案)。

#### <a name="hide-plan"></a>隱藏方案

如果您的解決方案範本僅在透過另一個解決方案範本或受控應用程式參考時間接部署，請核取此方塊以發佈您的解決方案範本，但在客戶直接搜尋和瀏覽時將其隱藏。

選取 [儲存草稿] 後再繼續。

### <a name="pricing-and-availability-managed-application-plans-only"></a>定價和可用性 (僅限受控應用程式方案)

使用這個選項設定此方案將適用的 [市場]、解決方案管理每個月的 [價格]，以及是否只有特定客戶才看得到該方案 (私人對象) 的 [方案可視性]。

選取 [儲存草稿] 後再繼續。

#### <a name="markets"></a>市場

每個方案必須至少可用於一個市場。 對於您想要讓此方案可供使用的任何市場位置選取核取方塊。 包含搜尋方塊和按鈕，可協助您選取 Microsoft 代表您繳交銷售和使用稅金的「已繳交稅金」國家/地區。

如果您已經用美元 (USD) 設定方案價格，並新增另一個市場位置，則新市場的價格會根據目前的匯率計算。 在您發佈之前，請務必先檢閱每個市場的價格。 儲存變更之後，可以使用 [匯出價格 (.xlsx)] 連結來檢閱價格。

#### <a name="pricing"></a>定價

提供此方案的每月價格。  此價格不包括此解決方案所部署資源所產生的任何 Azure 基礎結構或隨用隨付軟體成本。

除了每月價格之外，您也可以使用[計量付費計費](./azure-app-metered-billing.md)，設定非標準單位耗用量的價格。  如有需要，您可以將每月價格設定為零，並只使用計量付費計費。 

以 USD (USD = 美元) 設定的價格會在儲存時使用目前的匯率，轉換為所有選定市場的當地貨幣。 在發佈之前，請先匯出價格試算表，並檢閱每個市場的價格，以驗證這些價格。 如果您想要在個別市場中設定自訂價格，請修改並匯入價格試算表。 

>[!Note]
>您必須先儲存您的價格變更，才能匯出價格資料。

發佈之前，請先仔細檢閱您的價格，因為在方案發佈後可能會變更的部分有一些限制。  

>[!Note]
>一旦您方案中的市場價格發佈之後，就無法再變更。

#### <a name="plan-visibility"></a>方案可視性

您可以將每個方案設定為每個人都可以看到，或僅供您選擇的特定對象看到。 您可以使用 Azure 訂用帳戶識別碼，為這個限制的對象指派成員資格。

選取 [這是私人方案]，將您的方案設定為私人，而且只有您選擇的限制對象可以看到。 發佈為私人方案之後，您可以更新對象，也可以選擇將方案提供給所有人使用。 一旦發佈每個人都可以看到的方案之後，必須保持每個人都可以看到的狀態 (無法重新設定為私人方案)。

>[!Note]
>私人對象或有限對象不同於您在 [預覽] 索引標籤上定義的預覽對象。預覽對象可以在供應項目即時發佈到 Marketplace 上_之前_先存取。 雖然私人對象選擇僅適用於特定方案，但預覽對象可以基於驗證目的，檢視所有方案 (無論是否為私人方案)。

如果您將方案設定為私人，請輸入 **Azure 訂用帳戶識別碼**及其描述。 每個都是可以存取此私人方案的對象。 存取權是使用 Azure 訂用帳戶識別碼指派的，而且可以選擇包含指派給每個 Azure 訂用帳戶識別碼的描述。 個別新增最多 10 個客戶訂用帳戶識別碼；透過匯入 CSV 檔案，則最多可新增 20,000 個客戶訂用帳戶識別碼。 Azure 訂用帳戶識別碼會以 GUID 表示，而且字母必須是小寫。

>[!Note]
>透過雲端解決方案提供者方案（CSP）的轉銷商所建立的 Azure 訂用帳戶，不支援私用供應專案。


### <a name="technical-configuration"></a>技術設定 

此索引標籤可讓您上傳允許客戶部署您方案的部署套件。

>[!Note]
>如果您已在 [方案設定] 索引標籤上，將此方案設定為重複使用來自另一個方案的套件，則不會看到此索引標籤。

#### <a name="package-details"></a>套件詳細資料

此索引標籤可讓您編輯技術設定的草稿版本。

**版本** – 指派技術設定的目前版本。  在每次發佈此頁面的變更時，遞增此版本。 版本必須採用 `{integer}.{integer}.{integer}` 格式。

**套件檔案** (.zip) – 這個套件包含此方案所需的所有範本檔案，以及封裝為 `.zip` 檔案的其他任何資源。

所有 Azure 應用程式方案套件在 `.zip` 封存的根資料夾中都至少包含這兩個檔案：

* Resource Manager 範本檔案，名為 [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)。 此範本會自動將資源部署到客戶的 Azure 訂用帳戶。  如需 Resource Manager 範本的範例，請參閱 [Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/)或對應的 [GitHub：Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)存放庫。
* Azure 應用程式建立體驗的使用者介面定義，名為 [createUiDefinition.json](../../azure-resource-manager/managed-application-createuidefinition-overview.md)。

支援的檔案大小上限為：

* 壓縮過的 `.zip` 封存大小總計上限為 1 Gb
* 針對 `.zip` 封存中的任何個別未壓縮檔，最多可達 1 Gb  

所有新的 Azure 應用程式供應項目也必須包含一個 [Azure 合作夥伴客戶使用方式屬性 GUID](../azure-partner-customer-usage-attribution.md) \(部分機器翻譯\)。

>[!Note]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 https://upload.xboxlive.com 服務。

受控應用程式方案需要此索引標籤的其他資訊。

#### <a name="previously-published-packages"></a>先前發佈的套件

[先前發佈的套件] 子索引標籤可讓您檢視所有已發佈的技術設定版本。

#### <a name="enable-just-in-time-jit-access"></a>啟用 Just-In-Time (JIT) 存取

選取此選項，以啟用此方案的 Just-In-Time (JIT) 存取。  JIT 存取可讓您對受控應用程式的資源要求更高的存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以擁有更高的存取權。  如需詳細資訊，請參閱[啟用並要求 Azure 受控應用程式的 Just-In-Time 存取](../../managed-applications/request-just-in-time-access.md) \(部分機器翻譯\)。  若要要求受控應用程式的取用者授與您的帳戶永久存取權，請將此選項保留為未核取狀態。

>[!Note]
>請務必更新您的 `createUiDefinition.json` 檔案，才能支援此功能。  

#### <a name="deployment-mode"></a>部署模式

選取是否要在部署此方案時，設定**完整**或**漸進式部署模式**： 

* 在**完整模式**下，如果未在 `mainTemplate.json` 中定義資源，客戶重新部署應用程式將會導致移除受控資源群組中的資源。 
* 在**漸進式模式**下，重新部署應用程式不會變更現有的資源。

若要深入了解部署模式，請參閱 [Azure Resource Manager 部署模式](../../azure-resource-manager/deployment-modes.md) \(部分機器翻譯\)。

#### <a name="notification-endpoint-url"></a>通知端點 URL

指定 HTTPS Webhook 端點，以接收有關此方案版本的受控應用程式執行個體上所有 CRUD 作業的通知。

#### <a name="customize-allowed-customer-actions"></a>自訂允許的客戶動作

選取此選項可指定客戶除了預設可用的 `*/read` 動作以外，還可以對受控資源執行哪些動作。 

列出您想要讓客戶在此處執行的其他動作，並以分號分隔。  如需詳細資訊，請參閱[了解 Azure 資源的拒絕指派](../../role-based-access-control/deny-assignments.md) \(部分機器翻譯\)。  如需可用動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](../../role-based-access-control/resource-provider-operations.md)。 例如，若要允許取用者重新啟動虛擬機器，可將 `Microsoft.Compute/virtualMachines/restart/action` 新增至允許的動作。

#### <a name="global-azure--azure-government-cloud"></a>Global Azure / Azure Government 雲端

指出在每個支援的雲端中，誰應該具備此受控應用程式的管理存取權。 您想要對其授與受控資源群組權限的使用者、群組或應用程式，都是使用 Azure Active Directory (AAD) 身分識別來識別的。

**Azure Active Directory 租用戶識別碼** – AAD 租用戶識別碼 (亦稱為目錄識別碼)，其中包含您想要對其授與權限之使用者、群組或應用程式的身分識別。 您可以在 Azure 入口網站上的 [Azure Active Directory 內容](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)中找到您的 AAD 租用戶識別碼。

**授權** – 針對您要對其授與權限的使用者、群組或應用程式，將其 Azure Active Directory 物件識別碼新增至受控資源群組。 依使用者的主體識別碼 (可在 [Azure 入口網站上的 Azure Active Directory 使用者刀鋒視窗](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到) 識別使用者。

針對每個主體，從清單中選取其中一個 Azure AD 內建角色 ([擁有者] 或 [參與者])。 您選取的角色將會描述主體對客戶訂用帳戶中的資源所擁有的權限。 如需詳細資訊，請參閱[Azure 內建角色](../../role-based-access-control/built-in-roles.md)。 如需有關角色型存取控制 (RBAC) 的詳細資訊，請參閱[在 Azure 入口網站中開始使用 RBAC](../../role-based-access-control/overview.md)。

>[!Note]
>雖然您最多可以在每個雲端新增 100 個授權，但建立 Active Directory 使用者群組並在「主體識別碼」中指定其識別碼通常會比較容易。 這可讓您在部署方案之後，將更多使用者新增至管理群組，並減少更新方案的需求，僅新增更多授權。

#### <a name="policy-settings"></a>原則設定

將 [Azure 原則](../../governance/policy/overview.md)套用至您的受控應用程式，以指定已部署解決方案的合規性需求。 如需了解原則定義和參數值格式，請參閱 [Azure 原則範例](../../governance/policy/samples/index.md)。 您最多可以設定五個原則，而且每個 [原則] 選項只能有一個執行個體。 有些原則需要額外的參數。 稽核原則需要標準 SKU。 原則名稱限制為 50 個字元。

選取 [儲存草稿] 後再繼續。

## <a name="co-sell-with-microsoft"></a>與 Microsoft 共同銷售

提供 [共同銷售] 索引標籤的相關資訊對於發佈供應項目而言，完全是選擇性的。 其必須達到共同銷售準備就緒和 IP 共同共同銷售準備就緒的狀態。 Microsoft 銷售小組將會使用您提供的資訊，在評估其是否符合客戶需求時，深入了解您的解決方案。 您的解決方案無法直接提供給客戶。

如需有關此索引標籤的詳細資訊，請參閱[合作夥伴中心內的共同銷售選項](commercial-marketplace-co-sell.md) \(部分機器翻譯\)。

## <a name="resell-through-csps"></a>透過 CSP 轉售

在[雲端解決方案提供者](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) 計畫中，將供應項目提供給合作夥伴，以延伸您供應項目的觸角。 如此可讓轉銷商向其客戶銷售您的供應項目，並建立配套解決方案。

選取 [儲存草稿] 後再繼續。

## <a name="test-drive"></a>試用產品

設定示範 (試用產品)，讓客戶在購買之前先試用您的供應項目。 若要建立示範環境，讓客戶可在一段固定時間內試用您的供應項目，請參閱[試用商業 Marketplace 中的供應項目](test-drive.md) \(部分機器翻譯\)。

若要啟用試用產品，請在[[供應項目設定]](#test-drive) 索引標籤中，選取 [啟用試用產品] 核取方塊。若要從您的供應項目中移除試用產品，請清除此核取方塊。

### <a name="test-drive-technical-configuration"></a>試用產品技術設定

- **Azure AD 應用程式識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如50c464d3-4930-494c-963c-1e951d15360e）。

#### <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要允許代表您部署試用產品，請建立並提供另一個唯一的 Azure 訂用帳戶 (Power BI 試用產品則不需要)。

* **Azure 訂用帳戶識別碼** (Azure Resource Manager 和邏輯應用程式為必要) – 輸入訂用帳戶識別碼以授與您的 Azure 帳戶服務存取權，並用於資源使用量報告和計費。 建議您考慮[建立個別的 Azure 訂閱](../../billing/billing-create-subscription.md)，以便用於試用產品 (如果您沒有訂閱的話)。 您可以透過登入 [Azure 入口網站](https://portal.azure.com/)並瀏覽到左側功能表的 [訂用帳戶] 索引標籤，以尋找 Azure 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用帳戶識別碼（例如 "a83645ac-1234-5ab6-6789-1h234g764ghty"）。
* **Azure AD 租使用者識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要尋找此識別碼，請登入 [Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [內容]，然後尋找列出的 [目錄識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 您也可以在 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 中，使用網域名稱 URL 來查閱組織的租用戶識別碼。
* **Azure AD 租用戶名稱** (Dynamic 365 為必要) – 輸入您的 Azure Active Directory (AD) 名稱。 若要尋找此名稱，請登入 [Azure 入口網站](https://portal.azure.com/)，位於右上角的租用戶名稱會列在您的帳戶名稱底下。
* **Azure AD 應用程式識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)）。 若要尋找此識別碼，請登入 [Azure 入口網站](https://portal.azure.com/)，選取左側導覽功能表中的 [Active Directory] 索引標籤，選取 [應用程式註冊]，然後尋找列出的 [應用程式識別碼] 號碼 (例如 50c464d3-4930-494c-963c-1e951d15360e)。
* **Azure Active Directory 應用程式用戶端密碼**（必要）–輸入您的 Azure AD 應用程式[用戶端密碼](../../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)）。 若要尋找此值，請登入 [Azure 入口網站](https://portal.azure.com/)。 在左側導覽功能表中，選取 [Azure Active Directory] 索引標籤，選取 [應用程式註冊]，然後選取您的試用產品應用程式。 接下來，選取 [憑證及祕密]、選取 [新增用戶端密碼]、輸入描述、選取 [到期] 底下的 [永不]，然後選擇 [新增]。 離開此頁面之前，請務必複製此值。

選取 [儲存草稿] 後再繼續。

### <a name="marketplace-listing-optional"></a>Marketplace 清單 (選用)

描述試用產品體驗。

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **描述** (必要) - 描述您的試用產品、示範的項目、使用者可實驗的目標、可探索的功能，以及可協助使用者決定是否要購買供應項目的任何相關資訊。 最多可以在此欄位中輸入 3,000 個字元的文字。 
* **存取資訊** (針對 Azure Resource Manager 和邏輯試用產品為必要) – 說明客戶必須知道才能存取與使用此試用產品的內容。 逐步解說使用供應項目的案例，以及客戶在完整試用產品階段中，應該知道的確切存取功能。 最多可以在此欄位中輸入 10,000 個字元的文字。
* **使用者手冊** (必要) - 這是試用產品體驗的詳細說明。 使用者手冊應涵蓋您想要讓客戶從試用產品中獲得的確切內容，並做為客戶可能會遇到的任何問題的參考。 檔案必須是 PDF 格式，並在上傳後命名 (最多 255 個字元)。
* **影片：新增影片** (選用) – 您可以將影片上傳到 YouTube 或 Vimeo，並在這裡提供參考連結和縮圖影像 (533 x 324 像素)，讓客戶可以檢視協助他們更加了解試用產品的逐步解說資訊，包括如何順利使用供應項目的功能，並了解強調其優點的案例。
  * **名稱** (必要)
  * **網址** (僅限 YouTube 或 Vimeo，必要)
  * **縮圖**（影像檔案必須是 PNG 格式，而 533 x 324 圖元）。

選取 **[儲存草稿]** 後再繼續。

## <a name="publish"></a>發佈

當您完成供應項目的所有必要區段之後，請選取入口網站右上角的 [檢閱並發佈]。

檢閱供應項目每個區段的完成狀態。
    - *尚未開始* - 表示此區段必須已完成卻尚未觸及。
    - *不完整* – 表示此區段中有需要修正的錯誤，或是需要您提供更多詳細資訊。 回到上一個區段並加以更新。
    - *完成* – 表示此區段已完成，並已提供所有必要的資料且沒有任何錯誤。 供應項目的所有區段皆需要處於完成狀態，您才能提交供應項目。

如果這是您第一次發佈此供應項目，請將測試指示提供給認證小組，以確保您的應用程式已經過正確測試，並加上有助於了解您應用程式的任何補充注意事項。

選取 [提交] 以提交您的供應項目以供發佈。 我們將向您傳送一封電子郵件，讓您知道供應項目的預覽版本何時可供檢閱及核准。

若要將您的供應項目公開發佈 (針對私人供應項目，則向私人對象發佈)，請返回 [合作夥伴中心]，然後選取 [上線]。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈程序中的 [手動驗證] 步驟代表對您供應項目及其相關聯技術資產 (特別是 Azure Resource Manager 範本) 的廣泛審核，因此，通常會將問題顯示為提取要求 (PR) 連結。 如需如何檢視和回應這些 PR 的說明，請參閱[處理審核意見反應](./azure-apps-review-feedback.md)。

如果您在一或多個發佈步驟中出現錯誤，請加以更正，然後重新發佈您的供應項目。

## <a name="next-steps"></a>後續步驟

* [更新商業市集中的現有供應項目](./update-existing-offer.md)
