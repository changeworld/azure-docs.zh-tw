---
title: 包含檔案
description: 包含檔案
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541799"
---
1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 將工作區升級到企業版。

    升級後，所有可視介面實驗都將轉換為設計器中的管道拔模。
    
    > [!NOTE]
    > 您無需升級到企業版，可將可視介面 Web 服務轉換為即時終結點。
    
1. 轉到工作區的設計器部分以查看管道草稿清單。 
    
    轉換後的 Web 服務可以通過導航到**端點** > **即時終結點**找到。

1. 選擇管道草稿以打開它。

    如果在轉換過程中出現錯誤，則會顯示一條錯誤訊息，其中包含解決問題的說明。 

### <a name="known-issues"></a>已知問題

 以下是需要手動解決的已知遷移問題：

- **導入資料**或**匯出資料**模組
        
    如果實驗中有**導入資料**或**匯出資料**模組，則需要更新資料來源才能使用資料存儲。 要瞭解如何創建資料存儲，請參閱如何在 Azure[存儲服務中訪問資料](../articles/machine-learning/how-to-access-data.md)。 為了您的方便，您的雲存儲帳戶資訊已添加到**導入資料**或**匯出資料**模組的注釋中。 
      