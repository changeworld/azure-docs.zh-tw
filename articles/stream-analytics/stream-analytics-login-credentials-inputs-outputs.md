---
title: 替換 Azure 串流分析作業中的登入認證
description: 本文說明如何更新 Azure 串流分析作業中輸入和輸出接收的認證。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3154447e4df64b9b335beae99cfd208d1a21efc4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044408"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>針對串流分析作業的輸入和輸出替換登入認證

每當您針對串流分析作業的輸入或輸出重新產生認證時，您應該使用新的認證來更新該作業。 您必須先停止作業才能更新認證，而不能在作業執行的時候替換認證。 為了減少停止和重新啟動作業之間的延遲，串流分析支援作業從其最後的輸出繼續進行。 本主題說明替換登入認證並使用新認證重新啟動作業的程序。

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>重新產生新認證，並使用新認證更新作業 

在本節中，我們將引導您重新產生 Blob 儲存體、事件中樞、SQL Database 和表格儲存體的認證。 

### <a name="blob-storagetable-storage"></a>Blob 儲存體/資料表儲存體 
1. 登入 Azure 入口網站 > 瀏覽您作為串流分析作業輸入/輸出使用的儲存體帳戶。    
2. 在設定區段中，開啟 [存取金鑰]****。 從兩個預設金鑰 (key1、key2) 之中，挑選作業未使用的金鑰並重新產生它：  
   ![重新產生儲存體帳戶金鑰](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. 複製新產生的金鑰。    
4. 在 Azure 入口網站中，瀏覽至您的串流分析作業 > 選取 [停止]****，並等候作業停止。    
5. 找出您要更新認證的 Blob/表格儲存體輸出/輸入。    
6. 尋找 [儲存體帳戶金鑰]**** 欄位，然後貼上新產生的金鑰 > 按一下 [儲存]****。    
7. 當您儲存變更時，會自動啟動連線測試，您可以從 [通知] 索引標籤加以查看。有兩個通知-一個對應于儲存更新，另一個對應至測試連線：  
   ![編輯金鑰之後的通知](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. 繼續進行[從上次停止的時間啟動您的作業](#start-your-job-from-the-last-stopped-time)一節。

### <a name="event-hubs"></a>事件中樞

1. 登入 Azure 入口網站 > 瀏覽您作為串流分析作業輸入/輸出使用的事件中樞。    
2. 從設定區段，開啟 [共用存取原則]**** 並選取必要的存取原則。 在 [主要金鑰]**** 和 [次要金鑰]**** 之間，挑選作業未使用的金鑰並重新生它：  
   ![重新產生事件中樞的金鑰](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. 複製新產生的金鑰。    
4. 在 Azure 入口網站中，瀏覽至您的串流分析作業 > 選取 [停止]****，並等候作業停止。    
5. 找出您要更新認證的事件中樞輸出/輸入。    
6. 尋找 [事件中樞原則金鑰]**** 欄位，然後貼上新產生的金鑰 > 按一下 [儲存]****。    
7. 當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。    
8. 繼續進行[從上次停止的時間啟動您的作業](#start-your-job-from-the-last-stopped-time)一節。

### <a name="sql-database"></a>SQL Database

您必須連接到 SQL Database，以更新現有使用者的登入認證。 您可以使用 Azure 入口網站或用戶端工具 (例如 SQL Server Management Studio) 來更新認證。 本節將示範使用 Azure 入口網站更新認證的程序。

1. 登入 Azure 入口網站 > 瀏覽至您作為串流分析輸入/輸出使用的 SQL 資料庫。    
2. 從 [資料總管]****，登入/連線到您的資料庫 > 將 [驗證類型] 選取為 [SQL Server 驗證]**** > 輸入您的 [登入]**** 和 [密碼]**** 詳細資料 > 選取 [確定]****。  
   ![重新產生 SQL Database 的認證](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. 在查詢索引標籤中，執行下列查詢以修改其中一個使用者的密碼 (請務必將 `<user_name>` 取代為您的使用者名稱，並將 `<new_password>` 取代為您的新密碼)：  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. 記下新的密碼。    
5. 在 Azure 入口網站中，瀏覽至您的串流分析作業 > 選取 [停止]****，並等候作業停止。    
6. 找出您要輪替認證的 SQL Database 輸出。 更新密碼並儲存變更。    
7. 當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。    
8. 繼續進行[從上次停止的時間啟動您的作業](#start-your-job-from-the-last-stopped-time)一節。

### <a name="power-bi"></a>Power BI
1. 登入 Azure 入口網站 > 瀏覽至您的串流分析作業 > 選取 [停止]****，並等候作業停止。    
2. 找出您要更新認證的 Power BI 輸出 > 按一下 [更新授權]**** (您應該會看見成功訊息) > [儲存]**** 變更。    
3. 當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。    
4. 繼續進行[從上次停止的時間啟動您的作業](#start-your-job-from-the-last-stopped-time)一節。

## <a name="start-your-job-from-the-last-stopped-time"></a>從上次停止的時間啟動您的作業

1. 瀏覽至作業的 [概觀]**** 窗格 > 選取 [啟動]**** 以啟動作業。    
2. 選取 [前次停止時間]**** > 按一下 [啟動]****。 請注意，只有在您先前有執行作業並有產生輸出的情況下，系統才會顯示 [前次停止時間] 選項。 作業會根據上次輸出值的時間重新啟動。
   ![啟動串流分析作業](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
