---
title: 快速入門：將 Azure 資料總管連線至 Synapse 工作區
description: 如何使用 Azure Synapse Apache Spark 將 Azure 資料總管叢集連線至 Synapse 工作區
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 53092f5c49073098f28d2fd06f38391e858b2b9d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946908"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>使用 Synapse Apache Spark 連線至 Azure 資料總管

本文說明如何使用 Synapse Apache Spark 從 Synapse Studio 存取 Azure 資料總管資料庫。 

## <a name="prerequisites"></a>必要條件

* [建立 Azure 資料總管叢集與資料庫](/azure/data-explorer/create-cluster-database-portal)。
* 現有的 Synapse 工作區，或依照此[快速入門](./quickstart-create-workspace.md)的指示建立新的工作區 
* 現有的 Synapse Apache Spark 工作區，或依照此[快速入門](./quickstart-create-apache-spark-pool-portal.md)的指示建立新的集區
* [藉由佈建 AzureAD 應用程式來建立 Azure AD 應用程式。](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* 依照[管理 Azure 資料總管資料庫權限](/azure/data-explorer/manage-database-permissions)的指示，授與您的 Azure AD 應用程式存取您資料庫的權限

## <a name="navigate-to-synapse-studio"></a>瀏覽至 Synapse Studio

從 Synapse 工作區，選取 [啟動 Synapse Studio]。 在 Synapse Studio 首頁上選取 [資料]，這會使您進入 [資料物件總管]。

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>將 Azure 資料總管資料庫連線至 Synapse 工作區

將 Azure 資料總管資料庫連線到工作區是透過連結服務來完成。 Azure 資料總管連結服務可讓使用者瀏覽及探索資料、從適用於 Azure Synapse Analytics 的 Apache Spark 讀取和或寫入資料，以及在管線中執行整合作業。

在資料物件總管中，遵循下列步驟來直接連線 Azure 資料總管叢集：

1. 選取 [資料] 附近的 **+** 圖示
2. 選取 [連線至外部資料]
3. 選取 [Azure 資料總管 (Kusto)]
5. 選取 [繼續]
6. 為連結服務命名。 此名稱會顯示在物件總管中，且供 Synapse 執行階段用來連線至資料庫。 建議使用易記名稱
7. 從您的訂用帳戶選取 Azure 資料探索叢集，或輸入 URI。
8. 輸入「服務主體識別碼」和「服務主體金鑰」(確保此服務主體具有資料庫的檢視權限以進行讀取作業，以及具有擷取器存取權以便擷取資料)
9. 輸入 Azure 資料總管資料庫名稱
10. 按一下 [測試連線] 以確保您擁有正確的權限
11. 選取 [建立] 

    ![新的連結服務](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (選擇性) 測試連線不會驗證寫入權限，請確保您的服務主體識別碼具有 Azure 資料總管資料庫的寫入權限。

12. Azure 資料總管叢集和資料庫會顯示在 [Azure 資料總管] 區段中 [已連結] 索引標籤底下。 

    ![瀏覽叢集](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > 在目前版本中，系統會根據您對 Azure 資料總管資料庫的 AAD 帳戶權限來填入資料庫物件。 當您執行 Apache Spark 筆記本或整合作業時，將會使用連結服務中的認證(亦即服務主體)。


## <a name="quickly-interact-with-code-generated-actions"></a>與程式碼產生的動作快速互動

* 當您以滑鼠右鍵按一下資料庫或資料表時，您會有一份手勢清單可會觸發範例 Spark 筆記本，以供讀取資料、寫入資料、將資料串流至 Azure 資料總管。 
    [![新的範例筆記本](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* 以下是讀取資料的範例。 已將筆記本連結到您的 Spark 集區並執行資料格 [![新的讀取筆記本](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE] 
   > 第一次執行可能需要 3 分鐘以上的時間來起始 Spark 工作階段。 後續執行速度將會大幅加快。  


## <a name="limitations"></a>限制
Azure Synapse 受控 VNET 目前不支援 Azure 資料總管連接器。


## <a name="next-steps"></a>後續步驟

* [使用進階選項的範例程式碼](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure 資料總管 (Kusto) Spark 連接器](https://github.com/Azure/azure-kusto-spark)