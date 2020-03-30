---
title: 茲比斯同步 |微軟文檔
description: 本文為茲複製同步命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988238"
---
# <a name="azcopy-sync"></a>azcopy 同步

將源位置複製到目標位置。

## <a name="synopsis"></a>概要

上次修改的時間用於比較。 如果目標中最後一次修改的時間較新，則跳過該檔。

支援的對包括：

- 本地<> Azure Blob（可以使用 SAS 或 OAuth 身份驗證）
- Azure Blob <-> Azure Blob（源必須包含 SAS 或可公開訪問;SAS 或 OAuth 身份驗證可用於目標）
- Azure 檔<-> Azure 檔（源必須包含 SAS 或可公開訪問;SAS 身份驗證應用於目標）

同步命令與複製命令在以下幾方面有所不同：

1. 預設情況下，遞迴標誌為 true，並同步複製所有子目錄。 僅當遞迴標誌為 false 時，僅同步將複製目錄中的頂級檔。
2. 在虛擬目錄之間同步時，如果存在與虛擬目錄之一同名的 Blob，則向路徑添加尾隨斜杠（請參閱示例）。
3. 如果"刪除目的地"標誌設置為 true 或提示，則同步將刪除源中不存在的目的檔案和 Blob。

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

### <a name="advanced"></a>進階

如果不指定檔副檔名，則 AzCopy 會根據檔副檔名或內容（如果未指定副檔名）從本地磁片上載時自動檢測檔的內容類型。

內置查閱資料表很小，但在 Unix 上，它由本地系統的 mime.type 檔（如果可在一個或多個名稱下）增強：

- /等/mime.類型
- /等/apache2/mime.類型
- /等/apache/mime.類型

在 Windows 上，從註冊表中提取 MIME 類型。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>範例

同步單個檔：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

與上面相同，但這次也計算檔內容的 MD5 雜湊並將其保存為 blob 的 Content-MD5 屬性：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步整個目錄，包括其子目錄（請注意，預設情況下遞迴處於打開狀態）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

僅同步目錄中的頂部檔，但不同步其子目錄：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目錄中的檔子集（例如：僅 jpg 和 pdf 檔，或者如果檔案名為"精確名稱"）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

同步整個目錄，但從作用域中排除某些檔（例如：以 foo 開頭或以條形結尾的每個檔）：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

同步單個 Blob：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

同步虛擬目錄：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

同步與 Blob 同名的虛擬目錄（向路徑添加尾隨斜杠以消除歧義）：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

同步 Azure 檔目錄（與 Blob 相同的語法）：

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 如果一起使用包含/排除標誌，則只查看與包含模式匹配的檔，但始終忽略那些與排除模式匹配的檔。

## <a name="options"></a>選項。

**--塊大小-mb**浮動 在上載到 Azure 存儲或從 Azure 存儲下載時使用此塊大小（在 MiB 中指定）。 預設值根據檔案大小自動計算。 允許小數分數（例如：0.25）。

**--檢查 md5**字串 指定下載時應嚴格驗證 MD5 雜湊。 此選項僅在下載時可用。 可用值包括：無檢查、僅日誌、失敗（如果已轉移）、失敗轉移或缺失。 （預設為"失敗"。 （預設"失敗"）

**--刪除目標**字串 定義是否從源中不存在的目標中刪除額外檔。 可以設置為真、假或提示。 如果設置為提示，將在計畫檔和 blob 進行刪除之前向使用者提問。 （預設為"false"）。 （預設"假"）

**--排除屬性**字串（僅限 Windows） 排除其屬性與屬性清單匹配的檔。 例如：A;S;R

**--外**字串複製時排除這些路徑。 此選項不支援萬用字元 （*）。 檢查相對路徑首碼（例如：我的資料夾;我的資料夾/子 DirName/file.pdf）。 當與帳戶遍歷結合使用時，路徑不包括容器名稱。

**--排除模式**字串 排除名稱與模式清單匹配的檔。 例如： \*.jpg;\*.pdf;確切名稱

**-h， -- 説明**同步

**--包含屬性**字串（僅限 Windows） 僅包括其屬性與屬性清單匹配的檔。 例如：A;S;R

**--包括模式**字串 僅包括名稱與模式清單匹配的檔。 例如： \*.jpg;\*.pdf;確切名稱

**--日誌級**字串 定義日誌檔的日誌詳細性、可用級別：INFO（所有請求和回應）、警告（慢速回應）、ERROR（僅失敗的請求）和 NONE（無輸出日誌）。 （預設資訊）。 （預設"INFO"）

**--put-md5**                    創建每個檔的 MD5 雜湊，並將雜湊保存為目標 blob 或檔的內容-MD5 屬性。 （預設情況下不會創建雜湊。僅在上載時可用。

**--遞迴**                  預設情況下為 true，在目錄之間同步時，遞迴地查看子目錄。 （預設為 true）。 （預設為 true）

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

|選項|描述|
|---|---|
|--蓋-mbps uint32|以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。|
|--輸出類型字串|命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。|

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
