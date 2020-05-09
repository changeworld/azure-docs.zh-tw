---
title: 在 Azure Marketplace 中建立 Azure 虛擬機器供應專案
description: 瞭解如何在 Azure Marketplace 中使用所需的 SKU 建立虛擬機器供應專案。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 31b8960f5617566a72545510cf03771f7a3bfcbd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82745013"
---
# <a name="create-an-azure-virtual-machine-offer-in-the-azure-marketplace"></a>在 Azure Marketplace 中建立 Azure 虛擬機器供應專案

> [!IMPORTANT]
> 我們正在將您 Azure VM 供應專案的管理從 Cloud Partner 入口網站移至合作夥伴中心。 在您的供應專案遷移之前，請繼續遵循在 Cloud Partner 入口網站中[建立虛擬機器供應](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer)專案中的指示來管理供應專案。

本文說明如何建立 Azure 虛擬機器供應專案，並將其發佈至[Azure Marketplace](https://azuremarketplace.microsoft.com/)。 它會處理以 Windows 和 Linux 為基礎的虛擬機器，其中包含作業系統、虛擬硬碟（VHD）和最多16個數據磁片。 開始之前，請先[在合作夥伴中心建立商業 Marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果您尚未這麼做）。 請確定您的帳戶已在商業 marketplace 方案中註冊。

## <a name="introduction"></a>簡介

### <a name="publishing-benefits"></a>發佈的優點

發行至 Azure Marketplace 具有下列優點：

- 使用 Microsoft 品牌推廣您的公司
- 透過 Azure Marketplace，觸及超過100000000的 Office 365 和 Dynamics 365 使用者及超過200000的組織
- 從這些市場取得高品質的潛在客戶
- 取得由 Microsoft 現場和電話銷售團隊推廣的服務

### <a name="before-you-begin"></a>開始之前

如果您尚未這麼做，請參閱[虛擬機器供應專案發佈指南](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)和此 Azure 虛擬機器材料：

- 快速入門手冊
  - [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
- 教學課程
  - [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- 範例
  - [適用于 Linux Vm 的 Azure CLI 範例](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [適用于 Linux Vm 的 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [適用于 Windows Vm 的 Azure CLI 範例](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [適用于 Windows Vm 的 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>技術知識的基本概念

設計、建立及測試這些資產需要一些時間，而且需要 Azure 平臺和用來建立供應專案之技術的技術知識。

您的工程小組應該瞭解下列 Microsoft 技術：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和[azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的使用知識

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商用 Marketplace** > **總覽**]。
3. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案] [**Azure 虛擬機器**]。

    ![說明左側導覽功能表。](./media/new-offer-azure-vm.png)

> [!NOTE]
> 發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

- 在 marketplace 供應專案的網址中，客戶可以看到此識別碼，如果適用，則會顯示在 Azure PowerShell 和 Azure CLI 中。
- 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您在這裡輸入 [**測試-供應專案-1** ]，則供應專案`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`web 位址會是。
- 選取 [**建立**] 之後，即無法變更供應專案識別碼。

輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

- 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。

選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-setup"></a>供應專案設定

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應專案的絕佳方式，方法是讓他們選擇「在購買前試用」，進而提升轉換並產生高度合格的潛在客戶。 [深入瞭解試用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)產品。

若要啟用一段固定時間的試用產品，請選取 [**啟用試用**產品] 核取方塊。 若要從您的供應專案中移除試用產品，請清除此核取方塊。

其他試用產品資源：

- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [試用產品總覽](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)PDF （請確定您的快顯封鎖程式已關閉）

### <a name="lead-management"></a>潛在客戶管理

將您的供應專案發佈至具有合作夥伴中心的商用 marketplace 時，請將其連接到您的客戶關係管理（CRM）系統。 這可讓您在有人對您的產品感興趣或使用時，立即收到客戶連絡人資訊。 如果您將啟用**試用**產品（請參閱前一節），則需要連接到 CRM，否則它是選擇性的。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援下列 CRM 系統：
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for customer engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果您的 CRM 系統未列于上方，請使用[Azure 資料表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 端點](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)來儲存客戶潛在客戶資料。 然後將資料匯出至您的 CRM 系統。

2. 在合作夥伴中心發佈時，將您的供應專案連接到潛在客戶目的地。
3. 確認已正確設定與潛在客戶目的地的連接。 在合作夥伴中心發佈之後，我們會驗證連線並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，您也可以嘗試在預覽環境中自行部署供應專案，藉此測試您的潛在客戶連線。
4. 請確定與潛在客戶目的地的連線持續更新，讓您不會遺失任何潛在客戶。

選取 [**儲存草稿**] 再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在 marketplace、應用程式版本，以及支援供應專案之法律合約上分組供應專案的類別和產業。

### <a name="categories"></a>類別

選取最少1個，最多五個類別。 這些類別是用來將您的供應專案放在適當的 marketplace 搜尋區域中。 在 [供應專案描述] 中，說明您的供應專案如何支援這些類別。 虛擬機器供應專案會出現在 Azure Marketplace 的 [**計算**] 類別之下。

### <a name="legal"></a>法律

您必須提供供應專案的條款及條件。 您有兩個選擇：

- 使用您自己的條款及條件
- 使用 Microsoft 商業 marketplace 的標準合約

#### <a name="use-your-own-terms-and-conditions"></a>使用您自己的條款及條件

若要提供您自己的自訂條款及條件，請在 [**條款及條件**] 方塊中輸入最多10000個字元的文字。 如果您需要較長的描述，請輸入單一網址，以指向您的條款及條件可以在何處找到。 它會向客戶顯示為作用中連結。

客戶必須接受這些條款，才能試用您的供應專案。

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>使用 Microsoft 商業 marketplace 的標準合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜度，Microsoft 為商業市場提供了標準合約。 當您以標準合約提供軟體時，客戶只需閱讀並接受一次，您就不需要建立自訂的條款及條件。

若要使用標準合約，請選取 [**使用 Microsoft 的商業 Marketplace 標準合約**] 核取方塊，然後在快顯視窗上 [**接受**] （您可能必須向上滾動才能看到它）。

![說明合作夥伴中心的 [總覽] 頁面，其中已選取 [新增供應專案] 按鈕和諮詢服務供應專案。](media/use-standard-contract.png)

> [!NOTE]
> 當您使用商業 marketplace 的標準合約發佈供應專案之後，就無法使用自己的自訂條款及條件。 請根據標準合約**或**您自己的條款及條件來提供您的解決方案。

若要深入瞭解標準合約，請參閱 Microsoft[商業 marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract)的標準合約。 您可以將[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)下載為 PDF （請確定您的快顯封鎖程式已關閉）。

##### <a name="standard-contract-amendments"></a>標準合約修訂

標準合約修訂可讓您選取標準合約條款以求簡單，並為您的產品或企業建立條款。 只有當客戶已審查並接受 Microsoft 標準合約時，才需要審查合約的修訂。 有兩種可用的改正：「通用」和「自訂」。

**通用**修訂–這些適用于所有客戶的標準合約。 這些專案會顯示在購買流程中供應專案的每個客戶。 客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。 您可以提供每個供應專案的單一通用修訂。 您可以在此方塊中輸入不限數目的字元。 這些條款會向客戶顯示在探索和購買流程期間的 AppSource、Azure Marketplace 和/或 Azure 入口網站。

**自訂**修訂–這些是標準合約的特殊修訂，透過 Azure 租使用者識別碼以特定客戶為目標。 您可以選擇您想要設為目標的租使用者。 只有租使用者的客戶才會看到供應專案購買流程中的自訂修訂條款。 客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。

從選取 **[新增自訂修訂條款（最多10個）**] 開始。 每個供應專案最多可提供10個自訂修訂條款。

- **自訂修訂條款**–在 [自訂修訂條款] 方塊中輸入您自己的修訂條款。 您可以輸入不限數目的字元。 只有來自您為這些自訂條款指定之租使用者識別碼的客戶，才會在 Azure 入口網站的供應專案購買流程中看到這些專案。
- **租使用者識別碼**（必要）–每個自訂修訂的目標最多可達20個租使用者識別碼。 如果您新增自訂修訂，您必須至少提供一個租使用者識別碼，以在 Azure 中識別您的客戶。 您的客戶可以在 Azure 中的下找到您，然後按 [屬性]。 目錄識別碼值是租使用者識別碼（例如，50c464d3-4930-494c-963c-1e951d15360e）。 您也可以使用其 [[我的 Microsoft Azure 和 Office 365 租使用者識別碼？](https://www.whatismytenantid.com/)] 的功能變數名稱網址，尋找客戶的組織租使用者識別碼。
- **描述**（選擇性）–提供租使用者識別碼的易記描述，可協助您識別目標為修訂的客戶。

> [!NOTE]
> 這兩種類型的改正堆疊在彼此之上。 以自訂修訂為目標的客戶，也會在購買期間取得標準合約的通用修訂。

選取 [**儲存草稿**] 再繼續。

## <a name="offer-listing"></a>供應專案清單

此頁面可讓您定義供應專案的詳細資料，例如供應專案名稱、描述、連結和連絡人。

> [!NOTE]
> 供應專案清單內容（例如描述、檔、螢幕擷取畫面和使用規定）不需要英文，只要供應專案的描述開頭為片語，「此應用程式僅適用于 [非英文語言]。」 您也可以提供_有用的連結網址_來提供內容，而不是供應專案清單內容所使用的語言。

### <a name="marketplace-details"></a>Marketplace 詳細資料

#### <a name="name"></a>名稱

您在此處輸入的名稱會向客戶顯示，做為供應專案清單的標題。 當您建立供應專案時，此欄位會預先填入您在 [**供應專案別名**] 方塊中輸入的文字。 您可以稍後變更此名稱。

名稱：

- 可以是商標（也可以包含商標和著作權符號）
- 長度不能超過50個字元
- 不能包含 emoji。

#### <a name="search-results-summary"></a>搜尋結果摘要

您的供應專案簡短描述。 最多可有100個字元，並用於 marketplace 搜尋結果。

#### <a name="long-summary"></a>完整摘要

提供供應專案的較長描述。 最多可有256個字元，並用於 marketplace 搜尋結果。

#### <a name="description"></a>描述

提供供應專案的詳細描述，最多3000個字元。 這會在商業 marketplace 清單總覽中向客戶顯示。

在您的描述中包含下列一或多項：

- 供應專案的價值與主要優點
- 類別或產業關聯，或兩者
- 應用程式內購買機會
- 任何必要的公開

以下是撰寫描述的一些秘訣：

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 包含下列專案：
  - 供應專案的描述。
  - 從您的供應專案獲益的使用者類型。
  - 供應專案所解決的客戶需求或問題。
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。
- 請勿依賴特性和功能來銷售您的供應專案。 相反地，請專注于您的供應專案提供的值。
- 使用產業特定或以優點為基礎的單字。

若要讓您的供應專案描述更吸引人，請使用 rtf 編輯器來套用格式設定。

![使用 rich 文字編輯器](./media/rich-text-editor.png)

| <center>變更文字格式 | <center>新增專案符號或編號 | <center>新增或移除文字縮排 |
| --- | --- | --- |
| <center>![使用 rich 文字編輯器來變更文字格式](./media/text-editor3.png) |  <center>![使用 rich 文字編輯器加入清單](./media/text-editor4.png) |  <center>![使用 rich 文字編輯器縮排](./media/text-editor5.png) |

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入您組織隱私權原則的網址（URL）。 請確定您的供應專案符合隱私權法規。 您也必須在您的網站上張貼有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

提供有關您供應專案的補充線上檔。 若要新增連結，請選取 [ **+ 新增連結**]，然後完成下欄欄位：

- **名稱**–客戶會在 [詳細資料] 頁面上看到名稱。
- **連結（URL）** –輸入客戶的連結以查看您的線上檔。

### <a name="customer-support-links"></a>客戶支援連結

提供支援網站，讓客戶可以觸達您的支援小組。

- Azure 全球支援網站
- Azure Government 支援網站

### <a name="partner-support-contact"></a>合作夥伴支援連絡人

提供連絡人資訊，讓 Microsoft 合作夥伴在您的客戶開啟支援票證時使用。 這不會列在 marketplace 中。

- 名稱
- 電子郵件
- 電話

### <a name="engineering-contact"></a>工程連絡人

提供您的供應專案問題時，Microsoft 所要使用的連絡人資訊，包括認證的問題。 這不會列在 marketplace 中。

- 名稱
- 電子郵件
- 電話

### <a name="marketplace-media"></a>Marketplace 媒體

提供要與您的供應專案搭配使用的標誌和影像。 所有影像都必須是 PNG 格式。 模糊影像會導致您的提交遭到拒絕。

>[!Note]
>如果您在上傳檔案時發生問題，請確定您的區域網路不https://upload.xboxlive.com會封鎖合作夥伴中心所使用的服務。

#### <a name="marketplace-logos"></a>Marketplace 標誌

以下列四個圖元大小，提供供應專案標誌的 PNG 檔案：

- **小型**（48 x 48）
- **中**（90 x 90）
- **大型**（216 x 216）
- **寬**（255 x 115）

這四個標誌都是必要的，並且用於 marketplace 清單中的不同位置。

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 每個螢幕擷取畫面的大小都必須是 1280 x 720 圖元，而且採用 PNG 格式。 每個螢幕擷取畫面都必須包含標題。

#### <a name="videos"></a>影片

新增最多五個示範您供應專案的影片。 這些應該裝載于外部影片服務。 輸入每部影片的名稱、網址，以及影片 1280 x 720 圖元的縮圖 PNG 影像。

如需其他 marketplace 清單資源，請參閱[marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

選取 [**儲存草稿**] 再繼續。

## <a name="preview"></a>預覽

在 [預覽] 索引標籤上，選擇有限的**預覽物件**來驗證您的供應專案，再將其上線發佈到更廣泛的 marketplace 物件。

> [!IMPORTANT]
> 在預覽您的供應專案之後，**請選取 [上線]** ，將您的供應專案發佈給商業 marketplace 公用物件。

您的預覽物件是由 Azure 訂用帳戶識別碼 Guid 所識別，以及各自的選擇性描述。 客戶無法看到這兩個欄位。 您**可以在 Azure 入口網站的 [** 訂用帳戶] 頁面上找到您的 Azure 訂用帳戶識別碼。

新增至少一個 Azure 訂用帳戶識別碼，分別是（最多10個）或上傳 CSV 檔案（最多100個）。 藉由新增這些訂用帳戶識別碼，您可以定義誰可以在現場發佈之前預覽您的供應專案。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試供應專案的變更或更新。

> [!NOTE]
> 預覽物件與私用物件不同。 預覽物件可以存取您的供應專案，_然後_才能在 marketplace 中即時發佈。 他們可以查看並驗證所有方案，包括當您的供應專案完全發佈至 marketplace 之後，僅供私用物件使用的計畫。 私用物件（在 [方案**價格與可用性**] 索引標籤中定義）具有特定方案的獨佔存取權。

選取 [**儲存草稿**]，然後繼續進行下一節 [計畫總覽]。

## <a name="plan-overview"></a>計畫總覽

您可以在合作夥伴中心的相同供應專案中提供不同的方案選項。 這些方案先前稱為 Sku。 供應專案需要至少一個方案，在營收物件、Azure 區域、功能或 VM 映射方面可能有所不同。

建立計畫之後，[**計畫總覽**] 索引標籤會顯示：

- 計畫名稱
- 授權模型
- 物件（公用或私用）
- 目前的發行狀態
- 可執行的動作

計畫總覽中的可用動作會根據方案的目前狀態而有所不同。 其中包括：

- **刪除草稿**–如果計畫狀態為草稿
- **停止銷售方案**或**同步私用物件**–如果計畫狀態是即時發行

### <a name="create-new-plan"></a>建立新的方案

選取頂端的 [ **+ 建立新方案**]。 [**新增方案**] 對話方塊隨即出現。

在 [**方案識別碼**] 方塊中，為此供應專案中的每個方案建立唯一的方案識別碼。 此識別碼將會顯示在產品網址中的客戶。 僅使用小寫字母和數位、連字號或底線，以及最多50個字元。

> [!NOTE]
> 選取 [**建立**] 之後，就無法變更方案識別碼。

在 [**方案名稱**] 方塊中，輸入此計畫的名稱。 客戶在決定要在您的供應專案內選取哪一個方案時，會看到此名稱。 建立唯一的名稱，以清楚指出每個方案的差異。 例如，您可以使用具有方案隨用隨**付**、 **BYOL**、 **Advanced**和**Enterprise**的**Windows Server** 。

選取 [建立]  。

### <a name="plan-setup"></a>規劃設定

設定方案類型的高階設定、是否重複使用來自另一個方案的技術設定，以及應提供方案的 Azure 區域。 您在此處選取的專案會決定要在相同方案的其他索引標籤上顯示哪些欄位。

#### <a name="re-use-technical-configuration"></a>重複使用技術設定

如果您有多個相同類型的方案，而且它們之間的封裝相同，則可以選取**此計畫重複使用另一個方案的技術**設定。 此選項可讓您為此供應專案選取另一個相同類型的方案，並重複使用其技術設定。

> [!NOTE]
> 當您重複使用另一個方案的技術設定時，整個 [**技術**設定] 索引標籤就會從這個計畫中消失。 另一個方案中的技術設定詳細資料，包括您在未來所做的任何更新，也會用於此計畫。 發行此計畫之後，即無法變更此設定。

#### <a name="azure-regions"></a>Azure 區域

您的方案必須至少在一個 Azure 區域中提供。

選取 [ **Azure 全域**] 選項，讓您的方案可供所有具有商業 marketplace 整合之 Azure 全球區域中的客戶使用。 如需詳細資訊，請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選取 [ **Azure Government** ] 選項，讓您的方案可在[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)區域中使用。 此區域為美國聯邦、州、地方或部落的實體，以及符合資格服務的合作夥伴提供受控存取權。 身為發行者的您，會負責任何合規性控制、安全性措施和最佳作法。 Azure Government 會使用實際隔離的資料中心和網路（僅限美國地區）。

發行至[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，請在環境中測試並驗證您的方案，因為某些端點可能會有所不同。 若要設定及測試您的方案，請從[Microsoft Azure Government 試用](https://azure.microsoft.com/global-infrastructure/government/request/)版要求試用帳戶。

> [!NOTE]
> 當您的方案發佈並可在特定的 Azure 區域中使用之後，您就無法移除該區域。

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

### <a name="pricing-and-availability"></a>價格與可用性

在此索引標籤上，您將設定下列各項：

- 此方案將于推出的市場
- 每小時的價格
- 是否要讓所有人都能看到此計畫，或僅供特定客戶使用（私用物件）

#### <a name="markets"></a>市場

必須至少有一個市場提供每個方案。 選取此方案應可供購買的每個市場位置的核取方塊（這些市場中的使用者仍然可以將供應專案部署至 [**[規劃設定](#plan-setup)**] 中所選取的所有 Azure 區域）。 [**稅金已匯款**] 按鈕會顯示 Microsoft 免除銷售並代表您使用稅金的國家/地區。 發行至中國僅限於**免費**或自備**授權**（BYOL）的方案。

如果您已經在美國美元（USD）設定方案的價格，並新增另一個市場位置，新市場的價格將會根據目前的匯率計算。 在發佈之前，請務必先檢查每個市場的價格。 儲存變更之後，請使用**匯出價格（.xlsx）** 連結來檢查價格。

當您移除市場時，使用作用中部署的市場客戶將無法建立新的部署或擴充其現有部署。 現有的部署將不會受到影響。

#### <a name="pricing"></a>定價

**授權模型**–選取 [以**使用量為基礎的每月計費方案**]，以設定此方案的定價或自備**授權**，讓客戶將此方案與現有的授權搭配使用。

針對以使用量為基礎的每月計費方案，請使用下列三個價格輸入選項的其中一個：

- **每個核心**–提供美國美元（USD）中每個核心的價格。 我們會計算每個核心大小的定價，並使用目前的匯率轉換成當地貨幣。
- **每個核心大小**-提供每個核心大小的價格，以美元為單位。 我們會使用目前的匯率，將價格轉換成當地貨幣。
- **每個市場和核心大小**-為所有市場提供每個核心大小的價格。 您可以從試算表匯入價格。

> [!NOTE]
> 儲存定價變更以啟用匯出定價資料。 在您的方案中發佈市場價格之後，就無法再進行變更。 藉由匯出定價表並在每個市場中查看價格，確保這些價格在發行前都是正確的。

#### <a name="free-trial"></a>免費試用

您可以為您的客戶提供一個月或三個月的免費試用版。

#### <a name="visibility"></a>可見性

您可以設計每個計畫，讓每個人都能看見，或僅針對預先選取的物件。 使用 Azure 訂用帳戶識別碼指派此限制物件中的成員資格。

**公用**–所有人都可以看到您的方案。

**私用物件**–讓您的計畫只對預先選取的物件顯示。 將其發佈為私用方案之後，您可以更新物件或將其變更為 [公用]。 在您將方案設為公用之後，它必須保持公用;您無法將它變更回 [私人]。

**限制的物件（Azure 訂用帳戶識別碼）** –使用 Azure 訂用帳戶識別碼指派可存取此私人方案的物件。 （選擇性）包含您指派的每個 Azure 訂用帳戶識別碼的描述。 手動新增最多10個訂用帳戶識別碼，如果匯入 CSV 試算表，則為20000。 Azure 訂用帳戶識別碼會以 Guid 表示，而字母必須是小寫。

> [!NOTE]
> 私用或限制的物件與您在 [**預覽**] 索引標籤上定義的預覽物件不同。預覽物件可以先存取您的供應專案，_再_將其發佈到 marketplace 中。 雖然私用物件選擇僅適用于特定方案，但預覽物件可以針對驗證目的來查看所有計劃（私用或非）。

#### <a name="hide-plan"></a>隱藏方案

如果您的虛擬機器僅在透過另一個解決方案範本或受控應用程式參考時間接使用，請選取此方塊以發佈虛擬機器，但將其從直接搜尋和流覽的客戶隱藏起來。

> [!NOTE]
> 隱藏的方案不支援預覽連結。

選取 [**儲存草稿**] 再繼續。

### <a name="technical-configuration"></a>技術設定

提供與此方案相關聯的影像和其他技術屬性。 如需詳細資訊，請參閱[建立 AZURE VM 技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 如果您已設定此計畫在 [**計畫設定**] 索引標籤上重複使用另一個方案的封裝，則不會顯示此索引標籤

#### <a name="operating-system"></a>作業系統

- **作業系統系列**–從**Windows**或**Linux**作業系統選取
- **發行**或**廠商**–選擇 Windows 版本或 Linux 廠商
- **OS 易記名稱**–選擇易記的作業系統名稱。 客戶可以看到此名稱

#### <a name="recommended-vm-sizes"></a>建議的 VM 大小

選取最多六個建議的虛擬機器大小，以顯示在 Azure Marketplace 中。

#### <a name="open-ports"></a>開啟連接埠

在已部署的虛擬機器上開啟公用或私用埠。

#### <a name="storage-option-for-deployment"></a>部署的儲存選項

**磁片部署選項**–選取使用者在使用虛擬機器時可使用的磁片部署類型。 Microsoft 建議將部署限制為僅限受控磁片部署。

#### <a name="properties"></a>屬性

**支援加速網路**–如果您的 VM 支援[加速網路](https://go.microsoft.com/fwlink/?linkid=2124513)，請選取此功能。

#### <a name="vm-images"></a>VM 映像

提供虛擬機器映射的磁片版本和 SAS URI。 為每個 VM 映射新增最多16個數據磁片。 在給定的提交中，每個方案只提供一個新的映射版本。 在發佈映射之後，您就無法進行編輯，但可以將它刪除。 刪除版本將會使新的和現有的使用者無法部署已刪除版本的新實例。

- **光碟版本**是您所提供的映射版本。
- [ **SAS URI** ] 是您儲存作業系統 VHD Azure 儲存體中的位置。
- 資料磁片映射也是儲存在其 Azure 儲存體中的 VHD SAS Uri。
- 在方案中每次提交時僅新增一個影像。

無論您使用哪一種作業系統，請只新增解決方案所需的最小資料磁片數目。 客戶無法在部署時移除屬於映射一部分的磁片，但它們一律可在部署期間或之後新增磁片。

選取 [**儲存草稿**] 再繼續，然後返回 **[計畫總覽**]。

## <a name="resell-through-csps"></a>透過 Csp 轉售

將供應專案提供給[雲端解決方案提供者](https://azure.microsoft.com/offers/ms-azr-0145p/)（CSP）方案中的合作夥伴，以擴大其範圍。 所有自備授權（BYOL）方案都會自動加入宣告;您可以加入宣告您的非 BYOL 計畫。

選取 [**儲存草稿**] 再繼續。

## <a name="test-drive"></a>試用產品

設定示範（試用產品），讓客戶在購買之前先試用您的供應專案。 若要建立示範環境，讓客戶可以在一段固定時間內試用您的供應專案，請參閱在[商業 marketplace 中測試您的供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)專案。

若要啟用試用產品，請在 [[供應專案設定](#test-drive)] 索引標籤上選取 [啟用試用磁片] 核取方塊。若要從您的供應專案中移除試用產品，請清除此核取方塊。

其他試用產品資源：

- [行銷最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技術最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [總覽](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF; 請確定您的快顯封鎖程式已關閉）

選取 [**儲存草稿**] 再繼續。

## <a name="review-and-publish"></a>審查和發行

當您完成供應專案的所有必要區段之後，您可以提交它來進行審查和發佈。

在入口網站的右上角，選取 [**審查併發布**]。

在此頁面上，您可以：

- 請參閱供應專案每個區段的完成狀態。
  - **未啟動**–區段尚未啟動，需要完成。
  - **不完整**–區段包含必須修正的錯誤，或需要您提供詳細資訊。 如需如何更新本節的指引，請參閱本檔稍早的章節。
  - **Complete** –區段完成，而且沒有任何錯誤。 供應專案的所有區段都必須完成，您才能提交供應專案。
- **認證注意事項**-提供測試指示給認證小組，以確保您的應用程式已正確測試。 提供有助於瞭解應用程式的任何補充附注。

若要提交供應專案以供發佈，請選取 [**審查併發布**]。

我們會傳送一封電子郵件給您，讓您知道供應專案的預覽版本何時可供審查和核准。 若要將您的供應專案發佈到公開（或如果私人供應專案為私人物件），請移至合作夥伴中心，找出供應專案的 **[總覽**] 頁面，然後選取 [**上線**]。 當發佈正在進行時，您的供應專案狀態會顯示在頁面頂端。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈程式中的**手動驗證**步驟代表您的供應專案和其相關技術資產的廣泛審查。 如果此程式發現您的供應專案發生錯誤，您將會收到詳細說明問題的認證報告。 只要進行必要的修正，然後重新發佈您的供應專案即可。

## <a name="offer-overview"></a>供應項目概觀

[**供應專案總覽**] 頁面會顯示發佈此供應專案所需步驟的視覺標記法（已完成和進行中），以及每個步驟應完成幾段時間。

此頁面包含根據您所做的選擇，在此供應專案上執行作業的連結。 例如：

- 如果供應專案為草稿-[刪除草稿供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)專案
- 如果供應專案已上線，則會[停止銷售供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)專案
- 如果供應專案處於預覽[狀態-上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者簽核-[取消發行](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace 範例

這些範例會顯示供應專案在 Azure Marketplace 中的顯示方式。

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace 供應專案詳細資料

![Azure Marketplace 供應專案詳細資料畫面範例](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure Marketplace 搜尋結果

![Azure Marketplace 搜尋詳細資料畫面範例](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure Marketplace 方案詳細資料

![Azure Marketplace 方案詳細資料畫面範例](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure 入口網站供應專案詳細資料

![Azure 入口網站供應專案詳細資料畫面範例](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure 入口網站搜尋結果

![Azure 入口網站搜尋結果畫面範例](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure 入口網站方案詳細資料

![Azure 入口網站方案詳細資料畫面範例](media/avm-create6.png)

## <a name="next-step"></a>後續步驟

- [更新商用 marketplace 中的現有供應專案](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
