---
title: Azure SQL Edge 的版本資訊
description: 版本資訊詳述 Azure SQL Edge 映射中的新功能或變更內容。
keywords: 版本資訊 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696374"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 版本資訊 

本文說明新功能，以及 Azure SQL Edge 每個新組建的變更內容。

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0。2

SQL 引擎組建15.0.2000.1554

### <a name="fixes"></a>修正

- T-sql 串流  
   - 修正串流物件的擁有權和許可權
   - 記錄檔輪替和記錄前置記錄的改進
   - Azure 串流分析：記錄改良功能，改善介面卡中的錯誤碼/錯誤訊息 

- ONNX
    - 平行查詢案例和模型清除失敗的 Bug 修正
    - 已將 ONNX 執行時間升級至1.5。1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0。1

SQL 引擎組建15.0.2000.1553

### <a name="whats-new"></a>新功能

- 允許在計算資料行中定義 Date_Bucket 運算式。

### <a name="fixes"></a>修正

- 保留原則修正，可卸載已啟用保留原則且具有無限超時時間的資料表
- 串流功能和保留原則功能的 DacFx 部署支援 
- DacFx 部署修正程式，以從 SAS URL 中的嵌套資料夾啟用部署 
- 預測修正以支援錯誤訊息中的長資料行名稱

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM) 

SQL 引擎組建15.0.2000.1552

### <a name="whats-new"></a>新功能
- 以 Ubuntu 18.04 為基礎的容器映射 
- 和函式的支援與 `IGNORE NULL` `RESPECT NULL` 語法 `LAST_VALUE()` `FIRST_VALUE()` 
- 使用 ONNX 進行預測的可靠性改進
- 支援根據資料保留原則進行清除：
   - 用於疑難排解保留清除工作的信號緩衝區支援
- 新功能支援： 
   - 快速復原
   - 自動調整查詢
   - 平行執行案例
- 低電源模式的省電改進
- 串流新功能支援： 
   - [快照集視窗](/stream-analytics-query/snapshot-window-azure-stream-analytics)：新的視窗類型可讓您依相同時間抵達的事件分組。
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) 和 [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) 可以啟用為分析函數。 您可以傳回依您選擇的資料行排序的記錄。 它們不需要是視窗的一部分。 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)的改善。 

### <a name="fixes"></a>修正
- 疑難排解 T-sql 串流作業的其他錯誤訊息和詳細資料 
- 以閒置模式保持電池壽命的改進 
- T-sql 串流引擎修正： 
   - 清除已停止的串流作業 
   - 當地語系化的修正 
   - 改進的 Unicode 處理 
   - 改進 SQL Edge T-sql 串流的偵錯工具，可讓使用者從 get_streaming_job 查詢作業失敗錯誤
- 根據資料保留原則進行清除： 
    - 保留原則建立和清除案例的修正
- 修正背景計時器工作，以改善低電源模式的電源節約

### <a name="known-issues"></a>已知問題 
- Date_Bucket T-sql 函數不能用在計算資料行中。


## <a name="ctp-23"></a>CTP 2.3
SQL 引擎組建15.0.2000.1549
### <a name="whats-new"></a>新功能
- Date_Bucket ( # A1 函式中的自訂來源支援 
- 在 SQL 部署過程中支援 BACPAC 檔案
- 支援根據資料保留原則進行清除：      
   - 啟用保留原則的 DDL 支援 
   - 清除預存程式和背景清除工作
   - 擴充事件以監視清除工作

### <a name="fixes"></a>修正
- 疑難排解 T-sql 串流作業的其他錯誤訊息和詳細資料 
- 以閒置模式保持電池壽命的改進 
- T-sql 串流引擎： 
   - 修正 substreamed 跳動視窗中停滯的浮水印 
   - 修正架構例外狀況處理，以確保其被收集為使用者可採取動作的錯誤


## <a name="ctp-22"></a>CTP 2.2
SQL 引擎組建15.0.2000.1546
### <a name="whats-new"></a>新功能
- 支援非根容器 
- 使用方式和診斷資料收集的支援 
- T-sql 串流更新：
   - 支援資料流程物件名稱的 Unicode 字元

### <a name="fixes"></a>修正
- T-sql 串流更新：
   - 進程清除改進
   - 記錄和診斷改善
- 資料內嵌的效能改進

## <a name="ctp-21"></a>CTP 2.1 
SQL 引擎組建15.0.2000.1545
### <a name="fixes"></a>修正
- 允許 ONNX 模型在 ARM 中處理 CPUID 問題 
- 改進 T-sql 串流啟動時失敗路徑的處理
- 更正工作計量中沒有資料時的浮水印延遲值。
- 修正當介面卡在批次之間具有變數架構時，輸出介面卡的問題  

## <a name="ctp-20"></a>CTP 2.0 
SQL 引擎組建15.0.2000.1401
### <a name="whats-new"></a>新功能
-   更新為 *AZURE SQL Edge* 的產品名稱
-  Date_Bucket 函式：
    - 日期、時間和日期時間類型的支援
- 使用 ONNX 進行預測：
    - 執行時間參數的 ONNX 需求  
- T-sql 串流支援 (有限預覽)  
 
### <a name="known-issues"></a>已知問題

- 問題：因計時問題而在啟動時套用 DACPAC 的潛在失敗。
- 因應措施：重新開機 SQL Server。 否則，容器會重試套用 DACPAC。

### <a name="request-support"></a>要求支援
您可以在 [支援頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)上要求支援。 選取下欄欄位： 
- **問題類型**：*技術* 
- **服務**： *IoT Edge*
- **問題類型**： *我的問題與 IoT Edge 模組相關*
- **問題子類型**： *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="顯示範例支援票證的螢幕擷取畫面。":::

## <a name="ctp-15"></a>CTP 1。5
SQL 引擎組建15.0.2000.1331
### <a name="whats-new"></a>新功能
- Date_Bucket 函式：
    - DateTimeOffset 類型的支援
- 使用 ONNX 模型來預測：
  - NVARCHAR 支援
 
## <a name="ctp-14"></a>CTP 1。4
SQL 引擎組建15.0.2000.1247
### <a name="whats-new"></a>新功能
-   使用 ONNX 模型來預測：
    - VARCHAR 支援
    - 遷移至 ONNX 執行階段版本1。0 

- 啟用下列功能：
  - CDC 支援
  - 具有壓縮的歷程記錄資料表
  - 記錄檔預先讀取的較高比例因素
  - 批次模式 ES 篩選下推
  - 預先讀取優化
 
## <a name="ctp-13"></a>CTP 1。3
SQL 引擎組建15.0.2000.1147
### <a name="whats-new"></a>新功能
- Azure IoT 入口網站部署： 
    - 支援部署 AMD64 和 ARM 映射
    - 支援串流作業建立
    - DACPAC 部署
- 使用 ONNX 模型來預測：
    - 數數值型別支援
- 啟用下列功能：
    - 下推匯總至資料行存放區掃描
    - 快樂-輪流掃描
- 使用量和記憶體耗用量減少工作
