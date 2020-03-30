---
title: 在商業應用商店中創建新的 Azure 應用產品/服務
description: 如何使用 Microsoft 合作夥伴中心的商業應用商店門戶創建新的 Azure 應用產品/服務，用於在 Azure 應用商店、AppSource 或通過雲解決方案供應商 （CSP） 計畫進行上市或銷售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 0ff1bbd976273a7d0cbfb22effebdf45c84d2f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277234"
---
# <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

此處概述了在商業市場中發佈 Azure 應用程式產品/服務的步驟。

## <a name="azure-application-offer-type"></a>Azure 應用程式產品/服務類型

本主題概述了有關 Azure 應用程式提供的基礎知識。  在開始在應用商店中發佈新的 Azure 應用程式產品/服務之前，應熟悉這些概念。

### <a name="publishing-overview"></a>發佈概觀

Azure[應用商店的視頻構建解決方案範本和託管應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)是 Azure 應用程式產品類型簡介：

* 可用的產品/服務類型;
* 需要哪些技術資產;
* 如何編寫 Azure 資源管理器範本;
* 開發和測試應用程式 UI;
* 如何發佈應用產品/服務;
* 應用程式審閱過程。

### <a name="types-of-azure-application-plans"></a>Azure 應用程式計畫的類型

有兩種類型的 Azure 應用程式計畫，託管應用程式和解決方案範本。

* **解決方案範本**是在應用商店中發佈解決方案的主要方法之一。 當解決方案需要單個虛擬機器 （VM） 以外的其他部署和配置自動化時，使用此計畫類型。  借助解決方案範本，您可以自動提供多個資源，包括 VM、網路和存儲資源，以提供複雜的 IaaS 解決方案。  有關構建解決方案範本的詳細資訊，請參閱[Azure 資源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)文檔。

* **託管應用程式**類似于解決方案範本，有一個關鍵區別。 在受控應用程式中，資源會部署到應用程式發行者所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 身為發行者，您可以指定解決方案持續支援的成本。 使用託管應用程式輕鬆構建和交付完全託管的交鑰匙應用程式給您的客戶。  如需受控應用程式的優點與類型詳細資訊，請參閱 [Azure 受控應用程式概觀](https://docs.microsoft.com/azure/managed-applications/overview)。

所有 Azure 應用程式在存檔的根資料夾中至少包含兩個`.zip`檔：

* 名為[mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的資源管理器範本檔。  這是定義要部署到客戶的 Azure 訂閱中的資源的範本。  有關資源管理器範本的示例，請參閱[Azure 快速入門範本庫](https://azure.microsoft.com/resources/templates/)或相應的[GitHub：Azure 資源管理器快速入門範本](https://github.com/azure/azure-quickstart-templates)存儲庫。

* 名為[createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)的 Azure 應用程式創建體驗的使用者介面定義。  在使用者介面中，您可以指定讓取用者提供參數值的項目。

所有新的 Azure 應用程式產品/服務都必須包括[Azure 合作夥伴客戶使用方式歸因 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)。

### <a name="before-you-begin"></a>開始之前

請參閱以下 Azure 應用程式文件，其中提供快速入門、教學課程和範例。

* [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 快速入門：

    * [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
    * [發佈應用程式定義](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [部署服務目錄應用](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 教學課程：

    * [建立定義檔案](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [發佈市集應用程式](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 範例：

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure 電源外殼](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [受控應用程式解決方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>技術知識基礎知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。

您的工程小組應具備下列 Microsoft 技術的知識：

* 對[Azure 服務](https://azure.microsoft.com/services/)的基本瞭解。
* 如何[設計和構建 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)。
* [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)[、Azure 存儲](https://azure.microsoft.com/services/?filter=storage#storage)和 Azure[網路](https://azure.microsoft.com/services/?filter=networking#networking)的工作知識。
* [Azure 資源管理器](https://azure.microsoft.com/features/resource-manager/)的工作知識。
* [JSON](https://www.json.org/)的工作知識 。

### <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

* [Azure 電源外殼](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

建議您將下列工具新增至開發環境：

* [Azure 存儲資源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：
    * 延伸模組：[Azure Resource Manager 工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 延伸模組：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 延伸模組：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

您可以在[Azure 開發人員工具](https://azure.microsoft.com/tools/)頁中查看可用工具。  此外，如果您正在使用視覺工作室，[視覺工作室市場](https://marketplace.visualstudio.com/)。

## <a name="create-an-azure-application-offer"></a>建立 Azure 應用程式供應項目

在創建 Azure 應用程式產品/服務之前，必須先[創建合作夥伴中心帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)並打開["商業市場"儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，並選中 **"概覽**"選項卡。

>[!Note]
>發佈產品/服務後，只有在重新發佈後，才會在系統和網店中更新對合作夥伴中心中提供的報價進行編輯。  請確保您在進行更改後提交要發佈的要約。

### <a name="create-a-new-offer"></a>建立新的供應項目

選擇 **" 新增產品/服務**"按鈕，然後選擇**Azure 應用程式**功能表項目。 將顯示"**新建產品/服務**"對話方塊。

### <a name="offer-id-and-alias"></a>優惠 ID 和別名

* **優惠 ID**：帳戶中每個產品/服務的唯一識別碼。 此 ID 將在市場產品/服務的 URL 位址和 Azure 資源管理器範本（如果適用）中向客戶可見。 <br> <br> 優惠 ID 必須是小寫字母數位字元（包括連字號和底線，但沒有空格）。 它限制為 50 個字元，在選擇"創建"後無法更改。 <br> <br> 例如，如果您在此處輸入`test-offer-1`，則產品/服務 URL`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`將為 。 

* **優惠別名**：用於指合作夥伴中心內產品/服務的名稱。 此名稱不會在市場中使用，並且與向客戶顯示的產品/服務名稱和其他值不同。 選擇 "**創建**"後，無法更改此值。

輸入**優惠 ID**和**優惠別名**後，選擇 **"創建**"。 然後，您將能夠處理產品/服務的所有其他部分。

## <a name="offer-setup"></a>優惠設置

**"產品/服務設置"** 頁要求提供以下資訊。 請務必在完成這些欄位後選擇 **"保存**"。

### <a name="test-drive"></a>試用產品

試駕是向潛在客戶展示您的報價的絕佳方式，為他們提供"購買前試用"選項，從而提高轉化率並生成高素質潛在客戶。 [瞭解有關測試磁碟機的更多詳細資訊。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

要啟用試駕，請選中 **"啟用試駕"** 框。 然後，您需要在[測試磁碟機技術配置](#types-of-azure-application-plans)配置中配置演示環境，以便客戶在固定時間段內試用您的產品/服務。 

>[!Note]
>由於所有 Azure 應用程式都是使用 Azure 資源管理器範本實現的，因此可以為 Azure 應用程式佈建的唯一類型的測試磁碟機是基於[Azure 資源管理器的測試磁碟機](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

#### <a name="additional-test-drive-resources"></a>其他試駕資源

- [試駕技術最佳實踐](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [試駕行銷最佳實踐](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試駕概述一個呼叫器](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>連接潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有關詳細資訊，請參閱[潛在客戶管理概述](./commercial-marketplace-get-customer-leads.md)。

在繼續下一節之前，請記住**保存**！

## <a name="properties"></a>屬性

屬性**頁面**允許您定義用於在市場對產品/服務進行分組的類別和行業、應用版本和支援產品/服務的法律合同。 完成此頁面後選擇 **"保存**"。

### <a name="category"></a>類別

選擇至少一個且最多三個類別，這些類別將用於將產品/服務放入相應的市場搜索區域。 請務必在產品/服務說明中標注您的產品/服務如何支援這些類別。 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微軟商業市場的標準合同

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>優惠清單

"產品/服務"清單頁顯示您的優惠將在其中列出的語言。 目前，**英語（美國）** 是唯一可用的選項。

您需要為每個語言/市場定義市場詳細資訊（報價名稱、說明、圖像等）。 選擇語言/市場名稱以提供此資訊。

> [!NOTE]
> 報價清單內容（如說明、文檔、螢幕截圖、使用條款等）不需要以英文表示，只要要約說明以短語開頭，"此應用程式僅以 [非英語語言]提供。 提供*有用的連結 URL*以提供產品/服務清單內容中所用語言以外的語言提供內容也是可接受的。

### <a name="name"></a>名稱

您在此處輸入的名稱將作為產品/服務清單的標題顯示給客戶。 此欄位預填充了創建產品/服務時為 **"產品/服務"別名**輸入的文本，但您可以更改此值。 此名稱可以注冊商標（您可以包括商標或版權符號）。 名稱不能超過 50 個字元，不能包含任何表情符號。

### <a name="summary"></a>總結

提供產品/服務的簡短說明（最多 100 個字元），可在市場搜尋結果中使用。

### <a name="long-summary"></a>長摘要

提供更久的報價說明（最多 256 個字元）。 該說明可用於市場搜尋結果。

### <a name="description"></a>描述

提供更久的報價說明（最多 3，000 個字元）。 此描述將顯示在市場清單概述中的客戶。 包括您的產品/服務的價值主張、主要優勢、類別和/或行業協會、應用內購買機會以及任何必需的披露。 

編寫說明的一些提示：  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在價值主張中包括以下專案：
  - 產品的描述
  - 從產品中受益的使用者類型
  - 產品解決的客戶需求或痛苦
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。 
- 請考慮使用 HTML 標籤來格式化您的描述，使其更具吸引力。

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜索關鍵字，説明客戶在市場上找到您的產品/服務。 為獲得最佳效果，請嘗試在描述中使用這些關鍵字。

### <a name="support-urls"></a>支援 URL

本部分允許您提供連結，説明客戶更好地瞭解您的產品/服務。

#### <a name="privacy-policy-url"></a>隱私權原則 URL

輸入組織隱私政策的 URL。 您有責任確保您的應用符合隱私法律和法規，並提供有效的隱私政策。

#### <a name="useful-links"></a>實用的連結

提供有關解決方案的可選補充連線文檔。  通過按一下 **" 添加連結**"添加其他有用的連結。

### <a name="contacts"></a>連絡人

在本節中，您必須提供**支援連絡人**和**工程連絡人**的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，並且可能會提供給 CSP 合作夥伴。

在 **"支援"連絡人**部分中，還必須提供**支援 URL，** 其中 CSP 合作夥伴可以找到對您的產品/服務的支援。

### <a name="marketplace-images"></a>市場映射

在本節中，您可以提供向客戶展示產品/服務時將使用的徽標和圖像。 所有圖像必須採用 .png 格式。

#### <a name="store-logos"></a>Microsoft Store標誌。

提供三種尺寸的產品徽標：**小 （48 x 48）**、**中等 （90 X 90）** 和大 **（216 x 216）**。

#### <a name="hero"></a>主圖

英雄形象是可選的。 如果提供一個，它必須測量 815 x 290 圖元。

#### <a name="screenshots"></a>螢幕擷取畫面

添加顯示優惠工作原理的螢幕截圖。 您最多可以添加五個螢幕截圖。 所有螢幕截圖必須為 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇添加最多五個演示您的優惠的視頻。 這些視頻應託管在 YouTube 和/或 Vimeo 上。 對於每個視頻，輸入視頻的名稱、URL 和視頻的縮略圖圖像（1280 x 720 圖元）。

#### <a name="additional-marketplace-listing-resources"></a>其他市場清單資源

- [市場產品/服務清單的最佳實踐](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>預覽

通過 **"預覽"** 選項卡，您可以在向更廣泛的市場受眾即時發佈優惠之前定義有限的**預覽受眾以**驗證您的優惠。

> [!IMPORTANT]
> 在預覽版中查看您的優惠後，您必須選擇 **"上線**"，然後才能將優惠即時發佈到市場公眾受眾。

預覽受眾由 Azure 訂閱 ID GUID 標識，並為每個訪問物件加上可選說明。  這兩個欄位對客戶都不可見。

手動添加最多 10 個 Azure 訂閱標識，如果上載 CSV 檔，則最多添加 100 個。  通過添加這些訂閱，您將定義一個受眾，該受眾將允許在產品/服務完全發佈之前預覽訪問產品/服務。  如果您的優惠已是即時的，您仍然可以定義預覽受眾以測試產品/服務的任何更改或更新。

>[!Note]
>預覽觀眾與私人觀眾不同。 預覽受眾在市場現場發佈*之前*，可以訪問您的優惠。 您還可以選擇創建計畫，使其僅對私人受眾可用（使用計畫可用性選項卡）。  您的預覽受眾將能夠查看和驗證所有計劃，包括計畫，這些計畫僅在您的優惠完全發佈到市場後才能提供給私人受眾。

## <a name="plan-overview"></a>計畫概述

"**計畫概述**"選項卡使您能夠在同一產品/服務中提供不同的計畫選項。 這些計畫（在雲合作夥伴門戶中稱為 SKU）在計畫類型（解決方案範本與託管應用程式）、貨幣化或受眾方面可能有所不同。  配置至少一個計畫，以便在市場上列出您的產品/服務。

創建後，您將看到計畫名稱、ID、計畫類型、可用性（公共或私有）、當前發佈狀態以及此選項卡上的任何可用操作。

**計畫概述**中可用的**操作**因計畫的目前狀態而異，可能包括：

* 如果計畫狀態為**草稿**- 刪除草稿。
* 如果計畫狀態為 **"即時**" - 停止銷售計畫或同步私人受眾。

### <a name="create-new-plan"></a>建立新的方案

***計畫 ID*** - 為此產品/服務中的每個計畫創建唯一的計畫 ID。 此 ID 將在產品 URL 中對客戶可見。  僅使用小寫、字母數位字元、破折號或底線。 此計畫 ID 最多允許 50 個字元。 選擇創建後無法修改此 ID。

***計畫名稱***- 客戶在決定在您的產品/服務中選擇哪個計畫時，將看到此名稱。 為此產品/服務中的每個計畫創建唯一的優惠名稱。 計畫名稱用於區分可能是同一產品/服務一部分的軟體計畫（例如， 產品名稱：Windows 伺服器;計畫： Windows 伺服器 2016， Windows 伺服器 2019）.

### <a name="plan-setup"></a>計畫設置

"**計畫設置**"選項卡使您能夠為計畫類型設置高級配置，它是否重用來自其他計畫的包，以及計畫應在哪些雲中可用。  此選項卡上的答案將影響同一計畫的其他選項卡上顯示哪些欄位。

#### <a name="plan-type"></a>計畫類型

如[Azure 應用程式計畫類型](#types-of-azure-application-plans)中所述，選擇計畫是包含解決方案範本還是託管應用程式。

#### <a name="this-plan-reuses-packages"></a>此計畫重用包

如果您有多個相同類型的計畫，並且包之間相同，則可以選擇**此計畫重用來自另一個計畫的包**。  選擇此選項後，您可以選擇相同類型的其他計畫之一，以便此產品/服務從中重用包。 

>[!Note]
>當您從其他計畫重用包時，整個技術配置選項卡將從這個計畫中消失。  其他計畫中的技術配置詳細資訊（包括您將來進行的任何更新）也將用於此計畫。 <br> <br> 此外，此計畫發佈後，無法更改此設置。

#### <a name="cloud-availability"></a>雲可用性

此計畫必須在至少一個雲中提供。 

選擇 **"公共 Azure"** 選項可使解決方案部署到具有應用商店集成的所有公共 Azure 區域的客戶。  瞭解有關[地理可用性](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)的更多資訊。

選擇**Azure 政府雲**選項可使解決方案在 Azure[政府雲](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)中部署，這是一種政府社區雲，對有資格為這些實體提供服務的來自美國聯邦、州、本地或部落的客戶以及合作夥伴具有受控存取權限。  作為發行者，您負責為此雲社區提供服務的任何合規性控制、安全措施和最佳實踐。  Azure 政府使用物理隔離的資料中心和網路（僅位於美國）。  在發佈到[Azure 政府](https://aka.ms/azuregovpublish)之前，Microsoft 建議您在環境中測試和驗證解決方案，因為某些終結點可能有所不同。 要暫示和測試您的解決方案，請從此[連結](https://azure.microsoft.com/global-infrastructure/government/request/)請求試用帳戶。

>[!Note]
>在特定雲中發佈計畫後，無法刪除該雲。

**Azure 政府雲認證**

僅當在**雲可用性**下選擇**Azure 政府雲**時，此選項才可見。

Azure 政府服務處理受某些政府法規和要求約束的資料，例如 FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。  為了提高您對這些計畫的認證的認識，您最多可以提供 100 個描述您的認證的連結。  這些連結可以是直接指向您在程式上清單的連結，也可以是指向您自己網站上遵守這些連結的連結。  這些連結將僅對 Azure 政府雲客戶可見。

## <a name="plan-listing"></a>計畫清單

**"計畫清單**"選項卡顯示特定于計畫的清單資訊，這些資訊可以不同于同一報價的不同計畫。

### <a name="name"></a>名稱

根據創建計畫時分配的計畫的名稱進行預填充。  此名稱將顯示為市場上顯示的此"軟體計畫"的標題。  可能最多包含 100 個字元。

### <a name="summary"></a>總結

提供軟體計畫的簡短摘要。  可能最多包含 100 個字元。

### <a name="description"></a>描述

此說明是一個解釋是什麼讓此軟體計畫獨一無二以及您產品/服務中其他軟體計畫的任何差異的機會。 可能包含多達 2，000 個字元。

完成這些欄位後選擇 **"保存**"。

## <a name="availability"></a>可用性

"**可用性**"選項卡僅對解決方案範本計畫可見。  您可以使計畫僅對特定客戶（私人訪問群體）以及是否隱藏計畫以僅供其他解決方案範本或託管應用程式使用，使計畫對所有人可見。

### <a name="plan-audience"></a>規劃受眾

您可以選擇將每個計畫配置為對所有人可見，或者僅對您選擇的特定受眾可見。 您可以使用 Azure 訂閱標識分配此受限訪問群體的成員身份。

**隱私 /這是一個私人計畫**（可選核取方塊） - 選中此框，使您的計畫成為私有的，並且僅對您選擇的受限受眾可見。 作為私人計畫發佈後，您可以更新受眾或選擇使計畫可供所有人使用。 一旦計畫發佈為對每個人都可見，它必須保持對每個人都可見。 （無法再次將計畫配置為私有計劃）。

**受限訪問群體（Azure 訂閱標識）** - 分配將有權訪問此私人計畫的受眾。 使用 Azure 訂閱 ID 分配存取權限，並選擇包括分配的每個 Azure 訂閱 ID 的說明。 最多可以添加 10 個訂閱 I，如果導入 .csv 試算表檔，最多可以添加 20，000 個客戶訂閱 I。  Azure 訂閱標識表示為 GUID，字母必須小寫。

>[!Note]
>私人受眾（或受限觀眾）不同于您在[**"預覽"**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)選項卡中定義的預覽受眾。 在產品/服務在市場上即時發佈*之前*，預覽受眾可以訪問您的優惠。 雖然專用受眾指定僅適用于特定計劃，但預覽觀眾可以出於驗證目的查看所有計劃（私有計劃或計畫）。

### <a name="hide-plan"></a>隱藏計畫

如果解決方案範本打算僅在通過其他解決方案範本或託管應用程式引用時間接部署，請選中此框以發佈解決方案範本，但直接從客戶搜索和流覽它時將其隱藏。

## <a name="pricing-and-availability"></a>價格與可用性

**定價和可用性**選項卡僅對託管應用程式計畫可見。  您可以配置此計畫可用的市場、解決方案管理每月的價格，以及是使計畫對所有人可見，還是僅對特定客戶（私人受眾）可見。

### <a name="markets"></a>市場

每個計畫必須至少在一個市場可用。 選擇您希望提供此計畫的任何市場位置的核取方塊。 包含用於選擇"免稅"國家/地區的搜索框和按鈕，其中 Microsoft 代表您匯出銷售和使用稅，以説明。

如果您已為計畫設定價格（美元），並添加其他市場位置，則新市場的價格將根據當前匯率計算。 在出版之前，務必查看每個市場的價格。 在保存更改後，可以使用"出口價格 （xlsx）"連結查看定價。

### <a name="pricing"></a>定價

提供此計畫的每月價格。  此價格是此解決方案部署的資源產生的任何 Azure 基礎結構或即用即付軟體成本的補充。

以當地貨幣（美元 = 美元）設置的價格使用設置期間可用的當前匯率轉換為所有選定市場的當地貨幣。 通過匯出定價試算表並查看每個市場的價格，在發佈之前驗證這些價格。 如果要在單個市場設置自訂價格，請修改並導入定價試算表。 

>[!Note]
>您必須首先保存定價更改，以啟用定價資料的匯出。

在發佈之前，請仔細查看您的價格，因為在計畫發佈後，可能會更改哪些內容有一些限制。  

>[!Note]
>一旦計畫中市場的價格公佈，以後就改變不了。

### <a name="plan-audience"></a>規劃受眾

您可以選擇將每個計畫配置為對所有人可見，或者僅對您選擇的特定受眾可見。 您可以使用 Azure 訂閱標識分配此受限訪問群體的成員身份。

**隱私 /這是一個私人計畫**（可選核取方塊） - 選中此框，使您的計畫成為私有的，並且僅對您選擇的受限受眾可見。 作為私人計畫發佈後，您可以更新受眾或選擇使計畫可供所有人使用。 一旦計畫發佈為對每個人都可見，它必須保持對每個人都可見。 （無法再次將計畫配置為私有計劃）。

**受限訪問群體（Azure 訂閱標識）** - 分配將有權訪問此私人計畫的受眾。 使用 Azure 訂閱 ID 分配存取權限，並選擇包括分配的每個 Azure 訂閱 ID 的說明。 最多可以添加 10 個訂閱 I，如果導入 .csv 試算表檔，最多可以添加 20，000 個客戶訂閱 I。  Azure 訂閱標識表示為 GUID，字母必須小寫。

>[!Note]
>私人受眾（或受限觀眾）不同于您在[**"預覽"**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)選項卡中定義的預覽受眾。 在產品/服務在市場上即時發佈*之前*，預覽受眾可以訪問您的優惠。 雖然專用受眾指定僅適用于特定計劃，但預覽觀眾可以出於驗證目的查看所有計劃（私有計劃或計畫）。

## <a name="technical-configuration"></a>技術配置 

**技術配置**選項卡允許您上載部署包，以便客戶能夠部署您的計畫。

>[!Note]
>如果此計畫配置為在 **"計畫設置"** 選項卡上重用來自其他計畫的包，則此選項卡將不可見。

### <a name="package-details"></a>套件詳細資料

通過"**包詳細資訊**"子選項卡，您可以編輯技術配置的草稿版本。

***版本***- 分配技術配置的當前版本。  每次發佈此頁的更改時，都會增加此版本。 版本必須採用格式`{integer}.{integer}.{integer}`。

***包檔***`.zip`（ ） - 此包包含此計畫所需的所有範本檔，以及作為`.zip`檔打包的任何其他資源。

所有 Azure 應用程式計畫包都必須在存檔的根資料夾中包含這兩`.zip`個檔：

* 名為[mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的資源管理器範本檔。  此範本自動將資源部署到客戶 Azure 訂閱中。  有關資源管理器範本的示例，請參閱[Azure 快速入門範本庫](https://azure.microsoft.com/documentation/templates/)或相應的[GitHub：Azure 資源管理器快速入門範本](https://github.com/azure/azure-quickstart-templates)存儲庫。

* 名為[createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)的 Azure 應用程式創建體驗的使用者介面定義。

所有新的 Azure 應用程式產品/服務還必須包括[Azure 合作夥伴客戶使用方式歸因](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)GUID。

### <a name="previously-published-packages"></a>以前發佈的套裝軟體 

通過 **"以前發佈的包**子選項卡"，您可以查看技術配置的所有已發佈版本。

## <a name="technical-configuration-managed-application-plans-only"></a>技術配置（僅限託管應用程式計畫）

除了上述**版本**和**包檔**欄位之外，"**技術配置**"選項卡上託管的應用程式計畫具有額外的複雜性。 

### <a name="enable-just-in-time-jit-access"></a>啟用準時 （JIT） 訪問

選擇此選項可啟用此計畫的準時 （JIT） 存取權限。  JIT 訪問使您能夠請求對託管應用程式資源的提升訪問，以便進行故障排除或維護。 您始終對資源具有唯讀存取權限，但在特定時間段內，您可以具有更大的存取權限。  有關詳細資訊，請參閱[啟用和請求 Azure 託管應用程式的及時訪問](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  要要求託管應用程式的消費者授予您的帳戶永久存取權限，請取消選中此選項。

>[!Note]
>請務必更新檔`createUiDefinition.json`以支援此功能。  

### <a name="deployment-mode"></a>部署模式

選擇在部署此計畫時是配置 **"完成****"還是"增量部署模式**"： 

* 在**完全模式下**，如果資源未在 中定義，則客戶重新部署應用程式將導致刪除託管資源組中的資源`mainTemplate.json`。 
* 在**增量模式下**，應用程式的重新部署使現有資源保持不變。

要瞭解有關部署模式的更多資訊，請參閱[Azure 資源管理器部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

### <a name="notification-endpoint-url"></a>通知終結點 URL

指定 HTTPS Webhook 終結點以接收有關此計畫版本的託管應用程式實例上所有 CRUD 操作的通知。

### <a name="customize-allowed-customer-actions"></a>自訂允許的客戶操作

選擇此選項可指定客戶除了預設可用的""`*/read`操作外，還可以對託管資源執行的操作。 

列出您希望使客戶在此處執行的其他操作，這些操作以分號分隔。  有關詳細資訊，請參閱瞭解[Azure 資源的拒絕分配](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  如需可用動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允許取用者重新啟動虛擬機器，可將 `Microsoft.Compute/virtualMachines/restart/action` 新增至允許的動作。

### <a name="global-azure--azure-government-cloud"></a>全域 Azure / Azure 政府雲

指示誰應該在每個受支援的雲中對此託管應用程式具有管理存取權限。  使用 Azure 活動目錄 （AAD） 標識要被授予託管資源組許可權的使用者、組或應用程式。

***Azure 活動目錄租戶 ID*** - AAD 租戶 ID（也稱為目錄 ID），包含要向其授予許可權的使用者、組或應用程式的身份。  您可以在 Azure 門戶[（Azure 活動目錄的屬性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)）中找到 AAD 租戶 ID。

***授權***- 添加要授予託管資源組許可權的使用者、組或應用程式的 Azure 活動目錄物件識別碼。 通過其主體 ID 標識使用者，可在 Azure 門戶上的[Azure 活動目錄使用者邊欄選項卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到該 ID。

對於每個主體，從清單中選擇一個 Azure AD 內置角色（擁有者或參與者）。 您選擇的角色將描述主體將具有的客戶訂閱中資源的許可權。 如需詳細資訊，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。  有關基於角色的存取控制 （RBAC） 的詳細資訊，請參閱[在 Azure 門戶中開始使用 RBAC。](https://docs.microsoft.com/azure/role-based-access-control/overview)

>[!Note]
>儘管每個雲最多可以添加 100 個授權，但通常更容易創建活動目錄使用者組並在"主體 ID"中指定其 ID。  這將使您能夠在部署計畫後向管理組添加更多使用者，並減少僅更新計畫以添加更多授權的需要。

### <a name="policy-settings"></a>原則設定

將[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)應用於託管應用程式，以指定已部署解決方案的合規性要求。  如需了解原則定義和參數值格式，請參閱 [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)。  您最多可以配置五個策略，並且每個策略選項只能配置一個實例。  某些策略需要額外的參數。  稽核原則需要標準 SKU。  策略名稱限制為 50 個字元。

## <a name="co-sell"></a>共同銷售

在 Cosell 選項卡上提供資訊是完全可選的，可用於發佈您的產品/服務。 它需要實現聯合銷售就緒和 IP 共同銷售就緒狀態。 Microsoft 銷售團隊將使用您提供的資訊，在評估解決方案是否適合客戶需求時瞭解有關解決方案的更多資訊。 它不能直接提供給客戶。

有關完成此選項卡的詳細資訊，請參閱[合作夥伴中心中的"共同銷售"選項](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="test-drive"></a>試用產品

通過 **"測試磁碟機**"選項卡，您可以設置演示（或"試駕"），使客戶能夠在承諾購買之前試用您的產品/服務。 在文章"[什麼是試駕"](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)中瞭解更多資訊？  如果您不想再為產品/服務提供試駕，請返回 **"產品/服務設置**"頁面並取消選中**啟用試駕**。

### <a name="technical-configuration"></a>技術配置

Azure 應用程式本質上使用 Azure 資源管理器測試磁碟機類型。  有關詳細資訊[，請參閱 Azure 資源管理器測試磁碟機的技術配置](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)。

### <a name="deployment-subscription-details"></a>部署訂閱詳細資訊

為了代表您部署測試磁碟機，請創建並提供單獨的唯一 Azure 訂閱。 （電源 BI 測試磁碟機不需要）。

- **Azure 訂閱 ID（Azure**資源管理器和邏輯應用需要）：輸入訂閱 ID 以授予對 Azure 帳戶服務的存取權限，以便進行資源使用方式報告和計費。 如果尚未創建，我們建議您考慮[創建單獨的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用於測試磁碟機。 您可以通過登錄到[Azure 門戶](https://portal.azure.com/)並導航到左側功能表的 **"訂閱"** 選項卡來查找 Azure 訂閱 ID。 選擇該選項卡將顯示您的訂閱 ID（例如"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租戶 ID（** 必需）：輸入 Azure 活動目錄 （AD）[租戶 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，請登錄到[Azure 門戶](https://portal.azure.com/)，選擇左側功能表中的"活動目錄"選項卡，選擇 "屬性"，然後查找列出的**目錄 ID**號（例如 50c464d3-4930-494c-963c-1e951d15360e）。 您還可以使用功能變數名稱 URL 在 ：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)查找組織的租戶 ID。

- **Azure AD 租戶名稱**（動態 365 需要）：輸入 Azure 活動目錄 （AD） 名稱。 要查找此名稱，請登錄到[Azure 門戶](https://portal.azure.com/)，在右上角，租戶名稱將列在您的帳戶名稱下。

- **Azure AD 應用 ID（** 必需）：輸入 Azure 活動目錄 （AD）[應用程式 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，請登錄到[Azure 門戶](https://portal.azure.com/)，選擇左側功能表中的"活動目錄"選項卡，選擇**應用註冊**，然後查找列出的應用程式**ID**號（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 應用用戶端機密**（必需）：輸入 Azure AD 應用程式[用戶端金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要查找此值，請登錄到 Azure[門戶](https://portal.azure.com/)。 選擇左側功能表中的**Azure 活動目錄**選項卡，選擇**應用註冊**，然後選擇試駕應用。 接下來，選擇**證書和機密**，選擇 **"新建用戶端機密**"，輸入說明，選擇 **"從不在過期**"下，然後選擇"**添加**"。 **Never** 請確保向下複製該值。 （在複製該值之前，不要離開頁面，否則您將無法訪問該值。

在繼續下一節之前，請記住**保存**！

### <a name="test-drive-listings-optional"></a>試駕清單（可選）

**"試駕****"選項卡下的"試駕清單**"選項顯示您的試駕可用的語言（和市場），當前英語（美國）是唯一可用的位置。 此外，此頁顯示特定于語言的清單的狀態以及添加該清單的日期/時間。 您需要為每個語言/市場定義試駕詳細資訊（說明、使用者手冊、視頻等）。

- **說明**（必需）：描述您的試駕、演示的內容、供使用者試驗的目標、要流覽的功能以及任何相關資訊，以説明使用者確定是否獲取您的產品/服務。 此欄位中最多可輸入 3，000 個字元的文本。 

- **訪問資訊**（Azure 資源管理器和邏輯測試磁碟機所需的資訊）：解釋客戶需要瞭解哪些內容才能訪問和使用此測試磁碟機。 演練使用產品/服務的方案，以及客戶在整個試駕中訪問功能的準確情況。 此欄位中最多可輸入 10，000 個字元的文本。

- **使用者手冊**（必需）：對試駕體驗的深入演練。 《使用者手冊》應準確介紹您希望客戶從體驗試駕中獲得什麼，並作為他們可能存在的任何問題的參考。 檔必須採用 PDF 格式，並在上載後命名（最多 255 個字元）。

- **視頻：添加視頻**（可選）：視頻可以上傳到 YouTube 或 Vimeo，並在此處引用連結和縮略圖圖像 （533 x 324 圖元），以便客戶可以查看資訊的演練，以説明他們更好地瞭解試駕，包括如何成功使用您的產品/服務的功能，並瞭解突出其優勢的方案。
  - **名稱**（必需）
  - **網址（僅限 YouTube 或 Vimeo）（** 必填）
  - **縮略圖 （533 x 324 px）**： 影像檔必須採用 PNG 格式。

完成這些欄位後選擇 **"保存**"。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交優惠以預覽

完成產品/服務的所有必需部分後，選擇門戶右上角的**發佈**。 您將被重定向到 **"審閱"和"發佈"** 頁。 

如果您是第一次發佈此優惠，您可以：

- 請參閱產品/服務的每個部分的完成狀態。
    - *未啟動*- 表示該部分尚未觸摸且需要完成。
    - *不完整*- 表示該節有需要修復的錯誤或需要提供更多資訊。 請返回該部分並更新它。
    - *完成*- 表示部分已完成，提供了所有必需的資料，並且沒有錯誤。 報價的所有部分都必須處於完全狀態，然後才能提交報價。
- 除了任何有助於瞭解應用的補充說明外，向認證團隊提供測試說明，以確保正確測試你的應用。
- 通過選擇 **"提交**"提交要發佈的產品/ 我們將向您發送一封電子郵件，告知您何時可以審核和批准優惠的預覽版本。 返回合作夥伴中心，選擇 **"Go-live"** 以優惠形式向公眾發佈您的優惠（如果是私人優惠，則向私人受眾發佈）。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈過程中的**手動驗證**步驟表示對產品/服務及其相關技術資產（尤其是 Azure 資源管理器範本）的廣泛審查，問題通常以拉取請求 （PR） 連結的形式顯示。 如需如何檢視和回應這些 PR 的說明，請參閱[處理審核意見反應](./azure-apps-review-feedback.md)。

如果您在一或多個發佈步驟中遇到了錯誤，則必須加以更正，然後重新發佈您的供應項目。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
