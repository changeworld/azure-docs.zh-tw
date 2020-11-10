---
title: 快速入門：使用 Azure 入口網站建立專用 SQL 集區 (預覽)
description: 遵循本指南中的步驟，使用 Azure 入口網站建立新的專用 SQL 集區。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c9dcb2fa8be22c21cc2b005cce1bf8bb1c614381
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324275"
---
# <a name="quickstart-create-a-dedicated-sql-pool-preview-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立專用 SQL 集區 (預覽)

Azure Synapse Analytics 提供各種分析引擎，協助您內嵌、轉換、模型化及分析您的資料。 專用 SQL 集區提供以 T-SQL 為基礎的計算和儲存功能。 在 Synapse 工作區中建立專用 SQL 集區後，就可以載入、模型化、處理及提供資料，以提供更快速的分析見解。

在本快速入門中，您會了解如何使用 Azure 入口網站在 Synapse 工作區中建立專用 SQL 集區。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [Synapse 工作區](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>瀏覽至 Synapse 工作區

1. 在搜尋列中輸入服務名稱 (或直接輸入資源名稱)，瀏覽至要建立專用 SQL 集區的 Synapse 工作區。
![Azure 入口網站搜尋列中輸入了 Synapse 工作區](media/quickstart-create-sql-pool/create-sql-pool-00a.png)。 
1. 從工作區清單中，輸入要開啟之工作區的名稱 (或名稱的一部分)。 在此範例中，我們將使用名為 **contosoanalytics** 的工作區。
![已篩選成顯示包含 Contoso 名稱的 Synapse 工作區清單。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>建立新的專用 SQL 集區

1. 在您要建立專用 SQL 集區的 Synapse 工作區中，選取頂端功能列中的 [新增專用 SQL 集區] 命令。
![Synapse 工作區概觀，並在建立新專用 SQL 集區命令的周圍加上紅色方塊。](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. 在 [基本資料] 索引標籤中輸入下列詳細資料：

    | 設定 | 建議的值 | 描述 |
    | :------ | :-------------- | :---------- |
    | **專用 SQL 集區名稱** | 任何有效名稱 | 專用 SQL 集區名稱。 |
    | **效能等級** | DW100c | 設定為最小的大小，以降低本快速入門的成本 |

  
    ![專用 SQL 集區建立流程 - 基本索引標籤。](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > 請注意，專用 SQL 集區可以使用的名稱有特定限制。 名稱不能包含特殊字元、必須是 15 個或更少的字元、不能包含保留字，而且必須是工作區中唯一的名稱。

3. 完成時，選取 [下一步:其他設定]。
4. 選取 [無] 以佈建不含資料的專用 SQL 集區。 保留選取預設定序。
![專用 SQL 集區建立流程 - [其他設定] 索引標籤。](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. 選取 [檢閱 + 建立]。
6. 請根據先前輸入的內容確定詳細資料正確無誤。 選取 [建立]。
![專用 SQL 集區建立流程 - [檢閱設定] 索引標籤。](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. 此時，資源佈建流程將會啟動。
 ![顯示「您的部署已完成」頁面的螢幕擷取畫面。](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. 佈建完成之後，瀏覽回工作區時會顯示新建立的專用 SQL 集區項目。
 ![SQL 集區建立流程 - 資源佈建。](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)



建立專用 SQL 集區之後，就可以在工作區中用該集區來載入資料、處理資料流、從資料湖讀取等。

## <a name="clean-up-resources"></a>清除資源

請遵循下列步驟，從工作區中刪除專用 SQL 集區。
> [!WARNING]
> 從工作區刪除專用 SQL 集區會一併移除分析引擎及儲存在該專用 SQL 集區資料庫中的資料。 表示引擎無法再連線到專用 SQL 集區，且讀取或寫入到此專用 SQL 集區的所有筆記本將無法再使用。

如果您想要刪除專用 SQL 集區，請完成下列步驟：

1. 瀏覽至工作區刀鋒視窗中的 SQL 集區刀鋒視窗
1. 選取要刪除的專用 SQL 集區 (在此案例中為 **contosowdw** )
1. 選取之後，請按 [刪除]
1. 確認刪除，然後按 [刪除] 按鈕 ![專用 SQL 集區概觀 - 反白顯示刪除確認。](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. 當流程成功完成時，專用 SQL 集區就不會再列入工作區資源中。

## <a name="next-steps"></a>後續步驟

- 請參閱[快速入門：使用 Web 工具在 Synapse Studio 中建立無伺服器 Apache Spark 集區](quickstart-apache-spark-notebook.md)。
- 請參閱[快速入門：使用 Azure 入口網站建立無伺服器 Apache Spark 集區](quickstart-create-apache-spark-pool-portal.md)。
