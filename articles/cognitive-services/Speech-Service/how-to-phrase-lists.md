---
title: 片語清單-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用物件來提供語音服務的片語清單， `PhraseListGrammar` 以改善語音轉換文字的辨識結果。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1e0b345eb4f1d6b3ab8ba917794b6878180ac558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320943"
---
# <a name="phrase-lists-for-speech-to-text"></a>語音轉換文字的片語清單

藉由提供語音服務的片語清單，您可以改善語音辨識的精確度。 片語清單可用來識別音訊資料中的已知片語，例如人員的姓名或特定位置。

例如，如果您有一個「移至」命令，以及可能被說出的「Ward」可能的目的地，您可以新增「移至 Ward」的專案。 新增片語將會增加辨識音訊時，將會辨識「移至 Ward」，而不是「移至」的可能性。

您可以將單字或完整片語新增至片語清單中。 在辨識期間，如果整個片語的完全相符專案以個別片語的形式包含在音訊中，則會使用片語清單中的專案。 如果找不到與片語完全相符的項目，就不會協助辨識。

>[!Note]
> 目前，片語清單僅支援英文的語音轉換文字。

## <a name="how-to-use-phrase-lists"></a>如何使用片語清單

下列範例說明如何使用物件建立片語清單 `PhraseListGrammar` 。

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

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 語音服務將用來比對語音的片語清單數目上限為1024個片語。

您也可以藉 `PhraseListGrammar` 由呼叫 clear ( # A1 來清除與相關聯的片語。

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

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> 對物件所做 `PhraseListGrammar` 的變更會在下一次辨識時生效，或在重新連接到語音服務之後生效。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)
