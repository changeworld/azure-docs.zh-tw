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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75541799"
---
1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 將您的工作區升級至 Enterprise edition。

    升級之後，所有視覺化介面實驗都會轉換成設計工具中的管線草稿。
    
    > [!NOTE]
    > 您不需要升級至 Enterprise edition，就能將視覺化介面 web 服務轉換成即時端點。
    
1. 移至工作區的 [設計工具] 區段，以查看您的管線草稿清單。 
    
    流覽至 [**端點** > ] [**即時端點**]，即可找到已轉換的 web 服務。

1. 選取管線草稿以開啟它。

    如果轉換程式期間發生錯誤，則會出現一則錯誤訊息，其中包含解決問題的指示。 

### <a name="known-issues"></a>已知問題

 以下是需要手動解決的已知遷移問題：

- 匯**入資料**或**匯出資料**模組
        
    如果您在實驗中有 [匯**入資料**] 或 [**匯出資料**] 模組，您必須更新資料來源以使用資料存放區。 若要瞭解如何建立資料存放區，請參閱[如何存取 Azure 儲存體服務中的資料](../articles/machine-learning/how-to-access-data.md)。 您的雲端儲存體帳戶資訊已新增至 [匯**入資料**] 或 [**匯出資料**] 模組的批註中，以方便您使用。 
      