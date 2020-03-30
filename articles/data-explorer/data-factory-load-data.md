---
title: 將資料從 Azure 資料工廠複製到 Azure 資料資源管理器
description: 在本文中，您將瞭解如何使用 Azure 資料工廠複製工具將資料引入 Azure 資料資源管理器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300591"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到 Azure 資料資源管理器 

Azure 資料資源管理器是一種快速、完全託管的資料分析服務。 它提供來自許多來源（如應用程式、網站和 IoT 設備）流的大量資料的即時分析。 使用 Azure 資料資源管理器，您可以反覆運算流覽資料並識別模式和異常，以改進產品、增強客戶體驗、監視設備並提升操作。 它可以説明您探索新問題，並在幾分鐘內獲得答案。 

Azure 資料工廠是一個完全託管的基於雲的資料整合服務。 可以使用它使用現有系統的資料填充 Azure 資料資源管理器資料庫。 在構建分析解決方案時，它可以説明您節省時間。

將資料載入到 Azure 資料資源管理器中時，資料工廠提供了以下好處：

* **簡單的設置**：獲得一個直觀的五步嚮導，無需編寫腳本。
* **豐富的資料存儲支援**：獲得對一組豐富的本地和基於雲的資料存儲的內置支援。 如需詳細清單，請參閱[支援的資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)的資料表。
* **安全和合規**：資料通過 HTTPS 或 Azure 快速路由傳輸。 具有全域服務，可確保資料絕不會離開地理界限。
* **高性能**：資料載入速度高達每秒 1 GB （GBps） 到 Azure 資料資源管理器。 有關詳細資訊，請參閱[複製活動性能](/azure/data-factory/copy-activity-performance)。

在本文中，您可以使用"資料工廠複製資料"工具將資料從 Amazon 簡單存儲服務 （S3） 載入到 Azure 資料資源管理器中。 您可以遵循類似的過程來從其他資料存儲複製資料，例如：
* [Azure Blob 儲存體](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL 資料倉儲](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [甲骨文](/azure/data-factory/connector-oracle)
* [檔案系統](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
* 資料來源。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 登錄到 Azure[門戶](https://ms.portal.azure.com)。

1. 在左側窗格中，選擇 **"創建資源** > **分析** > **資料工廠**"。

   ![在 Azure 門戶中創建資料工廠](media/data-factory-load-data/create-adf.png)

1. 在 **"新建資料工廠"** 窗格中，為下表中的欄位提供值：

   !["新資料工廠"窗格](media/data-factory-load-data/my-new-data-factory.png)  

   | 設定  | 要輸入的值  |
   |---|---|
   | **名稱** | 在框中，輸入資料工廠的全域唯一名稱。 如果收到錯誤，*則資料工廠名稱\"LoadADXDemo\"不可用*，請輸入資料工廠的其他名稱。 有關命名資料工廠專案的規則，請參閱[資料工廠命名規則](/azure/data-factory/naming-rules)。|
   | **訂閱** | 在下拉清單中，選擇要在其中創建資料工廠的 Azure 訂閱。 |
   | **資源組** | 選擇 **"創建新**"，然後輸入新資源組的名稱。 如果已有資源組，請選擇"**使用現有**"。 |
   | **版本** | 在下拉清單中，選擇**V2**。 |  
   | **位置** | 在下拉清單中，選擇資料工廠的位置。 清單中僅顯示受支援的位置。 資料工廠使用的資料存儲可以存在於其他位置或區域。 |

1. 選取 [建立]****。

1. 要監視創建過程，請在工具列上選擇 **"通知**"。 創建資料工廠後，選擇它。
   
   將打開 **"資料工廠**"窗格。

   !["資料工廠"窗格](media/data-factory-load-data/data-factory-home-page.png)

1. 要在單獨的窗格中打開應用程式，請選擇 **"作者&監視器"** 磁貼。

## <a name="load-data-into-azure-data-explorer"></a>將資料載入到 Azure 資料資源管理器中

您可以將多種類型的[資料存儲](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)中的資料載入到 Azure 資料資源管理器中。 本文討論如何從 Amazon S3 載入資料。

您可以通過以下任一方式載入資料：

* 在 Azure 資料工廠使用者介面（左窗格中）中，選擇 **"作者"** 圖示，如[使用 Azure 資料工廠 UI 創建資料工廠的](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)"創建資料工廠"部分所示。
* 在 Azure 資料工廠複製資料工具中，如["使用複製資料"工具複製資料](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

### <a name="copy-data-from-amazon-s3-source"></a>從亞馬遜 S3 複製資料（源）

1. 在"**讓我們開始"** 窗格中，通過選擇 **"複製資料**"打開"複製資料"工具。

   !["複製資料"工具按鈕](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在"**屬性"** 窗格中，在 **"任務名稱"** 框中，輸入名稱，然後選擇 **"下一步**"。

    !["複製資料屬性"窗格](media/data-factory-load-data/copy-from-source.png)

1. 在 **"來源資料存儲"** 窗格中，選擇 **"創建新連接**"。

    ![複製資料"來源資料存儲"窗格](media/data-factory-load-data/source-create-connection.png)

1. 選擇**亞馬遜 S3**，然後選擇 **"繼續**"。

    !["新建連結服務"窗格](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在新**連結服務 （Amazon S3）** 窗格中，執行以下操作：

    ![指定亞馬遜 S3 連結服務](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. 在 **"名稱"** 框中，輸入新連結服務的名稱。

    b. 在 **"通過集成連接運行時**下拉清單"中，選擇該值。

    c. 在 **"訪問金鑰識別碼"** 框中，輸入該值。
    
    > [!NOTE]
    > 在 Amazon S3 中，要查找您的訪問金鑰，請在巡覽列上選擇您的亞馬遜使用者名，然後選擇 **"我的安全憑據**"。
    
    d. 在 **"金鑰"** 框中，輸入值。

    e. 要測試您創建的連結服務連接，請選擇 **"測試連接**"。

    f. 選取 [完成]****。
    
      "**來源資料存儲"** 窗格顯示新的 AmazonS31 連接。 

1. 選取 [下一步]****。

   ![來源資料存儲創建連接](media/data-factory-load-data/source-data-store-created-connection.png)

1. **在"選擇輸入檔"或"資料夾**"窗格中，執行以下操作：

    a. 流覽到要複製的檔或資料夾，然後選擇它。

    b. 選擇所需的複製行為。 確保清除**二進位副本**核取方塊。

    c. 選取 [下一步]****。

    ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-choose-input-file.png)

1. 在"**檔案格式設置"** 窗格中，選擇檔的相關設置。 然後選擇 **"下一步**"。

   !["檔案格式設置"窗格](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>將資料複製到 Azure 資料資源管理器（目標）

創建新的 Azure 資料資源管理器連結服務是為了將資料複製到本節中指定的 Azure 資料資源管理器目標表（接收器）。

#### <a name="create-the-azure-data-explorer-linked-service"></a>創建 Azure 資料資源管理器連結服務

要創建 Azure 資料資源管理器連結服務，請執行以下操作;請執行以下操作。

1. 要使用現有資料存儲連接或指定新的資料存儲，請在 **"目標資料存儲"** 窗格中，選擇 **"創建新連接**"。

    ![目標資料存儲窗格](media/data-factory-load-data/destination-create-connection.png)

1. 在 **"新建連結服務"** 窗格中，選擇**Azure 資料資源管理器**，然後選擇"**繼續**"。

    !["新建連結服務"窗格](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在新**連結服務（Azure 資料資源管理器）** 窗格中，執行以下操作：

    ![Azure 資料資源管理器新連結服務窗格](media/data-factory-load-data/adx-new-linked-service.png)

   a. 在 **"名稱"** 框中，輸入 Azure 資料資源管理器連結服務的名稱。

   b. 在 **"帳戶選擇方法**"下，執行以下操作之一： 

    * **從 Azure 訂閱**中選擇，然後在下拉清單中選擇 Azure**訂閱**和**群集**。 

        > [!NOTE]
        > **群集**下拉控制項僅列出與您的訂閱關聯的群集。

    * 選擇**手動輸入**，然後輸入**終結點**。

   c. 在 **"租戶"** 框中，輸入租戶名稱。

   d. 在 **"服務主體 ID"** 框中，輸入服務主體 ID。

   e. 選擇**服務主體鍵**，然後在 **"服務主體鍵**"框中輸入鍵的值。

   f. 在 **"資料庫**"下拉清單中，選擇資料庫名稱。 或者，選擇 **"編輯"** 核取方塊，然後輸入資料庫名稱。

   g. 要測試您創建的連結服務連接，請選擇 **"測試連接**"。 如果可以連接到連結的服務，窗格將顯示綠色核取記號和**連接成功**消息。

   h. 選擇 **"完成"** 以完成連結的服務創建。

    > [!NOTE]
    > Azure 資料工廠使用服務主體訪問 Azure 資料資源管理器服務。 要創建服務主體，請轉到[創建 Azure 活動目錄 （Azure AD） 服務主體](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 不要使用 Azure 金鑰保存庫方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>配置 Azure 資料資源管理器資料連線

創建連結服務連接後，**將打開"目標資料存儲"** 窗格，並且您創建的連接可供使用。 要配置連接，請執行以下操作;請執行以下操作。

1. 選取 [下一步]****。

    ![Azure 資料資源管理器"目標資料存儲"窗格](media/data-factory-load-data/destination-data-store.png)

1. 在 **"表映射**"窗格中，設置目標表名稱，然後選擇 **"下一步**"。

    ![目標資料集"表映射"窗格](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在 **"列"映射**窗格中，將執行以下映射：

    a. 第一個映射由 Azure 資料工廠根據[Azure 資料工廠架構映射](/azure/data-factory/copy-activity-schema-and-type-mapping)執行。 執行下列動作：

    * 設置 Azure 資料工廠目標表的**列映射**。 預設映射從源顯示到 Azure 資料工廠目標表。

    * 取消不需要定義列映射的列的選擇。

    b. 當此表格資料引入 Azure 資料資源管理器時，將發生第二次映射。 映射根據[CSV 映射規則](/azure/kusto/management/mappings#csv-mapping)執行。 即使來源資料不是 CSV 格式，Azure 資料工廠也會將資料轉換為表格格式。 因此，CSV 映射是此階段唯一的相關映射。 執行下列動作：

    * （可選）在**Azure 資料資源管理器 （Kusto） 接收器屬性**下，添加相關的**引入映射名稱**，以便可以使用列映射。

    * 如果未指定**引入映射名稱**，將使用"**列映射**"部分中定義的*按名稱*映射順序。 如果*名*映射失敗，Azure 資料資源管理器將嘗試按*分列位置*順序引入資料（即，按位置映射為預設值）。

    * 選取 [下一步]****。

    ![目標資料集"列映射"窗格](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 **"設置"** 窗格中，執行以下操作：

    a. 在**容錯設置**下，輸入相關設置。

    b. 在 **"性能設置****"下，啟用暫存**不適用，**而"高級設置**"包括成本注意事項。 如果沒有特定要求，請保留這些設置。

    c. 選取 [下一步]****。

    ![複製資料"設置"窗格](media/data-factory-load-data/copy-data-settings.png)

1. 在 **"摘要"** 窗格中，查看設置，然後選擇 **"下一步**"。

    ![複製資料"摘要"窗格](media/data-factory-load-data/copy-data-summary.png)

1. 在 **"部署完成"** 窗格中，執行以下操作：

    a. 要切換到 **"監視器"** 選項卡並查看管道的狀態（即進度、錯誤和資料流程），請選擇 **"監視器**"。

    b. 要編輯連結的服務、資料集和管道，請選擇 **"編輯管道**"。

    c. 選擇 **"完成"** 以完成複製資料任務。

    !["部署完成"窗格](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>後續步驟

* 瞭解 Azure 資料工廠中的[Azure 資料資源管理器連接器](/azure/data-factory/connector-azure-data-explorer)。
* 詳細瞭解在[資料工廠 UI](/azure/data-factory/quickstart-create-data-factory-portal)中編輯連結的服務、資料集和管道。
* 瞭解[用於資料查詢的 Azure 資料資源管理器查詢](/azure/data-explorer/web-query-data)。
