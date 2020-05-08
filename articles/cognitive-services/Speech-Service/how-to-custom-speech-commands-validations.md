---
title: 如何：將驗證新增至自訂命令參數
titleSuffix: Azure Cognitive Services
description: 在本文中，我們會說明如何將驗證新增至自訂命令中的參數。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857206"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>如何：將驗證新增至自訂命令參數（預覽）

在本文中，您會將驗證新增至參數，並提示更正。

## <a name="prerequisites"></a>Prerequisites

您必須已完成下列文章中的步驟：

> [!div class="checklist"]
> * [快速入門：建立自訂命令](./quickstart-custom-speech-commands-create-new.md)
> * [快速入門：建立具有參數的自訂命令](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>建立 SetTemperature 命令

為了示範驗證，讓我們建立可讓使用者設定溫度的新命令。

1. 在[語音 Studio](https://speech.microsoft.com/)中開啟您先前建立的自訂命令應用程式
1. 建立新的命令`SetTemperature`
1. 新增目標溫度的參數。

   | 參數設定           | 建議的值    |描述                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | 名稱              | 溫度                       | 參數的描述性名稱                                |
   | 必要          | checked                           | 指出是否需要此參數值的核取方塊，才能完成命令 |
   | 必要參數的回應     | 簡單編輯器-> 您想要的溫度為何？  | 當不知道此參數的值時，所要求的提示 |
   | 類型              | 數字                            | 參數類型，例如數位、字串、日期時間或地理位置   |

1. 新增溫度參數的驗證。

    - 在`Temperature`參數的 [**參數**設定] 頁面中`Add a validation` ，從 [驗證] 區段中選取。
    - 如下所示，在**新的驗證**快顯視窗中填入值，然後選取 [**建立**]。

  
       | 參數設定         | 建議的值                                          | 描述                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | 最小值        | 60               | 對於 Number 參數，這個參數可以採用的最小值 |
       | 最大值        | 80               | 對於 Number 參數，這個參數可以採用的最大值 |
       | 失敗回應-簡單編輯器| 第一種變化-抱歉，我只能設定60到80度      | 如果驗證失敗，提示要求輸入新值                                       |

       > [!div class="mx-imgBorder"]
       > ![新增範圍驗證](media/custom-speech-commands/validations-add-temperature.png)

1. 新增一些範例句子

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 新增完成規則以確認結果

   | 設定    | 建議的值                                           |描述                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名稱       | 確認訊息                                      |描述規則用途的名稱 |
   | 條件 | 必要參數-`Temperature`                       |判斷規則何時可執行檔條件    |   
   | 動作    | 傳送語音回應-`Ok, setting temperature to {Temperature} degrees` | 規則條件為 true 時要採取的動作 |

> [!TIP]
> 這個範例會使用語音回應來確認結果。 如需使用用戶端動作完成命令的範例，請參閱： how [To：使用語音 SDK 在用戶端上執行命令](./how-to-custom-speech-commands-fulfill-sdk.md)。


## <a name="try-it-out"></a>試試看
1. 選取`Train`右窗格頂端的 [圖示]。

1. 訓練完成後，請選取`Test`並嘗試一些互動。

    - 輸入：將溫度設定為72度
    - 輸出：正常，將溫度設定為72度
    - 輸入：將溫度設定為45度
    - 輸出：抱歉，我只能設定60到80度
    - 輸入：請改為72度
    - 輸出：正常，將溫度設定為72度

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：在自訂命令中新增確認（預覽）](./how-to-custom-speech-commands-confirmations.md)
