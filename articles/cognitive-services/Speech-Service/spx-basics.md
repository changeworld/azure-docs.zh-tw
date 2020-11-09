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
ms.openlocfilehash: 7ca290b7b7df9e4e03aec78b611361ba52942523
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286677"
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

在此命令中，您必須指定來源語言 (要 **翻譯** 的語言) 和目標語言 (要 **譯成** 的語言)。 使用 `--microphone` 引數時，將會聽取目前作用中輸入裝置上的音訊，並在您按下 `ENTER` 後停止聽取。 輸出是目標語言的文字轉譯，會寫入文字檔中。

> [!NOTE]
> 請參閱[語言和地區設定文章](language-support.md)，其中列出了所有支援的語言及其對應的地區設定代碼。

### <a name="configuration-files-in-the-datastore"></a>資料存放區中的組態檔

語音 CLI 的行為可依賴組態檔中的設定，您可以在語音 CLI 呼叫中使用 @ 符號加以參考。
語音 CLI 會將新的設定儲存於其在目前工作目錄中建立的新 `./spx/data` 子目錄。
搜尋設定值時，語音 CLI 會依序在您目前的工作目錄中和 `./spx/data` 路徑中查看，然後再查看其他資料存放區，包括 `spx` 二進位檔案中最終的唯讀資料存放區。
先前，您已使用資料存放區來儲存 `@key` 和 `@region` 值，因此不需要使用每個命令列呼叫來進行指定。
您也可以使用組態檔來儲存自己的組態設定，或甚至使用其來傳遞在執行階段產生的 URL 或其他動態內容。

本節說明如何使用本機資料存放區中的組態檔，進而使用 `spx config` 來儲存和擷取命令設定，以及使用 `--output` 選項來儲存語音 CLI 的輸出。

下列範例會清除 `@my.defaults` 組態檔、在檔案中新增 **key** 和 **region** 的索引鍵/值組，並且在對 `spx recognize` 的呼叫中使用此組態。

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

您也可以將動態內容寫入至組態檔。 例如，下列命令會建立自訂語音模型，並將新模型的 URL 儲存在組態檔。中。 下一個命令會等到位於該 URL 的模型可供使用再傳回。

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

下列範例會將兩個 URL 寫入至 `@my.datasets.txt` 組態檔。
在此案例中，`--output` 可以包含選擇性 **和**  關鍵字，以建立組態檔或附加至現有檔案。


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

如需資料存放區檔案的詳細資訊，包括使用預設組態檔 (`@spx.default`、`@default.config` 和 `@*.default.config` 作為命令特定的預設設定)，請輸入下列命令：

```shell
spx help advanced setup
```

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

此命令相當於對 `.tsv` 檔案中的 **每個** 記錄執行 `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav`。 請留意以下幾點：

* 資料行標頭 `audio.output` 和 `text` 分別對應至命令列引數 `--audio output` 和 `--text`。 多部分的命令列引數 (例如 `--audio output`) 應在檔案中格式化，不含空格、不含前置的連字號，並且以句點分隔字串，例如 `audio.output`。 任何其他現有的命令列引數都可以使用此模式新增至檔案中，作為其他資料行。
* 以此方式格式化檔案時，不需將其他引數傳至 `--foreach`。
* 請務必以 **定位字元** 分隔 `.tsv` 中的每個值。

不過，如果您的 `.tsv` 檔案如下列範例所示，且資料行標頭 **不符合** 命令列引數：

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

* 使用語音 CLI 完成[語音辨識](get-started-speech-to-text.md?pivots=programmer-tool-spx)或[語音合成](get-started-text-to-speech.md?pivots=programmer-tool-spx)快速入門。
