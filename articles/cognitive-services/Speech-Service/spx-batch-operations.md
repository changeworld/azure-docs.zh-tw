---
title: 語音 CLI 批次作業 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 CLI 來執行批次的語音轉換文字 (語音辨識)、批次的文字轉換語音 (語音合成)。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540220"
---
# <a name="speech-cli-batch-operations"></a>Speech CLI 執行批次作業

使用 Azure 語音服務時的常見工作是批次作業。 在本文中，您將了解如何使用語音 CLI 來執行批次的語音轉換文字 (語音辨識)、批次的文字轉換語音 (語音合成)。 特別是，您將學到如何：

* 對音訊檔案的目錄執行批次語音辨識
* 逐一查看 `.tsv` 檔案來執行批次的語音合成

## <a name="batch-speech-to-text-speech-recognition"></a>批次的語音轉換文字 (語音辨識)

語音服務常會用來辨識音訊檔案中的語音。 在此範例中，您將了解如何使用語音 CLI 逐一查看目錄，以擷取每個 `.wav` 檔案的辨識輸出。 `--files` 旗標可用來指向音訊檔案儲存所在的目錄，而萬用字元 `*.wav` 則可用來告訴語音 CLI 在每個副檔名為 `.wav` 的檔案上執行辨識。 每個辨識檔案的輸出都會在 `speech_output.tsv` 中撰寫為定位字元分隔值。

> [!NOTE]
> `--threads` 引數也可以在下一節中用於 `spx synthesize` 命令，可用的執行緒數將取決於 CPU 及其目前的負載百分比。

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

以下是輸出檔案結構的範例。

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>批次的文字轉換語音 (語音合成)

要執行批次文字轉語音，最簡單的方式是建立新的 `.tsv` (定位字元分隔值) 檔案，並在語音 CLI 中使用 `--foreach` 命令。 您可以使用慣用的文字編輯器來建立 `.tsv` 檔案，在此範例中，我們將其稱為 `text_synthesis.tsv`：

>[!IMPORTANT]
> 在複製此文字檔的內容時，請確定檔案中的檔案位置與文字之間有 **定位字元** 而非空格。 有時在從這個範例複製內容時，定位字元會轉換為空格，而導致 `spx` 命令在執行時失敗。

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

接著，您會執行命令以指向 `text_synthesis.tsv`、對每個 `text` 欄位執行合成，然後將結果以 `.wav` 檔案的形式寫入至對應的 `audio.output` 路徑。

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

此命令相當於對 `.tsv` 檔案中的 **每個** 記錄執行 `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav`。

請留意以下幾點：

* 資料行標頭 `audio.output` 和 `text` 分別對應至命令列引數 `--audio output` 和 `--text`。 多部分的命令列引數 (例如 `--audio output`) 應在檔案中格式化，不含空格、不含前置的連字號，並且以句點分隔字串，例如 `audio.output`。 任何其他現有的命令列引數都可以使用此模式新增至檔案中，作為其他資料行。
* 以此方式格式化檔案時，不需將其他引數傳至 `--foreach`。
* 請務必以 **定位字元** 分隔 `.tsv` 中的每個值。

不過，如果您的 `.tsv` 檔案如下列範例所示，且資料行標頭 **不符合** 命令列引數：

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

您可以在 `--foreach` 呼叫中使用下列語法，將這些欄位名稱覆寫為正確的引數。 此呼叫與前述呼叫相同。

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>後續步驟

* [Speech CLI 概觀](./spx-overview.md)
* [Speech CLI 快速入門](./spx-basics.md)
