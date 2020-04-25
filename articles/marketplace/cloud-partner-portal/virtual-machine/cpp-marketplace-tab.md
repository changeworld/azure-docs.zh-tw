---
title: Azure Marketplace 的 Cloud Partner 入口網站中的 [虛擬機器 Marketplace] 索引標籤
description: 說明用來建立 Microsoft Azure Marketplace 虛擬機器供應項目的 Marketplace 索引標籤。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146867"
---
# <a name="virtual-machine-marketplace-tab"></a>虛擬機器 Marketplace 索引標籤

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請依照[建立 Azure 虛擬機器供應](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)專案中的指示來管理您的已遷移優惠。

在 [新增供應項目]**** 頁面的 [Marketplace]**** 索引標籤上，可提供行銷、銷售和法律方面的資訊與合約給您的潛在客戶，並管理 Marketplace 產生的潛在客戶。 這個長表單分成四個區段：**概觀**、**行銷成品**、**潛在客戶管理**和**法律聲明**。


## <a name="overview-section"></a>概觀區段
在本區段中，可以輸入 Azure Marketplace Offer 的一般資訊。  欄位名稱上附加星號 (*) 表示為必填欄位。

![虛擬機器的 [Marketplace] 索引標籤的 [總覽] 區段](./media/publishvm_008.png)

下表說明這些欄位的用途和內容。 必要欄位由星號 (*) 標示。

|  **欄位**                |     **描述**                                                          |
|  ---------                |     ---------------                                                          |
| **標題\***                 | 供應項目的標題，通常是較長的正式名稱。 這個標題會醒目顯示在 Marketplace 中。  長度上限為 50 個字元。 |
| **摘要\***               | 解決方案功能的簡短用途。  長度上限是 100 個字元。 |
| **完整摘要\***          | 解決方案功能的用途。  長度上限為 256 個字元。 |
| **說明\***           | 解決方案說明。  長度上限是 3000 個字元，支援簡單的 HTML 格式。 |
| **Microsoft CSP 轉銷商頻道\*** | 雲端解決方案提供者（CSP）合作夥伴頻道加入宣告現已推出。  如需透過 Microsoft CSP 合作夥伴頻道行銷供應專案的詳細資訊，請參閱[雲端解決方案提供者](../../cloud-solution-providers.md)。 |
| **行銷識別碼\***  | 與本供應項目相關聯的唯一不重複網址，通常會包含您的組織與解決方案名稱，長度上限是 50 個字元。  例如： <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **預覽訂用帳戶識別碼\*** | 可新增 1 到 100 個預覽使用者的訂閱帳戶識別碼。 這些列入允許清單的訂閱帳戶，待供應項目一發佈，即能在供應項目上線前先行存取。 |
| **實用連結**          | 新增文件、版本資訊、常見問題等等的 URL。 |
| **建議的類別\*** | 最多可選取兩個（2）類別，包括主要和次要類別（選擇性）。 針對每個主要和/或次要類別目錄選取最多兩個（2）子類別。 如果未選取任何子類別，您仍然可以在選取的類別上找到您的供應專案。 |
|  |  |


## <a name="marketing-artifacts-section"></a>行銷成品區段

第二個區段分成三個子區段：**標誌**、**螢幕擷取畫面**和**影片**。 標誌是唯一必要的行銷成品，但強烈建議您填寫所有項目，以提升對客戶的吸引力。 

![虛擬機器 [新增供應項目] 表單上的 [Marketplace] 索引標籤行銷成品區段](./media/publishvm_009.png)

下表說明這些欄位的用途和內容。 必要欄位由星號 (*) 標示。

|  **欄位**                |     **描述**                                                          |
|  ---------                |     ---------------                                                          |
| *標誌*  |  |
| **小型\***                 | 40x40 像素 .ico 點陣圖                                                      |
| **中\***                | 90x90 像素 .ico 點陣圖                                                      |
| **大型\***                 | 115x115 像素 .ico 點陣圖                                                   |
| **廣\***                  | 255x115 像素 .ico 點陣圖                                                    |
| **主圖**                  | 815 x 290 點陣圖。  (選擇性) 主圖圖示一旦上傳，即無法刪除。 |
| *螢幕擷取畫面*  | (選擇性) 每個 SKU 的螢幕擷取畫面上限為五個。 |
| **名稱**                  | 名稱或標題  <!-- TODO - max char length? none specified in UI -->                               |
| **影像**                 | 螢幕擷取映像，533x324 像素                                         |
| *影片*  |  |
| **名稱**                  | 名稱或標題   <!-- TODO - max char length? -->                              |
| **連結**                  | 裝載於 YouTube 或 Vimeo 的影片 URL                                        |
| **縮圖**             | 533x324 點陣圖                                                               |
|   |   |

### <a name="logo-guidelines"></a>標誌指導方針

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud Partner 入口網站中所上傳的所有標誌都應該遵循指導方針︰

*  Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。
*  Azure 入口網站的佈景主題色彩是白色與黑色。 因此請避免使用這些色彩作為您標誌的背景色彩。 請使用會讓您的標誌在 Azure 入口網站顯得突出的某些色彩。 建議您使用簡單的主要色彩。 如果您使用透明背景，請確定標誌/文字不是白色、黑色或藍色。
*  不要在標誌上使用漸層背景。
*  避免在標誌上放置文字 (甚至是公司或品牌名稱)。 您標誌的外觀與風格應該很「平面」，而且應避免使用漸層。
*  不要延展標誌。

#### <a name="hero-logo"></a>主圖標誌

主圖圖示為選擇性項目，不過一旦上傳即無法刪除。  主圖標誌圖示應該遵循下列指導方針：

*  主圖圖示不允許使用黑色、白色與透明背景。
*  避免在主圖圖示使用淺色系背景。  發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型色彩顯示，並且必須在背景中醒目顯示。
*  設計主圖標誌時避免使用大部分文字。  供應項目列出之後，會以程式設計方式將發行者名稱、方案標題、供應項目完整摘要與 [建立] 按鈕內嵌在主圖標誌內。 
* 包含位於主圖標誌右側的未使用矩形，大小為 415x100 像素，從左位移 370 像素。  

例如，以下主圖圖示適用於 Azure Container Service。  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Azure Container Service 的範例主圖圖示](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>行銷資訊範例 

下列映像示範如何將行銷資訊顯示在 Microsoft Windows Server 主要產品頁面。

![Microsoft Windows Server 的範例產品頁面](./media/publishvm_011.png)


## <a name="lead-management-section"></a>潛在客戶管理區段

第三個區段可讓您收集 Azure Marketplace 供應項目產生的潛在客戶。 此區段提供下列潛在客戶資訊的儲存選項 (位於下拉式清單)。

* **無**：預設值，未收集潛在客戶資訊。
* Azure 資料表：寫入連接字串所指定的 Azure 資料表。
* Dynamics CRM Online：寫入 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 執行個體，由 URL 和驗證認證加以指定。
* HTTPS 端點：寫入指定的 HTTPS 端點，做為 JSON 承載。
* Marketo：寫入指定的 [Marketo](https://www.marketo.com/) 執行個體，由伺服器識別碼、munchkin 識別碼和表單識別碼加以指定。
* Salesforce：寫入 [Salesforce](https://www.salesforce.com/) 資料庫，由物件識別碼指定。

成功發行供應項目之後，即已驗證潛在客戶連線，而且測試潛在客戶會自動傳送到您設定的目的地。 請持續管理潛在客戶資訊，只要客戶管理架構有所變更，就應立即更新設定。

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>法律聲明區段

最後一個區段可讓您提供每個供應專案所需的必要法律檔。  

|  **欄位**                    |     **描述**                                        |
|  ---------                    |     ---------------                                        |
| **隱私權原則 URL\***      | 張貼隱私權原則的 URL                          |
| **使用標準合約嗎？\***  |   |
| **使用規定\***            | 純文字或簡單 HTML 原則。                       |
|  |  |


## <a name="next-steps"></a>後續步驟

您可以在下一個[支援](./cpp-support-tab.md)索引標籤中，為供應項目提供技術和使用者支援資源。
