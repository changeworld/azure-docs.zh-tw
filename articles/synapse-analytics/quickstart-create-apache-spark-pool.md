---
title: 建立 Apache Spark 集區快速入門 (預覽)
description: 遵循本指南中的步驟，為 Azure Synapse Analytics 工作區建立新的 Apache Spark 集區。
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420622"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>快速入門：建立新的 Apache Spark 集區 (預覽)

Synapse Analytics 提供各種分析引擎，協助您內嵌、轉換、塑造、分析和服務您的資料。 Apache Spark 集區提供開放原始碼的巨量資料 計算功能。 在 Synapse 工作區中建立 Apache Spark 集區後，就可以載入、模型化、處理和提供資料，以取得深入解析。

在本快速入門中，您會了解如何使用 Azure 入口網站在 Synapse 工作區中建立 Apache Spark 集區。

> [!IMPORTANT]
> 無論使用與否，Spark 執行個體都是按分鐘計費。 當您使用完 Spark 執行個體之後，請務必將其關閉，或設定短暫的逾時時間。 如需詳細資訊，請參閱本文的**清除資源**一節。

如果您沒有 Azure 訂用帳戶，請[在開始前建立免費帳戶](https:/azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https:/azure.microsoft.com/free/)
- [Synapse Analytics 工作區](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>建立新的 Apache Spark 集區

1. 在您要建立 Apache Spark 集區的 Synapse 工作區中，按一下 [新增 Apache Spark 集區]  。
![Synapse 工作區的概觀，並在命令周圍加上紅色方塊，以建立新的 Apache Spark 集區](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. 在 [基本]  索引標籤中輸入下列詳細資料：

    |設定 | 建議的值 | 描述 |
    | :------ | :-------------- | :---------- |
    | **Apache Spark 集區名稱** | 有效的集區名稱 | 這是 Apache Spark 集區將擁有的名稱。 |
    | **節點大小** | 小 (4 個虛擬 CPU/32 GB) | 將此設為最小的大小，以降低本快速入門的成本 |
    | **Autoscale** | 啟用 | 保留預設設定 |
    | **節點數目** | 3 - 40 | 保留預設設定 |
    ||||

    ![Apache Spark 集區建立流程 - 基本索引標籤。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > 請注意，Apache Spark 集區可以使用的名稱有特定限制。 名稱只可包含字母或數字，字元數必須為 15 或更少，且必須以字母開頭，不能包含保留字組，而且名稱在工作區中必須是唯一的。

3. 按一下 [下一步: 其他設定]  並檢閱預設設定。 請勿修改任何預設設定。
![Apache Spark 集區建立流程 - [其他設定] 索引標籤。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. 按一下 [下一步: 標籤]  。 請勿新增任何標籤。
![Apache Spark 集區建立流程 - [其他設定] 索引標籤。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. 按一下 [檢閱 + 建立]  。

6. 請根據先前輸入的內容確定詳細資料正確無誤，然後按一下 [建立]  。
![Apache Spark 集區建立流程 - [檢閱設定] 索引標籤。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. 此時，資源佈建流程將會啟動，表示完成了 ![Apache Spark 集區建立流程 - 資源佈布建。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. 佈建完成之後，瀏覽回工作區時會顯示新建立的 Apache Spark 集區項目。
 ![Apache Spark 集區建立流程 - 資源佈建。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. 此時，沒有正在執行中的資源，Spark 不會產生費用，您已建立想建立的 Spark 執行個體中繼資料。

## <a name="clean-up-resources"></a>清除資源

請遵循下列步驟，從工作區中刪除 Apache Spark 集區。
> [!WARNING]
> 刪除 Apache Spark 集區將會從工作區移除分析引擎， 表示引擎無法再連線到集區，且使用此 Apache Spark 集區的所有查詢、管線和筆記本將無法再使用。

若確定要刪除 Apache Spark 集區，請執行下列動作：

1. 瀏覽至工作區中的 Apache Spark 集區刀鋒視窗。
2. 選取要刪除的 Apache 集區 (在此案例中為 **contosospark**)
3. 按下 [刪除]  。
 ![已選取最近建立之集區的 Apache Spark 集區清單。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. 確認刪除，然後按 [刪除]  按鈕。
 ![刪除選取的 Apache Spark 集區之確認對話方塊。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. 當流程成功完成時，Apache Spark 集區就不會再列入工作區資源中。

建立 SQL 集區之後，就可以在工作區中用來載入資料、處理資料流、從資料湖讀取等。

## <a name="next-steps"></a>後續步驟

- 請參閱[快速入門：使用 Web 工具在 Synapse Studio 中建立 Apache Spark 集區](spark/apache-spark-notebook-create-spark-use-sql.md)。
- 請參閱[快速入門：使用 Azure 入口網站來建立 Synapse SQL 集區](quickstart-create-sql-pool.md)。
