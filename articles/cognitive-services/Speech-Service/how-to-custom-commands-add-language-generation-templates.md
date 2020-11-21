---
title: 如何：使用適用于語音回應的語言產生範本-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何搭配使用語言產生範本和自訂命令。 語言產生範本可讓您自訂傳送給用戶端的回應，並在回應中引進變異數。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020941"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>新增語音回應的語言產生範本

在本文中，您將瞭解如何搭配使用語言產生範本和自訂命令。 語言產生範本可讓您自訂傳送給用戶端的回應，並在回應中引進變異數。 您可以透過下列方式來達成語言產生自訂：

- 使用語言產生範本
- 使用自我調整運算式

## <a name="prerequisites"></a>必要條件

您必須已完成下列文章中的步驟：

> [!div class="checklist"]
> * [How To：使用簡單的命令來建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：將參數加入至命令](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>語言產生範本總覽

自訂命令範本是根據 BotFramework 的 [LG 範本](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)。 由於自訂命令會在必要時建立新的 LG 範本 (也就是說，針對參數或動作中的語音回應) 您不需要指定 LG 範本的名稱。 因此，請不要將您的範本定義為：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

您只需要定義沒有名稱的範本主體，如下所示。

> [!div class="mx-imgBorder"]
> ![範本編輯器範例](./media/custom-commands/template-editor-example.png)


這種變更會對傳送至用戶端的語音回應帶來變化。 因此，在相同的語句中，對應的語音回應會從提供的選項中隨機挑選。

利用 LG 範本也可讓您使用適應性運算式來定義命令的複雜語音回應。 如需詳細資料，您可以參考 [LG 範本格式](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) 。 自訂命令預設支援所有功能，但有下列差異：

* 在 LG 範本中，實體會以 $ {entityName} 表示。 在自訂命令中，我們不會使用實體，但參數可以用來做為具有其中一個標記法 $ {parameterName} 或 {parameterName} 的變數
* 自訂命令中不支援範本撰寫和展開。 這是因為您永遠不會 `.lg` 直接編輯檔案，只會回應自動建立的範本。
* 自訂命令中不支援 LG 插入的自訂函數。 仍支援預先定義的函數。
* 自訂命令中不支援 (strict，replaceNull & lineBreakStyle) 的選項。

## <a name="add-template-responses-to-turnonoff-command"></a>將範本回應新增至 TurnOnOff 命令

修改 **TurnOnOff** 命令以新增具有下列設定的新參數：

| 設定            | 建議的值       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| 為全域          | unchecked             | 
| 必要           | unchecked               | 
| 類型               | String                |
| 預設值      | `all` |
| 組態      | 接受來自內部目錄的預先定義輸入值 | 
| 預先定義的輸入值 | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>修改完成規則

編輯現有完成規則 **ConfirmationResponse** 的 [**動作**] 區段。 在 [ **編輯動作** ] 快顯視窗中，切換至 [ **範本編輯器** ]，並將文字取代為下列範例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

依照下列方式 **定型** 和 **測試** 您的應用程式。 請注意，回應的變化是因為使用了多個範本值的替代專案，而且也使用了引數運算式。

* 輸入：開啟電視
* 輸出：確定，開啟電視
* 輸入：開啟電視
* 輸出：已完成，已開啟電視
* 輸入：關閉燈光
* 輸出：確定，關閉所有燈光
* 輸入：關閉房間燈
* 輸出：確定，關閉房間燈

## <a name="use-custom-voice"></a>使用自訂語音

自訂自訂命令回應的另一種方式是選取自訂輸出語音。 使用下列步驟將預設語音切換為自訂語音。

1. 在您的自訂命令應用程式中，從左窗格中選取 [ **設定** ]。
1. 從中間窗格中選取 [ **自訂語音** ]。
1. 從資料表中選取所需的自訂或公用語音。
1. 選取 [儲存]。

> [!div class="mx-imgBorder"]
> ![具有參數的範例句子](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - 針對 **公用語音**， **類神經類型** 只適用于特定區域。 若要檢查可用性，請參閱 [依區域/端點的標準和類神經語音](./regions.md#standard-and-neural-voices)。
> - 針對 **自訂語音**，您可以從 [自訂語音專案] 頁面建立它們。 請參閱 [自訂語音的開始](./how-to-custom-voice.md)。

現在應用程式將會以選取的語音回應，而不是預設的語音。

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [使用語音 SDK 整合您的自訂命令](./how-to-custom-commands-setup-speech-sdk.md)。