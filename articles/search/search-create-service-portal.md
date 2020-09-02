---
title: 在入口網站中建立搜尋服務
titleSuffix: Azure Cognitive Search
description: 在本入口網站快速入門中，了解如何在 Azure 入口網站中設定 Azure 認知搜尋資源。 選擇資源群組、區域、SKU 或定價層。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 6813db0b7416695fb9433a701114f2684dd0e122
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927114"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>快速入門：在入口網站中建立 Azure 認知搜尋服務

Azure 認知搜尋是一項可用來在自訂應用程式中插入搜尋體驗的獨立資源。 認知搜尋可與其他 Azure 服務、網路伺服器上的應用程式，或其他雲端平台上執行的軟體輕易整合。

在本文中，請了解如何在 [Azure 入口網站](https://portal.azure.com/)中建立資源。

[![動畫 GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

是否偏好使用 PowerShell？ 請使用 Azure Resource Manager [服務範本](https://azure.microsoft.com/resources/templates/101-azure-search-create/)。 如需入門說明，請參閱[使用 PowerShell 管理 Azure 認知搜尋](search-manage-powershell.md)。

## <a name="before-you-start"></a>開始之前

下列服務屬性已針對服務的存留期進行修正，需要新的服務才能變更。 因為這些服務屬性是固定的，所以在填寫每個屬性時，請將屬性使用方式的含意納入考慮：

* 服務名稱會成為 URL 端點的一部分 ([檢閱秘訣](#name-the-service)了解實用的服務名稱)。
* 服務層級會[影響帳單](search-sku-tier.md)並設定容量上限。 有些功能無法在免費層使用。
* 服務區域可決定特定案例的可用性。 如果您需要[高安全性功能](search-security-overview.md)或 [AI 擴充](cognitive-search-concept-intro.md)，必須將 Azure 認知搜尋放在與其他服務相同的區域中，或放在提供問題功能的區域中。 

## <a name="subscribe-free-or-paid"></a>訂閱 (免費或付費)

[開啟免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，並使用免費信用額度來試用付費的 Azure 服務。 當您用完信用額度之後，請保留帳戶，並繼續使用免費的 Azure 服務，例如網站。 除非您明確變更您的設定且同意付費，否則我們絕對不會從您的信用卡收取任何費用。

或者，請[啟用 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN 訂用帳戶每月會提供您信用額度，讓您可以用於 Azure 付費服務。 

## <a name="find-azure-cognitive-search"></a>尋找 Azue 認知搜尋

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 按一下左上角的加號 (「+ 建立資源」)。

1. 使用搜尋列尋找「Azure 認知搜尋」，或透過 [Web] > [Azure 認知搜尋] 瀏覽至資源。

![在入口網站建立資源](./media/search-create-service-portal/find-search3.png "在入口網站建立資源")

## <a name="choose-a-subscription"></a>選擇訂用帳戶

如果您有多個訂用帳戶，請針對您的搜尋服務選擇一個訂用帳戶。 如果您要實作[雙重加密](search-security-overview.md#double-encryption)或其他相依於受控服務識別的功能，請選擇與用於 Azure Key Vault 的訂用帳戶，或使用受控識別的其他服務。

## <a name="set-a-resource-group"></a>設定資源群組

資源群組是存放 Azure 方案相關資源的容器。 這是搜尋服務的必要項目。 也適用於所有管理資源 (包括成本)。 資源群組可以包含一個服務，或多個一起使用的服務。 例如，如果您使用 Azure 認知搜尋來編製 Azure Cosmos DB 資料庫的索引，您可以將這兩個服務納入相同的資源群組中，以便管理。 

如果您不想將資源結合成單一群組，或現有的資源群組中有許多資源用於不相關的解決方案中，請為您的 Azure 認知搜尋資源建立專屬的新資源群組。 

![建立新的資源群組](./media/search-create-service-portal/new-resource-group.png "建立新的資源群組")

經過一段時間，您可以追蹤目前和預估的所有成本，或查看個別資源的費用。 下列螢幕擷取畫面顯示當您將多個資源結合成一個群組時，您會看到的成本資訊類型。

![在資源群組層級管理成本](./media/search-create-service-portal/resource-group-cost-management.png "在資源群組層級管理成本")

> [!TIP]
> 資源群組可簡化清除，因為刪除群組也會刪除其中的所有服務。 針對使用多個服務的原型專案，將它們全部放入同一個資源群組，在專案結束之後就能更容易清除。

## <a name="name-the-service"></a>為服務命名

在 [執行個體詳細資料] 的 [URL] 欄位中提供服務名稱。 該名稱是 URL 端點的一部分，API 呼叫是根據此端點所發出：`https://your-service-name.search.windows.net`。 例如，如果您所需的端點是 `https://myservice.search.windows.net`，則應輸入 `myservice`。

服務名稱需求：

* 此名稱在 search.windows.net 命名空間中必須是唯一名稱
* 其長度必須介於 2 到 60 個字元之間
* 您必須使用小寫字母、數字或連字號 ("-")
* 前 2 個字元或最後一個字元不可使用連字號 ("-")
* 任何地方都不能使用連續的連字號 ("--")

> [!TIP]
> 如果您認為您將使用多個服務，建議您在服務名稱中包含區域 (或位置) 以作為為命名慣例。 相同區域內的服務可以免費交換資料，因此，如果 Azure 認知搜尋位於美國西部，而您在美國西部也有其他服務，則類似 `mysearchservice-westus` 的名稱可讓您在決定如何結合或連結資源時，為您省去前往屬性頁面的步驟。

## <a name="choose-a-location"></a>選擇位置

Azure 認知搜尋可在大部分區域中使用。 支援的區域清單可在[定價頁面](https://azure.microsoft.com/pricing/details/search/)中找到。

> [!Note]
> 新的服務目前無法在印度中部與阿拉伯聯合大公國北部使用。 對於已在那些區域提供的服務，您可以無限制地擴大，且您的服務在該區域中受到完整的支援。 限制是暫時的且僅限於新的服務。 當限制不再適用時，我們將移除此注意事項。
>
> 雙重加密僅適用於特定區域。 如需詳細資訊，請參閱[雙重加密](search-security-overview.md#double-encryption)。

### <a name="requirements"></a>需求

 如果您使用 AI 擴充資料，請在與認知服務相同的區域中建立您的搜尋服務。 *將 Azure 認知搜尋與認知服務共置於相同區域中，是 AI 擴充的需求之一*。

 具有商務持續性和災害復原 (BCDR) 需求的客戶應在[區域配對](../best-practices-availability-paired-regions.md#azure-regional-pairs) \(部分機器翻譯\) 中建立其服務。 例如，如果您是在北美洲營運，可以針對每個服務選擇美國東部與美國西部，或美國中北部與美國中南部。

### <a name="recommendations"></a>建議

如果您使用多個 Azure 服務，請選擇同時裝載您資料或應用程式服務的區域。 這樣做可將輸出資料的頻寬費用降至最低或避免產生費用 (當服務位於相同區域時，輸出資料不會產生費用)。

## <a name="choose-a-pricing-tier-sku"></a>選擇定價層 (SKU)

[Azure 認知搜尋目前提供多個定價層](https://azure.microsoft.com/pricing/details/search/)︰免費、基本或標準。 每一層都有自己的[容量和限制](search-limits-quotas-capacity.md)。 請參閱[選擇定價層或 SKU](search-sku-tier.md) 以取得指導方針。

一般對於生產工作負載通常會選擇基本和標準服務，但大部分的客戶一開始都會使用免費服務。 層級之間的主要差異在於分割區大小與速度，以及您可以建立的物件數目限制。

請記住，服務建立之後便無法變更定價層。 如果您需要更高或更低的定價層，必須重新建立服務。

## <a name="create-your-service"></a>建立您的服務

在您提供必要的輸入之後，請繼續並建立服務。 

![檢閱及建立服務](./media/search-create-service-portal/new-service3.png "檢閱及建立服務")

您的服務會在幾分鐘內部署完成。 您可以透過 Azure 通知來監視進度。 請考慮將服務釘選在儀表板上，以便日後存取。

![監視及釘選服務](./media/search-create-service-portal/monitor-notifications.png "監視及釘選服務")

## <a name="get-a-key-and-url-endpoint"></a>取得金鑰和 URL 端點

除非您使用入口網站，否則以程式設計方式存取新服務時，您需要提供 URL 端點和授權 API 金鑰。

1. 在 [概觀] 頁面中，找出並複製頁面右側的 URL 端點。

2. 在 [金鑰] 頁面上，複製其中一個系統管理金鑰 (它們是相等的)。 在您的服務上建立、更新及刪除物件時，需要系統管理員 API 金鑰。 相反地，查詢金鑰會提供索引內容的讀取存取權。

   ![包含 URL 端點的服務概觀頁面](./media/search-create-service-portal/get-url-key.png "URL 端點和其他服務詳細資料")

入口網站工作不需要端點和金鑰。 入口網站已透過管理員權限連結至您的 Azure 認知搜尋資源。 如需入口網站逐步解說，請從[快速入門：在入口網站中建立 Azure 認知搜尋索引](search-get-started-portal.md)。

## <a name="scale-your-service"></a>調整您的服務

佈建完您的服務之後，您可以調整它以符合您的需求。 如果您為 Azure 認知搜尋服務選擇了「標準」層，您可以在兩個維度調整服務︰複本和分割區。 如果您選擇的是「基本」層，則只能新增複本。 如果您佈建的是免費服務，則無法進行調整。

「資料分割」允許您的服務儲存及搜尋更多文件。

「複本」允許服務來處理更高的搜尋查詢負載。

新增資源會增加每月的帳單。 [定價計算機](https://azure.microsoft.com/pricing/calculator/)可協助您了解新增資源對帳單的影響。 請記住，您可以根據負載來調整資源。 例如，您可增加資源來建立完整的初始索引，並於稍後將資源減少到較適合累加式編製索引的層級。

> [!Important]
> 服務必須具有 [2 個唯讀 SLA 的複本和 3 個讀/寫 SLA 的複本](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 在 Azure 入口網站中移至您的搜尋服務頁面。
2. 在左導覽窗格中，選取 [設定] > [調整]。
3. 您可以使用滑桿來新增任何一種類型的資源。

![新增容量](./media/search-create-service-portal/settings-scale.png "透過複本和分割區新增容量")

> [!Note]
> 每個分割區的儲存體和速度均以更高的層次增加。 如需詳細資訊，請參閱[容量和限制](search-limits-quotas-capacity.md)。

## <a name="when-to-add-a-second-service"></a>新增第二個服務的時機

大部分的客戶都是使用在提供[正確資源平衡](search-sku-tier.md)的層次上佈建的單一服務。 單一服務可以裝載多個索引 (數量受限於[所選層的最大限制](search-capacity-planning.md))，且每個索引都互相隔離。 在 Azure 認知搜尋中，要求只能導向至單一索引，以盡可能降低意外或刻意從相同服務的其他索引中擷取資料的機會。

雖然大部分的客戶只使用單一服務，但如果操作需求包含下列項目，則可能需要服務備援：

+ [商務持續性和災害復原 (BCDR)](../best-practices-availability-paired-regions.md) \(部分機器翻譯\)。 Azure 認知搜尋不提供中斷時的即時容錯移轉功能。

+ [多租用戶架構](search-modeling-multitenant-saas-applications.md)有時會呼叫兩個以上的服務。

+ 全球部署的應用程式可能在每個地理位置都需要搜尋服務，以盡可能縮短延遲。

> [!NOTE]
> 在 Azure 認知搜尋中，您無法區隔索引和查詢作業，因此您永遠不會針對區隔的工作負載建立多個服務。 一律是在建立索引的服務上查詢該索引 (您無法在某個服務中建立索引，並將它複製到另一個服務)。

不需要第二個服務即可獲得高可用性。 當您在同一個服務中使用 2 個或更多的複本，查詢就會達到高可用性。 複本更新是循序的，這表示當服務更新推出時，至少會有一個複本是可運作的。如需執行時間的詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

## <a name="next-steps"></a>後續步驟

佈建服務之後，您可以繼續在入口網站中建立第一個索引。

> [!div class="nextstepaction"]
> [快速入門：在入口網站中建立 Azure 認知搜尋索引](search-get-started-portal.md)