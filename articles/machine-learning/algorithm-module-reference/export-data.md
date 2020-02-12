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
ms.date: 10/22/2019
ms.openlocfilehash: 11f5bd7f01e142273509ae59ddc19a2557464bde
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152307"
---
# <a name="export-data-module"></a>匯出資料模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組可將您管線中的結果、中繼資料和工作資料儲存到 Azure Machine Learning 外部的雲端儲存體目的地。 

此模組支援將您的資料匯出至下列雲端資料服務：

- Azure Blob 容器
- Azure 檔案共用
- Azure Data Lake
- Azure Data Lake Gen2

在匯出資料之前，您必須先在您的 Azure Machine Learning 工作區中先註冊資料存放區。 如需詳細資訊，請參閱[存取 Azure 儲存體服務中的資料](../how-to-access-data.md)。

## <a name="how-to-configure-export-data"></a>如何設定匯出資料

1. 在設計工具中，將「**匯出資料**」模組新增至您的管線。 您可以在 [**輸入和輸出**] 分類中找到此模組。

1. 將**匯出資料**連接到包含您要匯出之資料的模組。

1. 選取 [**匯出資料**] 以開啟 [**屬性**] 窗格。

1. 針對**資料**存放區，請從下拉式清單中選取現有的資料存放區。 您也可以建立新的資料存放區。 查看如何造訪[Azure 儲存體服務中的存取資料](../how-to-access-data.md)。

1. 定義資料存放區中用來寫入資料的路徑。 


1. 在 [**檔案格式**] 中，選取資料的儲存格式。
 
1. 執行管道。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 
