---
title: 茲抄刪除 |微軟文檔
description: 本文為 azcopy 刪除命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033988"
---
# <a name="azcopy-remove"></a>azcopy 移除

從 Azure 存儲帳戶中刪除 Blob 或檔。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

使用 SAS 刪除單個 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 刪除整個虛擬目錄：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

僅刪除虛擬目錄中的頂部 blob，但刪除其子目錄：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

刪除虛擬目錄中的 blob 子集（例如：僅 jpg 和 pdf 檔，或者如果 blob 名稱為"精確名稱"）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

刪除整個虛擬目錄，但從作用域中排除某些 Blob（例如：以 foo 開頭或以條形結尾的每個 Blob）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

通過將特定 Blob 和虛擬目錄放在檔中的相對路徑（非 URL 編碼）來刪除它們：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

從具有階層命名空間（不支援包括/排除）的 Blob 存儲帳戶中刪除單個檔。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

刪除具有階層命名空間的 Blob 存儲帳戶的單個目錄（不支援包括/排除）：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>選項。

**--排除路徑字串**     刪除時排除這些路徑。 此選項不支援萬用字元 （*）。 檢查相對路徑首碼。 例如：我的資料夾;我的資料夾/子 Dirname/檔.pdf。

**--排除模式**字串 排除名稱與模式清單匹配的檔。 例如： *.jpg;*.pdf;確切名稱

**-h， -- 説明**刪除

**--包含路徑**字串在刪除時僅包含這些路徑。 此選項不支援萬用字元 （*）。 檢查相對路徑首碼。 例如：我的資料夾;我的資料夾/子 Dirname/檔.pdf

**--包括模式**字串 僅包括名稱與模式清單匹配的檔。 例如： *.jpg;*.pdf;確切名稱

**--檔案清單**字串 定義包含要刪除的檔和目錄清單的檔的位置。 相對路徑應按分行符號分隔，並且路徑不應對 URL 進行編碼。

**--日誌級**字串 定義日誌檔的日誌詳細性。 可用級別包括：INFO（所有請求/回應）、警告（回應緩慢）、ERROR（僅失敗的請求）和 NONE（無輸出日誌）。 （預設"INFO"）（預設"INFO"）

**--遞迴**               在目錄之間同步時，可重複地查看子目錄。

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

|選項|描述|
|---|---|
|--蓋-mbps uint32|以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。|
|--輸出類型字串|命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。|

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
