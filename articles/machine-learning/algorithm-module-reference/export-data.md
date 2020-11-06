---
title: 匯出資料：模組參考
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設計工具中的 [匯出資料] 模組，可將結果和中繼資料儲存在 Azure Machine Learning 之外。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421306"
---
# <a name="export-data-module"></a>匯出資料模組

本文描述 Azure Machine Learning 設計工具中的模組。

使用此模組可將您管線中的結果、中繼資料和工作資料儲存到雲端儲存體目的地。 

此模組支援將您的資料匯出至下列雲端資料服務：

- Azure Blob 容器
- Azure 檔案共用
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL 資料庫

在匯出資料之前，您必須先在 Azure Machine Learning 工作區中註冊資料存放區。 如需詳細資訊，請參閱 [存取 Azure 儲存體服務中的資料](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 在設計工具中，將「 **匯出資料** 」模組新增至您的管線。 您可以在 [ **輸入] 和 [輸出** ] 分類中找到此模組。

1. 將 **匯出資料** 連接到包含您要匯出之資料的模組。

1. 選取 [ **匯出資料** ] 以開啟 [ **屬性** ] 窗格。

1. 針對 **資料** 存放區，從下拉式清單中選取現有的資料存放區。 您也可以建立新的資料存放區。 查看如何造訪 [Azure 儲存體服務中的存取資料](../how-to-access-data.md)。

    > [!NOTE]
    > 不支援將特定資料類型的資料匯出至指定為另一個資料類型的 SQL 資料庫資料行。 目標資料表不需要先存在。

1. [重新產生 **輸出** ] 核取方塊會決定是否要執行模組，以便在執行時間重新產生輸出。 

    預設為未選取，這表示如果已使用先前相同的參數來執行模組，系統將會重複使用上次執行的輸出，以縮短執行時間。 

    如果選取此選項，系統會再次執行模組以重新產生輸出。

1. 定義資料存放區中資料的路徑。 路徑是相對路徑。 不允許空白路徑或 URL 路徑。


1. 在 [ **檔案格式** ] 中，選取儲存資料的格式。
 
1. 提交管線。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
