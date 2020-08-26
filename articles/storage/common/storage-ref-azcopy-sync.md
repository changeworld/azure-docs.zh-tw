---
title: azcopy 同步 |Microsoft Docs
description: 本文提供 azcopy 同步命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ee2f01e1b7771e71afe49c4b69b1fb39e43f37
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869434"
---
# <a name="azcopy-sync"></a>azcopy 同步

將來源位置複製到目的地位置。

## <a name="synopsis"></a>概要

上次修改時間是用來進行比較。 如果目的地中的上次修改時間較新，則會略過該檔案。

支援的配對為：

- 本機 <-> Azure Blob (可以使用 SAS 或 OAuth 驗證) 
- Azure Blob <-> Azure Blob (來源必須包含 SAS 或可公開存取;SAS 或 OAuth 驗證都可用於目的地) 
- Azure File <-> Azure 檔案 (來源必須包含 SAS 或可公開存取;目的地) 應使用 SAS 驗證

Sync 命令不同于 copy 命令的方式有好幾種：

1. 根據預設，遞迴旗標為 true，而且同步會複製所有子目錄。 如果遞迴旗標為 false，則 Sync 只會複製目錄內的最上層檔案。
2. 在虛擬目錄之間進行同步處理時，請將尾端斜線新增至路徑 (參閱範例) 如果 blob 的名稱與其中一個虛擬目錄相同，則為。
3. 如果 `deleteDestination` 旗標設定為 true 或 prompt，則同步處理將會刪除來源端沒有目的地的檔案和 blob。

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

### <a name="advanced"></a>進階

如果您未指定副檔名，AzCopy 會在從本機磁片上傳時，根據副檔名或內容 (自動偵測檔案的內容類型（如果未指定) 的副檔名）。

內建的查閱資料表很小，但是在 Unix 上，它是由本機系統的 mime. 類型檔案 (s 的) （如果可在下列一或多個名稱下使用）來擴充：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，會從登錄解壓縮 MIME 類型。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>範例

同步處理單一檔案：

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

與上述相同，但也會計算檔案內容的 MD5 雜湊，然後將該 MD5 雜湊儲存為 blob 的 Content-MD5 屬性。 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

同步處理整個目錄（包括其子目錄） (請注意，) 上的遞迴預設為：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

或

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

只同步目錄內的檔案，但不同步子目錄中的檔案或子目錄內的檔案：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

同步目錄中的檔案子集 (例如：只有 jpg 和 pdf 檔案，或檔案名為 `exactName`) ：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

同步整個目錄，但從範圍中排除特定檔案 (例如：以 foo 開頭或以 bar) 結尾的每個檔案：

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

同步處理單一 blob：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

同步虛擬目錄：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

將名稱與 blob 相同的虛擬目錄同步 (在路徑中加入尾端的斜線，以區分) ：

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

將 Azure 檔案目錄同步 (與 Blob) 相同的語法：

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 如果同時使用 include/exclude 旗標，則只會查看符合 include 模式的檔案，但會一律忽略符合排除模式的檔案。

## <a name="options"></a>選項

**--區塊大小-mb** 浮點數使用 () 上傳至 Azure 儲存體或從 Azure 儲存體下載時所指定的區塊大小。 預設值會根據檔案大小自動計算。 允許使用小數小數 (例如： `0.25`) 。

**--check-md5** 字串指定在下載時，應如何驗證嚴格的 md5 雜湊。 只有在下載時才可使用此選項。 可用的值包括： `NoCheck` 、 `LogOnly` 、 `FailIfDifferent` 、 `FailIfDifferentOrMissing` 。  (預設 `FailIfDifferent`) 。  (預設 `FailIfDifferent`) 

**--delete-destination** 字串定義是否要刪除來源不存在之目的地的額外檔案。 可以設定為 `true` 、 `false` 或 `prompt` 。 如果設定為 `prompt` ，就會在排程要刪除的檔案和 blob 之前，先詢問使用者問題。  (預設 `false`) 。  (預設 `false`) 

**--排除-** 僅限 Windows 的屬性字串 () 排除屬性符合屬性清單的檔案。 例如：`A;S;R`

**--** 在比較來源與目的地時，排除路徑字串會排除這些路徑。 此選項不支援萬用字元字元 ( * ) 。 檢查相對路徑首碼 (例如： `myFolder;myFolder/subDirName/file.pdf`) 。

**--排除-模式** 字串排除名稱符合模式清單的檔案。 例如：`*.jpg;*.pdf;exactName`

**--**    說明如何進行同步處理。

**--include-** attribute 字串 (僅限 Windows) 只包含其屬性符合屬性清單的檔案。 例如：`A;S;R`

**--include-模式** 字串只包含名稱符合模式清單的檔案。 例如：`*.jpg;*.pdf;exactName`

**--記錄層級** 字串會定義記錄檔的記錄詳細資訊，可用層級： `INFO` (所有要求和回應) 、 `WARNING` (回應緩慢的) 回應、 `ERROR` (僅) 失敗的要求，以及 (不) 的 `NONE` 輸出記錄。  (預設 `INFO`) 。 

**--preserve-smb-資訊**    預設為 False。保留 SMB 屬性資訊 (上次寫入時間、建立時間、在 SMB 感知資源 (Windows 和 Azure 檔案儲存體) 之間) 屬性位。此旗標適用于檔案和資料夾，除非已指定僅限檔案的篩選 (例如，包含模式) 。針對資料夾所傳送的資訊與檔案的資訊相同，但不會保留給資料夾的最後寫入時間除外。

**--preserve-smb-許可權**    預設為 False。在感知資源之間保留 SMB Acl (Windows 和 Azure 檔案儲存體) 。此旗標適用于檔案和資料夾，除非已指定僅限檔案的篩選 (例如  `include-pattern`) 。

**--put-md5**     建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob 或檔案的 Content-type-MD5 屬性。  (預設不會建立雜湊。只有在上傳時才能使用 ) 。

**--遞迴** `True`依預設，會在目錄之間進行同步處理時，以遞迴方式查看子目錄。      (預設 `True`) 。 

**--s2s-保留-存取層**  保留服務至服務複製期間的存取層。 請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取和封存存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) ，以確保目的地儲存體帳戶支援設定存取層。 在不支援設定存取層的情況下，請使用 s2sPreserveAccessTier = false 來略過複製存取層。  (預設 `true`) 。 

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

|選項|描述|
|---|---|
|--cap-mbps uint32|以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。|
|--output 類型字串|命令輸出的格式。 選項包括： text、json。 預設值為 "text"。|
|--受信任-microsoft-尾碼字串   |指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設值為 '*. core.windows.net;*。core.chinacloudapi.cn;*. core.cloudapi.de;*。core.usgovcloudapi.net '。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。|

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
