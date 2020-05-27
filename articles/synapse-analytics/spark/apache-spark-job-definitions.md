---
title: 教學課程 - 適用於 Azure Synapse Analytics 的 Apache Spark：適用於 Synapse 的 Apache Spark 作業定義
description: 教學課程 - 使用 Azure Synapse Analytics 來建立 Spark 作業定義，並將其提交至適用於 Azure Synapse Analytics 的 Apache Spark 集區。
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587930"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>教學課程：使用 Azure Synapse Analytics 來建立適用於 Synapse Spark 集區的 Apache Spark 作業定義

本教學課程示範如何使用 Azure Synapse Analytics 來建立 Spark 作業定義，然後將其提交至 Synapse Spark 集區。 您可以利用數個方式來使用此外掛程式：

* 在 Synapse Spark 集區上開發和提交 Spark 作業定義。
* 在提交後檢視作業詳細資料。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 在 Synapse Spark 集區上開發和提交 Spark 作業定義。
> * 在提交後檢視作業詳細資料。

## <a name="prerequisites"></a>Prerequisites

* Azure Synapse Analytics 工作區。 如需指示，請參閱[建立 Azure Synapse Analytics 工作區](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。

## <a name="get-started"></a>開始使用

在提交 Spark 作業定義之前，您必須先針對想要使用的 ADLS Gen2 檔案系統成為其儲存體 Blob 資料擁有者。 如果您還不是，則必須手動新增權限。

### <a name="scenario-1-add-permission"></a>案例 1：新增權限

1. 開啟 [Microsoft Azure](https://ms.portal.azure.com)，然後開啟儲存體帳戶。

2. 按一下 [容器]，然後建立 [檔案系統]。 本教學課程使用 `sparkjob`。

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/open-azure-container.png)

    ![[提交 Spark] 對話方塊](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. 開啟 `sparkjob`，按一下 [存取控制 (IAM)]，然後按一下 [新增] 並選取 [新增角色指派]。

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. 按一下 [角色指派]、輸入使用者名稱，然後確認使用者角色。

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>案例 2：準備資料夾結構

在提交 Spark 作業定義之前，您必須先將檔案上傳到 ADLS Gen2 並在該處準備資料夾結構。 我們會使用 Synapse Studio 中的「儲存體」節點來儲存檔案。

1. 開啟 [Azure Synapse Analytics](https://web.azuresynapse.net/)。

2. 按一下 [資料]、選取 [儲存體帳戶]，然後將相關檔案上傳到您的 ADLS Gen2 檔案系統。 我們支援 Scala、Java、.NET 及 Python。 本教學課程會使用下圖中的範例作為示範，但您可以視需要變更專案結構。

    ![設定 Spark 作業定義的值](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>建立 Spark 作業定義

1. 開啟 [Azure Synapse Analytics](https://web.azuresynapse.net/)，然後選取 [開發]。

2. 從左窗格中選取 [Spark 作業定義]。

3. 按一下 [Spark 作業定義] 右邊的 [動作] 節點。

     ![建立新的 Spark 作業定義](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. 從 [動作] 下拉式清單中，選取 [新增 Spark 作業定義]

     ![建立新的 Spark 作業定義](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. 在 [新增 Spark 作業定義] 視窗中選取 [語言]，然後提供下列資訊：  

   * 將 [語言] 選取為 [Spark (Scala)]。

    |  屬性   | 描述   |  
    | ----- | ----- |  
    |作業定義名稱| 可為 Spark 作業定義輸入名稱。  本教學課程使用 `job definition sample`。 此名稱可隨時更新，直到作業定義發佈出去為止。|  
    |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 JAR 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 |
    |Main class name (主要類別名稱)| 主要定義檔中的完整識別碼或主要類別。|
    |命令列引數| 作業的選擇性引數。|
    |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
    |Spark 集區| 作業將會提交至選取的 Spark 集區。|
    |Spark 版本| Spark 集區正在執行的 Spark 版本。|
    |執行程式| 要在指定 Spark 集區中提供給作業使用的執行程式數目。|
    |執行程式大小| 所指定 Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|  
    |驅動程式大小| 所指定 Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

    ![設定 Spark 作業定義的值](./media/apache-spark-job-definitions/create-scala-definition.png)

   * 將 [語言] 選取為 [PySpark (Python)]。

    |  屬性   | 描述   |  
    | ----- | ----- |  
    |作業定義名稱| 可為 Spark 作業定義輸入名稱。  本教學課程使用 `job definition sample`。 此名稱可隨時更新，直到作業定義發佈出去為止。|  
    |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 PY 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
    |命令列引數| 作業的選擇性引數。|
    |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
    |Spark 集區| 作業將會提交至選取的 Spark 集區。|
    |Spark 版本| Spark 集區正在執行的 Spark 版本。|
    |執行程式| 要在指定 Spark 集區中提供給作業使用的執行程式數目。|
    |執行程式大小| 所指定 Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|  
    |驅動程式大小| 所指定 Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

    ![設定 Spark 作業定義的值](./media/apache-spark-job-definitions/create-py-definition.png)

   * 將 [語言] 選取為 [.NET Spark (C#/F#)]。

    |  屬性   | 描述   |  
    | ----- | ----- |  
    |作業定義名稱| 可為 Spark 作業定義輸入名稱。  本教學課程使用 `job definition sample`。 此名稱可隨時更新，直到作業定義發佈出去為止。|  
    |主要定義檔| 用於作業的主要檔案。 從儲存體中選取 .NET for Spark 應用程式 (亦即，主要的可執行檔、包含使用者定義函式的 DLL，以及其他必要檔案) 所在的 ZIP 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
    |主要可執行檔| 主要定義 ZIP 檔案中的主要可執行檔。|
    |命令列引數| 作業的選擇性引數。|
    |參考檔案| 背景工作節點為了執行主要定義 ZIP 檔案中未包含的 .NET for Spark 應用程式所需的其他檔案 (亦即，相依的 jar、其他的使用者定義函式 DLL，以及其他設定檔)。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
    |Spark 集區| 作業將會提交至選取的 Spark 集區。|
    |Spark 版本| Spark 集區正在執行的 Spark 版本。|
    |執行程式| 要在指定 Spark 集區中提供給作業使用的執行程式數目。|
    |執行程式大小| 所指定 Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|  
    |驅動程式大小| 所指定 Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

    ![設定 Spark 作業定義的值](./media/apache-spark-job-definitions/create-net-definition.png)

6. 選取 [發佈] 以儲存 Spark 作業定義。

    ![發佈 Spark 作業定義](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>提交 Spark 作業定義

在建立 Spark 作業定義之後，便可將其提交至 Synapse Spark 集區。 請先確定您已完成**開始使用**中的步驟，再嘗試這一部分中的範例。

### <a name="scenario-1-submit-spark-job-definition"></a>案例 1：提交 Spark 作業定義

1. 按一下 Spark 作業定義來開啟其視窗。

      ![開啟要提交的 Spark 作業定義 ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. 按一下 [提交] 圖示將您的專案提交至選取的 Spark 集區。 您可以按一下 [Spark 監視 URL] 索引標籤來查看 Spark 應用程式的 LogQuery。

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[提交 Spark] 對話方塊](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>案例 2：檢視 Spark 作業的執行進度

1. 按一下 [監視器]，然後選取 [Spark 應用程式] 選項。 您可以找到已提交的 Spark 應用程式。

    ![檢視 Spark 應用程式](./media/apache-spark-job-definitions/view-spark-application.png)

2. 然後按一下 Spark 應用程式，隨即會顯示 [LogQuery] 視窗。 您可以從 [LogQuery] 檢視作業的執行進度。

    ![檢視 Spark 應用程式 LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>案例 3：檢查輸出檔案

 1. 按一下 [資料]，然後選取 [儲存體帳戶]。 成功執行之後，便可以移至 ADLS Gen2 儲存體，然後檢查是否已產生輸出。

    ![檢視輸出檔案](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>後續步驟

本教學課程示範如何使用 Azure Synapse Analytics 來建立 Spark 作業定義，然後將其提交至 Synapse Spark 集區。 接下來，您可以使用 Azure Synapse Analytics 來建立 Power BI 資料集和管理 Power BI 資料。 

- [連線至 Power BI Desktop 中的資料](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [使用 Power BI 視覺化資料](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
