---
title: 在 Azure 突觸分析中引入到 SQL 池
description: 瞭解如何在 Azure 同步分析中將資料引入 SQL 池
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430561"
---
# <a name="ingesting-data-into-a-sql-pool"></a>將資料引入 SQL 池

在本文中,您將瞭解如何使用 Azure 突觸分析將 Azure 數據湖第 2 代儲存帳戶中的數據引入到 SQL 池中。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**:如果沒有 Azure 訂閱,請先建立一個[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存帳戶**:使用 Azure 資料儲存第 2 代作為*來源*資料儲存。 如果沒有存儲帳戶,請參閱[創建 Azure 存儲帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以執行創建存儲帳戶的步驟。
* **Azure 突觸分析**:使用 SQL 池作為*接收體*數據儲存。 如果沒有 Azure 同步分析實例,請參閱[創建 SQL 池](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)以執行創建一個範例。

## <a name="create-linked-services"></a>建立連結的服務

在 Azure Synapse 分析中,連結服務是定義與其他服務的連接資訊的位置。 在本節中,您將添加 Azure 突觸分析和 Azure 數據存儲來源第 2 代連結服務。

1. 開啟 Azure 同步分析 UX 並轉到 **'管理**'選項卡。
1. 在 **「外部連線**」下,選擇 **「連結的服務**」。。
1. 要添加連結的服務,請按下"**新建**"。
1. 從清單中選擇 Azure 資料儲存湖儲存 Gen2 磁貼,然後單擊「**繼續**」。
1. 輸入身份驗證憑據。 帳戶金鑰、服務主體和託管標識當前受支援身份驗證類型。 單擊測試連接以驗證您的憑據是否正確。 在完成作業後，按一下 [建立]****。
1. 重複步驟 3-5,但選擇 Azure 同步分析磁貼並輸入相應的連接憑據,而不是 Azure 數據湖存儲 Gen2。 對於 Azure 突觸分析,當前支援 SQL 身份驗證、託管標識和服務主體。

## <a name="create-pipeline"></a>建立管線

管道包含執行一組活動的邏輯流。 在本節中,您將創建一個管道,其中包含將 ADLS 第 2 代的數據引入 SQL 池的副本活動。

1. 跳到 **"協調"** 選項卡。按一下管道標題旁邊的加號圖示,然後選擇 **「管道**」。。
1. 在活動窗格中的 **「移動和轉換」** 下,將 **「複製資料」** 拖動到管道畫布上。
1. 按下複製活動並轉到 **「源」** 選項卡。按下 **「新建」** 以建立新的來源資料集。
1. 選擇 Azure 資料儲存第 2 代作為數據存儲,然後單擊" 繼續"
1. 選擇"分隔文字"作為格式,然後按一下"繼續"。
1. 在「設定屬性」窗格中,選擇您建立的 ADLS 連結服務。 指定來源資料的檔案路徑,並指定第一行是否具有標頭。 可以從檔案存儲或示例文件導入架構。 在完成作業後，按一下 [確定]。
1. 跳到 **「接收器」** 選項卡。按下 **'新增**" 以建立新的接收器資料集。
1. 選擇 Azure 同步分析作為數據存儲,然後單擊" 繼續"
1. 在「設定屬性」窗格中,選擇您建立的 Azure 同步分析連結服務。 如果要寫入現有表,請從下拉清單中選擇它。 否則,選中 **「編輯」** 並輸入新表名稱。 在完成作業後，按一下 [確定]
1. 如果要建立表,請在表選項欄位中啟用**自動建立表**格 。

## <a name="debug-and-publish-pipeline"></a>除錯和發佈導管

完成管道配置后,可以在發佈專案之前執行調試運行,以驗證一切是否正確。

1. 若要對管線進行偵錯，請選取工具列上的 [偵錯]****。 您可以在視窗底部的 [輸出]**** 索引標籤中檢視管線執行的狀態。 
1. 當管線可成功執行後，請在頂端的工具列中選取 [全部發佈]****。 此操作將發佈您創建的到 Synapse 分析服務的實體(資料集和管道)。
1. 請靜待 [發佈成功]**** 訊息顯示。 要查看通知消息,請按下右上角的鈴聲按鈕。 


## <a name="trigger-and-monitor-the-pipeline"></a>觸發並監視導管

在此步驟中,您可以手動觸發上一步驟中發佈的管道。 

1. 選取工具列上的 [新增觸發程序]****，然後選取 [立即觸發]****。 在 [管線執行]**** 頁面上，選取 [完成]****。  
1. 跳到左邊欄中的 **「監視器」** 選項卡。 您會看到手動觸發程序所觸發的管線執行。 您可以使用 **「操作」** 欄中的連結查看活動詳細資訊並重新執行管道。
1. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作]**** 資料行中的 [檢視活動執行]**** 連結。 此範例中只有一個活動，因此您在清單中只會看到一個項目。 如需關於複製作業的詳細資料，請選取 [動作]**** 資料行中的 [詳細資料]**** 連結 (眼鏡圖示)。 選取頂端的 [管線執行]**** 可回到 [管線執行] 檢視。 若要重新整理檢視，請選取 [重新整理]****。
1. 驗證數據是否正確寫入 SQL 池。


## <a name="next-steps"></a>後續步驟

有關 Synapse 分析的資料整合的詳細資訊,請參考將資料[引入 Azure 資料儲存第 2 代](data-integration-data-lake.md)。
