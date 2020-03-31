---
title: 安排您的工作
description: 使用作業計畫來管理工作。
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672429"
---
# <a name="schedule-jobs-for-efficiency"></a>計畫作業以提高效率

通過計畫 Batch 作業，您可以優先處理要首先運行的作業，同時考慮對其他任務具有依賴性的任務。 通過安排作業，可以確保使用最少的資源。 節點可以在不需要時停用，依賴于其他任務的任務只需及時優化工作流即可啟動。 一次只運行一個作業。 在上一個完成之前，新的一個不會啟動。 您可以將作業設置為自動完成。 

## <a name="benefit-of-job-scheduling"></a>作業排程的好處

計畫作業的好處是您可以指定創建作業的計畫。使用作業管理器任務計畫的任務與作業相關聯。 作業管理器任務將為作業創建任務。 為此，作業管理器任務需要使用 Batch 帳戶進行身份驗證。 使用AZ_BATCH_AUTHENTICATION_TOKEN訪問權杖。 權杖將允許訪問作業的其餘部分。 

## <a name="use-the-portal-to-schedule-a-job"></a>使用門戶安排作業

   1. 登錄到[Azure 門戶](https://portal.azure.com/)。

   2. 選擇要在其中安排作業的批次帳戶。

   3. 選擇 **"添加"** 以創建新的作業計畫並完成**基本表單**。



![安排作業][1]

**作業計畫 ID**：此作業計畫的唯一識別碼。

**顯示名稱**：作業的顯示名稱不必是唯一的，但最大長度為 1024 個字元。

**不要運行，直到**： 指定作業將運行的最早時間。 如果不設置此設置，計畫將立即運行作業。

**不要在**： 之後運行 ： 此處設置的時間後不運行作業。 如果不指定時間，則創建定期作業計畫，在顯式終止之前保持活動狀態。

**定期間隔**：您可以指定作業之間的時間量。 一次計畫只能有一個作業，因此，如果是在作業計畫下創建新作業，但以前的作業仍在運行，Batch 服務將不會創建新作業，直到上一個作業完成。  

**開始視窗**：在這裡，您指定時間間隔，從計畫指示應創建作業的時間開始，直到完成作業。 如果當前作業在其視窗期間未完成，則下一個作業將不會啟動。

在基本表單的底部，您將指定要在其上運行作業的池。 要查找池 ID 資訊，請選擇 **"更新**"。 

![指定池][2]


**池 ID**：標識要運行作業的池。

**作業配置任務**：選擇 **"更新**"以命名作業管理器任務以及作業準備和發佈任務（如果您正在使用）。

**優先順序**：優先作業。

**最大掛鐘時間**：設置作業可以運行的最大時間量。 如果無法在時間範圍內完成，Batch 將終止作業。 如果不設置此設置，則作業沒有時間限制。

**最大任務重試計數**：指定任務最多可以重試四次的次數。 這與 API 呼叫可能進行重試的次數不同。

**當所有任務完成**時 ：預設值為"無操作"。

**當任務失敗時**：預設值為"無操作"。 如果重試計數已用盡或啟動任務時出錯，則任務將失敗。 

選擇 **"保存"** 後，如果轉到左側導航中的**作業計畫**，則可以通過選擇 **"執行資訊"** 來跟蹤作業的執行。


## <a name="for-more-information"></a>取得詳細資訊

要使用 Azure CLI 管理作業，請參閱[az 批次處理作業計畫](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)。

## <a name="next-steps"></a>後續步驟

[創建任務依賴項以運行依賴于其他任務的任務](batch-task-dependencies.md)。





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


