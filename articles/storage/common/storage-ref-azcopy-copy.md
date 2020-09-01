---
title: azcopy 複製 |Microsoft Docs
description: 本文提供 azcopy copy 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b9d5a9e071cc1b2ac81e8cacea8c974181fbb3b6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070389"
---
# <a name="azcopy-copy"></a>azcopy 複製

將來源資料複製到目的地位置。

## <a name="synopsis"></a>概要

將來源資料複製到目的地位置。 支援的指示如下：

  - 本機 <-> Azure Blob (SAS 或 OAuth 驗證) 
  - 本機 <-> Azure 檔案儲存體 (共用/目錄 SAS 驗證) 
  - 本機 <-> Azure Data Lake Storage Gen 2 (SAS、OAuth 或共用金鑰驗證) 
  - Azure Blob (SAS 或公用) > Azure Blob (SAS 或 OAuth 驗證) 
  - Azure Blob (SAS 或公用) -> Azure 檔案儲存體 (SAS) 
  - Azure 檔案儲存體 (SAS) > Azure 檔案儲存體 (SAS) 
  - Azure 檔案儲存體 (SAS) -> Azure Blob (SAS 或 OAuth 驗證) 
  - Amazon Web Services (AWS) S3 (存取金鑰) -> Azure 區塊 Blob (SAS 或 OAuth 驗證) 

如需詳細資訊，請參閱本文的「範例」一節。

## <a name="related-conceptual-articles"></a>相關的概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="advanced"></a>進階

當您從本機磁片上傳檔案時，AzCopy 會自動偵測檔的內容類型。 AzCopy 會根據副檔名或內容 (來偵測內容類型。如果沒有指定) 的副檔名，則為。

內建的查閱資料表很小，但是在 Unix 上，它會由本機系統的檔案 (s 來增強， `mime.types`) 如果這些名稱有一或多個這些名稱可用：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，會從登錄解壓縮 MIME 類型。 您可以透過旗標的協助來關閉這項功能。 如需詳細資訊，請參閱本文的「旗標」一節。

如果您使用命令列設定環境變數，該變數將可在您的命令列歷程記錄中讀取。 請考慮清除包含命令列歷程記錄之認證的變數。 若要讓變數不會出現在歷程記錄中，您可以使用腳本提示使用者輸入其認證，並設定環境變數。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>範例

使用 OAuth 驗證上傳單一檔案。 如果您尚未登入 AzCopy，請執行 `azcopy login` 命令，然後執行下列命令。

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
與上述相同，但這次也會計算檔案內容的 MD5 雜湊，並將其儲存為 blob 的 Content-MD5 屬性：

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

使用 SAS 權杖上傳單一檔案：

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 權杖上傳單一檔案，並只將 (區塊 blob) ：
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

使用 SAS 權杖上傳整個目錄：
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

或

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

使用 SAS 權杖和萬用字元 ( * ) 字元來上傳一組檔案：
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

使用 SAS 權杖和萬用字元 ( * ) 字元來上傳檔案和目錄：

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

使用 OAuth 驗證下載單一檔案。 如果您尚未登入 AzCopy，請執行 `azcopy login` 命令，然後執行下列命令。

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

使用 SAS 權杖下載單一檔案：

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

使用 SAS 權杖下載單一檔案，然後使用管線將輸出傳送至檔案， (僅限區塊 blob) ：
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

使用 SAS 權杖下載整個目錄：
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

在 Url 中使用萬用字元 ( * ) 的附注：

在 URL 中使用萬用字元只有兩種支援的方式。 

- 您可以直接在 URL 的最後一個正斜線 (/) 之後使用。 使用萬用字元會將目錄中的所有檔案直接複製到目的地，而不會將它們放入子目錄中。 

- 您也可以在容器名稱中使用萬用字元，只要 URL 只參考容器，而不是 blob。 您可以使用這種方法，從容器的子集取得檔案。 

下載目錄的內容，但不復制包含的目錄本身。
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

下載整個儲存體帳戶。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

在容器名稱中使用萬用字元符號 ( * ) ，以在儲存體帳戶內下載容器的子集。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

使用 SAS 權杖，將單一 blob 複製到另一個 blob。

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用 SAS 權杖和驗證權杖，將單一 blob 複製到另一個 blob。 您必須在來源帳戶 URL 的結尾使用 SAS 權杖，但如果您使用命令登入 AzCopy，則目的地帳戶不需要一個 `azcopy login` 。 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

使用 SAS 權杖將一個 blob 虛擬目錄複製到另一個：

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

使用 SAS 權杖將所有 blob 容器、目錄和 blob 從儲存體帳戶複製到另一個：

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

使用存取金鑰和 SAS 權杖，將單一物件從 Amazon Web Services (AWS) S3 複製到 Blob 儲存體。 首先，設定環境變數 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` AWS S3 來源。
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

使用存取金鑰和 SAS 權杖，從 AWS S3 將整個目錄複寫到 Blob 儲存體。 首先，設定環境變數 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` AWS S3 來源。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  請參閱， https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 以進一步瞭解 [資料夾] 預留位置。

使用存取金鑰和 SAS 權杖，從 Amazon Web Services (AWS) 將所有 bucket 複製到 Blob 儲存體。 首先，設定環境變數 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` AWS S3 來源。
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

使用存取金鑰和 SAS 權杖，將所有 bucket 從 Amazon Web Services (AWS) 區域複製到 Blob 儲存體。 首先，設定環境變數 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` AWS S3 來源。
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

在值區名稱中使用萬用字元符號 ( * ) ，以複製 bucket 的子集。 如同先前的範例，您將需要存取金鑰和 SAS 權杖。 請務必設定環境變數 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` AWS S3 來源。

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>選項

**--備份** 啟用 Windows 的上傳 SeBackupPrivilege 或 SeRestorePrivilege for 下載，以允許 AzCopy 查看和讀取所有檔案，不論其檔案系統許可權為何，以及還原擁有權限。 要求執行 AzCopy 的帳戶必須具有這些許可權 (例如，具有系統管理員許可權或 `Backup Operators` 群組) 的成員。 此旗標會啟用帳戶已有的許可權。

**--blob 類型** 字串會定義目的地的 blob 類型。 這可用於上傳 blob，以及在帳戶之間進行複製時 (預設 `Detect`) 。 有效值包括 `Detect`、 `BlockBlob`、 `PageBlob`和 `AppendBlob`。 在帳戶之間進行複製時，的值 `Detect` 會讓 AzCopy 使用來源 blob 的類型來判斷目的地 blob 的類型。 上傳檔案時， `Detect` 會根據副檔名判斷檔案是 VHD 或 VHDX 檔案。 如果檔案正在乙太幣 VHD 或 VHDX 檔案，則 AzCopy 會將檔案視為分頁 blob。  (預設值「偵測」 ) 

**--區塊-blob 層** 字串上傳區塊 blob，以使用此 blob 層 Azure 儲存體。  (預設值為 [無] ) 

**--區塊大小-mb** 浮點數使用此區塊大小 (在) 上傳至 Azure 儲存體，並從 Azure 儲存體下載時指定。 預設值會根據檔案大小自動計算。 允許小數小數 (例如： 0.25) 。

**--cache-控制** 字串設定快取控制標頭。 在下載時傳回。

**--檢查長度** 在傳輸之後，檢查目的地上的檔案長度。 如果來源和目的地之間有不相符的情況，則會將傳輸標示為失敗。  (預設值為 `true`) 

**--check-md5** 字串指定在下載時，應如何驗證嚴格的 md5 雜湊。 只有在下載時才可使用。 可用的選項： `NoCheck` 、 `LogOnly` 、 `FailIfDifferent` 、 `FailIfDifferentOrMissing` 。  (預設值 `FailIfDifferent`)  (預設值 "FailIfDifferent" ) 

**--內容** 配置字串設定內容配置標頭。 在下載時傳回。

**--content-type 編碼** 字串：設定內容編碼標頭。 在下載時傳回。

**--content-type** 字串設定內容語言標頭。 在下載時傳回。

**--content-type** 字串會指定檔案的內容類型。 隱含不猜測 mime 類型。 在下載時傳回。

**--解壓縮** 下載時自動解壓縮檔案（如果其內容編碼表示已壓縮）。 支援的內容編碼值為 `gzip` 和 `deflate` 。 或的副檔名 `.gz` / `.gzip` `.zz` 不是必要的，但如果有的話，將會移除。

**--排除-** 僅限 Windows 的屬性字串 () 排除屬性符合屬性清單的檔案。 例如： A;！R

**--exclude-blob 類型**字串可選擇性地指定 `BlockBlob` /  `PageBlob` /  `AppendBlob` 從容器或帳戶複製 blob 時所要排除的 blob () 類型。 使用此旗標不適用於從非 Azure 服務複製資料至服務。 應以一個以上的 blob 分隔 `;` 。 

**--排除-** 在複製時排除這些路徑的路徑字串。 此選項不支援萬用字元字元 ( * ) 。 檢查相對路徑首碼 (例如： `myFolder;myFolder/subDirName/file.pdf`) 。 搭配帳戶遍歷使用時，路徑不會包含容器名稱。

**--排除-模式** 字串在複製時排除這些檔案。 此選項支援萬用字元字元 ( * ) 。

**--關注-符號連結**  從本機檔案系統上傳時，請遵循符號連結。

**--強制-唯讀** 覆寫 Windows 或 Azure 檔案儲存體上的現有檔案時，即使現有的檔案已設定唯讀屬性，仍強制覆寫工作。

**--from 至** String （選擇性）指定來源目的地的組合。 例如： `LocalBlob` 、 `BlobLocal` 、 `LocalBlobFS` 。

**--**  說明複製的協助。

**--include-** 字串只包含在指定日期/時間或之後修改的檔案。 此值應為 ISO8601 格式。 如果未指定時區，則會假設值為執行 AzCopy 之電腦的當地時區。 例如， `2020-08-19T15:04:00Z` 針對 UTC 時間，或 `2020-08-19` 當地時區的午夜 (00:00) 。 在 AzCopy 10.5 時，此旗標只會套用至檔案，而不會套用至資料夾，因此在搭配或使用此旗標時，不會複製資料夾屬性 `--preserve-smb-info` `--preserve-smb-permissions` 。

**--include-** 僅限 Windows 的屬性字串 () 包含屬性符合屬性清單的檔案。 例如： A;！R

**--include-路徑** 字串在複製時只包含這些路徑。 此選項不支援萬用字元字元 ( * ) 。 檢查相對路徑首碼 (例如： `myFolder;myFolder/subDirName/file.pdf`) 。

**--include-模式** 字串在複製時只會包含這些檔案。 此選項支援萬用字元字元 ( * ) 。 使用來分隔檔案 `;` 。

**--版本清單** 字串會指定檔案，其中每個版本識別碼都會列在個別的行上。 請確定來源必須指向單一 blob，而且使用此旗標在檔案中指定的所有版本識別碼都必須僅屬於來源 blob。 AzCopy 會在提供的目的資料夾中下載指定的版本。 如需詳細資訊，請參閱 [下載舊版的 blob](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob)。

**--記錄層級** 字串會定義記錄檔的記錄詳細資訊、可用層級： (所有要求/回應的資訊) 、警告 (回應緩慢) 、錯誤 (只有失敗的要求) ，以及無 (沒有輸出記錄) 。  (預設 `INFO`) 。 

**--** 使用這些索引鍵/值組作為中繼資料，上傳至 Azure 儲存體的中繼資料字串。

**--不猜測-mime 類型**  防止 AzCopy 根據檔案的副檔名或內容來偵測內容類型。

**--** 如果此旗標設定為 true，則覆寫字串會覆寫目的地的衝突檔案和 blob。  (預設 `true`) 可能的值包括 `true` 、、 `false` `prompt` 和 `ifSourceNewer` 。 若為支援資料夾的目的地，則會覆寫衝突的資料夾層級屬性， `true` 或如果提示提供正面回應，則會覆寫此旗標。  (預設值為 "true" ) 

**--page-blob 層** 字串上傳分頁 blob 至使用此 blob 層 Azure 儲存體。  (預設 `None`) 。  (預設值為 [無] ) 

**--保留-上次修改時間**  只有在目的地為檔案系統時才可使用。

**--保留-擁有**    者   只有在使用時才會有效果，而且只有在使用時才 `--preserve-smb-permissions` 會生效。 如果為 true (預設) ，則會在下載中保留檔案擁有者和群組。 如果設定為 false， `--preserve-smb-permissions` 仍會保留 acl，但擁有者和群組將會以執行 AzCopy 的使用者為基礎， (預設值) 

**--preserve-smb-資訊**   預設為 False。 保留 SMB 屬性資訊 (上次寫入時間、建立時間、在 SMB 感知資源 (Windows 和 Azure 檔案儲存體) 之間) 屬性位。 只會傳輸 Azure 檔案儲存體支援的屬性位;任何其他專案都會被忽略。 此旗標適用于檔案和資料夾，除非已指定僅限檔案的篩選 (例如，包含模式) 。 針對資料夾傳送的資訊與檔案的資訊相同，但絕對不會保留給資料夾的寫入時間除外。

**--preserve-smb-許可權**   預設為 False。 在感知資源之間保留 SMB Acl (Windows 和 Azure 檔案儲存體) 。 若要下載，您也需要 `--backup` 旗標來還原許可權，其中新的擁有者將不會是執行 AzCopy 的使用者。 此旗標適用于檔案和資料夾，除非已指定僅限檔案的篩選 (例如 `include-pattern`) 。

**--put-md5**    建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob 或檔案的 Content-type-MD5 屬性。  (預設不會建立雜湊。只有在上傳時才能使用 ) 。

**--遞迴**    從本機檔案系統上傳時，以遞迴方式查看子目錄。

**--s2s-偵測-來源-已變更**   偵測正在讀取原始程式檔/blob 時是否變更。  (此參數只適用于服務對服務的複本，因為對應的檢查已針對上傳和下載永久啟用。 ) 

**--s2s-控制碼-無效-中繼資料** 字串指定如何處理不正確中繼資料索引鍵。 可用的選項： ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。  (預設 `ExcludeIfInvalid`) 。  (預設值 "ExcludeIfInvalid" ) 

**--s2s-保留-存取層**   保留服務至服務複製期間的存取層。 請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取和封存存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) ，以確保目的地儲存體帳戶支援設定存取層。 在不支援設定存取層的情況下，請使用 s2sPreserveAccessTier = false 來略過複製存取層。  (預設值為 true) 。   (預設值為 true) 

**--s2s-保留-屬性**   保留服務至服務複製期間的完整屬性。 針對 AWS S3 和 Azure 檔案非單一檔案來源，清單作業不會傳回物件和檔案的完整屬性。 為了保留完整的屬性，AzCopy 需要為每個物件或檔案傳送一個額外的要求。  (預設值為 true) 

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps float**   以每秒 mb 數為單位的傳輸速率上限。 時間的輸送量可能會與端點稍有不同。 如果此選項設定為零，或省略，則輸送量不會有上限。

**--** 命令輸出的輸出類型字串格式。 選項包括： text、json。 預設值是 `text`。  (預設的「文字」 ) 

**--受信任-microsoft 尾碼** 字串指定可能傳送 Azure Active Directory 登入權杖的其他網域尾碼。  預設為 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`。 此處所列的任何一種都會新增至預設值。 基於安全性，您應該只在這裡放置 Microsoft Azure 網域。 以分號分隔多個專案。

## <a name="see-also"></a>另請參閱

- [azcopy](storage-ref-azcopy.md)
