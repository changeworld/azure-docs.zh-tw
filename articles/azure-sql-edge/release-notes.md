---
title: Azure SQL Edge 的版本資訊
description: 說明 Azure SQL Edge 映射新功能或已變更內容的版本資訊
keywords: 版本資訊 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: bb0c176ee8c145e1268827d79b453628e6f1bc28
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808616"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 版本資訊 

本文說明新功能，以及 Azure SQL Edge 每個新組建的變更內容。

## <a name="azure-sql-edge---101"></a>Azure SQL Edge-1.0。1

### <a name="sql-engine-build-number---15020001553"></a>SQL 引擎組建編號-15.0.2000.1553

### <a name="whats-new"></a>新功能

- 允許計算資料行中定義的 Datebucket 運算式

### <a name="fixes"></a>修正

- 保留原則修正，用來卸載已啟用保留原則且具有無限超時時間的資料表
- 支援串流和保留原則功能的 DacFx 部署 
- DacFx 部署修正，以從 SAS URL 中的嵌套資料夾啟用部署 
- 預測修正以支援錯誤訊息中的長資料行名稱

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM) 

### <a name="sql-engine-build-number---15020001552"></a>SQL 引擎組建編號-15.0.2000.1552

### <a name="whats-new"></a>新功能
1. 以 Ubuntu 18.04 為基礎的容器映射。 
2. 和函數的支援 `IGNORE NULL` 和 `RESPECT NULL` 語法 `LAST_VALUE()` `FIRST_VALUE()` 。 
3. 使用 ONNX 進行預測的可靠性改進。
4. 支援以資料保留原則為基礎的清除。
   - 用於疑難排解保留清除工作的信號緩衝區支援。
5. 新功能支援 
   - 快速復原
   - 自動調整查詢
   - 啟用平行執行案例
6. 低電源模式的省電改進
7. 串流新功能支援 
   - [快照集視窗](/stream-analytics-query/snapshot-window-azure-stream-analytics) ：新的視窗類型，可讓您依確切時間抵達的事件分組。 
   - 啟用 [TopOne](/stream-analytics-query/topone-azure-stream-analytics) 和 [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) as 分析函式，這將可讓您傳回由您選擇的資料行排序的記錄，而不需要成為視窗的一部分。 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)的改善。 

### <a name="fixes"></a>修正
1. 疑難排解 TSQL 串流作業的其他錯誤訊息和詳細資料。 
2. 以閒置模式保持電池壽命的增強功能。 
3. TSQL 串流引擎修正： 
   - 清除已停止的串流作業 
   - 修正當地語系化和 unicode 處理的改進
   - 改善 Edge TSQL 資料流程的有用，讓使用者能夠從 get_streaming_job 查詢作業失敗錯誤。
4. 以資料保留原則為基礎的清除
   - 針對保留原則建立和清除案例的修正。
5. 修正背景計時器工作，以改善低電源模式的電源節約。

### <a name="known-issues"></a>已知問題 
1. Date_Bucket T-sql 函數不能用在計算資料行中。


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>SQL 引擎組建編號-15.0.2000.1549
### <a name="whats-new"></a>新功能
1. 支援 Date_Bucket ( # A1 函式中的自訂來源。 
2. 支援 BacPac 檔案做為 SQL 部署的一部分。
3. 支援以資料保留原則為基礎的清除。      
   - 啟用保留原則的 DDL 支援 
   - 清除預存程式和背景清除工作
   - 擴充事件以監視清除工作

### <a name="fixes"></a>修正
1. 疑難排解 TSQL 串流作業的其他錯誤訊息和詳細資料。 
2. 以閒置模式保持電池壽命的增強功能。 
3. TSQL 串流引擎修正： 
   - 修正 substreamed 跳動視窗的停滯水位線問題 
   - 修正架構例外狀況處理，以確保它會被收集為使用者可採取動作的錯誤


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL 引擎組建編號-15.0.2000.1546
### <a name="whats-new"></a>新功能
1. 支援非根容器 
2. 使用方式和診斷資料收集的支援 
3. T-sql 串流更新
   - 支援資料流程物件名稱的 Unicode 字元

### <a name="fixes"></a>修正
1. T-sql 串流更新
   - 進程清除改進
   - 記錄和診斷改善
2. 資料內嵌的效能改進

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL 引擎組建編號-15.0.2000.1545
### <a name="fixes"></a>修正
1. 修正 ONNX 模型的預測，以處理 ARM 中的 CPUID 問題 
2. 修正以改善啟動 TSQL 資料流程時的失敗路徑處理 
3. 當沒有任何資料時，修正工作計量中浮水印延遲的錯誤值。 
4. 修正當介面卡在批次之間具有變數架構時，輸出介面卡的問題。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL 引擎組建編號-15.0.2000.1401
### <a name="whats-new"></a>新功能
1.  產品名稱已更新為「Azure SQL Edge」
1.  Date_bucket 函式

    i.  日期、時間、日期時間類型的支援
3.  使用 ONNX 進行預測
    
    i.  ONNX 所需的執行時間參數 
    
4.   (有限預覽) 的 TSQL 串流支援 
 
### <a name="known-issues"></a>已知問題

1. <b>問題：</b> 因為計時問題而在啟動時套用 dacpac 的潛在失敗。

    因應措施<b>：</b>重新開機 SQL Server 或容器將會重試套用 dacpac，並應修正問題
### <a name="request-support"></a>要求支援
1. 您可以在 [支援頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中要求支援。

4. 確定已選取下欄欄位： 
    * 問題類型-技術 
    * 服務-IoT Edge
    * 問題類型-我的問題與 IoT Edge 模組相關
    * 問題子類型-Azure SQL Edge

   ![範例支援票證](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1。5
### <a name="sql-engine-build-number---15020001331"></a>SQL 引擎組建編號-15.0.2000.1331
### <a name="whats-new"></a>新功能
1. Date_bucket 函式
    
    i. 支援 DateTimeOffset 類型
2. 使用 ONNX 模型進行預測

    i. Nvarchar 支援
 
## <a name="ctp-14"></a>CTP 1。4
### <a name="sql-engine-build-number---15020001247"></a>SQL 引擎組建編號-15.0.2000.1247
### <a name="whats-new"></a>新功能
1.  使用 ONNX 模型進行預測
 
    i.  Varchar 支援
    
    ii. 遷移至 ONNX 執行階段版本1。0 
2.  功能支援-已啟用下列功能：

    i.   CDC 支援

    ii.  具有壓縮的歷程記錄資料表

    iii. 預先讀取記錄的更高比例因素

    iv.  批次模式 ES 篩選下推

    v.   預先讀取優化
 
## <a name="ctp-13"></a>CTP 1。3
### <a name="sql-engine-build-number---15020001147"></a>SQL 引擎組建編號-15.0.2000.1147
### <a name="whats-new"></a>新功能
1. Azure IOT 入口網站部署 

    i.   支援部署 AMD64 和 ARM 映射

    ii.  支援串流作業建立

    iii. Dacpac 部署
2. 使用 ONNX 模型進行預測

    i. 數數值型別支援
3. 功能支援-已啟用下列功能：

    i.  下推匯總至資料行存放區掃描

    ii. 快樂-輪流掃描
4. 使用量和記憶體耗用量減少工作
