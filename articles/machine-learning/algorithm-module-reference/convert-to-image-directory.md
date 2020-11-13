---
title: 轉換至映像目錄
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 [轉換為影像目錄] 模組，將資料集轉換為影像目錄格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555578"
---
# <a name="convert-to-image-directory"></a>轉換至映像目錄

本文說明如何使用 [轉換成映射目錄] 模組，協助將映射資料集轉換為影像 *目錄* 資料類型，這是影像相關工作（例如 Azure Machine Learning 表設計工具中的影像分類）的標準化資料格式。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用轉換成影像目錄  

1. 先準備您的映射資料集。 

    針對受監督的學習，您需要指定訓練資料集的標籤。 映射資料集檔案應為下列結構：
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    在 [影像資料集] 資料夾中，有多個子資料夾。 每個子資料夾分別包含一個類別目錄的影像。 子資料夾的名稱會被視為影像分類等工作的標籤。 如需詳細資訊，請參閱 [torchvision 資料集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 。

    > [!WARNING]
    > 設計工具中不支援從資料標記匯出的資料集目前標記。

    支援以小寫)  (的影像： ' .jpg '、' jpeg '、' .png '、' ppm '、' .bmp '、' pgm '、' .tif '、' tiff '、'. webp '。 您也可以在一個資料夾中有多個類型的影像。 在每個類別目錄資料夾中都不需要包含相同的影像計數。

    您可以使用副檔名為 ' .zip '、' tar '、'. gz ' 和 '. bz2 ' 的資料夾或壓縮檔案。 **建議使用壓縮檔案，以獲得更好的效能。** 
    
    ![影像範例資料集](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > 針對推斷，映射資料集資料夾只需要包含未分類的影像。

1. 在您的工作區中 [註冊映射資料集做為檔案資料集](../how-to-create-register-datasets.md)，因為 [轉換成映射目錄] 模組的輸入必須是檔案 **資料集** 。

1. 將已註冊的影像資料集加入至畫布。 您可以在畫布左側的模組清單中，于 [ **資料集** ] 類別目錄中找到已註冊的資料集。 目前的設計工具不支援將影像資料集視覺化。

    > [!WARNING]
    > 您 **無法** 使用 [匯 **入資料** ] 模組匯入映射資料集，因為 [匯 **入資料** ] 模組的輸出類型是資料框架目錄，它只包含檔案路徑字串。

1. 將 [ **轉換成映射目錄** ] 模組新增至畫布。 您可以在模組清單中的「電腦視覺/影像資料轉換」類別中找到此模組。 將它連接至映射資料集。
    
3.  提交管線。 此模組可在 GPU 或 CPU 上執行。

## <a name="results"></a>結果

[ **轉換成映射目錄** ] 模組的輸出為 **影像目錄** 格式，而且可以連接到其他與影像相關的模組，也就是輸入埠格式也是影像目錄。

![轉換為影像目錄輸出](./media/module/convert-to-image-directory-output.png)

## <a name="technical-notes"></a>技術說明 

###  <a name="expected-inputs"></a>預期的輸入  

| 名稱          | 類型                  | 描述   |
| ------------- | --------------------- | ------------- |
| 輸入資料集 | AnyDirectory、ZipFile | 輸入資料集 |

###  <a name="output"></a>輸出  

| 名稱                   | 類型           | 描述            |
| ---------------------- | -------------- | ---------------------- |
| 輸出映射目錄 | ImageDirectory | 輸出映射目錄 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。