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
ms.date: 05/26/2020
ms.openlocfilehash: 677cf60ff3e614fd1486445786154fbf026b7cd9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898692"
---
# <a name="convert-to-image-directory"></a>轉換至映像目錄

本文說明如何使用 [轉換成映射目錄] 模組，協助將映射資料集轉換成「映射目錄」資料類型，這是影像相關工作（例如 Azure Machine Learning 表設計工具中的影像分類）的標準化資料格式。

## <a name="how-to-use-convert-to-image-directory"></a>如何使用轉換成影像目錄  

1.  將 [ **轉換成映射目錄** ] 模組新增至您的實驗。 您可以在模組清單中的「電腦視覺/影像資料轉換」類別中找到此模組。 

2.  [註冊映射資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) ，並將它連接到模組輸入埠。 請確認輸入資料集中有影像。 
    支援下列資料集格式：

    - 這些延伸模組中的壓縮檔案： ' .zip '、' tar '、'. gz '、'. bz2 '。
    - 包含映射的資料夾。 **強烈建議您先壓縮這類資料夾，然後使用壓縮檔案作為資料集**。

    > [!WARNING]
    > 您 **無法** 使用 [匯 **入資料** ] 模組匯入映射資料集，因為 [匯 **入資料** ] 模組的輸出類型是資料框架目錄，它只包含檔案路徑字串。
    

    > [!NOTE]
    > 如果在受監督的學習中使用影像資料集，則需要標籤。
    > 若為影像分類工作，如果此影像資料集是以 torchvision ImageFolder 格式組織，則標籤會在模組輸出中產生為影像「類別」。 否則，只會儲存沒有標籤的影像。 以下範例說明如何組織影像資料集以取得標籤、使用影像類別目錄做為子資料夾名稱。 如需詳細資訊，請參閱 [torchvision 資料集](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) 。
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
