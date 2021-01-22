---
title: 語音 CLI 設定選項 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何建立及管理要與 Azure 語音 CLI 搭配使用的設定檔。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540223"
---
# <a name="speech-cli-configuration-options"></a>Speech CLI 設定選項

語音 CLI 的行為可依賴設定檔中的設定，您可以使用 `@` 符號加以參考。 語音 CLI 會將新的設定儲存到新的 `./spx/data` 子目錄 (會建立在語音 CLI 的目前工作目錄中)。 尋找設定值時，語音 CLI 會先搜尋您目前的工作目錄，然後再依序搜尋 `./spx/data` 中的資料存放區，以及其他資料存放區，包括 `spx` 二進位檔案中最終的唯讀資料存放區。 

在語音 CLI 快速入門中，您已使用資料存放區來儲存 `@key` 和 `@region` 值，因此不需要使用每個 `spx` 命令來進行指定。 請記住，您可以使用設定檔來儲存自己的組態設定，或甚至使用其來傳遞在執行階段產生的 URL 或其他動態內容。

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>建立和管理資料存放區中的設定檔

本節說明如何使用本機資料存放區中的設定檔，進而使用 `spx config` 來儲存和擷取命令設定，以及使用 `--output` 選項來儲存語音 CLI 的輸出。

下列範例會清除 `@my.defaults` 組態檔、在檔案中新增 **key** 和 **region** 的索引鍵/值組，並且在對 `spx recognize` 的呼叫中使用此組態。

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

您也可以將動態內容寫入至組態檔。 例如，下列命令會建立自訂語音模型，並將新模型的 URL 儲存在組態檔。中。 下一個命令會等到位於該 URL 的模型可供使用再傳回。

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

下列範例會將兩個 URL 寫入至 `@my.datasets.txt` 組態檔。 在此案例中，`--output` 可以包含選擇性 **和**  關鍵字，以建立組態檔或附加至現有檔案。


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

如需資料存放區檔案的詳細資訊，包括使用預設組態檔 (`@spx.default`、`@default.config` 和 `@*.default.config` 作為命令特定的預設設定)，請輸入下列命令：

```console
spx help advanced setup
```

## <a name="next-steps"></a>後續步驟 

* [使用語音 CLI 的批次作業](./spx-batch-operations.md)