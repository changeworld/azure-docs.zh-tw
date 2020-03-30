---
title: 阿茲比恩恩夫 |微軟文檔
description: 本文為 azcopy env 命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033753"
---
# <a name="azcopy-env"></a>azcopy 環境

顯示可以配置 AzCopy 行為的環境變數。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 如果使用命令列設置環境變數，則該變數將在命令列歷史記錄中可讀。 請考慮清除包含命令列歷史記錄中憑據的變數。 要防止變數出現在歷史記錄中，可以使用腳本提示使用者輸入其憑據，並設置環境變數。

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="options"></a>選項。

|選項|描述|
|--|--|
|-h, --help|顯示 env 命令的説明內容。 |
|--對顯示敏感|顯示敏感/機密環境變數。|

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

|選項|描述|
|---|---|
|--蓋-mbps uint32|以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。|
|--輸出類型字串|命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。|

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
