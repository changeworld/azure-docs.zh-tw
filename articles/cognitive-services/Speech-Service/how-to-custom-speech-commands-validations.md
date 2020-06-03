---
title: 在自訂命令預覽中新增驗證-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何將驗證新增至自訂命令預覽應用程式中的命令參數。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310405"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>將驗證新增至自訂命令預覽應用程式中的命令參數

在本文中，您將瞭解如何將驗證新增至參數，並提示更正。

## <a name="prerequisites"></a>Prerequisites

完成下列文章中的步驟：

> [!div class="checklist"]
 
> * [快速入門：建立自訂命令預覽應用程式](./quickstart-custom-speech-commands-create-new.md)
> * [快速入門：使用參數來建立自訂命令預覽應用程式](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>建立 SetTemperature 命令

若要示範驗證，請建立可讓使用者設定溫度的新命令。

1. 在[語音 Studio](https://speech.microsoft.com/)中，開啟您所建立的自訂命令預覽應用程式。
1. 建立新的**SetTemperature**命令。
1. 新增具有下列設定的溫度參數：

   | 參數設定           | 建議的值    |描述                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **名稱**              | **低**                       | 參數的描述性名稱                                |
   | **必要**          | 已檢查                           | 指出是否需要此參數值的核取方塊，才能完成命令 |
   | **必要參數的回應**     | **簡單編輯器-> 您想要的溫度為何？**  | 當不知道此參數的值時，所要求的提示 |
   | **類型**              | **Number**                            | 參數的類型，例如 Number、String、DateTime 或 Geography   |

1. 新增溫度參數的驗證。

    1. 在 [溫度] 參數的 [**參數**設定] 頁面中，選取 **[驗證] 區段中**的 [**新增驗證**]。

    1. 在 [**新增驗證**] 快顯視窗中，設定驗證，如下所示：
  
       | 參數設定         | 建議的值                                          | 描述                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **最小值**        | **60**               | 對於 Number 參數，這個參數可以採用的最小值 |
       | **最大值**        | **80**               | 對於 Number 參數，這個參數可以採用的最大值 |
       | **失敗回應-簡單編輯器**| **第一種變化-抱歉，我只能設定60到80度**      | 如果驗證失敗，提示要求輸入新值                                       |

       > [!div class="mx-imgBorder"]
       > ![新增範圍驗證](media/custom-speech-commands/validations-add-temperature.png)

1. 選取 [建立]。

1. 新增一些範例句子。

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 新增具有下列設定的完成規則。 此規則會確認結果。

   | 設定    | 建議的值                                           |描述                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名稱       | 確認訊息                                      |描述規則用途的名稱 |
   | **條件** | **必要參數-溫度**                       |判斷規則何時可執行檔條件    |   
   | **動作**    | **傳送語音回應-確定，將溫度設定為 {溫度} 度** | 規則條件為 true 時要採取的動作 |

> [!TIP]
> 這個範例會使用語音回應來確認結果。 如需使用用戶端動作完成命令的範例，請參閱[如何：使用語音 SDK 在用戶端上完成命令](./how-to-custom-speech-commands-fulfill-sdk.md)。

## <a name="try-it-out"></a>試試看

1. 選取 [訓練]。

1. 定型完成之後，請選取 [**測試**]，然後嘗試下列互動：

    - 輸入：將溫度設定為72度
    - 輸出：正常，將溫度設定為72度
    - 輸入：將溫度設定為45度
    - 輸出：抱歉，我只能設定60到80度
    - 輸入：請改為72度
    - 輸出：正常，將溫度設定為72度

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在自訂命令預覽應用程式中將確認新增至命令](./how-to-custom-speech-commands-confirmations.md)
