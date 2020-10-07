---
title: 語音 CLI 基本概念
titleSuffix: Azure Cognitive Services
description: 了解如何透過語音 CLI 命令工具使用語音服務，而完全不需要程式碼且僅需最低限度的設定。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e859ac13c72ed07d3f57da6e61fd6d9f827f0fca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88854891"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>了解語音 CLI 的基本概念

在本文中，您將了解語音 CLI 的基本使用模式，這是一項可讓您直接使用語音服務而無須撰寫程式碼的命令列工具。 您可以快速測試語音服務的主要功能而無須建立開發環境或撰寫任何程式碼，以確認是否足以因應您使用案例的需求。 此外，語音 CLI 已可在生產環境中使用，可使用 `.bat` 或殼層指令碼將語音服務中的簡易工作流程自動化。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本使用方式

本節將說明一些常可運用在首次測試和實驗的基本 SPX 命令。 藉由執行下列命令，開始檢視工具內建的說明。

```shell
spx
```

注意 **請參閱：** 命令參數右側所列的說明主題。 您可以輸入這些命令，以取得子命令的詳細說明。

您可以依關鍵字搜尋說明主題。 例如，輸入下列命令以查看語音 CLI 使用量範例的清單：

```shell
spx help find --topics "examples"
```

輸入下列命令以查看辨識命令的選項：

```shell
spx help recognize
```

現在執行下列命令，使用您的預設麥克風，使用語音服務執行一些語音辨識。

```shell
spx recognize --microphone
```

輸入此命令後，SPX 會開始聽取目前作用中輸入裝置上的音訊，並在您按下 `ENTER` 之後停止聽取。 接著將會辨識錄製的語音，並將其轉換為主控台輸出中的文字。 使用語音 CLI 也可輕易執行文字轉語音的合成。 

執行下列命令時，將會以輸入的文字作為輸入，並將合成的語音輸出至目前作用中的輸出裝置。

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

除了語音辨識和合成以外，您也可以使用語音 CLI 執行語音翻譯。 類似於上述的語音辨識命令，請執行下列命令，從您的預設麥克風擷取音訊，並翻譯成目標語言的文字。

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

在此命令中，您必須指定來源語言 (要**翻譯**的語言) 和目標語言 (要**譯成**的語言)。 使用 `--microphone` 引數時，將會聽取目前作用中輸入裝置上的音訊，並在您按下 `ENTER` 後停止聽取。 輸出是目標語言的文字轉譯，會寫入文字檔中。

> [!NOTE]
> 請參閱[語言和地區設定文章](language-support.md)，其中列出了所有支援的語言及其對應的地區設定代碼。

## <a name="batch-operations"></a>批次作業

上一節中的命令非常適合用來快速查看語音服務的運作方式。 不過，在評估是否能因應您使用案例的需求時，您可能需要對您已有的某些輸入執行批次作業，以確認服務處理各種不同案例的情形。 本節說明如何：

* 對音訊檔案的目錄執行批次語音辨識
* 逐一查看 `.tsv` 檔案，並分批執行文字轉語音的合成

## <a name="batch-speech-recognition"></a>批次語音辨識

如果您有音訊檔案的目錄，使用語音 CLI 將可輕易快速執行批次語音辨識。 只要使用 `--files` 命令指向您的目錄，並執行下列命令即可。 在此範例中，您會將 `\*.wav` 附加至目錄，以辨識所有存在於目錄中的 `.wav` 檔案。 此外，請指定 `--threads` 引數，以 10 個平行的執行緒執行辨識。

> [!NOTE]
> `--threads` 引數也可以在下一節中用於 `spx synthesize` 命令，可用的執行緒數將取決於 CPU 及其目前的負載百分比。

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

識別的語音輸出可使用 `--output file` 引數寫入至 `speech_output.tsv`。 以下是輸出檔案結構的範例。

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>批次文字轉語音的合成

要執行批次文字轉語音，最簡單的方式是建立新的 `.tsv` (定位字元分隔值) 檔案，並在語音 CLI 中使用 `--foreach` 命令。 請考量下列 `text_synthesis.tsv` 檔案：

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 接著，您會執行命令以指向 `text_synthesis.tsv`、對每個 `text` 欄位執行合成，然後將結果以 `.wav` 檔案的形式寫入至對應的 `audio.output` 路徑。 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

此命令相當於對 `.tsv` 檔案中的**每個**記錄執行 `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav`。 請留意以下幾點：

* 資料行標頭 `audio.output` 和 `text` 分別對應至命令列引數 `--audio output` 和 `--text`。 多部分的命令列引數 (例如 `--audio output`) 應在檔案中格式化，不含空格、不含前置的連字號，並且以句點分隔字串，例如 `audio.output`。 任何其他現有的命令列引數都可以使用此模式新增至檔案中，作為其他資料行。
* 以此方式格式化檔案時，不需將其他引數傳至 `--foreach`。
* 請務必以**定位字元**分隔 `.tsv` 中的每個值。

不過，如果您的 `.tsv` 檔案如下列範例所示，且資料行標頭**不符合**命令列引數：

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

您可以在 `--foreach` 呼叫中使用下列語法，將這些欄位名稱覆寫為正確的引數。 此呼叫與前述呼叫相同。

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>後續步驟

* 使用 SDK 完成[語音辨識](./quickstarts/speech-to-text-from-microphone.md)或[語音合成](./quickstarts/text-to-speech.md)快速入門。
