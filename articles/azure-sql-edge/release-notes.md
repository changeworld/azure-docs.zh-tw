---
title: Azure SQL Edge 的版本資訊
description: 版本資訊詳細說明 Azure SQL Edge 映射中的新功能或變更內容
keywords: 版本資訊 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: d5d60db2cc0c35b908454be3f00716db6d2da945
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361637"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 版本資訊 

本文說明新功能，以及每個 Azure SQL Edge 新組建的變更內容。

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL 引擎組建編號-15.0.2000.1545
### <a name="fixes"></a>修正
1. 使用 ONNX 模型修正預測，以處理 ARM 中的 CPUID 問題 
2. 修正以改善啟動 TSQL 串流時的失敗路徑處理 
3. 當沒有資料時，修正作業計量中的浮水印延遲不正確的值。 
4. 修正當介面卡具有批次之間的變數架構時，輸出介面卡的問題。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL 引擎組建編號-15.0.2000.1401
### <a name="whats-new"></a>新功能
1.  產品名稱已更新為「Azure SQL Edge」
1.  Date_bucket 函式

    i.  日期、時間、日期時間類型的支援
3.  使用 ONNX 進行預測
    
    i.  ONNX 需要執行時間參數 
    
4.  TSQL 串流支援（有限預覽） 
 
### <a name="known-issues"></a>已知問題

1. <b>問題：</b>因時間問題而在啟動時套用 dacpac 的可能失敗。

    因應措施<b>：</b>重新開機 SQL Server 或容器將會重試套用 dacpac，並應修正此問題
### <a name="request-support"></a>要求支援
1. 您可以在[支援頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)中要求支援。

4. 請確定已選取下欄欄位： 
    * 問題類型-技術 
    * 服務-IoT Edge
    * 問題類型-我的問題與 IoT Edge 模組相關
    * 問題子類型-Azure SQL Edge

   ![範例支援票證](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1。5
### <a name="sql-engine-build-number---15020001331"></a>SQL 引擎組建編號-15.0.2000.1331
### <a name="whats-new"></a>新功能
1. Date_bucket 函式
    
    i. DateTimeOffset 類型的支援
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

    ii.  具有壓縮的記錄資料表

    iii. 向前讀取記錄的較高比例因素

    iv.  批次模式 ES 篩選下推

    v.   讀取先行優化
 
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

    i.  下推匯總到資料行存放區掃描

    ii. 快樂-迴圈掃描
4. 使用量和記憶體耗用量縮減工作量
