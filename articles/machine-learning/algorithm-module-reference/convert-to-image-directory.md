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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450102"
---
# <a name="convert-to-image-directory"></a>轉換至映像目錄

本文說明如何使用 [轉換成映射目錄] 模組，協助將映射資料集轉換成「映射目錄」資料類型，這是影像相關工作（例如 Azure Machine Learning 表設計工具中的影像分類）的標準化資料格式。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用轉換成影像目錄  

1.  將 [ **轉換成映射目錄** ] 模組新增至畫布。 您可以在模組清單中的「電腦視覺/影像資料轉換」類別中找到此模組。 

2.  [ **轉換成映射目錄** ] 模組的輸入必須是檔案資料集。 [註冊映射資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) ，並將它連接到模組輸入埠。 請確認輸入資料集中有影像。 目前的設計工具不支援將影像資料集視覺化。
 
    支援下列資料集格式：

    - 這些延伸模組中的壓縮檔案： ' .zip '、' tar '、'. gz '、'. bz2 '。
    - 包含映射的資料夾。 **強烈建議您先壓縮這類資料夾，然後使用壓縮檔案作為資料集**。

    > [!WARNING]
    > 您 **無法** 使用 [匯 **入資料** ] 模組匯入映射資料集，因為 [匯 **入資料** ] 模組的輸出類型是資料框架目錄，它只包含檔案路徑字串。
    

    > [!NOTE]
    > - 如果在受監督的學習中使用影像資料集，您必須指定訓練資料集的標籤。
    > - 若為影像分類工作，如果此影像資料集是以 torchvision ImageFolder 格式組織，則標籤會在模組輸出中產生為影像「類別」。 否則，只會儲存沒有標籤的影像。 以下是如何組織影像資料集以取得標籤的範例，使用影像類別目錄做為子資料夾名稱。 
    > - 您不需要在每個類別目錄資料夾上傳相同數量的影像。
    > - 支援以小寫)  (的影像： ' .jpg '、' jpeg '、' .png '、' ppm '、' .bmp '、' pgm '、' .tif '、' tiff '、'. webp '。 您也可以在一個資料夾中有多個類型的影像。    
    > - 如需詳細資訊，請參閱 [torchvision 資料集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 。
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - 如果使用影像資料集進行計分，則此模組的輸入檔案資料集會包含未分類的影像。
    
3.  提交管線。 此模組可在 GPU 或 CPU 上執行。

## <a name="results"></a>結果

[ **轉換成映射目錄** ] 模組的輸出為影像目錄格式，而且可以連接至其他影像相關的模組，也就是輸入埠格式也是影像目錄。

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
