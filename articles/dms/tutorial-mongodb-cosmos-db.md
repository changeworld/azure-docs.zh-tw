---
title: 教程：將蒙戈DB離線遷移到蒙戈DB的 Azure 宇宙 DB API
titleSuffix: Azure Database Migration Service
description: 瞭解如何使用 Azure 資料庫移轉服務從本地蒙戈DB遷移到蒙戈DB的 Azure Cosmos DB API 離線。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 08fa94dbe71299a6653df0b40aa5083375526172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255583"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>教程：使用 DMS 將蒙戈DB遷移到 Azure Cosmos DB 的 API，使蒙戈DB離線

您可以使用 Azure 資料庫移轉服務，在離線狀態下將資料庫從內部部署或雲端的 MongoDB 執行個體 (單次) 移轉至 Azure Cosmos DB 的 Mongo 版 API。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

在本教學課程中，您會使用 Azure 資料庫移轉服務，從裝載在 Azure 虛擬機器中的 MongoDB，將其中的某個資料集遷移至 Azure Cosmos DB 的 Mongo 版 API。 如果您尚未設定 MongoDB 來源，請參閱[在 Azure 中的 Windows VM 上安裝及設定 MongoDB](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb) 一文。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* [完成移轉前](../cosmos-db/mongodb-pre-migration.md)步驟，例如估計輸送量、選擇分割索引鍵和索引編製原則。
* [建立 Azure Cosmos DB 的 Mongo 版 API 帳戶](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
* 通過使用 Azure 資源管理器部署模型為 Azure 資料庫移轉服務創建 Microsoft Azure 虛擬網路，該模型通過使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)提供與本地源伺服器的網站到網站的連接。 有關創建虛擬網路的詳細資訊，請參閱[虛擬網路文檔](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步詳細資訊的快速入門文章。

    > [!NOTE]
    > 在虛擬網路設置期間，如果將 ExpressRoute 與網路對等互連到 Microsoft，則向將服務預配的子網添加以下服務[終結點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)：
    >
    > * 目標資料庫端點 (例如，SQL 端點、Cosmos DB 端點等)
    > * 儲存體端點
    > * 服務匯流排端點
    >
    > 此為必要設定，因為 Azure 資料庫移轉服務沒有網際網路連線。

* 確保您的虛擬網路網路安全性群組 （NSG） 規則不會阻止以下通訊連接埠：53、443、445、9354 和 10000-20000。 有關虛擬網路 NSG 流量篩選的更多詳細資訊，請參閱文章["使用網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)"。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 MongoDB 伺服器 (依預設會使用 TCP 連接埠 27017)。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]****，然後選取 [訂用帳戶]****。

   ![顯示入口網站訂用帳戶](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]****。

    ![顯示資源提供者](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. 搜索遷移，然後搜索 Microsoft 的右側 **。** **Register**

    ![註冊資源提供者](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>建立執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]****，搜尋「Azure 資料庫移轉服務」，然後從下拉式清單選取 [Azure 資料庫移轉服務]****。

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，選取 [建立]****。

    ![建立 Azure 資料庫移轉服務執行個體](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. 在 [建立移轉服務]**** 畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的位置。 

5. 選擇現有虛擬網路或創建新虛擬網路。

    虛擬網路為 Azure 資料庫移轉服務提供對源 MongoDB 實例和目標 Azure Cosmos 資料庫帳戶的存取權限。

    有關如何在 Azure 門戶中創建虛擬網路的詳細資訊，請參閱[使用 Azure 門戶創建虛擬網路](https://aka.ms/DMSVnet)的文章。

6. 選取定價層。

    有關成本和定價層的詳細資訊，請參閱[定價頁](https://aka.ms/dms-pricing)。

    ![設定 Azure 資料庫移轉服務執行個體設定](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. 選取 [建立]**** 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]****，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]****。

      ![找出 Azure 資料庫移轉服務的所有執行個體](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. 在 [Azure 資料庫移轉服務]**** 畫面上，搜尋您建立的 Azure 資料庫移轉服務執行個體名稱，然後選取該執行個體。

3. 選取 [+ 新增移轉專案]****。

4. 在 [新增移轉專案]**** 畫面上指定專案名稱、在 [來源伺服器類型]**** 文字方塊中選取 [MongoDB]****、在 [目標伺服器類型]**** 文字方塊中選取 [CosmosDB (MongoDB API)]****，然後針對 [選擇活動類型]****，選取 [離線資料移轉]****。 

    ![建立資料庫移轉服務專案](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. 選取 [建立及執行活動]****，以建立專案並執行移轉活動。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [來源詳細資料]**** 畫面上，指定來源 MongoDB 伺服器的連線詳細資料。

   > [!IMPORTANT]
   > Azure 資料庫移轉服務不支援以 Azure Cosmos DB 作為來源。

    有三種模式可連線至來源：
   * **標準模式**，可接受完整網域名稱或 IP 位址、連接埠號碼和連線認證。
   * **連接字串模式**，可接受[連接字串 URI 格式](https://docs.mongodb.com/manual/reference/connection-string/)一文中說明的 MongoDB 連接字串。
   * **來自 Azure 儲存體的資料**，可接受 Blob 容器 SAS URL。 如果 Blob 容器含有 MongoDB [bsondump 工具](https://docs.mongodb.com/manual/reference/program/bsondump/)所產生的 BSON 傾印，請選取 [Blob 包含 BSON 傾印]****，如果容器包含 JSON 檔案，則將其取消選取。

     如果您選取此選項，請確定儲存體帳戶連接字串以下列格式顯示：

     ```
     https://blobnameurl/container?SASKEY
     ```

     在 Azure 儲存體總管中可以找到此 Blob 容器 SAS 連接字串。 建立相關容器的 SAS 將為您提供上述要求格式的 URL。
     
     此外，根據 Azure 存儲中的轉儲類型資訊，請記住以下詳細資訊。

     * 就 BSON 傾印而言，Blob 容器內的資料必須採用 bsondump 格式，使資料檔案以 collection.bson 的格式放入依所屬資料庫命名的資料夾中。 中繼資料檔案 (如果有的話) 則應使用 *collection*.metadata.json 的格式命名。

     * 就 JSON 傾印而言，Blob 容器中的檔案必須放入依所屬資料庫命名的資料夾中。 在每個資料庫資料夾中，資料檔案必須放在名為「資料」的子資料夾中，並使用 *collection*.json 的格式命名。 中繼資料檔案 (如果有的話) 必須放在名為「中繼資料」的子資料夾中，並使用相同的格式 *collection*.json 命名。 中繼資料檔案必須採用 MongoDB bsondump 工具所產生的相同格式。

    > [!IMPORTANT]
    > 不鼓勵在 mongo 伺服器上使用自我簽署憑證。 不過，若已使用，請使用**連接字串模式**連線到伺服器，並確保您的連接字串具有 “”
    >
    >```
    >&sslVerifyCertificate=false
    >```

   如果無法解析 DNS 名稱，您也可以使用 IP 位址。

   ![指定來源詳細資料](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. 選取 [儲存]****。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 [移轉目標詳細資料]**** 畫面上，對目標 Azure Cosmos DB 帳戶指定連線詳細資料，此帳戶就是要作為 MongoDB 資料遷移目的地的預先佈建 Azure Cosmos DB Mongo 版 API 帳戶。

    ![指定目標詳細資料](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. 選取 [儲存]****。

## <a name="map-to-target-databases"></a>對應到目標資料庫

1. 在 [Map to target databases] \(對應到目標資料庫\)**** 畫面上，對應要進行移轉的來源資料庫和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure 資料庫移轉服務依預設會選取目標資料庫。

    如果字串 **Create** 出現在資料庫名稱旁邊，則代表 Azure 資料庫移轉服務未找到目標資料庫，且服務會為您建立該資料庫。

    在進行到這個移轉階段時，您可以[佈建輸送量](https://docs.microsoft.com/azure/cosmos-db/set-throughput)。 在 Cosmos DB 中，您可以在資料庫層級或以個別進行的方式，為每個集合佈建輸送量。 輸送量會以[要求單位](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU) 來測量。 深入了解 [Azure Cosmos DB 定價](https://azure.microsoft.com/pricing/details/cosmos-db/)。

    ![對應到目標資料庫](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. 選取 [儲存]****。
3. 在 [集合設定]**** 畫面上，展開集合清單，然後檢閱要遷移的集合清單。

    Azure 資料庫移轉服務會自動選取所有存在於來源 MongoDB 執行個體上，卻不存在於目標 Azure Cosmos DB 帳戶上的集合。 如果您想要重新移轉已包含資料的集合，就必須在此刀鋒視窗上明確地選取集合。

    您可以指定您想要讓集合使用的 RU 數量。 Azure 資料庫移轉服務會根據集合大小來建議智慧的預設值。

    > [!NOTE]
    > 如有必要，可使用多個 Azure 資料庫移轉服務執行個體來平行執行資料庫移轉及集合，以加快執行速度。

    您也可以指定分區索引鍵以便利用 [Azure Cosmos DB 中的資料分割](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)，從而獲得最佳延展性。 請務必檢閱[選取分區/資料分割索引鍵的最佳做法](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。

    ![選取集合資料表](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. 選取 [儲存]****。

5. 在 [移轉摘要]**** 畫面上的 [活動名稱]**** 文字方塊中，指定移轉活動的名稱。

    ![移轉摘要](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]****。

    [移轉活動] 視窗隨即出現，而且活動的 [狀態]**** 為 [未啟動]****。

    ![活動狀態](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>監視移轉

* 在移轉活動畫面上，選取 [重新整理]**** 以更新顯示，直到移轉的**狀態**顯示為 [已完成]**** 為止。

   > [!NOTE]
   > 您可以選取活動來取得資料庫層級和集合層級移轉計量的詳細資料。

    ![活動狀態已完成](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>確認 Cosmos DB 中的資料

* 在移轉完成之後，您可以檢查 Azure Cosmos DB 帳戶，以確認所有集合都已成功移轉。

    ![活動狀態已完成](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>移轉後最佳化

將 MongoDB 資料庫中儲存的資料遷移至 Azure Cosmos DB 的 MongoDB 版 API 之後，您就可以連線到 Azure Cosmos DB 並管理這些資料。 您也可以執行其他的移轉後最佳化步驟，例如將索引編製原則最佳化、更新預設的一致性層級或設定 Azure Cosmos DB 帳戶的全域散發。 如需詳細資訊，請參閱[移轉後最佳化](../cosmos-db/mongodb-post-migration.md)一文。

## <a name="additional-resources"></a>其他資源

* [Cosmos DB 服務資訊](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/)中檢閱其他案例的移轉指導方針。
