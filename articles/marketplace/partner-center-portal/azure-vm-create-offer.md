---
title: 建立 Azure 虛擬機器產品 /服務 - Azure 應用商店
description: 瞭解如何在商業市場中創建虛擬機產品/服務。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 161fd9276427db0d0d7d56da1bfc0bb8ccf52a52
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536653"
---
# <a name="create-an-azure-virtual-machine-offer"></a>建立 Azure 虛擬機器產品/服務

> [!IMPORTANT]
> 我們將 Azure VM 產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請繼續按照雲合作夥伴門戶中的[「創建虛擬機」產品/服務](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer)中的說明來管理您的產品/服務。

本文介紹如何創建 Azure 虛擬機器產品/服務並將其發表到[Azure 應用商店](https://azuremarketplace.microsoft.com/)。 它面向包含作業系統、虛擬硬碟 (VHD) 和多達 16 個數據磁碟的基於 Windows 和 Linux 的虛擬機器。

## <a name="introduction"></a>簡介

### <a name="publishing-benefits"></a>發佈的優點

發佈到 Azure 應用商店具有以下優點:

- 使用 Microsoft 品牌推廣您的公司
- 通過 Azure 應用商店覆蓋超過 1 億台 Office 365 和 Dynamics 365 使用者和 200,000 多個組織
- 從這些市場獲得高品質的潛在客戶
- 獲得 Microsoft 現場和電話銷售團隊推廣的服務

### <a name="before-you-begin"></a>開始之前

如果尚未這樣做,請查看[虛擬機器提供發佈指南](https://aka.ms/Virtualmachineofferpublishingguide)和此 Azure 虛擬機器材料:

- 快速入門指南
  - [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
- 教學課程
  - [Linux VM](https://aka.ms/LinuxVMtutorial)
  - [Windows VM](https://aka.ms/windowsvms)
- 範例
  - [Linux VM 的 Azure CLI 範例](https://aka.ms/linuxclisamples)
  - [適用於 Linux VM 的 Azure 電源外殼](https://aka.ms/linuxpowershellsamples)
  - [適用於 Windows VM 的 Azure CLI 範例](https://aka.ms/windowsclisamples)
  - [適用於 Windows VM 的 Azure 電源外殼](https://aka.ms/windowspowershellvmsamples)

### <a name="fundamentals-in-technical-knowledge"></a>技術知識基礎知識

設計、構建和測試這些資產需要時間,需要對 Azure 平臺和用於構建產品/服務的技術進行技術知識。

您的專案團隊應瞭解以下 Microsoft 技術:

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)[、Azure 儲存](https://azure.microsoft.com/services/?filter=storage#storage)和 Azure[網路](https://azure.microsoft.com/services/?filter=networking#networking)的工作知識

## <a name="create-an-azure-virtual-machine-offer"></a>建立 Azure 虛擬機器產品/服務

在創建 Azure 虛擬機器產品/服務之前,必須在合作夥伴中心中具有商業市場帳戶。 如果您尚未建立一個帳戶,請參閱[在合作夥伴中心建立商業市場帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home),然後從頂端選單中選擇**儀表板**。
2. 在左導航欄中,選擇**商業市場**,然後 **「概述**」。
3. 在 **「概述」** 頁上,選擇 **+新產品/服務**,然後選擇**Azure 虛擬機器**。 將顯示 **「新建產品/服務**」對話框。

![使用「新建產品/服務」按鈕和 Azure 虛擬機器產品/服務(已選擇)在合作夥伴中心中示範「概述」頁面。](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>優惠識別碼與別名

輸入**優惠代碼**。 這是您帳戶中每個產品/服務的唯一標識碼。

- 此 ID 在市場產品/服務的 Web 位址以及 Azure PowerShell 和 Azure CLI(如果適用)中可見。
- 請一律使用小寫字母和數字。 它可以包括連字元和下劃線,但沒有空格,並且限制為 50 個字元。 例如,如果您在此處輸入**測試報價 1,** 則產品/服務`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`Web 位址將為 。
- 選擇 「**建立**」 後,無法更改優惠識別碼。

輸入**產品/服務別名**。 這是合作夥伴中心中用於產品/服務的名稱。 此名稱不在市場上使用,並且不同於向客戶顯示的要約名稱和其他值。

選擇 **「創建**」以生成產品/服務並繼續。

## <a name="offer-setup"></a>優惠設定

### <a name="test-drive"></a>試用產品

設置示範(測試驅動器),讓客戶在購買之前試用您的產品/服務。 要建立展示的環境,讓客戶在固定的時間內試用您的產品/服務,請參閱[在商業市場試用您的產品/服務](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

要啟用試駕,請選擇「**啟用試駕**」複選框。 要從產品/服務中刪除試駕,請清除此複選框。

其他試駕資源:

- [技術最佳實踐](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [行銷最佳做法](https://aka.ms/TestDriveMarketingBestPractices)
- [測試磁碟式概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)PDF(確保彈出視窗阻止程式已關閉)。

### <a name="lead-management"></a>潛在客戶管理

使用合作夥伴中心將產品/服務發佈到商業市場時,請將其連接到您的客戶關係管理 (CRM) 系統。 這樣,一旦有人對您的產品表示興趣或使用,您就可以立即接收客戶聯繫資訊。 如果要啟用 **「測試驅動器**」(請參閱上一節),則需要連接到 CRM,否則它是可選的。

1. 選取您要我們傳送潛在客戶至其中的潛在客戶目的地。 合作夥伴中心支援以下 CRM 系統:
    - [動態 365](https://aka.ms/Dyn365LeadMgmt)用於客戶互動
    - [Marketo](https://aka.ms/LeadMgmtMarketo)
    - [Salesforce](https://aka.ms/LeadMgmtSalesforce)

    > [!NOTE]
    > 如果上面未列出 CRM 系統,請使用[Azure 表](https://aka.ms/AzureTableLeadMgmt)或[Hs 終結點](https://aka.ms/LeadMgmtHTTPS)來儲存客戶潛在顧客數據。 然後將數據匯出到 CRM 系統。

2. 在合作夥伴中心發佈時,請將您的產品/服務連接到潛在顧客目標。
3. 確認與潛在顧客目標的連接配置正確。 在合作夥伴中心發佈後,我們將驗證連接並向您發送測試線索。 在產品/服務上線之前預覽產品/服務時,還可以嘗試在預覽環境中自行部署產品/服務,以測試潛在顧客連接。
4. 確保與潛在顧客目標的連接保持更新狀態,這樣您就不會丟失任何潛在顧客。

選擇 **"保存"草稿**,然後再繼續。

## <a name="properties"></a>屬性

此頁面允許您定義用於在市場對產品/服務進行分組的類別和行業、應用版本和支援您的產品/服務的法律合同。

### <a name="categories"></a>類別

選擇至少 1 個類別,最多選擇五個類別。 這些類別用於將產品/服務放置在相應的市場搜索區域。 在產品/服務說明中,說明您的優惠如何支援這些類別。 虛擬機產品/服務顯示在 Azure 應用商店中的 **「計算**」類別下。

### <a name="legal"></a>法律

您必須為優惠提供條款和條件。 您有兩個選擇：

- 使用您自己的條款和條件
- 使用微軟商業市場的標準合約

#### <a name="use-your-own-terms-and-conditions"></a>使用您自己的條款和條件

要提供您自己的自定義條款和條件,請在 **「條款和條件」** 框中輸入最多 10,000 個字元的文本。 如果您需要較長的說明,請輸入指向您的條款和條件的單個網址。 它將作為活動連結向客戶顯示。

客戶必須先接受這些條款,然後才能試用您的優惠。

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>使用微軟商業市場的標準合約

為了簡化客戶的採購流程並降低軟體供應商的法律複雜性,Microsoft 為商業市場提供了標準合同。 當您根據標準合同提供軟體時,客戶只需閱讀並接受一次軟體,您就無需創建自定義條款和條件。

使用標準合約,選擇「**使用 Microsoft 商業市場的標準合約**」複選框,然後在彈出視窗上 **「接受**」(您可能需要向上滾動才能查看)。

![使用「新產品/服務」按鈕和諮詢服務產品/服務產品,在合作夥伴中心中演示「概述」頁面。](media/use-standard-contract.png)

> [!NOTE]
> 使用商業市場的標準合同發佈產品/服務后,不能使用您自己的自定義條款和條件。 在標準合約下**或**根據您自己的條款和條件提供您的解決方案。

要瞭解有關標準合同的更多資訊,請參閱 Microsoft[商業市場](https://docs.microsoft.com/azure/marketplace/standard-contract)的標準合約。 您可以將[標準合約](https://go.microsoft.com/fwlink/?linkid=2041178)下載為 PDF(確保彈出視窗阻止程式已關閉)。

##### <a name="standard-contract-amendments"></a>標準合同修訂

標準合同修訂允許您選擇標準合同條款,以便簡化,併為您的產品或業務創建條款。 客戶只需審核並接受 Microsoft 標準合同,才需要查看合同的修訂。 有兩種修正案可供選擇:通用和習俗。

**通用修訂**– 這些修訂適用於所有客戶的標準合同。 在採購流程中,它們向產品/服務的每個客戶顯示。 客戶必須接受標準合同的條款和修改,然後才能使用您的優惠。 您可以提供每個報價的單一通用修訂。 您可以在此框中輸入無限數量的字元。 在發現和購買流期間,這些術語在 AppSource、Azure 應用商店和/或 Azure 門戶中向客戶顯示。

**自定義修訂**– 這些是標準合同的特殊修訂,通過 Azure 租戶 ID 面向特定客戶。 您可以選擇要定位的租戶。 只有租戶的客戶才會在優惠的採購流程中收到自定義修改條款。 客戶必須接受標準合同的條款和修改,然後才能使用您的優惠。

首先選擇**添加自定義修改條款 (最多 10 個)。** 每個優惠最多可以提供 10 個自定義修訂條款。

- **自定義修訂條款**– 在自定義修訂條款框中輸入您自己的修訂條款。 您可以輸入無限數量的字元。 只有您為這些自定義條款指定的租戶 ID 的客戶才能在 Azure 門戶中的產品/服務採購流中看到這些內容。
- **租戶 ID(** 必需) – 每個自定義修訂的目標可高達 20 個租戶 ID。 如果添加自定義修改,則必須至少提供一個租戶 ID,該 ID 標識 Azure 中的客戶。 您的客戶可以在 Azure 下找到,然後是屬性。 目錄 ID 值是租戶 ID(例如,50c464d3-4930-494c-963c-1e951d15360e)。 您還可以使用我的[Microsoft Azure 和 Office 365 租戶 ID](https://www.whatismytenantid.com/)上的功能變數名稱 Web 位址查找組織的客戶的租戶 ID。
- **說明**(可選) – 為租戶 ID 提供友好的說明,説明您通過修訂確定目標客戶的身份。

> [!NOTE]
> 這兩種類型的修正相互疊加。 以定製修訂為目標的客戶在購買過程中還將獲得標準合同的普遍修訂。

選擇 **"保存"草稿**,然後再繼續。

## <a name="offer-listing"></a>優惠清單

此頁面允許您定義產品/服務詳細資訊,如產品/服務名稱、說明、鏈接和連絡人。

> [!NOTE]
> 報價清單內容(如說明、文檔、螢幕截圖和使用條款)不需要以英文表示,只要產品/服務說明以短語開頭,"此應用程式僅以 [非英語語言]提供。 您還可以提供_有用的連結網址_,以"產品/服務"清單內容中使用的語言以外的語言提供內容。

### <a name="marketplace-details"></a>市場詳細資訊

#### <a name="name"></a>名稱

您在此處輸入的名稱將作為產品/服務列表的標題顯示給客戶。 此欄位預先填充您在建立產品/服務時在 **「產品/服務」別名**框中輸入的文字。 您可以稍後變更此名稱。

名稱:

- 可註冊商標(您可以包括商標和版權符號)
- 不能超過50個字元長
- 不能包括表情符號。

#### <a name="search-results-summary"></a>搜尋結果摘要

您的報價的簡短說明。 這最多可達 100 個字元,並且用於市場搜尋結果。

#### <a name="long-summary"></a>長摘要

提供更久的報價說明。 這最多可達 256 個字元,並且用於市場搜尋結果。

#### <a name="description"></a>描述

提供您的優惠的詳細描述,最多 3,000 個字元。 這在商業市場清單概述中向客戶顯示。

在描述中包括以下一項或多項:

- 您的報價的價值和主要優勢
- 類別或行業協會,或兩者
- 應用內購買機會
- 任何必需的披露

以下是編寫說明的一些提示:

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 包括以下專案:
  - 您的優惠說明。
  - 受益於您的產品/服務的用戶類型。
  - 客戶需要或產品/服務解決的問題。
- 請記住,前幾個句子可能顯示在搜尋引擎結果中。
- 不要依賴特性和功能來銷售您的產品/服務。 相反,關注您的產品/服務提供的價值。
- 使用特定於行業或基於利益的詞語。

為了使您的報價描述更具吸引力,請使用豐富的文本編輯器來設置您的說明格式。 富文字編輯器允許您添加數位、專案符號、粗體、斜體和縮進,以使描述更具可讀性。

![使用「新產品/服務」按鈕和諮詢服務產品/服務產品,在合作夥伴中心中演示「概述」頁面。](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>隱私政策連結

將網址 (URL) 輸入到組織的隱私政策中。 確保您的優惠符合隱私法律和法規。 您還必須在您的網站上發佈有效的隱私政策。

### <a name="useful-links"></a>實用的連結

提供有關您的優惠的補充在線文檔。 要新增連結,請選擇 **「 新增連結」,** 然後完成以下欄位:

- **名稱**– 客戶將在詳細資訊頁面上看到該名稱。
- **連結 (URL)** – 輸入客戶查看連線文件的連結。

### <a name="customer-support-links"></a>客戶支援連結

提供支援網站,客戶可以在該網站聯繫您的支援團隊。

- Azure 全球支援網站
- Azure 政府支援網站

### <a name="partner-support-contact"></a>合作夥伴支援聯絡人

為 Microsoft 合作夥伴提供聯絡資訊,供客戶打開支援票證時使用。 這將不會在市場上列出。

- 名稱
- 電子郵件
- 電話

### <a name="engineering-contact"></a>專案連絡人

在您的產品/服務出現問題(包括認證問題)時,為Microsoft提供要使用的聯絡資訊。 這將不會在市場上列出。

- 名稱
- 電子郵件
- 電話

### <a name="marketplace-media"></a>市場媒體

提供徽標和圖像,以便與您的產品配合使用。 所有圖像必須採用 PNG 格式。 模糊的圖像將導致您的提交被拒絕。

#### <a name="marketplace-logos"></a>市場徽標

以以下四個像素大小提供產品標誌的 PNG 檔:

- **小**(48 x 48)
- **中等**(90 x 90)
- **大**(216 x 216)
- **寬**(255 x 115)

所有四個徽標都是必需的,並且在市場清單中的不同位置使用。

#### <a name="screenshots"></a>螢幕擷取畫面

最多添加五個屏幕截圖,顯示您的優惠如何工作。 每個螢幕截圖的大小和 PNG 格式必須為 1280 x 720 圖元。 您還必須添加一個標題來描述您的螢幕截圖。

#### <a name="videos"></a>影片

最多添加五個視頻,演示您的優惠。 這些視頻應託管在外部視頻服務上。 輸入每個視頻的名稱、網址和視頻的縮略圖 PNG 圖像,大小為 1280 x 720 圖元。

有關其他市場清單資源,請參閱[市場產品/服務清單的最佳做法](https://aka.ms/LdMgmtOfferListingBestPractices)。

選擇 **"保存"草稿**,然後再繼續。

## <a name="preview"></a>預覽

在「預覽」選項卡上,選擇有限的**預覽受眾**以驗證您的優惠,然後再將其即時發佈到更廣泛的市場受眾。

> [!IMPORTANT]
> 在預覽版中檢查您的優惠后,選擇 **「上線」** 以向商業市場公眾受眾發佈您的優惠。

預覽受眾由 Azure 訂閱 ID GUID 以及每個選項的可選說明標識。 客戶看不到這兩個字段。 您可以在 Azure 門戶中的 **「訂閱」** 頁上找到 Azure 訂閱 ID。

添加至少一個 Azure 訂閱 ID,分別(最多 10 個)或通過上傳 CSV 檔(最多 100 個)。 通過添加這些訂閱 IT,您可以定義誰可以在產品/服務即時發佈之前預覽產品/服務。 如果您的優惠已是即時的,您仍然可以定義預覽受眾,以測試產品/服務的變化或更新。

> [!NOTE]
> 預覽觀眾與私人觀眾不同。 預覽受眾可以在產品/服務在市場現場發佈_之前_訪問它。 他們可以查看和驗證所有計劃,包括僅在您的優惠完全發佈到市場后才提供給私人受眾的計劃。 私人訪問群體(在計劃**定價和可用性**選項卡中定義)具有對特定計劃的獨佔訪問許可權。

在繼續下一節"計劃概述"之前選擇 **"保存草稿**"。

## <a name="plan-overview"></a>排程概述

您可以在合作夥伴中心的同一優惠中提供不同的計畫選項。 這些計劃以前稱為 SKU。 產品/服務至少需要一個計劃,在貨幣化受眾、Azure 區域、功能或VM映射方面可能有所不同。

建立計劃後,「**計畫概觀**」選項卡顯示:

- 排程名稱
- 授權模型
- 受眾(公共或私人)
- 目前發佈狀態
- 可用動作

計劃概述中可用的操作因計劃的當前狀態而異。 其中包括：

- **刪除草稿**= 如果排程狀態為草稿
- **停止銷售計劃**或**同步私人受眾**– 如果計劃狀態即時發佈

### <a name="create-new-plan"></a>建立新的方案

選擇 **= 在頂端建立新計畫**。 將顯示「**新建計畫**」對話框。

在 **「計劃 ID」** 框中,為此產品/服務中的每個計畫創建唯一的計畫 ID。 此 ID 將在產品 Web 位址中對客戶可見。 僅使用小寫字母和數位、破折號或下劃線,最多 50 個字元。

> [!NOTE]
> 選擇 「**建立**」 後,無法更改計畫 ID。

在 **「計畫名稱**」框中,輸入此計畫的名稱。 客戶在決定在您的產品/服務中選擇哪個計劃時,會看到此名稱。 創建一個唯一的名稱,清楚地指出每個計劃的差異。 例如,您可以將 Windows**伺服器**用於計畫即**用即付****、BYOL、****進階****和企業**。

選取 [建立]  。

### <a name="plan-setup"></a>計畫設定

設置計劃類型的高級配置,它是否重用其他計劃的技術配置,以及計劃應在哪些區域可用。 此處的選擇確定同一計劃的其他選項卡上顯示哪些欄位。

#### <a name="reuse-technical-configuration"></a>重用技術設定

如果您有多個相同類型的計畫,並且它們之間的套件相同,則可以選擇**此計劃重用其他計畫的技術設定**。 此選項允許您為此產品/服務選擇相同類型的其他計劃之一,並重複使用其技術配置。

> [!NOTE]
> 當您從另一個計劃重用技術配置時,整個**技術配置**選項卡將從這個計劃中消失。 其他計劃中的技術配置詳細資訊(包括您將來進行的任何更新)也將用於此計劃。 此計劃發佈後,無法更改此設置。

#### <a name="azure-regions"></a>Azure 區域

您的計劃必須至少在一個 Azure 區域中可用。

選擇**Azure 全域**選項,使計畫可供具有商業市場整合的所有公共 Azure 區域的客戶使用。 有關詳細資訊,請參閱[地理可用性和貨幣支援](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

選擇**Azure 政府**選項以使計畫在 Azure[政府](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)區域中可用。 此區域為美國聯邦、州、地方或部落實體的客戶以及有資格為其提供服務的合作夥伴提供受控訪問。 作為發佈者,您負責任何合規性控制、安全措施和最佳實踐。 Azure 政府使用物理隔離的數據中心和網路(僅適用於美國)。

在發佈到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前,在環境中測試和驗證計劃,因為某些終結點可能有所不同。 要設置和測試計畫,請從 Microsoft Azure[政府試用版](https://azure.microsoft.com/global-infrastructure/government/request/)請求試用帳戶。

> [!NOTE]
> 計劃發佈並在特定 Azure 區域中可用後,無法刪除該區域。

#### <a name="azure-government-certifications"></a>Azure 政府認證

僅當在**Azure 區域**下選擇**Azure 政府**時,此選項才可見。

Azure 政府服務處理受某些政府法規和要求約束的數據。 例如,FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 為了提高您對這些計劃的認證的認識,您最多可以提供 100 個描述它們的連結。 這些可以是直接指向您在程式上列表的連結,也可以是指向您自己網站上遵守這些清單的連結。 這些連結僅對 Azure 政府客戶可見。

選擇 **"保存"草稿**,然後再繼續。

### <a name="plan-listing"></a>排程清單

這是配置計劃清單詳細資訊的位置。 此選項卡顯示同一產品/服務中計劃之間可能有所不同的特定資訊。

#### <a name="plan-name"></a>計畫名稱

這預先填寫了您在創建計畫時給出的名稱。 此名稱作為此計劃的標題出現在市場中,限制為 100 個字元。

#### <a name="plan-summary"></a>計劃摘要

提供您的計劃(而不是報價)的簡短摘要。 此摘要限制為 100 個字元。

#### <a name="plan-description"></a>計劃描述

描述是什麼讓此軟體計畫獨一無二,以及您產品/服務中計劃之間的差異。 不要描述這個提議,只是計劃。 計劃描述最多可以包含 2,000 個字元。

選擇 **"保存"草稿**,然後再繼續。

### <a name="pricing-and-availability"></a>價格與可用性

在這裡選擇您設定以下內容:

- 此計劃將在
- 每小時價格
- 是使計劃對所有人可見,還是只對特定客戶(私人受眾) 可見

#### <a name="markets"></a>市場

每個計劃必須至少在一個市場可用。 選擇此計劃可供購買的每個市場位置的複選框(這些市場中的使用者仍然可以將產品/服務部署到**[計劃設置](#plan-setup)** 中選擇的所有 Azure 區域)。 **"稅務匯入**"按鈕顯示 Microsoft 代表您匯入銷售和使用稅款的國家/地區。 發佈到中國僅限於**免費**或**自帶許可證**(BYOL) 的計劃。

如果您已為計劃設定價格(美元),並添加其他市場位置,則新市場的價格將根據當前匯率計算。 在出版之前,務必查看每個市場的價格。 儲存變更後,使用 **「匯出價格 (xlsx)」** 連結查看價格。

刪除市場時,使用活動部署的客戶將無法創建新部署或擴展其現有部署。 現有部署不會受到影響。

#### <a name="pricing"></a>定價

**許可證模型**– 選擇**基於使用方式的每月計費計劃**,以設定此計劃的定價,或**自帶許可證**,讓客戶使用此計劃及其現有許可證。

對於基於使用方式的每月計費計劃,請使用以下三種定價輸入選項之一:

- **每個核心**– 以美元 (USD) 提供每個核心的價格。 我們將計算每個核心大小的定價,並使用當前匯率轉換為當地貨幣。
- **每個核心尺寸**– 提供每個核心尺寸的價格(美元)。 我們將使用當前匯率將價格轉換為當地貨幣。
- **每個市場和核心尺寸**– 為所有市場提供每個核心尺寸的價格。 您可以從電子錶格導入價格。

> [!NOTE]
> 保存定價更改以啟用定價數據匯出。 在計劃中市場的價格公佈后,以後無法更改。 通過匯出定價電子錶格並查看每個市場的價格,確保這些價格在發佈之前是正確的。

#### <a name="free-trial"></a>免費試用

您可以為客戶提供一個月或三個月的免費試用。

#### <a name="visibility"></a>可見性

您可以將每個計畫設計為對所有人可見,也可以僅對預選的受眾可見。 使用 Azure 訂閱標識分配此受限訪問群體中的成員身份。

**公開**– 您的計劃可以被所有人看到。

**私人受眾**– 使您的計劃僅對預選的受眾可見。 在以私人計畫身份發佈後,您可以更新受眾或將其更改為公共。 將計劃公之於眾後,必須保持公開;你不能把它改回私人。

**受限存取群體(Azure訂閱標識)** – 使用 Azure 訂閱標識分配將有權存取此私有計畫的受眾。 或者,包括您分配的每個 Azure 訂閱 ID 的說明。 如果導入 CSV 電子表格,則手動添加最多 10 個訂閱 I 或 20,000 個訂閱。 Azure 訂閱標識表示為 GUID,字母必須小寫。

> [!NOTE]
> 私人或受限受眾不同於您在 **「預覽」** 選項卡上定義的預覽受眾。預覽受眾可以在產品/服務發佈_於_市場直播之前訪問您的產品/服務。 雖然私人受眾選擇僅適用於特定計劃,但預覽受眾可以出於驗證目的查看所有計劃(私有計劃或計劃)。

#### <a name="hide-plan"></a>隱藏排程

如果虛擬機僅打算透過其他解決方案樣本或託管應用程式間接使用,請選擇此框以發佈虛擬機器,但直接從客戶搜尋和瀏覽虛擬機時將其隱藏。

> [!NOTE]
> 隱藏計劃不支援預覽連結。

選擇 **"保存"草稿**,然後再繼續。

### <a name="technical-configuration"></a>技術設定

提供與此計劃關聯的圖像和其他技術屬性。 有關詳細資訊,請參閱[創建 Azure VM 技術資產](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 如果您使用此計劃重用 **「計劃設定**」選項卡上其他計劃的包,則不顯示此選項卡。

#### <a name="operating-system"></a>作業系統

- **操作系統系列**- 從**Windows**或**Linux**作業系統中選擇
- **發布**或**供應商**-選擇 Windows 版本或 Linux 供應商
- **操作系統友好名稱**– 選擇友好的作業系統名稱。 此名稱對客戶可見

#### <a name="recommended-vm-sizes"></a>建議的 VM 大小

選擇最多六個建議的虛擬機大小,以顯示在 Azure 應用商店中。

#### <a name="open-ports"></a>開啟連接埠

在已部署的虛擬機器上打開公共或專用埠。

#### <a name="storage-option-for-deployment"></a>部署的儲存選項

**磁碟部署選項**- 選擇使用者在使用虛擬機器時可以使用的磁碟部署類型。 Microsoft 建議將部署僅限制為託管磁碟部署。

#### <a name="properties"></a>屬性

**支援加速網路**- 選擇 VM 是否支援[加速網路](https://go.microsoft.com/fwlink/?linkid=2124513)。

#### <a name="vm-images"></a>VM 映像

為虛擬機映射提供磁碟版本和 SAS URI。 為每個 VM 映射添加最多 16 個數據磁碟。 在給定提交中,每個計劃僅提供一個新映射版本。 發佈圖像后,無法對其進行編輯,但可以將其刪除。 刪除版本將阻止新使用者和現有使用者部署已刪除版本的新實例。

- **光碟版本**是您提供的圖像的版本。
- **SAS URI**是 Azure 儲存中儲存作業系統 VHD 的位置。
- 數據磁碟映射也是存儲在其 Azure 儲存中的 VHD SAS URI。
- 在計劃中每個提交只添加一個圖像。

無論您使用哪種作業系統,都只添加解決方案所需的最小數據磁碟數。 客戶無法在部署時刪除屬於映射的磁碟,但始終可以在部署期間或部署後添加磁碟。

選擇 **"儲存"草稿**,然後再繼續並傳回到**計畫概述**。

## <a name="resell-through-csps"></a>透過 CSP 轉售

通過向[雲端解決方案供應商](https://azure.microsoft.com/offers/ms-azr-0145p/)(CSP) 計畫中的合作夥伴提供產品/服務,從而擴大產品/服務範圍。 所有自帶許可證 (BYOL) 計劃均自動選擇;您可以選擇加入您的非 BYOL 計劃。

選擇 **"保存"草稿**,然後再繼續。

## <a name="test-drive"></a>試用產品

設置示範(測試驅動器),讓客戶在購買之前試用您的產品/服務。 要建立展示的環境,讓客戶在固定的時間內試用您的產品/服務,請參閱[在商業市場試用您的產品/服務](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

要啟用試駕,請在「[產品/服務設置」](#test-drive)選項卡上選中「啟用試駕」複選框。要從產品/服務中刪除試駕,請清除此複選框。

其他試駕資源:

- 技術最佳實踐
- 行銷最佳做法
- 測試驅動器概述(PDF;請確保您的彈出視窗阻止程式已關閉)。

選擇 **"保存"草稿**,然後再繼續。

## <a name="review-and-publish"></a>稽核和發佈

完成產品/服務所需的所有部分後,可以將其提交以供審核和發佈。

在門戶的右上角,選擇 **「審閱」併發佈**。

在這裡頁面上,您可以:

- 請參閱產品/服務的每個部分的完成狀態。
  - **未啟動**- 部分尚未啟動,需要完成。
  - **不完整**- 該部份有必須修復或要求您提供詳細資訊的錯誤。 有關如何更新本節的指導,請參閱本文檔前面的部分。
  - **完成**– 部分已完成,沒有錯誤。 優惠的所有部分必須完成,然後才能提交報價。
- **認證注意事項**– 向認證團隊提供測試說明,以確保應用測試正確。 提供有助於瞭解應用的任何補充說明。

要提交發佈要約,請選擇 **「審閱」併發表**。

我們將向您發送一封電子郵件,告知您產品/服務預覽版本何時可供審核和批准。 要向公眾發佈您的優惠(或者如果私人優惠,則向私人受眾),請轉到合作夥伴中心,找到優惠的 **「概述」** 頁面,然後選擇 **「上線**」。 發佈時,您的產品/服務狀態將顯示在頁面頂部。

### <a name="errors-and-review-feedback"></a>錯誤和審核意見反應

發佈過程中的**手動驗證**步驟是對您的產品/服務及其相關技術資產的廣泛審查。 如果此過程發現您的產品/服務出現錯誤,您將收到一份詳細說明問題的認證報告。 只需進行必要的更正並重新發佈您的報價。

## <a name="offer-overview"></a>供應項目概觀

**"產品/服務概述"** 頁顯示發佈此產品/服務所需的步驟(已完成和正在進行的)以及每個步驟應完成多長時間的可視化表示形式。

此頁包含基於您做出的選擇對此產品/服務執行操作的連結。 例如：

- 如果要約是草稿 -[刪除草稿要約](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果優惠是即時的 -[停止銷售優惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果優惠處於預覽版 -[上線](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成發行者簽核 -[取消發佈](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>市場範例

這些示例顯示了產品/服務在 Azure 應用商店中的顯示方式。

### <a name="azure-marketplace-offer-details"></a>Azure 應用商店提供詳細資訊

![Azure 應用商店提供詳細資訊螢幕範例](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure 應用商店搜尋結果

![Azure 應用商店搜索詳細資訊螢幕範例](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure 應用商店計劃詳細資訊

![Azure 應用商店計劃詳細資訊螢幕範例](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure 門戶提供詳細資訊

![Azure 門戶提供詳細資訊螢幕範例](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure 門戶搜尋結果

![Azure 門戶搜尋結果螢幕範例](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure 門戶計劃詳細資訊

![Azure 門戶計劃詳細資訊螢幕範例](media/avm-create6.png)

## <a name="next-step"></a>後續步驟

- [更新商業市場中的現有產品/服務](https://aka.ms/UpdateOfferCM)
