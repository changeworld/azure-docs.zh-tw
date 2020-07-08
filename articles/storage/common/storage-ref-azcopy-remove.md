---
title: azcopy 移除 |Microsoft Docs
description: 本文提供 azcopy remove 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221074"
---
# <a name="azcopy-remove"></a>azcopy 移除

從 Azure 儲存體帳戶中刪除 blob 或檔案。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="examples"></a>範例

移除具有 SAS 的單一 blob：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

移除具有 SAS 的整個虛擬目錄：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

只移除虛擬目錄中的最上層 blob，而不是其子目錄：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

移除虛擬目錄中的 blob 子集（例如：只有 jpg 和 pdf 檔案，或如果 blob 名稱是 "exactName"）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

移除整個虛擬目錄，但從範圍中排除特定的 blob （例如：以 foo 或結尾為 bar 的每個 blob）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

將特定的 blob 和虛擬目錄放在檔案中，藉以移除它們的相對路徑（未進行 URL 編碼）：

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

從具有階層式命名空間的 Blob 儲存體帳戶中移除單一檔案（包含/排除不受支援）。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

移除具有階層命名空間之 Blob 儲存體帳戶的單一目錄（包含/排除不受支援）：

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>選項

**--delete-快照**集字串根據預設，如果 blob 有快照集，刪除作業就會失敗。 指定 ' include ' 以移除根 blob 及其所有快照集;或者，指定 ' only ' 只移除快照集，但保留根 blob。

**--exclude-** 移除時，請排除這些路徑。 此選項不支援萬用字元（*）。 檢查相對路徑前置詞。 例如： myFolder; myFolder/subDirName/file.pdf。

**--排除-模式**字串排除名稱符合模式清單的檔案。 例如： *.jpg;*。pdf; exactName

**--force-if-唯讀**   刪除 Azure 檔案儲存體的檔案或資料夾時，即使現有的物件已設定唯讀屬性，仍強制刪除作業

**-h、--** 適用于移除的協助說明

**--include-路徑**字串只包含移除時的這些路徑。 此選項不支援萬用字元（*）。 檢查相對路徑前置詞。 例如： myFolder; myFolder/subDirName/file.pdf

**--include-模式**字串只包含名稱符合模式清單的檔案。 例如： *.jpg;*。pdf; exactName

**--files 檔案清單**會定義檔案的位置，其中包含要刪除之檔案和目錄的清單。 相對路徑應以分行符號分隔，且路徑不應以 URL 編碼。

**--記錄層級**字串會定義記錄檔的記錄詳細資訊。 可用的層級包括：資訊（所有要求/回應）、警告（回應緩慢）、錯誤（僅限失敗的要求）和無（沒有輸出記錄）。 （預設的「資訊」）（預設值為 "INFO"）

**--遞迴**               在目錄之間同步時，以遞迴方式查看子目錄。

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|說明|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--trusted-microsoft-尾碼字串   |指定可在其中傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處列出的任何內容都會新增至預設值。 基於安全性，您應該只將 Microsoft Azure 網域放在這裡。 以分號分隔多個專案。|

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
