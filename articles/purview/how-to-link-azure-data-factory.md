---
title: 連接到 Azure Data Factory
description: 本文說明如何連接 Azure Data Factory 和 Azure 範疇來追蹤資料歷程。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 01af7b251c9ce3bfebb87016c85ea3efd9c0e8ac
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928761"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>如何連接 Azure Data Factory 和 Azure 範疇

本檔說明使用 Azure 範疇帳戶連接 Azure Data Factory 帳戶以追蹤資料歷程所需的步驟。 檔也會在涵蓋範圍範圍和支援的歷程模式中取得詳細資料。

## <a name="view-existing-data-factory-connections"></a>查看現有的 Data Factory 連接

多個 Azure Data factory 可以連線至單一 Azure 範疇資料目錄，以推送歷程資訊。 目前的限制可讓您一次從範疇管理中心連接10個 Data Factory 帳戶。 若要顯示連線到範疇資料目錄 Data Factory 帳戶的清單，請執行下列動作：

1. 選取左側流覽窗格上的 [ **管理中心** ]。
2. 在 [ **外部連接**] 底下，選取 [ **Data Factory 連接**]。
3. Data Factory 連接清單隨即出現。

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="顯示 data factory 連接清單的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 請注意連接 **狀態** 的各種值：

    - **已連線**： data factory 已連線到資料目錄。
    - 已 **中斷** 連線：資料處理站具有目錄的存取權，但已連線到另一個目錄。 如此一來，資料歷程將不會自動回報給目錄。
    - **CannotAccess**：目前的使用者沒有 data factory 的存取權，因此連接狀態為 unknown。
 >[!Note]
 >若要查看 Data Factory 連接，您必須將任何一個範疇角色指派給您：
 >- 參與者
 >- 擁有者
 >- 讀者
 >- 使用者存取系統管理員

## <a name="create-new-data-factory-connection"></a>建立新的 Data Factory 連接

>[!Note]
>若要新增或移除 Data Factory 連接，您必須被指派任何一個範疇角色：
>- 擁有者
>- 使用者存取系統管理員
>
> 此外，它還會要求使用者必須是 data factory 的「擁有者」或「參與者」。 

請遵循下列步驟，將現有的 Data Factory 帳戶連接到您的範疇資料目錄。

1. 選取左側流覽窗格上的 [ **管理中心** ]。
2. 在 [ **外部連接**] 底下，選取 [ **Data Factory 連接**]。
3. 在 [ **Data Factory 連接** ] 頁面上，選取 [ **新增**]。

4. 從清單中選取您的 Data Factory 帳戶，然後選取 **[確定]**。 您也可以依訂用帳戶名稱篩選以限制清單。

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="顯示如何連接 Azure Data Factory 的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    如果資料處理站已連線到目前的範疇帳戶，或者資料處理站沒有受控識別，則可能會停用某些 Data Factory 實例。

    如果有任何選取的資料處理站已連線到其他範疇帳戶，將會顯示警告訊息。 選取 [確定] 之後，與其他範疇帳戶的 Data Factory 連接將會中斷連線。 不需要其他確認。


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="顯示中斷 Azure Data Factory 之警告的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>我們現在支援同時新增10個以上的資料處理站。 如果您想要一次新增10個以上的資料處理站，請提出支援票證。


### <a name="remove-data-factory-connections"></a>移除 data factory 連接
若要移除 data factory 連接，請執行下列動作：

1. 在 [ **Data Factory 連接** ] 頁面上，選取一或多個 Data Factory 連接旁邊的 [ **移除** ] 按鈕。
1. 選取快顯視窗中的 [ **確認** ] 以刪除選取的資料處理站連接。

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="顯示如何選取 data factory 以移除連接的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>設定自我裝載 IR 來收集內部內部部署 SQL 的歷程

Data Factory 複製活動的歷程適用于內部部署 SQL 資料庫。 如果您正在執行自我裝載的整合執行時間，以 Azure Data Factory 的資料移動，而且想要在 Azure 範疇中取得歷程記錄，請確定版本是4.8.7418.1 或更新版本。 如需自我裝載整合執行時間的詳細資訊，請參閱 [建立和設定自我裝載整合運行](../data-factory/create-self-hosted-integration-runtime.md)時間。

## <a name="supported-azure-data-factory-activities"></a>支援的 Azure Data Factory 活動

Azure 範疇會從下列 Azure Data Factory 活動中捕捉執行時間歷程：

- 複製資料
- 資料流程
- 執行 SSIS 套件

> [!IMPORTANT]
> 如果來源或目的地使用不支援的資料儲存系統，Azure 範疇就會卸載歷程。

Data Factory 與範疇之間的整合僅支援 Data Factory 支援的資料系統子集，如下列各節所述。

### <a name="data-factory-copy-data-support"></a>Data Factory 資料複製支援

| 資料儲存系統 | 支援作為來源 | 支援作為接收 |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 (沒有 JSON 支援)  | 是 | 是 (非二進位複製)  |
| ADLS Gen2 (沒有 JSON 支援)  | 是 | 是 |
| Azure Blob (沒有 JSON 支援)  | 是 | 是 |
| Azure Cosmos DB (SQL API) | 是 | 是 |
| Azure Cosmos DB (Mongo API)  | 是 | 是 |
| Azure 認知搜尋 | 是 | 是 |
| Azure 資料總管 | 是 | 是 |
| 適用于 Maria 資料庫的 Azure 資料庫 \* | 是 | 是 |
| 適用于 MYSQL 的 Azure 資料庫 \* | 是 | 是 |
| 適用於 PostgreSQL 的 Azure 資料庫 \* | 是 | 是 |
| Azure 檔案儲存體 | 是 | 是 |
| Azure 表格儲存體 | 是 | 是 |
| Azure SQL Database \* | 是 | 是 |
| Azure SQL MI \* | 是 | 是 |
| Azure Synapse Analytics (先前為 SQL DW) \* | 是 | 是 |
| SQL Server 內部內部部署 (SHIR 需要) \* | 是 | 是 |
| Amazon S3 | 是 | 是 |
| Teradata | 是 | 是 |
| SAP s4 Hana | 是 | 是 |
| SAP ECC | 是 | 是 |
| Hive | 是 | 是 |

> [!Note]
> 歷程功能在 Data Factory 複製活動中有某些效能負擔。 針對在範疇中設定資料處理站連線的使用者，您可能會發現某些複製作業需要較長的時間才能完成。 大部分的影響都是無可忽略的。 如果複製作業所花費的時間比平常長很多，請與支援人員聯繫。

### <a name="data-factory-data-flow-support"></a>Data Factory 資料流程支援

| 資料儲存系統 | 支援 |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | 是 |
| ADLS Gen2 | 是 |
| Azure Blob | 是 |
| Azure SQL Database \* | 是 |
| Azure Synapse Analytics (先前為 SQL DW) \* | 是 |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory 執行 SSIS 套件支援

| 資料儲存系統 | 支援 |
| ------------------- | ------------------- | ----------------- |
| Azure Blob | 是 |
| ADLS Gen1 | 是 |
| ADLS Gen2 | 是 |
| Azure SQL Database \* | 是 |
| Azure SQL MI \*| 是 |
| Azure Synapse Analytics (先前為 SQL DW) \* | 是 |
| SQL Server 內部內部部署 \* | 是 |
| Azure 檔案儲存體 | 是 |

*\* 針對 SQL (Azure 和內部部署) 案例，Azure 範疇不支援用於歷程或掃描的預存程式或腳本。歷程僅限於資料表和 view 來源。*

> [!Note]
> Azure Data Lake Storage Gen2 現已全面上市。 我們建議您現在就開始使用。 如需詳細資訊，請參閱[產品頁面](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)。

## <a name="supported-lineage-patterns"></a>支援的歷程模式

Azure 範疇支援幾種歷程模式。 產生的歷程資料是以 Data Factory 活動中使用的來源和接收類型為基礎。 雖然 Data Factory 支援超過80個來源和接收器，但 Azure 範疇僅支援一部分，如 [支援的 Azure Data Factory 活動](#supported-azure-data-factory-activities)所列。

若要設定 Data Factory 傳送歷程資訊，請參閱 [開始使用](catalog-lineage-user-guide.md#get-started-with-lineage)歷程。

在歷程視圖中尋找資訊的一些其他方法，包括下列各項：

- 在 [ **歷程] 索引** 標籤中，將滑鼠停留在圖形上，以預覽工具提示中資產的其他相關資訊。
- 選取節點或邊緣以查看其所屬的資產類型或切換資產。
- 資料集的資料行會顯示在 [ **歷程] 索引** 標籤的左邊。如需資料行層級歷程的詳細資訊，請參閱資料 [行層](catalog-lineage-user-guide.md#column-level-lineage)級歷程。

### <a name="data-lineage-for-11-operations"></a>1:1 作業的資料歷程

最常見的資料歷程模式是將資料從單一輸入資料集移至單一輸出資料集，並在其間進行處理。

此模式的範例如下所示：

- 1來源/輸入： *客戶* (SQL 資料表) 
- 1接收/輸出： *Customer1.csv* (Azure Blob) 
- 1進程： *CopyCustomerInfo1 \#Customer1.csv* (Data Factory 複製活動) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="顯示一到一個 Data Factory 複製作業歷程的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>具有1:1 歷程和萬用字元支援的資料移動

捕捉歷程的另一個常見案例是使用萬用字元將檔案從單一輸入資料集複製到單一輸出資料集。 萬用字元可讓複製活動比對多個檔案，以便使用檔案名的一般部分進行複製。 Azure 範疇會針對對應複製活動所複製的每個個別檔案，捕捉檔案層級歷程。

此模式的範例如下所示：

* 來源/輸入： *CustomerCall \* .csv* (ADLS Gen2 路徑) 
* 接收/輸出： *CustomerCall \** (Azure blob 檔案) 
* 1進程： *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory 複製活動)   

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="螢幕擷取畫面：顯示一次與萬用字元支援的複製作業歷程。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>以 n：1歷程資料移動

您可以使用資料流程活動來執行合併、聯結等等等資料作業。 您可以使用一個以上的源資料集來產生目標資料集。 在此範例中，Azure 範疇會針對屬於資料流程活動一部分的 SQL 資料表，捕獲個別輸入檔案的檔案層級歷程。

此模式的範例如下所示：

* 2個來源/輸入： *Customer.csv*， *Parquet* (ADLS Gen2 路徑) 
* 1接收/輸出： *公司資料* (Azure SQL 資料表) 
* 1進程： *DataFlowBlobsToSQL* (Data Factory 的資料流程活動) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="顯示 n 對一 A D F 資料流程作業歷程的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>資源集的歷程

資源集是目錄中的邏輯物件，代表基礎儲存體中的許多磁碟分割檔案。 如需詳細資訊，請參閱 [瞭解資源集](concept-resource-sets.md)。 當 Azure 範疇從 Azure Data Factory 中捕捉歷程時，它會套用規則來正規化個別的資料分割檔案，並建立單一邏輯物件。

下列範例會從 Azure Blob 產生 Azure Data Lake Gen2 資源集：

* 1來源/輸入： Azure Blob) 的 *員工 \_management.csv* (
* 1接收/輸出： *員工 \_management.csv* (Azure Data Lake Gen 2) 
* 1進程： *CopyBlobToAdlsGen2 \_ RS* (Data Factory 複製活動) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="顯示資源集歷程的螢幕擷取畫面。" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>後續步驟

- [目錄歷程使用者指南](catalog-lineage-user-guide.md)
- [連結至 Azure Data Share 進行歷程](how-to-link-azure-data-share.md)