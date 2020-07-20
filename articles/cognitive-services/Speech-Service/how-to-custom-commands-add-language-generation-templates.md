---
title: 如何：使用適用于語音回應的語言產生範本-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何搭配使用語言產生範本與自訂命令。 語言產生範本可讓您自訂傳送給用戶端的回應，並在回應中引進變異數。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308032"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>新增語音回應的語言產生範本

在本文中，您將瞭解如何搭配使用語言產生範本與自訂命令。 語言產生範本可讓您自訂傳送給用戶端的回應，並在回應中引進變異數。 可以藉由下列方式來完成語言產生自訂：

- 語言產生範本的使用
- 使用適應性運算式

## <a name="prerequisites"></a>必要條件

您必須已完成下列文章中的步驟：

> [!div class="checklist"]
> * [如何：使用簡單的命令建立應用程式](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [如何：將參數新增至命令](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>語言產生範本總覽

自訂命令範本是以 BotFramework 的[LG 範本](https://aka.ms/speech/cc-lg-format)為基礎。 由於自訂命令會在必要時建立新的 LG 範本（也就是，針對參數或動作中的語音回應），因此您不需要指定 LG 範本的名稱。 因此，您不需要將範本定義為：

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

您只需要定義不含名稱的範本主體，如下所示。

> [!div class="mx-imgBorder"]
> ![範本編輯器範例](./media/custom-commands/template-editor-example.png)


這種變更引進了傳送給用戶端的語音回應變化。 因此，針對相同的語句，會隨機從提供的選項中挑選對應的語音回應。

利用 LG 範本也可讓您使用適應性運算式為命令定義複雜的語音回應。 如需詳細資訊，您可以參考[LG 範本格式](https://aka.ms/speech/cc-lg-format)。 根據預設，自訂命令支援所有功能，但有下列些許差異：

* 在 LG 範本中，實體會以 $ {entityName} 表示。 在自訂命令中，我們不會使用實體，但參數可以當做其中一個代表 $ {parameterName} 或 {parameterName} 的變數使用
* 自訂命令中不支援範本組合和擴充。 這是因為您永遠不會 `.lg` 直接編輯檔案，而是只會回應自動建立的範本。
* 自訂命令中不支援由 LG 插入的自訂函式。 仍然支援預先定義的函數。
* 自訂命令中不支援選項（strict、replaceNull & lineBreakStyle）。

## <a name="add-template-responses-to-turnonoff-command"></a>將範本回應新增至 TurnOnOff 命令

修改**TurnOnOff**命令，以新增具有下列設定的參數：

| 設定            | 建議的值       | 
| ------------------ | --------------------- | 
| 名稱               | `SubjectContext`         | 
| 為全域          | unchecked             | 
| 必要           | unchecked               | 
| 類型               | String                |
| 預設值      | `all` |
| 設定      | 接受來自內部目錄的預先定義輸入值 | 
| 預先定義的輸入值 | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>修改完成規則

編輯現有完成規則**ConfirmationResponse**的 [**動作**] 區段。 在 [**編輯動作**] 快顯視窗中，切換至 [**範本編輯器**]，並將文字取代為下列範例。

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

依照下列方式**訓練**並**測試**您的應用程式。 請注意，因為使用範本值的多個替代專案，以及使用適應性運算式，而造成的回應變化。

* 輸入：開啟電視
* 輸出： [確定]，開啟電視
* 輸入：開啟電視
* 輸出：完成，已開啟電視
* 輸入：關閉燈
* 輸出： [確定]，關閉所有燈
* 輸入：關閉房間燈
* 輸出：正常，關閉房間燈

## <a name="use-custom-voice"></a>使用自訂語音

自訂自訂命令回應的另一種方式是選取自訂輸出語音。 使用下列步驟，將預設語音切換到自訂語音。

1. 在您的自訂命令應用程式中，從左窗格中選取 [**設定**]。
1. 從中間窗格選取 [**自訂語音**]。
1. 從資料表中選取想要的自訂或公用語音。
1. 選取 [儲存]。

> [!div class="mx-imgBorder"]
> ![含有參數的範例句子](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - 對於**公用語音**，**類神經類型**僅適用于特定區域。 若要檢查可用性，請參閱[依區域/端點的標準和類神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。
> - 針對**自訂**語音，您可以從 [自訂語音專案] 頁面建立它們。 請參閱[開始使用自訂語音](./how-to-custom-voice.md)。

應用程式現在會以選取的語音回應，而不是預設的語音。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用語音 SDK 來整合您的自訂命令](./how-to-custom-commands-setup-speech-sdk.md)。