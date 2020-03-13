---
title: 在商業 Marketplace 中建立新的 Azure 應用程式供應專案
description: 如何使用 Microsoft 合作夥伴中心的商業 Marketplace 入口網站，建立新的 Azure 應用程式供應專案，以便在 Azure Marketplace、AppSource 或透過雲端解決方案提供者（CSP）方案中進行列出或銷售。
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 69d7bc81f5adc4797be86f946fdb656b4b6cc682
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283364"
---
# <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

此處概述在商業 marketplace 中發佈 Azure 應用程式供應專案的步驟。

## <a name="azure-application-offer-type"></a>Azure 應用程式供應專案類型

本主題概述 Azure 應用程式供應專案的基本概念。  開始在 Marketplace 中發佈新的 Azure 應用程式供應專案之前，您應該先熟悉這些概念。

### <a name="publishing-overview"></a>發佈概觀

[建立解決方案範本的影片和適用于 Azure Marketplace 的受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)是 Azure 應用程式供應專案類型的簡介：

* 有哪些供應專案類型可供使用;
* 需要哪些技術資產;
* 如何撰寫 Azure Resource Manager 範本;
* 開發和測試應用程式 UI;
* 如何發佈應用程式供應專案;
* 應用程式審核進程。

### <a name="types-of-azure-application-plans"></a>Azure 應用程式方案的類型

Azure 應用程式方案有兩種：受控應用程式和解決方案範本。

* **解決方案範本**是在 Marketplace 中發佈解決方案的其中一種主要方式。 當您的解決方案需要超過單一虛擬機器（VM）的額外部署和設定自動化時，會使用此方案類型。  透過解決方案範本，您可以自動提供多個資源，包括 Vm、網路和儲存體資源，以提供複雜的 IaaS 解決方案。  如需建立解決方案範本的詳細資訊，請參閱[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)檔。

* **受控應用程式**與解決方案範本類似，但有一個主要差異。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用受控應用程式，輕鬆建立完全受控的全包式應用程式，並將其傳遞給您的客戶。  如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](https://docs.microsoft.com/azure/managed-applications/overview)。

所有 Azure 應用程式都在 `.zip` 封存的根資料夾中至少包含兩個檔案：

* 名為[mainTemplate](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 Resource Manager 範本檔案。  此範本會定義要部署到客戶 Azure 訂用帳戶中的資源。  如需 Resource Manager 範本的範例，請參閱[Azure 快速入門範本資源庫](https://azure.microsoft.com/resources/templates/)或對應的[GitHub： Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)儲存機制。

* 名為[createUiDefinition](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)之 Azure 應用程式建立體驗的使用者介面定義。  在使用者介面中，您可以指定讓取用者提供參數值的項目。

所有新的 Azure 應用程式供應專案都必須包含[azure 合作夥伴客戶使用狀況](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)的屬性 GUID。

### <a name="before-you-begin"></a>開始之前

請參閱以下 Azure 應用程式文件，其中提供快速入門、教學課程和範例。

* [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 快速入門：

    * [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
    * [發佈應用程式定義](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [部署服務目錄應用程式](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 教學課程：

    * [建立定義檔案](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [發佈 Marketplace 應用程式](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 範例：

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [受控應用程式解決方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>技術知識的基本概念

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。

您的工程小組應具備下列 Microsoft 技術的知識：

* [Azure 服務](https://azure.microsoft.com/services/)的基本瞭解。
* 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)。
* 使用[azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和[azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的知識。
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)的使用知識。
* [JSON](https://www.json.org/)的使用知識。

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

您可以在[Azure 開發人員工具](https://azure.microsoft.com/tools/)頁面中查看可用的工具。  此外，如果您使用 Visual Studio， [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

建立 Azure 應用程式供應專案之前，您必須先[建立合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)並開啟 [[商業 Marketplace] 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，並選取 [**總覽**] 索引標籤。

>[!Note]
>發行供應專案之後，在 [合作夥伴中心] 中對供應專案所做的編輯，只會在重新發佈之後于系統和店面中更新。  進行變更之後，請確定您已提交發行集的供應專案。

### <a name="create-a-new-offer"></a>建立新的供應項目

選取 [ **+ 新增供應**專案] 按鈕，然後選取 [ **Azure 應用程式**] 功能表項目。 [**新增供應**專案] 對話方塊隨即出現。

### <a name="offer-id-and-alias"></a>供應專案識別碼和別名

* **供應**專案識別碼：您帳戶中每個供應專案的唯一識別碼。 在 marketplace 供應專案的 URL 位址中，客戶會看到此識別碼，而 Azure Resource Manager 範本（如果適用）。 <br> <br> 您的供應專案識別碼必須是小寫的英數位元（包括連字號和底線，但不含空格）。 其限制為50個字元，且在您選取 [建立] 之後無法變更。 <br> <br> 例如，如果您在這裡輸入 `test-offer-1`，就會 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`供應專案 URL。 

* **供應專案別名**：用來參照合作夥伴中心內供應專案的名稱。 此名稱不會在 marketplace 中使用，而且與供應專案名稱和其他將向客戶顯示的值不同。 在您選取 [**建立**] 之後，就無法變更這個值。

輸入**供應專案識別碼**和**供應專案別名**之後，請選取 [**建立**]。 接著，您就能夠在您的供應專案的其他所有元件上工作。

## <a name="offer-setup"></a>供應專案設定

[**供應專案設定**] 頁面會要求您提供下列資訊。 完成這些欄位之後，請務必選取 [**儲存**]。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示您的供應專案的絕佳方式，方法是讓他們選擇「在購買前試用」，進而提升轉換並產生高度合格的潛在客戶。 [深入瞭解試用產品。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

若要啟用試用產品，請核取 [**啟用試用**產品] 方塊。 接著，您必須在試用產品的[技術](#types-of-azure-application-plans)設定中設定示範環境，讓客戶可以在一段固定時間內試用您的供應專案。 

>[!Note]
>因為所有 Azure 應用程式都是使用 Azure Resource Manager 範本來執行，所以可以為 Azure 應用程式設定的唯一試用產品類型是以[Azure Resource Manager 為基礎的試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)產品。

#### <a name="additional-test-drive-resources"></a>其他試用產品資源

- [試用技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試用產品行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試用產品總覽一頁](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連接潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理總覽](./commercial-marketplace-get-customer-leads.md)。

繼續進行下一節之前，請記得先**儲存**！

## <a name="properties"></a>屬性

[**屬性**] 頁面可讓您定義用來在 marketplace 上分組供應專案的類別和產業、應用程式版本，以及支援供應專案的法律合約。 完成此頁面後，選取 [**儲存**]。

### <a name="category"></a>類別

選取最少1個，最多三個類別，用來將您的供應專案放入適當的 marketplace 搜尋區域。 請務必在供應專案描述中，呼叫您的供應專案如何支援這些類別。 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 的標準合約

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>供應專案清單

[供應專案清單] 頁面會顯示您的供應專案列出的語言。 目前，**英文（美國）** 是唯一可用的選項。

您將需要針對每個語言/市場定義 marketplace 詳細資料（供應專案名稱、描述、影像等）。 選取語言/市場名稱來提供此資訊。

> [!NOTE]
> 供應專案清單內容（例如描述、檔、螢幕擷取畫面、使用規定等）不一定要使用英文，只要供應專案的描述開頭為片語，「此應用程式僅適用于 [非英文語言]。」 您也可以提供*有用的連結 URL*來提供內容，而不是供應專案清單內容所使用的語言。

### <a name="name"></a>名稱

您在此處輸入的名稱會向客戶顯示，做為供應專案清單的標題。 當您建立供應專案時，此欄位會預先填入您在**供應專案別名**中輸入的文字，但您可以變更此值。 此名稱可能是商標（您可以包含商標或著作權符號）。 名稱不能超過50個字元，而且不能包含任何 emoji。

### <a name="summary"></a>摘要

提供供應專案的簡短描述（最多100個字元），這可用於 marketplace 搜尋結果。

### <a name="long-summary"></a>完整摘要

提供供應專案的較長描述（最多256個字元）。 此描述可用於 marketplace 搜尋結果。

### <a name="description"></a>描述

提供供應專案的較長描述（最多3000個字元）。 在 marketplace 清單總覽中，客戶會看到這項描述。 包含您供應專案的價值主張、主要優點、類別及/或產業關聯、應用程式內購買機會，以及任何必要的公開。 

撰寫描述的一些秘訣：  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在您的價值主張中包含下列專案：
  - 產品的描述
  - 受益于產品的使用者類型
  - 客戶需要或痛苦的產品位址
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。 
- 請考慮使用 HTML 標籤來格式化您的描述，使其更吸引人。

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇性地輸入最多三個搜尋關鍵字，以協助客戶在 marketplace 中尋找您的供應專案。 為了獲得最佳結果，請在您的描述中也嘗試使用這些關鍵字。

### <a name="support-urls"></a>支援 Url

本節可讓您提供連結，以協助客戶深入瞭解您的供應專案。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入您組織隱私權原則的 URL。 貴使用者必須負責確保您的應用程式符合隱私權法律和法規，以及提供有效的隱私權原則。

#### <a name="useful-links"></a>實用的連結

提供解決方案的選擇性補充線上檔。  按一下 [ **+ 新增連結**] 來新增其他有用的連結。

### <a name="contacts"></a>連絡人

在本節中，您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人**。 此資訊不會向客戶顯示，但可供 Microsoft 使用，並可提供給 CSP 合作夥伴。

在**支援連絡人**區段中，您也必須提供**支援 URL** ，讓 CSP 合作夥伴可以在其中找到您的供應專案支援。

### <a name="marketplace-images"></a>Marketplace 映射

在本節中，您可以提供向客戶顯示供應專案時所要使用的標誌和影像。 所有影像都必須是 .png 格式。

#### <a name="store-logos"></a>儲存標誌

提供三種大小的供應專案標誌：**小型（48 x 48）** 、**中型（90 x 90）** 和**大型（216 x 216）** 。

#### <a name="hero"></a>主圖

主圖影像是選擇性的。 如果您提供一個，則必須測量 815 x 290 圖元。

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應專案的運作方式。 您最多可以新增五個螢幕擷取畫面。 所有螢幕擷取畫面都必須是 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇性地新增最多五個示範您供應專案的影片。 這些影片應裝載于 YouTube 和/或 Vimeo。 針對每一個，輸入影片的名稱、URL，以及影片的縮圖影像（1280 x 720 圖元）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

- [Marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>預覽

[**預覽**] 索引標籤可讓您定義有限的**預覽物件**，以便在將供應專案即時發佈到更廣泛的 marketplace 物件之前驗證您的供應專案。

> [!IMPORTANT]
> 您必須先**選取 [上線]** ，供應專案才會在預覽中核取您的供應專案之後，即時發佈給 marketplace 公用物件。

您的預覽物件是由 Azure 訂用帳戶識別碼 Guid 所識別，並與各自的選擇性描述配對。  客戶無法看到這兩個欄位。

手動新增最多10個 Azure 訂用帳戶識別碼，如果上傳 CSV 檔案，則上限為100。  藉由新增這些訂用帳戶，您將會定義可讓您的供應專案預覽存取權的物件，然後才會將其完全發佈。  如果您的供應專案已上線，您仍然可以定義預覽物件，以測試您的供應專案是否有任何變更或更新。

>[!Note]
>預覽物件與私用物件不同。 預覽物件可以在 marketplace 中即時發佈*之前，先*允許其存取您的供應專案。 您也可以選擇建立方案，並將其僅供私用物件（使用 [方案可用性] 索引標籤）使用。  您的預覽物件將能夠查看及驗證所有方案，包括僅在您的供應專案完全發佈至 marketplace 後，才可供私用物件使用的方案。

## <a name="plan-overview"></a>計畫總覽

[**計畫總覽**] 索引標籤可讓您在相同的供應專案中提供不同的方案選項。 這些計畫（在 Cloud Partner 入口網站中稱為 Sku）在計畫類型（解決方案範本與受控應用程式）、營收或物件方面可能有所不同。  設定至少一個方案，以便在 marketplace 中列出您的供應專案。

建立之後，您會在此索引標籤上看到您的方案名稱、識別碼、計畫類型、可用性（公用或私用）、目前發行狀態和任何可用的動作。

**計畫總覽**中的可用**動作**會因方案的目前狀態而有所不同，可能包括：

* 如果方案狀態為 [**草稿**–刪除草稿]。
* 如果方案狀態為 [**即時**] – [停止銷售方案] 或 [同步私用物件]。

### <a name="create-new-plan"></a>建立新的方案

***方案識別碼***-為此供應專案中的每個方案建立唯一的方案識別碼。 此識別碼將會顯示在產品 URL 中的客戶。  僅使用小寫、英數位元、連字號或底線。 此方案識別碼最多可以有50個字元。 選取 [建立] 之後，就無法修改此識別碼。

***方案名稱***-客戶在決定要在您的供應專案內選取哪一個方案時，會看到此名稱。 為此供應專案中的每個方案建立唯一的供應專案名稱。 方案名稱是用來區分可能屬於相同供應專案一部分的軟體方案（例如 供應專案名稱： Windows Server;方案： Windows Server 2016、Windows Server 2019）。

### <a name="plan-setup"></a>規劃設定

[**方案設定**] 索引標籤可讓您設定方案類型的高階設定、是否重複使用來自另一個方案的封裝，以及該計畫應提供的雲端。  您在此索引標籤上的答案將會影響相同方案的其他索引標籤上所顯示的欄位。

#### <a name="plan-type"></a>方案類型

如[Azure 應用程式方案的類型](#types-of-azure-application-plans)中所述，選取您的方案將包含解決方案範本或受控應用程式。

#### <a name="this-plan-reuses-packages"></a>此方案會重複使用封裝

如果您有多個相同類型的方案，而且它們之間的封裝相同，則可以選取**此計畫重複使用另一個方案中的封裝**。  當您選取此選項時，您將能夠為此供應專案選取另一個相同類型的方案，以重複使用中的封裝。 

>[!Note]
>當您從另一個方案重複使用套件時，整個 [技術設定] 索引標籤將會從此方案中消失。  另一個方案中的技術設定詳細資料，包括您在未來所做的任何更新，也會用於此計畫。 <br> <br> 此外，一旦此計畫發佈之後，就無法變更此設定。

#### <a name="cloud-availability"></a>雲端可用性

此方案至少必須在一個雲端中提供。 

選取 [**公用 Azure** ] 選項，讓您的解決方案可部署至所有具有 Marketplace 整合之公用 Azure 區域中的客戶。  深入瞭解[地理可用性](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [ **Azure Government 雲端**] 選項，讓您的解決方案可在「 [Azure Government 雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)」中進行部署，其為美國聯邦、州、地方或部落的客戶提供受控存取權，且合作夥伴可提供這些實體的資格。  身為發行者的您，會負責任何合規性控制、安全性措施，以及服務此雲端社區的最佳作法。  Azure Government 會使用實際隔離的資料中心和網路（僅限美國）。  發佈至[Azure Government](https://aka.ms/azuregovpublish)之前，Microsoft 建議您在環境中測試並驗證您的解決方案，因為某些端點可能會有所不同。 若要暫存並測試您的解決方案，請從這個[連結](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

>[!Note]
>一旦方案發佈為特定雲端中的可用，就無法移除該雲端。

**Azure Government 雲端認證**

只有在 [**雲端可用性**] 下選取 [ **Azure Government 雲端**] 時，才會顯示此選項。

Azure Government 服務會處理受特定政府法規和需求的資料，例如 FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。  若要讓您知道這些程式的認證，您可以提供最多100個連結來描述您的認證。  這些連結可以是直接連結到您在程式上的清單，或連結至您自己網站上的合規性描述。  只有 Azure Government 的雲端客戶才看得到這些連結。

## <a name="plan-listing"></a>計畫清單

[**計畫清單**] 索引標籤會顯示相同供應專案的不同方案之間，可能會有所不同的方案特定清單資訊。

### <a name="name"></a>名稱

已根據您在建立方案時指派的名稱預先填入。  此名稱會顯示為 [軟體方案] 的標題，顯示在 marketplace 中。  最多可包含100個字元。

### <a name="summary"></a>摘要

提供軟體方案的簡短摘要。  最多可包含100個字元。

### <a name="description"></a>描述

此描述可讓您瞭解此軟體方案的獨特之處，以及與您的供應專案內其他軟體方案有何差異。 最多可包含2000個字元。

完成這些欄位之後，請選取 [**儲存**]。

## <a name="availability"></a>可用性

只有解決方案範本方案可以看到 [**可用性**] 索引標籤。  您可以讓所有人都能看到此計畫，只有特定客戶（私用物件），以及是否要隱藏計畫以供其他解決方案範本或受控應用程式使用。

### <a name="plan-audience"></a>規劃物件

您可以選擇將每個方案設定為每個人都可看見，或僅供您選擇的特定物件使用。 您可以使用 Azure 訂用帳戶識別碼來指派此限制物件中的成員資格。

**隱私權/這是私用方案**（選擇性核取方塊）-核取此方塊可讓您的方案成為私用，並只對您選擇的限制物件為可見。 發行為私用方案之後，您可以更新物件，或選擇將方案提供給所有人。 一旦方案發行為每個人都可以看到，每個人都必須保持可見。 （方案無法再次設定為私用計畫）。

**限制的物件（Azure 訂用帳戶識別碼）** -指派可存取此私人方案的物件。 您可以使用 Azure 訂用帳戶識別碼來指派存取權，其中包含指派給每個 Azure 訂用帳戶識別碼的描述。 如果匯入 .csv 試算表檔案，最多可以新增10個訂用帳戶識別碼，或20000客戶訂用帳戶識別碼。  Azure 訂用帳戶識別碼會以 Guid 表示，而字母必須是小寫。

>[!Note]
>私用物件（或受限制的物件）與您在 [[**預覽**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)] 索引標籤中定義的預覽物件不同。 在 marketplace 中發佈供應專案*之前*，允許預覽物件存取您的供應專案。 雖然私用物件指定僅適用于特定方案，但預覽物件可以針對驗證目的來查看所有計劃（私用或非）。

### <a name="hide-plan"></a>隱藏方案

如果您的方案範本僅在透過另一個解決方案範本或受控應用程式參考時間接部署，請核取此方塊以發佈您的解決方案範本，但從客戶直接搜尋和流覽它時將其隱藏。

## <a name="pricing-and-availability"></a>價格與可用性

僅限受管理的應用程式方案可以看到 [**定價與可用性**] 索引標籤。  您可以設定此方案將在其中提供的市場、解決方案管理的每月價格，以及是否要讓所有人都能看到此方案，還是只對特定客戶（私用物件）顯示此計畫。

### <a name="markets"></a>市場

必須至少有一個市場提供每個方案。 選取您想要讓此方案可供使用的任何市場位置的核取方塊。 包含一個搜尋方塊和按鈕，用於選取「稅金已匯款」的國家/地區，其中 Microsoft 免除銷售並代表您使用稅額，以協助。

如果您已經在美國美元（USD）中設定方案的價格，並新增另一個市場位置，新市場的價格將會根據目前的匯率計算。 在發佈之前，請務必先檢查每個市場的價格。 儲存變更之後，您可以使用 [匯出價格（.xlsx）] 連結來檢查定價。

### <a name="pricing"></a>價格

提供此方案的每月價格。  此價格是除了此解決方案所部署的資源所產生的任何 Azure 基礎結構或隨用隨付軟體成本。

以當地貨幣（美元 = 美國貨幣）設定的價格，會使用安裝期間目前可用的匯率，轉換為所有選定市場的本地貨幣。 在發佈之前，請先匯出定價試算表，並查看每個市場的價格，以驗證這些價格。 如果您想要在個別市場中設定自訂價格，請修改並匯入定價試算表。 

>[!Note]
>您必須先儲存定價變更，以啟用匯出定價資料。

在發佈之前，請仔細檢查您的價格，因為在計畫發行後可能會變更的部分有所限制。  

>[!Note]
>一旦您的方案中的市場價格發佈之後，就無法再變更。

### <a name="plan-audience"></a>規劃物件

您可以選擇將每個方案設定為每個人都可看見，或僅供您選擇的特定物件使用。 您可以使用 Azure 訂用帳戶識別碼來指派此限制物件中的成員資格。

**隱私權/這是私用方案**（選擇性核取方塊）-核取此方塊可讓您的方案成為私用，並只對您選擇的限制物件為可見。 發行為私用方案之後，您可以更新物件，或選擇將方案提供給所有人。 一旦方案發行為每個人都可以看到，每個人都必須保持可見。 （方案無法再次設定為私用計畫）。

**限制的物件（Azure 訂用帳戶識別碼）** -指派可存取此私人方案的物件。 您可以使用 Azure 訂用帳戶識別碼來指派存取權，其中包含指派給每個 Azure 訂用帳戶識別碼的描述。 如果匯入 .csv 試算表檔案，最多可以新增10個訂用帳戶識別碼，或20000客戶訂用帳戶識別碼。  Azure 訂用帳戶識別碼會以 Guid 表示，而字母必須是小寫。

>[!Note]
>私用物件（或受限制的物件）與您在 [[**預覽**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)] 索引標籤中定義的預覽物件不同。 在 marketplace 中發佈供應專案*之前*，允許預覽物件存取您的供應專案。 雖然私用物件指定僅適用于特定方案，但預覽物件可以針對驗證目的來查看所有計劃（私用或非）。

## <a name="technical-configuration"></a>技術設定 

[**技術**設定] 索引標籤可讓您上傳部署套件，讓客戶能夠部署您的方案。

>[!Note]
>如果您已將此計畫設定為從 [**計畫設定**] 索引標籤上的另一個方案重複使用封裝，則不會顯示此索引標籤。

### <a name="package-details"></a>套件詳細資料

[**套件詳細資料**] 子標籤可讓您編輯技術設定的草稿版本。

***版本***-指派技術設定的目前版本。  請在每次發行變更至此頁面時，遞增此版本。 版本的格式必須是 `{integer}.{integer}.{integer}`。

***封裝***檔案（`.zip`）-此套件包含此方案所需的所有範本檔案，以及封裝為 `.zip` 檔案的任何其他資源。

所有 Azure 應用程式方案套件都必須在 `.zip` 封存的根資料夾中包含這兩個檔案：

* 名為[mainTemplate](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 Resource Manager 範本檔案。  此範本會自動將資源部署到客戶 Azure 訂用帳戶。  如需 Resource Manager 範本的範例，請參閱[Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/)或對應的[GitHub： Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)儲存機制。

* 名為[createUiDefinition](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)之 Azure 應用程式建立體驗的使用者介面定義。

所有新的 Azure 應用程式供應專案也必須包含[azure 合作夥伴客戶使用狀況](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)的屬性 GUID。

### <a name="previously-published-packages"></a>先前發行的封裝 

**先前發佈**的 [套件] 子功能可讓您查看所有已發行的技術設定版本。

## <a name="technical-configuration-managed-application-plans-only"></a>技術設定（僅限受管理的應用程式方案）

受控應用程式計畫在 [**技術**設定] 索引標籤上會有額外的複雜性，而不是上述**版本**和**套件**檔案欄位。 

### <a name="enable-just-in-time-jit-access"></a>啟用即時（JIT）存取

選取此選項，以啟用此計畫的即時（JIT）存取。  JIT 存取可讓您要求更高的受控應用程式資源存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以有更高的存取權。  如需詳細資訊，請參閱[啟用和要求 Azure 受控應用程式的即時存取](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  若要要求受控應用程式的取用者授與您的帳戶永久存取權，請將此選項保留為未核取。

>[!Note]
>請務必更新您的 `createUiDefinition.json` 檔案，才能支援這項功能。  

### <a name="deployment-mode"></a>部署模式

選取部署此計畫時，是否要設定**完整**或累加**部署模式**： 

* 在**完整模式**中，客戶應用程式的重新部署將會導致在 `mainTemplate.json`中未定義資源時，移除受控資源群組中的資源。 
* 在累加**模式**中，應用程式的重新部署會讓現有的資源保持不變。

若要深入瞭解部署模式，請參閱[Azure Resource Manager 部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

### <a name="notification-endpoint-url"></a>通知端點 URL

指定 HTTPS Webhook 端點，以接收此方案版本的受控應用程式實例上所有 CRUD 作業的相關通知。

### <a name="customize-allowed-customer-actions"></a>自訂允許的客戶動作

選取此選項可指定客戶除了預設可用的「`*/read`」動作以外，可以對受控資源執行哪些動作。 

列出您想要讓客戶在此處執行的其他動作（以分號分隔）。  如需詳細資訊，請參閱[瞭解 Azure 資源的拒絕指派](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  如需可用動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允許取用者重新啟動虛擬機器，可將 `Microsoft.Compute/virtualMachines/restart/action` 新增至允許的動作。

### <a name="global-azure--azure-government-cloud"></a>全球 Azure/Azure Government 雲端

指出在每個支援的雲端中，誰應具備此受控應用程式的管理存取權。  您想要授與受控資源群組之許可權的使用者、群組或應用程式，都是使用 Azure Active Directory （AAD）身分識別來識別。

***Azure Active Directory 租使用者識別碼***-AAD 租使用者識別碼（也稱為目錄識別碼），其中包含您想要授與許可權的使用者、群組或應用程式的身分識別。  您可以在 [Azure 入口網站] 的 [ [Azure Active Directory 屬性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)] 中找到您的 AAD 租使用者識別碼。

***授權***-新增您想要授與受控資源群組之許可權的使用者、群組或應用程式的 AZURE ACTIVE DIRECTORY 物件識別碼。 依據使用者的主體識別碼來識別使用者，這可以在 Azure 入口網站的 [ [Azure Active Directory 使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)] 分頁中找到。

針對每個主體，從清單中選取其中一個 Azure AD 內建角色（擁有者或參與者）。 您選取的角色將會描述主體對客戶訂用帳戶中的資源所擁有的許可權。 如需詳細資訊，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。  如需角色型存取控制（RBAC）的詳細資訊，請參閱[開始使用 Azure 入口網站中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)。

>[!Note]
>雖然您最多可以在每個雲端新增100個授權，但建立 Active Directory 使用者群組並在「主體識別碼」中指定其識別碼，通常會比較容易。  這可讓您在部署方案之後，將更多使用者新增至管理群組，並減少更新方案的需求，只是要新增更多授權。

### <a name="policy-settings"></a>原則設定

將[Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview)套用至您的受控應用程式，以指定已部署解決方案的合規性需求。  如需了解原則定義和參數值格式，請參閱 [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)。  您最多可以設定五個原則，而且每個原則選項只能有一個實例。  有些原則需要額外的參數。  稽核原則需要標準 SKU。  原則名稱限制為50個字元。

## <a name="co-sell"></a>共同銷售

在 [共同銷售] 索引標籤上提供資訊完全是發佈供應專案的選擇性選項。 必須達到共同銷售就緒和 IP 共同銷售就緒狀態。 Microsoft 銷售小組會使用您提供的資訊，在評估其是否符合客戶需求時，深入瞭解您的解決方案。 它無法直接提供給客戶。

如需完成此索引標籤的詳細資訊，請參閱[合作夥伴中心的共同銷售選項](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="test-drive"></a>試用產品

[**試用**產品] 索引標籤可讓您設定示範（或「試用產品」），讓客戶可以在承諾購買之前先試用您的供應專案。 若要深入瞭解，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  如果您不想再為供應專案提供試用產品，請返回 [供應專案**設定**] 頁面，然後取消核取 [**啟用試用**產品]。

### <a name="technical-configuration"></a>技術設定

Azure 應用程式原本就使用 Azure Resource Manager 的試用產品類型。  如需詳細資訊，請參閱[Azure Resource Manager 試用](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)產品的技術設定。

### <a name="deployment-subscription-details"></a>部署訂用帳戶詳細資料

若要代表您部署試用產品，請建立並提供另一個唯一的 Azure 訂用帳戶。 （不需要用於 Power BI 試用產品）。

- **Azure 訂**用帳戶識別碼（Azure Resource Manager 和邏輯應用程式的必要）：輸入訂用帳戶識別碼，以授與存取權給您的 azure 帳戶服務，以進行資源使用量報告和計費。 我們建議您考慮[建立個別的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-create-subscription)用帳戶，以用於試用產品（如果您還沒有的話）。 您可以藉由登入[Azure 入口網站](https://portal.azure.com/)並流覽至左側功能表的 [**訂閱**] 索引標籤，來尋找您的 AZURE 訂用帳戶識別碼。 選取此索引標籤會顯示您的訂用帳戶識別碼（例如 "a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租使用者識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[租使用者識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [屬性]，然後尋找列出的**目錄識別碼**（例如50c464d3-4930-494c-963c-1e951d15360e）。 您也可以在下列位置使用您的功能變數名稱 URL 來查詢組織的租使用者識別碼： [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租使用者名稱**（動態365所需）：輸入您的 AZURE ACTIVE DIRECTORY （AD）名稱。 若要尋找此名稱，請登入[Azure 入口網站](https://portal.azure.com/)，在右上角，您的租使用者名稱會列在 [您的帳戶名稱] 底下。

- **Azure AD 應用程式識別碼**（必要）：輸入您的 AZURE ACTIVE DIRECTORY （AD）[應用程式識別碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要尋找此識別碼，請登入[Azure 入口網站](https://portal.azure.com/)，選取左側功能表中的 [Active Directory] 索引標籤，選取 [**應用程式註冊**]，然後尋找所列的**應用程式識別碼**編號（例如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 應用程式用戶端密碼**（必要）：輸入您的 Azure AD 應用程式[用戶端密碼](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要尋找此值，請登入[Azure 入口網站](https://portal.azure.com/)。 選取左側功能表中的 [ **Azure Active Directory** ] 索引標籤，選取 [**應用程式註冊**]，然後選取您的試用產品應用程式。 接下來，依序選取 [**憑證和秘密**]、[**新增用戶端密碼**]、[描述]、[**永不** **過期**]，然後選擇 [**新增**]。 請務必向下複製值。 （請勿在複製值之前離開頁面，否則您將無法存取此值。）

繼續進行下一節之前，請記得先**儲存**！

### <a name="test-drive-listings-optional"></a>試用產品清單（選擇性）

在 [**試用**產品] 索引標籤下找到的 [**試用產品清單**] 選項會顯示您的試用產品可供使用的語言（和市場），目前的英文（美國）是唯一可用的位置。 此外，此頁面會顯示特定語言清單的狀態，以及它所加入的日期/時間。 您必須為每個語言/市場定義試用產品的詳細資料（描述、使用者手冊、影片等等）。

- **描述**（必要）：描述您的試用產品、要示範的專案、要進行實驗的目標、探索的功能，以及可協助使用者決定是否要取得您供應專案的任何相關資訊。 最多可以在此欄位中輸入3000個字元的文字。 

- **存取訊號**（Azure Resource Manager 和邏輯試用產品所需）：說明客戶必須知道才能存取和使用此試用產品的內容。 逐步解說使用您的供應專案的案例，以及客戶在整個試用產品中存取功能的確切方式。 最多可以在此欄位中輸入10000個字元的文字。

- **使用者手冊**（必要）：您的試用產品體驗的深入逐步解說。 使用者手冊應涵蓋您想要讓客戶從試用產品中取得的確切內容，並做為參考，以取得他們可能會遇到的任何問題。 檔案必須是 PDF 格式，並在上傳後命名為（最多255個字元）。

- 影片 **：新增**影片（選擇性）：您可以將影片上傳到 YouTube 或 Vimeo，並在這裡參考連結和縮圖影像（533 x 324 圖元），讓客戶可以查看資訊的逐步解說，以協助他們深入瞭解試用產品，包括如何成功使用供應專案的功能，並瞭解強調其優點的案例。
  - **名稱**（必要）
  - **URL （僅限 YouTube 或 Vimeo）** （必要）
  - **縮圖（533 x 324 px）** ：影像檔案必須是 PNG 格式。

完成這些欄位之後，請選取 [**儲存**]。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應專案以供預覽

當您完成供應專案的所有必要區段之後，請選取入口網站右上角的 [**發佈**]。 系統會將您重新導向至 [**審核及發佈**] 頁面。 

如果這是您第一次發佈此供應專案，您可以：

- 請參閱供應專案每個區段的完成狀態。
    - *未啟動*-表示區段尚未觸及，需要完成。
    - *不完整*-表示區段具有必須修正的錯誤，或需要提供更多資訊的詳細資訊。 請返回一節並加以更新。
    - *Complete* -表示區段已完成，所有必要的資料都已提供，而且沒有任何錯誤。 供應專案的所有區段都必須處於「完整」狀態，您才能提交供應專案。
- 將測試指示提供給認證小組，以確保您的應用程式已正確測試，以及有助於瞭解應用程式的任何補充注意事項。
- 選取 [**提交**] 以提交供應專案進行發佈。 我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供您審查和核准。 返回 [合作夥伴中心]，並選取供應專案的 [**上線**]，將您的供應專案發佈至公用（或私用供應專案給私人物件）。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈程式中的**手動驗證**步驟代表您的供應專案和其相關技術資產（尤其是 Azure Resource Manager 範本）的廣泛審查，問題通常會以提取要求（PR）連結的形式呈現。 如需如何檢視和回應這些 PR 的說明，請參閱[處理審核意見反應](./azure-apps-review-feedback.md)。

如果您在一或多個發佈步驟中遇到了錯誤，則必須加以更正，然後重新發佈您的供應項目。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
