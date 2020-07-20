---
title: 轉換至映像目錄
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 [轉換成映射目錄] 模組，將資料集轉換成映射目錄格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171137"
---
# <a name="convert-to-image-directory"></a>轉換至映像目錄

本文說明如何使用 [轉換成映射目錄] 模組，協助將影像資料集轉換為「影像目錄」資料類型，這是影像相關工作中的標準化資料格式，例如 Azure Machine Learning 設計工具中的影像分類 (預覽) 。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用轉換成影像目錄  

1.  將 [**轉換成映射目錄**] 模組新增至您的實驗。 您可以在模組清單的 [電腦視覺/影像資料轉換] 類別中找到此模組。 

2.  [註冊映射資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)，並將它連接到模組輸入埠。 請確認輸入資料集內有影像。 
    支援下列資料集格式：

    - 這些延伸模組中的壓縮檔案： ' .zip '、'. tar '、'. gz '、'. bz2 '。
    - 包含影像的資料夾。 **強烈建議先壓縮這類資料夾，然後使用壓縮檔案作為資料集**。

    > [!WARNING]
    > 您**不能**使用 [匯**入資料**] 模組匯入影像資料集，因為 [匯**入資料**] 模組的輸出類型是 [資料框架目錄]，其中只包含檔案路徑字串。
    

    > [!NOTE]
    > 如果在監督式學習中使用影像資料集，則需要標籤。
    > 針對影像分類工作，如果此影像資料集是以 torchvision ImageFolder 格式組織，則可以在模組輸出中，將標籤產生為影像「類別」。 否則，只會儲存不含標籤的影像。 以下是如何組織影像資料集以取得標籤的範例，請使用影像類別目錄做為子資料夾名稱。 如需詳細資訊，請參閱[torchvision 資料集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder)。
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  提交管線。

## <a name="results"></a>結果

[**轉換成映射目錄**] 模組的輸出為 [映射目錄格式]，而且可以連接到其他影像相關模組，也就是輸入埠格式也是 [映射目錄]。

## <a name="technical-notes"></a>技術說明 

###  <a name="expected-inputs"></a>預期的輸入  

| 名稱          | 類型                  | 描述   |
| ------------- | --------------------- | ------------- |
| 輸入資料集 | AnyDirectory、ZipFile | 輸入資料集 |

###  <a name="output"></a>輸出  

| 名稱                   | 類型           | 描述            |
| ---------------------- | -------------- | ---------------------- |
| 輸出影像目錄 | ImageDirectory | 輸出影像目錄 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
