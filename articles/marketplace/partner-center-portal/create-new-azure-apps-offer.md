---
title: 建立 Azure 應用程式供應專案-Microsoft 商業 marketplace
description: 瞭解在合作夥伴中心的商業 marketplace 入口網站中建立新 Azure 應用程式供應專案的步驟和考慮。 您可以在 Azure Marketplace 中或透過雲端解決方案提供者（CSP）方案，列出或銷售您的 Azure 應用程式供應專案。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 22d6c37b59488633394d7f3ed5ca5b0c78371e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790682"
---
# <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

本文說明在商業市場中建立新 Azure 應用程式供應專案的步驟和考慮。 在建立新的 Azure 應用程式供應專案之前，您應該先熟悉這些概念。 

在發佈新的 Azure 應用程式供應專案之前，請先[在合作夥伴中心建立商業 marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，並確定您的帳戶已在商業 marketplace 方案中註冊。

## <a name="before-you-begin"></a>開始之前

設計、建立和測試 Azure 應用程式供應專案需要 Azure 平臺的技術知識，以及用來建立供應專案的技術。 您的工程小組應具備下列 Microsoft 技術的知識：

* [Azure 服務](https://azure.microsoft.com/services/)的基本瞭解。
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)。
* 使用[azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和[azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的知識。
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)的使用知識。
* [JSON](https://www.json.org/)的使用知識。

### <a name="technical-documentation-and-resources"></a>技術檔和資源

當您準備好用於商用 marketplace 的 Azure 應用程式供應專案時，請參閱下列資源。

* [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

* 快速入門：

    * [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
    * [發佈應用程式定義](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [部署服務目錄應用程式](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 教學課程：

    * [建立定義檔案](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [發佈市集應用程式](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 範例：

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [受控應用程式解決方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

[建立解決方案範本的影片和適用于 Azure Marketplace 的受控應用程式，](https://channel9.msdn.com/Events/Build/2018/BRK3603)可提供 Azure 應用程式供應專案類型的完整簡介：

* 有哪些供應專案類型可供使用;
* 需要哪些技術資產;
* 如何撰寫 Azure Resource Manager 範本;
* 開發和測試應用程式 UI;
* 如何發佈應用程式供應專案;
* 應用程式審核進程。

### <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

建議您將下列工具新增至開發環境：

* [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：
    * 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

您可以在[Azure 開發人員工具](https://azure.microsoft.com/tools/)頁面中查看可用的工具。 此外，如果您使用 Visual Studio， [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="types-of-azure-application-plans"></a>Azure 應用程式方案的類型

Azure 應用程式方案有兩種：解決方案範本和受控應用程式。

* **解決方案範本**是在 Marketplace 中發佈解決方案的其中一種主要方式。 當您的解決方案需要超過單一虛擬機器（VM）的額外部署和設定自動化時，請使用此方案類型。 透過解決方案範本，您可以自動提供多個資源，包括 Vm、網路和儲存體資源，以提供複雜的 IaaS 解決方案。  如需建立解決方案範本的詳細資訊，請參閱[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

* **受控應用程式**與解決方案範本類似，但有一個主要差異。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用受控應用程式，輕鬆建立完全受控的全包式應用程式，並將其傳遞給您的客戶。  如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](https://docs.microsoft.com/azure/managed-applications/overview)。

## <a name="technical-requirements"></a>技術需求

所有 Azure 應用程式在封存的根資料夾中至少包含兩個`.zip`檔案：

* 名為[mainTemplate](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 Resource Manager 範本檔案。  此範本會定義要部署到客戶 Azure 訂用帳戶中的資源。  如需 Resource Manager 範本的範例，請參閱[Azure 快速入門範本資源庫](https://azure.microsoft.com/resources/templates/)或對應的[GitHub： Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)儲存機制。

* 名為[createUiDefinition](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)之 Azure 應用程式建立體驗的使用者介面定義。  在使用者介面中，您可以指定讓取用者提供參數值的項目。

所有新的 Azure 應用程式供應專案都必須包含[azure 合作夥伴客戶使用狀況](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)的屬性 GUID。 

若要瞭解每個應用程式方案的發佈需求，請參閱[解決方案範本供應專案發佈需求](../marketplace-solution-templates.md)和[受控應用程式供應專案發佈需求](../marketplace-managed-apps.md)。

## <a name="create-a-new-offer"></a>建立新的供應項目

>[!NOTE]
>供應專案發佈之後，您在合作夥伴中心進行的編輯將不會出現在店面中，直到您重新發佈供應專案為止。 進行變更之後，請務必一律重新發佈您的供應專案。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左側功能表中，選取 [**商業 Marketplace** > **總覽**]。

1. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案**Azure 應用程式**]。

    ![說明左側導覽功能表。](./media/new-offer-azure-app.png)

1. 在 [**新增供應**專案] 頁面上，輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

     * Marketplace 供應專案和 Azure Resource Manager 範本的網址中，客戶可以看到此識別碼（如果適用的話）。
     * 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您輸入 [**測試-供應專案-1**]，則供應專案 web `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`位址會是。
     * 選取 [**建立**] 之後，即無法變更供應專案識別碼。

1. 輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

     * 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
     * 在您選取 [**建立**] 之後，即無法變更供應專案別名。

1. 選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-setup"></a>供應專案設定

[**供應專案設定**] 頁面可讓您設定供應專案的試用產品和潛在客戶管理。 

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應專案的絕佳方式，方法是讓他們選擇「在購買前試用」，進而提升轉換並產生高度合格的潛在客戶。 [深入瞭解試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)產品。

若要啟用一段固定時間的試用產品，請選取 [**啟用試用**產品] 核取方塊。 若要從您的供應專案中移除試用產品，請清除此核取方塊。 在本主題稍後的試用產品[技術](#test-drive-technical-configuration)設定一節中設定試用產品環境。

如需其他資訊，請參閱在[商業 marketplace 中測試您的供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)專案。 您也可以閱讀有關[試用產品的最佳作法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)，並下載[試用產品總覽](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（請確定您的快顯封鎖程式已關閉）

>[!Note]
>因為所有 Azure 應用程式都是使用 Azure Resource Manager 範本來執行，所以 Azure 應用程式唯一可用的試用產品類型是以[Azure Resource Manager 為基礎的試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)產品。

## <a name="lead-management"></a>潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理總覽](./commercial-marketplace-get-customer-leads.md)。

選取 [**儲存草稿**] 再繼續。

## <a name="properties"></a>屬性

[**屬性**] 頁面可讓您定義用來在 marketplace 上分組供應專案的類別和產業、您的應用程式版本，以及支援供應專案的法律合約。

選取至少一個和最多三個類別，將您的供應專案放在適當的 marketplace 搜尋區域中。 請務必在供應專案描述中描述您的供應專案如何支援這些類別。

### <a name="legal"></a>法律

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>供應專案清單

您可以在此頁面管理商業 marketplace 供應專案的複製和影像。 

### <a name="marketplace-details"></a>Marketplace 詳細資料

> [!NOTE]
> 供應專案清單內容（例如描述、檔、螢幕擷取畫面和使用規定）不需要英文，只要供應專案的描述開頭為片語，「此應用程式僅適用于 [非英文語言]。」 您也可以提供*有用的連結 URL*來提供內容，而不是供應專案清單內容所使用的語言。

#### <a name="name"></a>名稱

您在此處輸入的名稱會向客戶顯示，做為供應專案清單的標題。 當您建立供應專案時，此欄位會預先填入您在**供應專案別名**中輸入的文字，但您可以變更此值。 此名稱可能是商標（您可以包含商標或著作權符號）。 名稱不能超過50個字元，而且不能包含任何 emoji。

#### <a name="search-results-summary"></a>搜尋結果摘要

提供供應專案的簡短描述（最多100個字元），這可用於搜尋結果。

#### <a name="long-summary"></a>完整摘要

提供供應專案的較長描述（最多256個字元）。 此描述可用於搜尋結果。

#### <a name="description"></a>描述

提供供應專案的較長描述（最多3000個字元）。 清單總覽中的客戶會看到此描述。 包含您供應專案的價值主張、主要優點、類別及/或產業關聯、應用程式內購買機會，以及任何必要的公開。

SHere 是撰寫描述的一些秘訣：

* 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 包含下列專案：
  * 供應專案的描述。
  * 從您的供應專案獲益的使用者類型。
  * 供應專案所解決的客戶需求或問題。
* 請記住，前幾個句子可能會顯示在搜尋引擎結果中。
* 請勿依賴特性和功能來銷售您的供應專案。 相反地，請專注于您的供應專案提供的值。
* 使用產業特定或以優點為基礎的單字。
* 請考慮使用 HTML 標籤來格式化您的描述，使其更吸引人。

#### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇性地輸入最多三個搜尋關鍵字，以協助客戶在 marketplace 中尋找您的供應專案。 為了獲得最佳結果，請在您的描述中使用這些關鍵字。

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入您組織隱私權原則的 URL。 貴使用者必須負責確保您的應用程式符合隱私權法律和法規，以及提供有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

選取 [ **+ 新增連結**]，將連結新增至您解決方案的選擇性補充線上檔。

### <a name="contact-information"></a>連絡人資訊

提供**支援連絡人**、**工程連絡人**和**CSP 計畫連絡人**的名稱、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，並可提供給 CSP 合作夥伴。 有些連絡人可能需要額外的資訊。

### <a name="marketplace-media"></a>Marketplace 媒體

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 PNG 格式。 模糊影像會導致您的提交遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不https://upload.xboxlive.com會封鎖合作夥伴中心所使用的服務。

#### <a name="store-logos"></a>Microsoft Store標誌。

在下列三個圖元大小中提供供應專案標誌的 PNG 檔案：

- **小型**（48 x 48）
- **中**（90 x 90）
- **大型**（216 x 216）
- **寬**（255 x 115）

這三個標誌都是必要的，而且會用於清單中的不同位置。

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個螢幕擷取畫面的大小都必須是 1280 x 720 圖元，而且採用 PNG 格式。 每個螢幕擷取畫面都必須包含標題。

#### <a name="videos"></a>影片

新增最多五個示範您供應專案的影片。 這些應該裝載于外部影片服務。 輸入每部影片的名稱、網址，以及影片 1280 x 720 圖元的縮圖 PNG 影像。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

- [Marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

選取 [**儲存草稿**] 再繼續。

## <a name="preview-audience"></a>預覽物件

在 [預覽] 索引標籤上，選擇有限的**預覽物件**來驗證您的供應專案，再將其上線發佈到更廣泛的 marketplace 物件。

> [!IMPORTANT]
> 在預覽您的供應專案之後，**請選取 [上線]** ，將您的供應專案發佈給商業 marketplace 公用物件。

您的預覽物件是由 Azure 訂用帳戶識別碼 Guid 所識別，以及各自的選擇性描述。 客戶無法看到這兩個欄位。 您**可以在 Azure 入口網站的 [** 訂用帳戶] 頁面上找到您的 Azure 訂用帳戶識別碼。

新增至少一個 Azure 訂用帳戶識別碼，分別是（最多10個）或上傳 CSV 檔案（最多100個）。 藉由新增這些訂用帳戶識別碼，您可以定義誰可以在現場發佈之前預覽您的供應專案。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試供應專案的變更或更新。

> [!NOTE]
> 預覽物件與私用物件不同。 預覽物件可以存取您的供應專案，_然後_才能在 marketplace 中即時發佈。 他們可以查看並驗證所有方案，包括當您的供應專案完全發佈至 marketplace 之後，僅供私用物件使用的計畫。 私用物件（在 [方案**價格與可用性**] 索引標籤中定義）具有特定方案的獨佔存取權。

選取 [**儲存草稿**] 再繼續。

## <a name="technical-configuration"></a>技術設定

只有在您的供應專案包含會使用 Marketplace 計量服務 API 發出計量事件的受控應用程式時，才完成本節。 輸入您的服務將在發出計量事件時使用的 [ **Azure Active Directory 租使用者識別碼**] 和 [ **AZURE ACTIVE DIRECTORY 應用程式識別碼**]。

選取 [**儲存草稿**] 再繼續。

## <a name="technical-configuration-offer-level"></a>技術設定（供應專案層級）

>[!Note]
>供應專案層級的技術詳細資料是選擇性的。  如果您要發佈具有計量付費計費的受控應用程式，且具有將使用 Azure AD 安全性權杖進行驗證的服務，您只需要設定這些詳細資料。 如需詳細資訊，請參閱不同驗證選項的[驗證策略](./marketplace-metering-service-authentication.md)。

技術設定會定義用來識別服務的詳細資料（租使用者識別碼和應用程式識別碼），這會使用[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)發出受控應用程式的計量事件。  輸入您的服務將在發出計量事件時使用的身分識別。

* **Azure AD 租使用者識別碼**（必要）：在 Azure 入口網站內，我們會要求您[建立 Azure Active Directory （AD）應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)，以便驗證這兩個服務之間的連線是在驗證通訊之後。 若要尋找[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，請移至您的 Azure Active Directory 並選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure AD 應用程式識別碼**（必要）：您也需要[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)和驗證金鑰。 若要取得這些值，請移至您的 Azure Active Directory 並選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如50c464d3-4930-494c-963c-1e951d15360e）。 若要尋找驗證金鑰，請移至 [**設定**]，然後選取 [**金鑰**]。 您必須提供 [描述] 和 [持續時間]，然後才會提供數值。

>[!Note]
>Azure 應用程式識別碼會與您的發行者識別碼相關聯，而且只能在此發行者帳戶內重複使用。

>[!Note]
>如果您想要使用[批次使用事件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event)，則需要此設定。  如果您想要提交[使用事件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event)，您也可以使用[實例中繼資料服務](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)來取得[JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)。

## <a name="plan-overview"></a>計畫總覽

此索引標籤可讓您在相同的供應專案中提供不同的方案選項。 這些計畫（在 Cloud Partner 入口網站中稱為 Sku）在計畫類型（解決方案範本與受控應用程式）、營收或物件方面可能有所不同。  設定至少一個方案，以便在 marketplace 中列出您的供應專案。

建立之後，您會在此索引標籤上看到您的方案名稱、識別碼、計畫類型、可用性（公用或私用）、目前發行狀態和任何可用的動作。

**計畫總覽**中的可用**動作**會因方案的目前狀態而有所不同，可能包括：

* 如果方案狀態為 [**草稿**–刪除草稿]。
* 如果方案狀態為 [**即時**] – [停止銷售方案] 或 [同步私用物件]。

### <a name="create-new-plan"></a>建立新的方案

***方案識別碼***–為此供應專案中的每個方案建立唯一的方案識別碼。 此識別碼將會顯示在產品 URL 中的客戶。  僅使用小寫、英數位元、連字號或底線。 此方案識別碼最多可以有50個字元。 選取 [建立] 之後，就無法修改此識別碼。

***方案名稱***-客戶在決定要在您的供應專案內選取哪一個方案時，會看到此名稱。 為此供應專案中的每個方案建立唯一的供應專案名稱。 方案名稱是用來區分可能屬於相同供應專案一部分的軟體方案（例如，供應專案名稱： Windows Server; 方案： Windows Server 2016、Windows Server 2019）。

### <a name="plan-setup"></a>規劃設定

此索引標籤可讓您設定方案類型的高階設定、是否重複使用來自另一個方案的封裝，以及該計畫應提供的雲端。 您在此索引標籤上的答案將會影響相同方案的其他索引標籤上所顯示的欄位。

#### <a name="plan-type"></a>方案類型
選取供應專案的方案類型。 **解決方案範本**計畫完全由客戶管理。 **受控應用程式**方案可讓發行者代表客戶管理應用程式。 如需詳細資訊，請參閱[Azure 應用程式方案的類型](#types-of-azure-application-plans)。

#### <a name="re-use-technical-configuration"></a>重複使用技術設定

如果您有多個相同類型的方案，而且它們之間的封裝相同，則可以選取**此計畫重複使用另一個方案中的封裝**。  當您選取此選項時，您將能夠為此供應專案選取另一個相同類型的方案，以重複使用中的封裝。 

>[!Note]
>當您從另一個方案重複使用套件時，整個 [技術設定] 索引標籤將會從此方案中消失。 另一個方案中的技術設定詳細資料，包括您在未來所做的任何更新，也會用於此計畫。<br><br>發行此計畫之後，即無法變更此設定。

#### <a name="azure-regions-cloud"></a>Azure 區域（雲端）

您的方案必須至少在一個 Azure 區域中提供。

選取 [ **Azure 全域**] 選項，讓您的方案可供所有具有商業 marketplace 整合之 Azure 全球區域中的客戶使用。 如需詳細資訊，請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [ **Azure Government** ] 選項，讓您的方案可在[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)區域中使用。 此區域為美國聯邦、州、地方或部落的實體，以及符合資格服務的合作夥伴提供受控存取權。 身為發行者的您，會負責任何合規性控制、安全性措施和最佳作法。 Azure Government 會使用實際隔離的資料中心和網路（僅限美國地區）。

發行至[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，請在環境中測試並驗證您的方案，因為某些端點可能會有所不同。 若要設定及測試您的方案，請從[Microsoft Azure Government 試用](https://azure.microsoft.com/global-infrastructure/government/request/)版要求試用帳戶。

>[!NOTE]
>當您的方案發佈並可在特定的 Azure 區域中使用之後，您就無法移除該區域。

#### <a name="azure-government-certifications"></a>Azure Government 認證

只有當您選取 [ **Azure Government**] 時，才會顯示此選項。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 若要讓您知道這些程式的認證，您可以提供最多100個描述的連結。 這些可以是直接連結到您在程式上的清單，或連結至您自己網站上的合規性描述。 只有 Azure Government 客戶可以看到這些連結。

選取 [**儲存草稿**] 再繼續。

### <a name="plan-listing"></a>計畫清單

這是您設定方案清單詳細資料的位置。 此索引標籤會顯示相同供應專案中的方案可能不同的特定資訊。

#### <a name="plan-name"></a>計畫名稱

這會預先填入您在建立方案時所提供的名稱。 此名稱會在 marketplace 中顯示為此方案的標題，而且限制為100個字元。

#### <a name="plan-summary"></a>計畫摘要

提供您方案的簡短摘要（不是供應專案）。 此摘要的限制為100個字元。

#### <a name="plan-description"></a>計畫描述

描述此軟體方案的獨特之處，以及供應專案內方案之間的差異。 請不要描述供應專案，只是計畫。 計畫描述最多可包含2000個字元。

選取 [**儲存草稿**] 再繼續。

### <a name="availability-solution-template-plans-only"></a>可用性（僅限解決方案範本方案）

您可以讓所有人都能看到此計畫，只有特定客戶（私用物件），以及是否要隱藏計畫以供其他解決方案範本或受控應用程式使用。

#### <a name="plan-visibility"></a>計畫可見度

您可以將每個方案設定為每個人都可以看見，或僅供您選擇的特定物件使用。 您可以使用 Azure 訂用帳戶識別碼來指派此限制物件中的成員資格。

選取 [**這是私人方案**]，將您的方案設為私用，而且只對您選擇的限制物件是可見的。 發行為私用方案之後，您可以更新物件，或選擇將方案提供給所有人。 一旦方案發行為每個人都可以看到，每個人都必須保持可見;它無法重新設定為私用方案。

如果您將工廠設為私用，請輸入**Azure 訂**用帳戶識別碼和其描述。 每個物件都可存取此私用方案。 您可以使用 Azure 訂用帳戶識別碼來指派存取權，其中包含指派給每個 Azure 訂用帳戶識別碼的描述。 藉由匯入 CSV 檔案，個別新增最多10個客戶訂用帳戶識別碼或20000。 Azure 訂用帳戶識別碼會以 Guid 表示，而字母必須是小寫。

>[!Note]
>私用或限制的物件與您在 [**預覽**] 索引標籤上定義的預覽物件不同。預覽物件可以先存取您的供應專案，_再_將其發佈到 marketplace 中。 雖然私用物件選擇僅適用于特定方案，但預覽物件可以針對驗證目的來查看所有計劃（私用或非）。

#### <a name="hide-plan"></a>隱藏方案

如果您的方案範本僅在透過另一個解決方案範本或受控應用程式參考時間接部署，請核取此方塊以發佈您的解決方案範本，但從客戶直接搜尋和流覽它時將其隱藏。

選取 [**儲存草稿**] 再繼續。

### <a name="pricing-and-availability-managed-application-plans-only"></a>定價和可用性（僅限受管理的應用程式方案）

您可以使用它來設定將提供此方案的**市場**、解決方案管理的每月**價格**，以及只有特定客戶應該看到的**方案可見度**（私用物件）。

選取 [**儲存草稿**] 再繼續。

#### <a name="markets"></a>市場

必須至少有一個市場提供每個方案。 選取您想要讓此方案可供使用的任何市場位置的核取方塊。 包含一個搜尋方塊和按鈕，用於選取「稅金已匯款」的國家/地區，其中 Microsoft 免除銷售並代表您使用稅額，以協助。

如果您已經在美國美元（USD）中設定方案的價格，並新增另一個市場位置，新市場的價格將會根據目前的匯率計算。 在發佈之前，請務必先檢查每個市場的價格。 儲存變更之後，您可以使用 [匯出價格（.xlsx）] 連結來檢查定價。

#### <a name="pricing"></a>定價

提供此方案的每月價格。  此價格是除了此解決方案所部署的資源所產生的任何 Azure 基礎結構或隨用隨付軟體成本。

除了每月價格以外，您也可以使用[計量付費](./azure-app-metered-billing.md)來設定非標準單位耗用量的價格。  如有需要，您可以將每月價格設定為零，並以獨佔方式使用計量付費收費。 

以美元設定的價格（美元 = 美國元）會在儲存時使用目前的匯率，轉換為所有選定市場的本地貨幣。 在發佈之前，請先匯出定價試算表，並查看每個市場的價格，以驗證這些價格。 如果您想要在個別市場中設定自訂價格，請修改並匯入定價試算表。 

>[!Note]
>您必須先儲存定價變更，以啟用匯出定價資料。

在發佈之前，請仔細檢查您的價格，因為在計畫發行後可能會變更的部分有所限制。  

>[!Note]
>一旦您的方案中的市場價格發佈之後，就無法再變更。

#### <a name="plan-visibility"></a>計畫可見度

您可以將每個方案設定為每個人都可以看見，或僅供您選擇的特定物件使用。 您可以使用 Azure 訂用帳戶識別碼來指派此限制物件中的成員資格。

選取 [**這是私人方案**]，將您的方案設為私用，而且只對您選擇的限制物件是可見的。 發行為私用方案之後，您可以更新物件，或選擇將方案提供給所有人。 一旦方案發行為每個人都可以看到，每個人都必須保持可見;它無法重新設定為私用方案。

如果您將工廠設為私用，請輸入**Azure 訂**用帳戶識別碼和其描述。 每個物件都可存取此私用方案。 您可以使用 Azure 訂用帳戶識別碼來指派存取權，其中包含指派給每個 Azure 訂用帳戶識別碼的描述。 藉由匯入 CSV 檔案，個別新增最多10個客戶訂用帳戶識別碼或20000。 Azure 訂用帳戶識別碼會以 Guid 表示，而字母必須是小寫。

>[!Note]
>私用或限制的物件與您在 [**預覽**] 索引標籤上定義的預覽物件不同。預覽物件可以先存取您的供應專案，_再_將其發佈到 marketplace 中。 雖然私用物件選擇僅適用于特定方案，但預覽物件可以針對驗證目的來查看所有計劃（私用或非）。

### <a name="technical-configuration"></a>技術設定 

此索引標籤可讓您上傳部署套件，以允許客戶部署您的方案。

>[!Note]
>如果您已將此計畫設定為從 [**計畫設定**] 索引標籤上的另一個方案重複使用封裝，則不會顯示此索引標籤。

#### <a name="package-details"></a>套件詳細資料

此索引標籤可讓您編輯技術設定的草稿版本。

**版本**–指派技術設定的目前版本。  請在每次發行變更至此頁面時，遞增此版本。 版本必須是格式`{integer}.{integer}.{integer}`。

**套件**檔案（.zip）-此套件包含此方案所需的所有範本檔案，以及封裝為`.zip`檔案的任何其他資源。

所有 Azure 應用程式方案套件都必須在封存的根資料夾中包含這`.zip`兩個檔案：

* 名為[mainTemplate](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 Resource Manager 範本檔案。 此範本會自動將資源部署到客戶 Azure 訂用帳戶。  如需 Resource Manager 範本的範例，請參閱[Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/)或對應的[GitHub： Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)儲存機制。
* 名為[createUiDefinition](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)之 Azure 應用程式建立體驗的使用者介面定義。

支援的檔案大小上限為：

* 總計壓縮`.zip`的封存大小上限為 1 Gb
* 針對封存中`.zip`的任何個別未壓縮檔，最多可達 1 Gb  

所有新的 Azure 應用程式供應專案也必須包含[azure 合作夥伴客戶使用狀況](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)的屬性 GUID。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

受控應用程式方案需要此索引標籤上的其他資訊。

#### <a name="previously-published-packages"></a>先前發行的封裝

[**先前發佈的套件**] 子索引標籤可讓您查看所有已發行的技術設定版本。

#### <a name="enable-just-in-time-jit-access"></a>啟用即時（JIT）存取

選取此選項，以啟用此計畫的即時（JIT）存取。  JIT 存取可讓您要求更高的受控應用程式資源存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以有更高的存取權。  如需詳細資訊，請參閱[啟用和要求 Azure 受控應用程式的即時存取](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  若要要求受控應用程式的取用者授與您的帳戶永久存取權，請將此選項保留為未核取。

>[!Note]
>請務必更新您`createUiDefinition.json`的檔案，以支援這項功能。  

#### <a name="deployment-mode"></a>部署模式

選取部署此計畫時，是否要設定**完整**或累加**部署模式**： 

* 在**完整模式**中，客戶應用程式的重新部署會導致在中未定義資源時，移除受控資源群組中的資源`mainTemplate.json`。 
* 在累加**模式**中，應用程式的重新部署會讓現有的資源保持不變。

若要深入瞭解部署模式，請參閱[Azure Resource Manager 部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

#### <a name="notification-endpoint-url"></a>通知端點 URL

指定 HTTPS Webhook 端點，以接收此方案版本的受控應用程式實例上所有 CRUD 作業的相關通知。

#### <a name="customize-allowed-customer-actions"></a>自訂允許的客戶動作

選取此選項可指定客戶可以在受管理資源上執行的動作，以及`*/read`預設可使用的「」動作。 

列出您想要讓客戶在此處執行的其他動作（以分號分隔）。  如需詳細資訊，請參閱[瞭解 Azure 資源的拒絕指派](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  如需可用動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允許取用者重新啟動虛擬機器，可將 `Microsoft.Compute/virtualMachines/restart/action` 新增至允許的動作。

#### <a name="global-azure--azure-government-cloud"></a>全球 Azure/Azure Government 雲端

指出在每個支援的雲端中，誰應具備此受控應用程式的管理存取權。 您想要授與受控資源群組之許可權的使用者、群組或應用程式，都是使用 Azure Active Directory （AAD）身分識別來識別。

**Azure Active Directory 租使用者識別碼**– AAD 租使用者識別碼（也稱為目錄識別碼），其中包含您想要授與許可權的使用者、群組或應用程式的身分識別。 您可以在 [Azure 入口網站] 的 [ [Azure Active Directory 屬性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)] 中找到您的 AAD 租使用者識別碼。

**授權**–新增您想要授與受控資源群組之許可權的使用者、群組或應用程式的 AZURE ACTIVE DIRECTORY 物件識別碼。 依據使用者的主體識別碼來識別使用者，這可以在 Azure 入口網站的 [ [Azure Active Directory 使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)] 分頁中找到。

針對每個主體，從清單中選取其中一個 Azure AD 內建角色（擁有者或參與者）。 您選取的角色將會描述主體對客戶訂用帳戶中的資源所擁有的許可權。 如需詳細資訊，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。 如需角色型存取控制（RBAC）的詳細資訊，請參閱[開始使用 Azure 入口網站中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)。

>[!Note]
>雖然您最多可以在每個雲端新增100個授權，但建立 Active Directory 使用者群組並在「主體識別碼」中指定其識別碼，通常會比較容易。 這可讓您在部署方案之後，將更多使用者新增至管理群組，並減少更新方案的需求，只是要新增更多授權。

#### <a name="policy-settings"></a>原則設定

將[Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview)套用至您的受控應用程式，以指定已部署解決方案的合規性需求。 如需了解原則定義和參數值格式，請參閱 [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)。 您最多可以設定五個原則，而且每個原則選項只能有一個實例。 有些原則需要額外的參數。 稽核原則需要標準 SKU。 原則名稱限制為50個字元。

選取 [**儲存草稿**] 再繼續。

## <a name="co-sell-with-microsoft"></a>與 Microsoft 共同銷售

提供 [共同銷售] 索引標籤上的資訊，對於發佈您的供應專案完全是選擇性的。 必須達到共同銷售就緒和 IP 共同銷售就緒狀態。 Microsoft 銷售小組會使用您提供的資訊，在評估其是否符合客戶需求時，深入瞭解您的解決方案。 它無法直接提供給客戶。

如需此索引標籤的詳細資訊，請參閱[合作夥伴中心的共同銷售選項](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="resell-through-csps"></a>透過 Csp 轉售

將供應專案提供給[雲端解決方案提供者](https://azure.microsoft.com/offers/ms-azr-0145p/)（CSP）方案中的合作夥伴，以擴大其範圍。 這可讓轉銷商向客戶銷售您的供應專案，並建立配套的解決方案。

選取 [**儲存草稿**] 再繼續。

## <a name="test-drive"></a>試用產品

設定示範（試用產品），讓客戶在購買之前先試用您的供應專案。 若要建立示範環境，讓客戶可以在一段固定時間內試用您的供應專案，請參閱在[商業 marketplace 中測試您的供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)專案。

若要啟用試用產品，請在 [[供應專案設定](#test-drive)] 索引標籤上選取 [**啟用試用磁片**] 核取方塊。若要從您的供應專案中移除試用產品，請清除此核取方塊。

### <a name="test-drive-technical-configuration"></a>試用產品技術設定

- **Azure AD 應用程式識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如50c464d3-4930-494c-963c-1e951d15360e）。

#### <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要允許代表您部署試用產品，請建立並提供獨立的唯一 Azure 訂用帳戶（Power BI 試用產品不需要）。

* **Azure 訂**用帳戶識別碼（Azure Resource Manager 和邏輯應用程式的必要）–輸入訂用帳戶識別碼，以授與您的 azure 帳戶服務存取權，以進行資源使用量報告和計費。 我們建議您考慮[建立個別的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-create-subscription)用帳戶，以用於試用產品（如果您還沒有的話）。 您可以藉由登入[Azure 入口網站](https://portal.azure.com/)並流覽至左側功能表的 [**訂閱**] 索引標籤，來尋找您的 AZURE 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用帳戶識別碼（例如 "a83645ac-1234-5ab6-6789-1h234g764ghty"）。
* **Azure AD 租使用者識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**屬性**]，然後尋找列出的**目錄識別碼**（例如50c464d3-4930-494c-963c-1e951d15360e）。 您也可以在下列位置使用您的功能變數名稱 URL 來查詢組織的租使用者[https://www.whatismytenantid.com](https://www.whatismytenantid.com)識別碼：。
* **Azure AD 租使用者名稱**（動態365所需）–輸入您的 AZURE ACTIVE DIRECTORY （AD）名稱。 若要尋找此名稱，請登入[Azure 入口網站](https://portal.azure.com/)，在右上角，您的租使用者名稱會列在 [您的帳戶名稱] 底下。
* **Azure AD 應用程式識別碼**（必要）–輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側導覽功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure Active Directory 應用程式用戶端密碼**（必要）–輸入您的 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值，請登入[Azure 入口網站](https://portal.azure.com/)。 選取左側導覽功能表中的 [ **Azure Active Directory** ] 索引標籤，選取 [**應用程式註冊**]，然後選取您的試用產品應用程式。 接下來，依序選取 [**憑證和秘密**]、[**新增用戶端密碼**]、[描述]、[**永不****過期**]，然後選擇 [**新增**]。 離開此頁面之前，請務必複製此值。）

選取 [**儲存草稿**] 再繼續。

### <a name="marketplace-listing-optional"></a>Marketplace 清單（選擇性）

描述試用產品體驗。

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **描述**（必要）–描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能，以及可協助使用者決定是否要取得您的供應專案的任何相關資訊。 最多可以在此欄位中輸入3000個字元的文字。 
* **存取訊號**（Azure Resource Manager 和邏輯試用產品所需）–說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用您的供應專案的案例，以及客戶在整個試用產品中存取功能的確切方式。 最多可以在此欄位中輸入10000個字元的文字。
* **使用者手冊**（必要）-深入瞭解您的試用產品體驗。 使用者手冊應涵蓋您想要讓客戶從試用產品中取得的確切內容，並做為參考，以取得他們可能會遇到的任何問題。 檔案必須是 PDF 格式，並在上傳後命名為（最多255個字元）。
* 影片 **：新增**影片（選擇性）–可以將影片上傳至 YouTube 或 Vimeo，並在這裡參考連結和縮圖影像（533 x 324 圖元），讓客戶可以查看資訊的逐步解說，以協助他們深入瞭解試用產品，包括如何成功使用供應專案的功能，並瞭解強調其優點的案例。
  * **名稱**（必要）
  * **位址**（僅限 YouTube 或 Vimeo; 必要）
  * **縮圖**（影像檔案必須是 PNG 格式，而 533 x 324 px）。

選取 [**儲存草稿**] 再繼續。

## <a name="publish"></a>發佈

當您完成供應專案的所有必要區段時，請選取入口網站右上角的 [**審查併發布**]。

查看供應專案每個區段的完成狀態。
    - *未啟動*-表示區段尚未觸及，需要完成。
    - *不完整*-表示區段具有必須修正的錯誤，或需要提供更多資訊的詳細資訊。 回到一節，並加以更新。
    - *Complete* -表示區段已完成，所有必要的資料都已提供，而且沒有任何錯誤。 供應專案的所有區段都必須處於「完整」狀態，您才能提交供應專案。

如果這是您第一次發佈此供應專案，您可以將測試指示提供給認證小組，以確保您的應用程式已正確測試，以及有助於瞭解應用程式的任何補充注意事項。

選取 [**提交**] 以提交您的供應專案以供發佈。 我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供您審查和核准。

返回 [合作夥伴中心]，並選取供應專案的 [**上線**]，將您的供應專案發佈至公用（或私用供應專案給私人物件）。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈程式中的**手動驗證**步驟代表您的供應專案和其相關技術資產（尤其是 Azure Resource Manager 範本）的廣泛審查，問題通常會以提取要求（PR）連結的形式呈現。 如需如何檢視和回應這些 PR 的說明，請參閱[處理審核意見反應](./azure-apps-review-feedback.md)。

如果您在一或多個發行步驟中發生錯誤，請在重新發佈您的供應專案之前加以更正。

## <a name="next-steps"></a>後續步驟

* [更新商業市集中的現有供應項目](./update-existing-offer.md)
