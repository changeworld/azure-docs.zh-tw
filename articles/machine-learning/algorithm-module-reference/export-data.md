---
title: 匯出資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"匯出資料"模組將管道中的結果、中間資料和工作資料保存到 Azure 機器學習之外的雲存儲目標。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456296"
---
# <a name="export-data-module"></a>匯出資料模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組將管道的結果、中間資料和工作資料保存到雲存儲目標。 

此模組支援將資料匯出到以下雲資料服務：

- Azure Blob 容器
- Azure 檔案共用
- Azure Data Lake
- Azure Data Lake Gen2

在匯出資料之前，需要首先在 Azure 機器學習工作區中註冊資料存儲。 有關詳細資訊，請參閱訪問[Azure 存儲服務中的資料](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 將**匯出資料**模組添加到設計器中的管道中。 您可以在 **"輸入和輸出**"類別中找到此模組。

1. 將**匯出資料**連接到包含要匯出資料的模組。

1. 選擇 **"匯出資料**"以打開"**屬性"** 窗格。

1. 對於**資料存儲**，從下拉清單中選擇現有資料存儲。 您還可以創建新的資料存儲。 通過訪問[Azure 存儲服務中的訪問資料](../how-to-access-data.md)來檢查如何。

1. 核取方塊"**重新生成輸出**"將決定是否執行模組以在運行時重新生成輸出。 

    預設情況下未選中，這意味著如果模組以前使用相同的參數執行，系統將重用上次運行的輸出以縮短執行時間。 

    如果選中，系統將再次執行模組以重新生成輸出。

1. 定義資料存儲中資料的路徑。 路徑是相對路徑。 不允許使用空路徑或 URL 路徑。


1. 對於**檔案格式**，選擇應存儲資料的格式。
 
1. 提交管道。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
