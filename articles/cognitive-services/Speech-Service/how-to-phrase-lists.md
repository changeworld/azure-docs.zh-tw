---
title: 短語清單 ─ 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用`PhraseListGrammar`物件為語音服務提供短語清單,以改善語音到文本的識別結果。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401974"
---
# <a name="phrase-lists-for-speech-to-text"></a>語音到文字的短語清單

通過向語音服務提供短語清單,可以提高語音辨識的準確性。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。

例如,如果您有一個命令「移動到」和「Ward」的可能目的地,則可以添加「移動到」的項目。 添加短語會增加當音訊被識別時,將識別"移動到病房"而不是"向"的可能性。

您可以將單字或完整片語新增至片語清單中。 在識別過程中,如果整個短語的完全匹配作為單獨的短語包含在音訊中,則使用短語清單中的條目。 如果找不到與片語完全相符的項目，就不會協助辨識。

>[!Note]
> 目前,短語清單僅支持語音到文本的英語。

## <a name="how-to-use-phrase-lists"></a>如何使用短語清單

下面的範例說明了如何使用`PhraseListGrammar`物件構建短語清單。

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 語音服務用於匹配語音的最大短語清單數為 1024 個短語。

您還可以透過調用 clear()`PhraseListGrammar`清除與 關聯的短語。

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> 對`PhraseListGrammar`物件的更改將生效於下一個識別或重新連接到語音服務之後。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 文件](speech-sdk.md)
