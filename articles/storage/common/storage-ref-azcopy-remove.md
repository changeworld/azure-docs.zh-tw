---
title: azcopy remove |Microsoft Docs
description: 本文提供 azcopy remove 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d28a20a6afc78939491e56f46a32782b4c0844cc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878998"
---
# <a name="azcopy-remove"></a>azcopy 移除

從 Azure 儲存體帳戶中刪除 blob 或檔案。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](./storage-use-azcopy-v10.md#transfer-data)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

使用 SAS 權杖來移除單一 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 權杖來移除整個虛擬目錄：
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

只移除虛擬目錄內的 blob，但不要移除這些子目錄內的任何子目錄或 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

移除虛擬目錄中的 blob 子集 (例如：僅移除 jpg 和 pdf 檔案，或如果 blob 名稱為 `exactName`) ：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

移除整個虛擬目錄，但從範圍中排除特定的 blob (例如：以 foo 開頭或以 bar) 結尾的每個 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

藉由將特定 blob 和虛擬目錄放在檔案中， (不是 URL 編碼的) ，來移除它們：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
從具有階層命名空間 (包含/排除不支援) 的 Blob 儲存體帳戶中移除單一檔案：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

從具有階層命名空間 (包含/排除不支援) 的 Blob 儲存體帳戶移除單一目錄：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>選項。

**--delete-** 依預設，如果 blob 有快照集，刪除作業將會失敗。 指定 `include` 以移除根 blob 及其所有快照集，或者指定 `only` 只移除快照集，但保留根 blob。

**--排除-** 移除時排除這些路徑的路徑字串。 此選項不支援萬用字元字元 ( * ) 。 檢查相對路徑前置詞。 例如：`myFolder;myFolder/subDirName/file.pdf`

**--排除-模式** 字串排除名稱符合模式清單的檔案。 例如： `*.jpg` ; `*.pdf` ;`exactName`

**--強制-唯讀**   刪除 Azure 檔案儲存體的檔案或資料夾時，即使現有的物件已設定唯讀屬性，仍要強制刪除工作。

**--**   說明移除的協助。

**--include-路徑** 字串在移除時只會包含這些路徑。 此選項不支援萬用字元字元 ( * ) 。 檢查相對路徑前置詞。 例如：`myFolder;myFolder/subDirName/file.pdf`

**--include-模式** 字串只包含名稱符合模式清單的檔案。 例如： *`.jpg` ;* `.pdf` ;`exactName`

**--檔案清單** 字串會定義檔案的位置，其中包含要刪除的檔案和目錄清單。 相對路徑應該以分行符號分隔，而且路徑不應以 URL 編碼。 

**--版本清單** 字串會指定檔案，其中每個版本識別碼都會列在個別的行上。 請確定來源必須指向單一 blob，而且使用此旗標在檔案中指定的所有版本識別碼都必須僅屬於來源 blob。 將從 Azure 儲存體中刪除指定 blob 的指定版本識別碼。 

**--記錄層級** 字串會定義記錄檔的記錄詳細程度。 可用的層級包括： `INFO` (所有的要求/回應) 、 `WARNING` (慢速回應) 、 `ERROR` (只) 失敗的要求，以及 (不) 的 `NONE` 輸出記錄。  (預設值 `INFO`)  (預設 `INFO`) 

**--遞迴**    在目錄之間進行同步處理時，以遞迴方式查看子目錄。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps float|以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。|
|--output 類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--受信任-microsoft-尾碼字串   |指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
