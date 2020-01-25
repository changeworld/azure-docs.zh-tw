---
title: Azure IoT Edge 模組的 Marketplace 描述 |Azure Marketplace
description: 建立 IoT Edge 模組的 Marketplace 說明。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8021b6fe40e006a95b3eeff1031606c38cc47b87
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721620"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge 模組 Marketplace 索引標籤

在 [新增供應項目] 頁面的 [Marketplace] 索引標籤上，可提供行銷、銷售和法律方面的資訊與合約給您的潛在客戶，並管理 Marketplace 產生的潛在客戶。 這個長表單分成四個區段：**概觀**、**行銷成品**、**潛在客戶管理**和**法律聲明**。


## <a name="overview"></a>概觀

在本區段中，可以輸入 Azure Marketplace Offer 的一般資訊。  欄位名稱上附加的星號 (*) 表示此為必填欄位。

![IoT Edge 模組 [新增供應項目] 表單上的 [Marketplace] 索引標籤概觀區段](./media/iot-edge-module-marketplace-tab-overview.png)

下表說明這些欄位的用途和內容。 必要欄位由星號 (*) 標示。

|  **欄位**                |     **說明**                                                          |
|  ---------                |     ---------------                                                          |
| **標題\***                 | 供應項目的標題。 此標題會在 Marketplace 中醒目顯示。  長度上限為 50 個字元。 <!--ADD PICTURE IN ACTION-->|
| **摘要\***               | 供應項目的簡短摘要。 長度上限是 100 個字元。 <!--ADD PICTURE IN ACTION-->|
| **長摘要\***          | 供應項目的完整摘要 (雖然可能與**摘要**相同)。  長度上限為 256 個字元。 <!--ADD PICTURE IN ACTION-->|
| **說明\***           | 供應項目的說明。  長度上限是 3000 個字元，支援簡單的 HTML 格式。<br/> 尾端必須提供*最低硬體需求*段落。 例如： <br/> <p><u>最低硬體需求：</u>Linux x64 和 arm32 OS，1 GB 的 RAM，500 Mb 的儲存空間</p>
| **行銷識別碼\***  | 與本供應項目相關聯的唯一不重複網址，通常會包含您的組織與解決方案名稱，長度上限是 50 個字元。  例如： <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **預覽訂用帳戶識別碼** | 可新增 1 到 100 個預覽使用者的訂閱帳戶識別碼。 這些列入允許清單的訂閱帳戶，待供應項目一發佈，即能在供應項目上線前先行存取。 |
| **實用連結**          | 可選擇多個適合與供應項目建立關聯的企業和技術類別，  上限為 10 個。 請務必從 [Azure IoT 裝置目錄](https://catalog.azureiotsolutions.com/)新增至少一個說明文件的連結和一個相容 IoT Edge 裝置的連結。 |
| **建議的類別\*** | 可挑選最多五個類別， 會顯示在您的產品詳細資料頁面上。 在瀏覽頁面中，所有 IoT Edge 模組都會顯示在*物聯網\> IoT Edge 模組*類別下。|
|  |  |


### <a name="offer-example"></a>供應項目範例

 下列範例顯示了供應項目的 [標題]、[摘要]、[說明]、[標誌] 以及 [螢幕擷取畫面] 欄位會以何種方式顯示在不同的檢視畫面上。

 
#### <a name="on-the-azure-marketplace-website"></a>在 Azure Marketplace 網站上：

- 瀏覽供應項目：

    ![Mrketplace 供應項目在 Azure Marketplace 網站上的顯示方式：瀏覽](./media/iot-edge-module-ampdotcom-card.png)

- 查看供應項目詳細資料：

    ![在網站中查看產品詳細資料時，IoT Edge 模組的顯示方式](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>在 Azure 入口網站上：

- 瀏覽供應項目：

    ![瀏覽 Azure 入口網站時，IoT Edge 模組的顯示方式 #1](./media/iot-edge-module-portal-browse.png)

    ![瀏覽 Azure 入口網站時，IoT Edge 模組的顯示方式 #2](./media/iot-edge-module-portal-product-picker.png)

- 搜尋供應項目：

    ![搜尋 Azure 入口網站時，IoT Edge 模組的顯示方式](./media/iot-edge-module-portal-search.png)

- 查看供應項目詳細資料：

    ![在入口網站中查看產品詳細資料時，IoT Edge 模組的顯示方式](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>行銷成品

此區段含有下列子區段：[標誌]、[螢幕擷取畫面] 以及 [影片]。 

>[!Note]
>標誌是唯一必要的行銷成品，但強烈建議您填寫所有項目，以提升對客戶的吸引力。

![虛擬機器 [新增供應項目] 表單上的 [Marketplace] 索引標籤行銷成品區段](./media/publishvm_009.png)

|  **欄位**                |     **說明**                                                          |
|  ---------                |     ---------------                                                          |
| *標誌*  | 請參閱先前的螢幕擷取畫面，了解標誌的使用方式和位置。  |
| **小型\***                 | 40x40 像素的 PNG 格式                                                     |
| **中型\***                | 90x90 像素的 PNG 格式                                                     |
| **大型\***                 | 115x115 像素的 PNG 格式                                                  |
| **寬\***                  | 255x115 像素的 PNG 格式                                                   |
| **主圖**                  | 815x290 像素的 PNG 格式。  (選擇性) 主圖圖示一旦上傳，即無法刪除。 |
| *螢幕擷取畫面*  | 螢幕擷取畫面會顯示在您的產品詳細資料頁面上， 相當適合用來以視覺方式傳達 IoT Edge 模組的功能及其運作方式。 例如可以顯示架構圖表，或是執行個體的使用案例圖例。 (選擇性) 每個 SKU 的螢幕擷取畫面上限為五個。 |
| **名稱**                  | 名稱或標題。 長度上限是 100 個字元。                             |
| **映像**                 | 螢幕擷取映像，533x324 像素的 PNG 格式                               |
| *影片*  | 影片會顯示在您的產品詳細資料頁面上。 相當適合用來以視覺方式傳達 IoT Edge 模組的功能及其運作方式。 |
| **名稱**                  | 名稱或標題。 長度上限是 100 個字元。                             |
| **連結**                  | 裝載於 YouTube 或 Vimeo 的影片 URL                                        |
| **縮圖**             | 533x324 像素的 PNG 格式                                                     |
|  |  |


### <a name="logo-guidelines"></a>標誌指導方針

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud Partner 入口網站中所上傳的所有標誌都應該遵循指導方針︰

*  Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。
*  Azure 入口網站的佈景主題色彩是白色與黑色。 請避免使用這些色彩做為您標誌的背景色彩。 請使用可在 Azure 入口網站突顯您的標誌的色彩。 建議您使用簡單的主要色彩。 如果您使用透明背景，請確定標誌/文字不是白色、黑色或藍色。
*  不要在標誌上使用漸層背景。
*  避免在標誌上放置文字 (甚至是公司或品牌名稱)。 您標誌的外觀與風格應該很「平面」，而且應避免使用漸層。
*  不要延展標誌。


#### <a name="hero-logo"></a>主圖標誌

主圖標誌為選用項。

>[!Important]
>上傳主圖標誌之後，便無法刪除。

請針對主圖標誌使用下列指引： 

*  不允許使用黑色、白色與透明背景。
*  避免在背景中使用任何淺色的標誌。  發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型色彩顯示，而且必會和背景有明顯對比。
*  設計標誌時，請避免以文字為主體。 供應項目刊出之後，會透過程式設計方式，將發行者名稱、方案標題、供應項目完整摘要以及 [建立] 按鈕內嵌在標誌內。 
* 請在主圖標誌的右側加入一個未使用的矩形空間。 此空白區域需為 415x100 像素，並從左側位移 370 像素。  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>潛在客戶管理

可在此區段設定用於收集 Azure Marketplace 供應項目產生之潛在客戶的選項。 可從下拉式清單中選取下列儲存體選項。

* **無**：預設值，未收集潛在客戶資訊。
* Azure 資料表：寫入連接字串所指定的 Azure 資料表。
* Dynamics CRM Online：寫入 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 執行個體，由 URL 和驗證認證加以指定。
* HTTPS 端點：寫入指定的 HTTPS 端點，做為 JSON 承載。
* Marketo：寫入指定的 [Marketo](https://www.marketo.com/) 執行個體，由伺服器識別碼、munchkin 識別碼和表單識別碼加以指定。
* Salesforce：寫入 [Salesforce](https://www.salesforce.com/) 資料庫，由物件識別碼指定。

成功發行供應項目之後，即已驗證潛在客戶連線，而且測試潛在客戶會自動傳送到您設定的目的地。 

>[!Note]
>請持續管理潛在客戶資訊，只要客戶管理架構有所變更，就應立即更新設定。

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>法律聲明

可在此區段提供每個供應項目兩份必要的法律文件：隱私權原則和使用條款。

|  **欄位**                    |     **說明**                                                          |
|  ---------                    |     ---------------                                                          |
| **隱私權原則 URL\***      | 張貼隱私權原則的 URL                                            |
| **使用標準 Constract\***  | 是否要使用標準 Microsoft 合約範本。  如需詳細資訊，請參閱[標準合約](https://docs.microsoft.com/azure/marketplace/standard-contract)。   |
| **使用規定\***            | *使用規定*為內嵌的簡單 HTML，或連結至您已張貼的使用條款頁面     |
|  |  |


## <a name="next-steps"></a>後續步驟

請使用 [[支援]](./cpp-support-tab.md) 索引標籤來協助您為供應項目提供技術及使用者支援資源。
