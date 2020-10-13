---
title: 將串流分析的資料串流至 Data Lake Storage Gen1-Azure
description: 瞭解如何使用 Azure Data Lake Storage Gen1 作為 Azure 串流分析作業的輸出，以及從 Azure 儲存體 blob 讀取資料的簡單案例。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576211"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>使用 Azure 串流分析將來自 Azure 儲存體 Blob 的資料串流處理至 Azure Data Lake Storage Gen1
在本文中，您將瞭解如何使用 Azure Data Lake Storage Gen1 作為 Azure 串流分析作業的輸出。 此文章示範從 Azure 儲存體 Blob (輸入) 讀取資料以及將資料寫入至 Data Lake Storage Gen1 (輸出) 的簡單案例。

## <a name="prerequisites"></a>Prerequisites
開始進行本教學課程之前，您必須具備下列條件：

* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure 儲存體帳戶**。 您將使用來自此帳戶的 Blob 容器來輸入串流分析作業的資料。 本教學課程中，假設您有名為 **storageforasa** 的儲存體帳戶，並在該帳戶中建立名為 **storageforasacontainer** 的容器。 一旦您已建立容器，請將範例資料檔案上傳給容器。 
  
* **Data Lake Storage Gen1 帳戶**。 請遵循[透過 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 的指示。 假設您有名為 **myadlsg1** 的 Data Lake Storage Gen1 帳戶。 

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業
您可以從建立包含輸入來源和輸出目的地的串流分析作業開始。 針對此教學課程，來源是 Azure Blob 容器，而目的地則是 Data Lake Storage Gen1。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從左窗格中，按一下 [串流分析作業]****，然後按一下 [新增]****。

    ![建立串流分析作業](./media/data-lake-store-stream-analytics/create.job.png "建立串流分析作業")

    > [!NOTE]
    > 請確定您所建立的作業位於和儲存體帳戶相同的區域，否則在區域之間移動資料，您會需要支付額外費用。
    >

## <a name="create-a-blob-input-for-the-job"></a>建立作業的 Blob 輸入

1. 開啟「串流分析作業」頁面，在左窗格中按一下 [輸入]**** 索引標籤，然後按一下 [新增]****。

    ![[串流分析作業] 分頁的螢幕擷取畫面，其中包含 [輸入] 選項以及已呼叫的 [新增資料流程輸入] 選項。](./media/data-lake-store-stream-analytics/create.input.1.png "將輸入新增至您的作業")

2. 在 [新增輸入]**** 刀鋒視窗中，提供下列值。

    ![Blob 儲存體-新輸入分頁的螢幕擷取畫面。](./media/data-lake-store-stream-analytics/create.input.2.png "將輸入新增至您的作業")

   * 在 [輸入別名]**** 中輸入作業輸入的唯一名稱。
   * 在 [來源類型]**** 中，選取 [資料流]****。
   * 在 [來源]**** 中，選取 [Blob 儲存體]****。
   * 在 [訂用帳戶]**** 中，選取 [使用來自目前訂用帳戶的 Blob 儲存體]****。
   * 在 [儲存體帳戶]**** 中，選取您在必要條件中所建立的儲存體帳戶。 
   * 在 [容器]**** 中，選取您在選取的儲存體帳戶中建立的容器。
   * 在 [事件序列化格式]**** 中，選取 [CSV]****。
   * 在 [分隔符號]**** 中，選取 [定位鍵]****。
   * 在 [編碼]**** 中，選取 [UTF-8]****。

     按一下 [建立]。 入口網站現在會新增輸入並測試其連線。


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>建立作業的 Data Lake Storage Gen1 輸出

1. 開啟 [串流分析工作] 頁面，按一下 [輸出]**** 索引標籤，再按一下 [新增]****，然後選取 [Data Lake Storage Gen1]****。

    ![[串流分析作業] 分頁的螢幕擷取畫面，其中包含 [輸出] 選項、[新增] 選項，以及稱為「Data Lake Storage Gen 1」選項。](./media/data-lake-store-stream-analytics/create.output.1.png "將輸出新增至您的作業")

2. 在 [新增輸出]**** 刀鋒視窗中，提供下列值。

    ![Data Lake Storage Gen 1-新輸出分頁的螢幕擷取畫面，其中已呼叫 [授權] 選項。](./media/data-lake-store-stream-analytics/create.output.2.png "將輸出新增至您的作業")

    * 在 [輸出別名]**** 中輸入作業輸出的唯一名稱。 這是易記名稱，用於在查詢中將查詢輸出指向這個 Data Lake Storage Gen1 帳戶。
    * 系統會提示您授權 Data Lake Storage Gen1 帳戶的存取權。 按一下 [授權]****。

3. 在 [新增輸出]**** 刀鋒視窗中，繼續來提供下列值。

    ![Data Lake Storage Gen 1-新輸出分頁的螢幕擷取畫面。](./media/data-lake-store-stream-analytics/create.output.3.png "將輸出新增至您的作業")

   * 在 [帳戶名稱]**** 中，選取您已建立並且要在其中傳入作業輸出的 Data Lake Storage Gen1 帳戶。
   * 在 [路徑前置詞模式]**** 中，輸入用來在指定的 Data Lake Storage Gen1 帳戶中寫入檔案的檔案路徑。
   * 在 [日期格式]**** 中，如果您在前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。
   * 在 [時間格式]**** 中，如果您在前置詞路徑中使用時間權杖，請指定組織檔案時要使用的時間格式。
   * 在 [事件序列化格式]**** 中，選取 [CSV]****。
   * 在 [分隔符號]**** 中，選取 [定位鍵]****。
   * 在 [編碼]**** 中，選取 [UTF-8]****。
    
     按一下 [建立]。 入口網站現在會新增輸出並測試其連線。
    
## <a name="run-the-stream-analytics-job"></a>執行串流分析作業

1. 若要執行串流分析作業，您必須從 [ **查詢** ] 索引標籤執行查詢。在本教學課程中，您可以藉由將預留位置取代為作業輸入和輸出別名來執行範例查詢，如下列螢幕擷取畫面所示。

    ![執行查詢](./media/data-lake-store-stream-analytics/run.query.png "執行查詢")

2. 在畫面頂端按一下 [儲存]****，然後在 [概觀]**** 索引標籤上按一下 [啟動]****。 在對話方塊中，選取 [自訂時間]****，然後設定目前的日期和時間。

    ![設定作業時間](./media/data-lake-store-stream-analytics/run.query.2.png "設定作業時間")

    按一下 [啟動]**** 以啟動作業。 啟動作業會需要費時幾分鐘。

3. 若要觸發作業以從 Blob 挑選資料，請將範例資料檔案複製到 Blob 容器。 您可以從 [Azure Data Lake Git 儲存機制](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)取得範例資料檔案。 在本教學課程中，我們要複製檔案 **vehicle1_09142014.csv**。 您可以使用各種用戶端，例如： [Azure 儲存體總管](https://storageexplorer.com/)，將資料上傳至 Blob 容器。

4. 在 [概觀]**** 索引標籤的 [監視]**** 底下，查看資料的處理情形。

    ![監視作業](./media/data-lake-store-stream-analytics/run.query.3.png "監視作業")

5. 最後，您可以確認作業輸出資料已可在 Data Lake Storage Gen1 帳戶中使用。 

    ![驗證輸出](./media/data-lake-store-stream-analytics/run.query.4.png "驗證輸出")

    請注意，在 [資料總管] 窗格中，輸出會寫入到 Data Lake Storage Gen1 輸出設定中所指定的資料夾路徑 (`streamanalytics/job/output/{date}/{time}`)。  

## <a name="see-also"></a>另請參閱
* [建立 HDInsight 叢集以使用 Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
