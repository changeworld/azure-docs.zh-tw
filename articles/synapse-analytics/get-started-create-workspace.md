---
title: 快速入門：開始 - 建立 Synapse 工作區
description: 在本教學課程中，您將了解如何建立 Synapse 工作區、專用 SQL 集區和無伺服器 Apache Spark 集區。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: f7b96bcebb2106e52c62426ca2b64f9305e09141
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515403"
---
# <a name="creating-a-synapse-workspace"></a>建立 Synapse 工作區

在本教學課程中，您將了解如何建立 Synapse 工作區、專用 SQL 集區和無伺服器 Apache Spark 集區。 

## <a name="prerequisites"></a>必要條件

若要完成本教學課程的步驟，您必須能夠存取您已被指派其 **擁有者** 角色的資源群組。 在此資源群組中建立 Synapse 工作區。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>在 Azure 入口網站中建立 Synapse 工作區

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 **Synapse**。
1. 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics (工作區預覽)]。
1. 選取 [新增] 以建立工作區。
1. 在 [基本] 中，輸入您偏好的 **訂用帳戶**、**資源群組** 和 **區域**，然後選擇工作區名稱。 在本教學課程中，我們將使用 **myworkspace**。
1. 瀏覽至 [選取 Data Lake Storage Gen 2]。 
1. 按一下 [新建]，並將其命名為 **contosolake**。
1. 按一下 [檔案系統]，並將其命名為 **users**。 這會建立名為 **users** 的容器
1. 工作區會使用此儲存體帳戶作為 Spark 資料表和 Spark 應用程式記錄的「主要」儲存體帳戶。
1. 選取 [檢閱+建立] > [建立]。 您的工作區將會在幾分鐘內就緒。

## <a name="open-synapse-studio"></a>開啟 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://portal.azure.com)中開啟 Synapse 工作區。 在 [概觀] 區段的頂端，選取 [啟動 Synapse Studio]。
* 移至 `https://web.azuresynapse.net` 並登入您的工作區。

## <a name="create-a-dedicated-sql-pool"></a>建立專用 SQL 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [SQL 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**SQL 集區名稱**| **SQLDB1**|
    |**效能等級**|**DW100C**|
    |||

1. 選取 [檢閱+建立] > [建立]。 您的專用 SQL 集區將會在幾分鐘內就緒。 您的專用 SQL 集區會與專用 SQL 集區資料庫 (也稱為 **SQLDB1**) 相關聯。

專用 SQL 集區只要在作用中，就會取用計費的資源。 您可於稍後暫停集區，以降低成本。

## <a name="create-a-serverless-apache-spark-pool"></a>建立無伺服器 Apache Spark 集區

1. 在 Synapse Studio 的左側窗格中，選取 [管理] > [Apache Spark 集區]。
1. 選取 [新增]，然後輸入下列設定：

    |設定 | 建議的值 | 
    |---|---|---|
    |**Apache Spark 集區名稱**|**Spark1**
    |**節點大小**| **小型**|
    |**節點數目**| 將最小值設定為 3，以及最大值設定為 3|

1. 選取 [檢閱+建立] > [建立]。 您的 Apache Spark 集區會在幾秒內準備就緒。

當您在 Azure Synapse 中執行 Spark 活動時，需指定要使用的 Spark 集區。 集區會通知 Azure Synapse 要使用多少 Spark 資源。 您只需就您使用的資源付費。 當您主動停止使用集區時，資源將會自動逾時並予以回收。

> [!NOTE]
> 會從 Spark 集區獨立建立 Spark 資料庫。 工作區一律會有一個名為 **default** 的 Spark 資料庫。 您可以建立其他 Spark 資料庫。

## <a name="the-serverless-sql-pool"></a>無伺服器 SQL 集區

每個工作區都有一個預先建立的集區，稱為 **內建**。 您無法刪除此集區。 無伺服器 SQL 集區可讓您使用 SQL，而不需要在 Azure Synapse 中建立或考慮管理無伺服器 SQL 集區。 不同於專用 SQL 集區，無伺服器 SQL 集區計費是根據掃描以執行查詢的資料量，而不是用於執行查詢的資源數目。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用專用 SQL 集區進行分析](get-started-analyze-sql-pool.md)
