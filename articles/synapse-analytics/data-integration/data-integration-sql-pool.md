---
title: 將資料內嵌至 SQL 集區
description: 瞭解如何在 Azure Synapse Analytics 中將資料內嵌至 SQL 集區
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 02efaf3f0382a7af63717e777036637de2bbec25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033195"
---
# <a name="ingest-data-into-a-sql-pool"></a>將資料內嵌至 SQL 集區

在本文中，您將瞭解如何使用 Azure Synapse Analytics 將 Azure Data Lake Gen 2 儲存體帳戶中的資料內嵌到 SQL 集區。

## <a name="prerequisites"></a>必要條件

* **Azure 訂**用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/) 。
* **Azure 儲存體帳戶**：您使用 Azure Data Lake Storage Gen 2 作為 *源* 資料存放區。 如果您沒有儲存體帳戶，請參閱 [建立 Azure 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 以取得建立帳戶的步驟。
* **Azure Synapse Analytics**：您使用 SQL 集區做為 *接收* 資料存放區。 如果您沒有 Azure Synapse Analytics 實例，請參閱 [建立 SQL 集](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 區以取得建立一個的步驟。

## <a name="create-linked-services"></a>建立連結的服務

在 Azure Synapse Analytics 中，連結的服務是您定義連接資訊給其他服務的位置。 在本節中，您會新增 Azure Synapse Analytics 和 Azure Data Lake Storage Gen2 連結服務。

1. 開啟 Azure Synapse Analytics UX 並移至 [ **管理** ] 索引標籤。
1. 在 [ **外部連接**] 底下，選取 [已 **連結的服務**]。
1. 若要加入連結服務，請選取 [ **新增**]。
1. 從清單中選取 Azure Data Lake Storage Gen2 圖格，然後選取 [ **繼續**]。
1. 輸入您的驗證認證。 帳戶金鑰、服務主體和受控識別目前支援的驗證類型。 選取 [測試連接] 以確認您的認證正確無誤。 完成後，請選取 [建立]****。
1. 重複步驟3-5，但不是 Azure Data Lake Storage Gen2，請選取 Azure Synapse Analytics 磚，然後輸入對應的連接認證。 針對 Azure Synapse Analytics，目前支援 SQL 驗證、受控識別與服務主體。

## <a name="create-pipeline"></a>建立管線

管線包含執行一組活動的邏輯流程。 在本節中，您將建立包含複製活動的管線，以將 ADLS Gen2 中的資料內嵌至 SQL 集區。

1. 移至 [ **協調** ] 索引標籤。選取 [管線] 標頭旁邊的加號圖示，然後選取 [ **管線**]。
1. 在 [活動] 窗格中的 [ **移動和轉換** ] 底下，將 [ **複製資料** ] 拖曳至管線畫布上。
1. 選取複製活動，然後移至 [ **來源** ] 索引標籤。選取 [ **新增** ] 以建立新的源資料集。
1. 選取 Azure Data Lake Storage gen2 作為資料存放區，然後選取 [繼續]。
1. 選取 DelimitedText 做為您的格式，然後選取 [繼續]。
1. 在 [設定屬性] 窗格中，選取您所建立的 ADLS 連結服務。 指定來源資料的檔案路徑，並指定第一個資料列是否有標頭。 您可以從檔案存放區或範例檔案匯入架構。 於完成時選取 [確定]。
1. 移至 [ **接收** ] 索引標籤。選取 [ **新增** ] 以建立新的接收資料集。
1. 選取 Azure Synapse Analytics 作為資料存放區，然後選取 [繼續]。
1. 在 [設定屬性] 窗格中，選取您建立的 Azure Synapse Analytics 連結服務。 如果您要寫入現有的資料表，請從下拉式清單中選取它。 否則，請核取 [ **編輯** ]，然後輸入新的資料表名稱。 完成時選取 [確定]
1. 如果您要建立資料表，請在 [資料表選項] 欄位中啟用 [ **自動建立資料表** ]。

## <a name="debug-and-publish-pipeline"></a>調試和發佈管線

完成管線的設定之後，您就可以在發行成品之前執行「偵測執行」，以確認所有專案都正確。

1. 若要對管線進行偵錯，請選取工具列上的 [偵錯]。 您可以在視窗底部的 [輸出] 索引標籤中檢視管線執行的狀態。 
1. 一旦管線可以順利執行，請在頂端工具列中選取 [ **全部發佈**]。 此動作會將實體 (資料集，以及您建立至 Synapse Analytics 服務) 的管線。
1. 請靜待 [發佈成功] 訊息顯示。 若要查看通知訊息，請選取右上方的 [鐘] 按鈕。 


## <a name="trigger-and-monitor-the-pipeline"></a>觸發和監視管線

在此步驟中，您會手動觸發上一個步驟中所發佈的管線。 

1. 選取工具列上的 [新增觸發程序]****，然後選取 [立即觸發]****。 在 [ **管線執行** ] 頁面上，選取 **[完成]**。  
1. 移至左側提要欄位中的 [ **監視** ] 索引標籤。 您會看到手動觸發程序所觸發的管線執行。 您可以使用 [ **動作** ] 資料行中的連結來查看活動詳細資料，以及重新執行管線。
1. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作]**** 資料行中的 [檢視活動執行]**** 連結。 此範例中只有一個活動，因此您在清單中只會看到一個項目。 如需關於複製作業的詳細資料，請選取 [動作]**** 資料行中的 [詳細資料]**** 連結 (眼鏡圖示)。 選取頂端的 [管線執行]**** 可回到 [管線執行] 檢視。 若要重新整理檢視，請選取 [重新整理]。
1. 確認您的資料已正確寫入 SQL 集區。


## <a name="next-steps"></a>後續步驟

如需有關 Synapse Analytics 資料整合的詳細資訊，請參閱 [擷取資料 Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) 文章。
