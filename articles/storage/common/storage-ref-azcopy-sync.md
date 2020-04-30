---
title: azcopy 同步 |Microsoft Docs
description: 本文提供 azcopy 同步命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086023"
---
# <a name="azcopy-sync"></a>azcopy 同步

將來源位置複製到目的地位置。

## <a name="synopsis"></a>概要

上次修改時間是用來進行比較。 如果目的地中上次修改時間較新，就會略過該檔案。

支援的配對如下：

- 本機 <-> Azure Blob （可以使用 SAS 或 OAuth 驗證）
- Azure Blob <-> Azure Blob （來源必須包含 SAS 或可公開存取; SAS 或 OAuth 驗證可以用於目的地）
- Azure 檔案 <-> Azure 檔案（來源必須包含 SAS 或可公開存取;SAS 驗證應用於目的地）

Sync 命令與 copy 命令的差異有好幾種：

1. 根據預設，遞迴旗標為 true，同步會複製所有子目錄。 如果遞迴旗標為 false，同步只會複製目錄中的最上層檔案。
2. 在虛擬目錄之間進行同步處理時，如果有 blob 的名稱與其中一個虛擬目錄相同，請在路徑中加上尾端斜線（請參閱範例）。
3. 如果 ' deleteDestination ' 旗標設為 true 或 prompt，則 sync 會刪除來源上不存在之目的地的檔案和 blob。

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

### <a name="advanced"></a>進階

如果您未指定副檔名，則 AzCopy 會根據副檔名或內容（如果未指定副檔名）從本機磁片上傳時，自動偵測檔案的內容類型。

內建查閱資料表很小，但是在 Unix 上，它會透過本機系統的 mime. 類型檔案來增強（如果有一或多個這些名稱可用的話）：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 類型會從登錄中解壓縮。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>範例

同步處理單一檔案：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> 目的地 blob*必須*存在。 用`azcopy copy`來複製目的地中尚未存在的單一檔案。 否則，會發生下列錯誤： `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`。

與上述相同，但這次也會計算檔案內容的 MD5 雜湊，並將它儲存為 blob 的 Content-MD5 屬性：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步整個目錄（包括其子目錄）（請注意，遞迴預設為開啟）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

只同步目錄中的前幾個檔案，而不是它的子目錄：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目錄中的檔案子集（例如：只有 jpg 和 pdf 檔案，或檔案名為 "exactName"）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整個目錄，但排除範圍中的某些檔案（例如：以 foo 或結尾為 bar 的每個檔案）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

同步處理單一 blob：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

同步處理虛擬目錄：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

同步與 blob 同名的虛擬目錄（將尾端斜線新增至路徑以區分）：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

同步處理 Azure 檔案目錄（與 Blob 相同的語法）：

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 如果同時使用包含/排除旗標，則只會查看符合 include 模式的檔案，但是符合排除模式的檔一律會被忽略。

## <a name="options"></a>選項

--當上傳至 Azure 儲存體或從 Azure 儲存體下載時，**區塊大小-mb** float 使用此區塊大小（以 MiB 指定）。 預設值會根據檔案大小自動計算。 允許小數分數（例如：0.25）。

**--check-md5**字串指定在下載時應如何驗證嚴格的 md5 雜湊。 只有在下載時，才可以使用此選項。 可用的值包括： NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 （預設值為 ' FailIfDifferent '）。 （預設值為 "FailIfDifferent"）

**--delete-destination** string 定義是否要從目的地刪除不存在於來源的額外檔案。 可以設定為 true、false 或 prompt。 如果設定為 [提示]，則會在排程檔案和 blob 以供刪除之前，詢問使用者一個問題。 （預設值為 ' false '）。 （預設值為 "false"）

**--exclude-** attribute 字串（僅限 Windows）排除其屬性符合屬性清單的檔案。 例如： A;今日R

**--exclude-** 在複製時排除這些路徑。 此選項不支援萬用字元（*）。 檢查相對路徑前置詞（例如： myFolder; myFolder/subDirName/file .pdf）。 與帳戶遍歷搭配使用時，路徑不會包含容器名稱。

**--排除-模式**字串排除名稱符合模式清單的檔案。 例如： \*.jpg;\*.Pdf; exactName

**-h、--** 適用于同步處理的協助說明

**--include-屬性**字串（僅限 Windows）只包含屬性符合屬性清單的檔案。 例如： A;今日R

**--include-模式**字串只包含名稱符合模式清單的檔案。 例如： \*.jpg;\*.Pdf; exactName

**--記錄層級**字串會定義記錄檔的記錄詳細資訊、可用的層級： INFO （所有要求和回應）、警告（回應緩慢）、錯誤（僅限失敗的要求）和無（沒有輸出記錄）。 （預設資訊）。 （預設值為 "INFO"）

**--put-md5**                    建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob 或檔案的 Content-MD5 屬性。 （根據預設，不會建立雜湊）。只有在上傳時才可使用。

**--遞迴**                  True 根據預設，在目錄之間同步時，會以遞迴方式查看子目錄。 （預設值為 true）。 （預設值為 true）

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。|
|--output-類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
