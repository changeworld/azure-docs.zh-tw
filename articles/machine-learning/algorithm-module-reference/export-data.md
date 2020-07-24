---
title: 匯出資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「匯出資料」模組，將您管線中的結果、中繼資料和工作資料儲存到 Azure Machine Learning 以外的雲端儲存體目的地。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 279ba2ca5b82d7bad5b55736179f92a0146bdc84
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048159"
---
# <a name="export-data-module"></a>匯出資料模組

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組可將您管線中的結果、中繼資料和工作資料儲存到雲端儲存體目的地。 

此模組支援將您的資料匯出至下列雲端資料服務：

- Azure Blob 容器
- Azure 檔案共用
- Azure Data Lake
- Azure Data Lake Gen2
- Azure SQL 資料庫

在匯出資料之前，您必須先在 Azure Machine Learning 工作區中註冊資料存放區。 如需詳細資訊，請參閱[存取 Azure 儲存體服務中的資料](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 在設計工具中，將「**匯出資料**」模組新增至您的管線。 您可以在 [**輸入和輸出**] 分類中找到此模組。

1. 將**匯出資料**連接到包含您要匯出之資料的模組。

1. 選取 [**匯出資料**] 以開啟 [**屬性**] 窗格。

1. 針對**資料**存放區，請從下拉式清單中選取現有的資料存放區。 您也可以建立新的資料存放區。 查看如何造訪[Azure 儲存體服務中的存取資料](../how-to-access-data.md)。

1. [重新產生**輸出**] 核取方塊會決定是否要執行模組，以便在執行時間重新產生輸出。 

    預設為未選取，這表示如果模組先前已使用相同的參數執行，則系統會重複使用最後一次執行的輸出，以縮短執行時間。 

    如果選取此選項，系統會再次執行模組以重新產生輸出。

1. 定義資料存放區中的路徑。 路徑是相對路徑。 不允許空的路徑或 URL 路徑。


1. 在 [**檔案格式**] 中，選取資料的儲存格式。
 
1. 提交管線。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
