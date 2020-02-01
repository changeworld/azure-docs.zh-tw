---
title: azcopy 複製 |Microsoft Docs
description: 本文提供 azcopy copy 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b9ac15e6909498c38f618a24be6b010dc2774b07
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905497"
---
# <a name="azcopy-copy"></a>azcopy 複製

將來源資料複製到目的地位置。

## <a name="synopsis"></a>摘要

將來源資料複製到目的地位置。 支援的指示如下：

  - 本機 <-> Azure Blob （SAS 或 OAuth 驗證）
  - 本機 <-> Azure 檔案儲存體（共用/目錄 SAS 驗證）
  - 本機 <-> ADLS Gen 2 （SAS、OAuth 或 SharedKey authentication）
  - Azure Blob （SAS 或公用）-> Azure Blob （SAS 或 OAuth 驗證）
  - Azure Blob （SAS 或公用）-> Azure 檔案儲存體（SAS）
  - Azure 檔案儲存體（SAS）-> Azure 檔案儲存體（SAS）
  - Azure 檔案儲存體（SAS）-> Azure Blob （SAS 或 OAuth 驗證）
  - AWS S3 （存取金鑰）-> Azure 區塊 Blob （SAS 或 OAuth 驗證）

如需詳細資訊，請參閱範例。

## <a name="related-conceptual-articles"></a>相關的概念性文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 儲存體傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="advanced"></a>進階

從本機磁片上傳時，AzCopy 會根據副檔名或內容（如果未指定副檔名），自動偵測檔案的內容類型。

內建查閱資料表很小，但是在 Unix 上，它會透過本機系統的 mime. 類型檔案來擴充（如果有一或多個這些名稱可使用的話）：

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

在 Windows 上，MIME 類型會從登錄中解壓縮。 您可以使用旗標的協助來關閉這項功能。 請參閱旗標一節。

如果您使用命令列設定環境變數，該變數將可在您的命令列歷程記錄中讀取。 請考慮從您的命令列歷程記錄中清除包含認證的變數。 若要讓變數不會出現在歷程記錄中，您可以使用腳本來提示使用者輸入其認證，並設定環境變數。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>範例

使用 OAuth 驗證上傳單一檔案。 如果您尚未登入 AzCopy，請先執行 AzCopy login 命令，然後再執行下列命令。

- azcopy cp "/path/to/file.txt" "HTTPs：//[account]. net/[container]/[path/to/blob]"

與上述相同，但這次也會計算檔案內容的 MD5 雜湊，並將它儲存為 blob 的 Content-MD5 屬性：

- azcopy cp "/path/to/file.txt" "HTTPs：//[account]. .net/[container]/[path/to/blob]"--put-md5

使用 SAS 權杖上傳單一檔案：

- azcopy cp "/path/to/file.txt" "HTTPs：//[account]. net/[container]/[path/to/blob]？[SAS] "

使用 SAS 權杖和管線（僅限區塊 blob）上傳單一檔案：
  
- 貓 "/path/to/file.txt" |azcopy cp "HTTPs：//[account]. core. net/[container]/[path/to/blob]？[SAS] "

使用 SAS 權杖上傳整個目錄：
  
- azcopy cp "/path/to/dir" "HTTPs：//[帳戶]. net/[container]/[路徑/到/目錄]？[SAS] "--recursive = true

或

- azcopy cp "/path/to/dir" "HTTPs：//[帳戶]. net/[container]/[路徑/到/目錄]？[SAS] "--遞迴 = true--put-md5

使用 SAS 權杖和萬用字元（*）字元來上傳一組檔案：

- azcopy cp "/path/*foo/* bar/* .pdf" "HTTPs：//[account]. .net/[container]/[路徑/到/目錄]？[SAS] "

使用 SAS 權杖和萬用字元（*）字元來上傳檔案和目錄：

- azcopy cp "/path/*foo/* bar *" "HTTPs：//[account]. net/[container]/[路徑/到/目錄]？[SAS] "--recursive = true

使用 OAuth 驗證來下載單一檔案。 如果您尚未登入 AzCopy，請先執行 AzCopy login 命令，然後再執行下列命令。

- azcopy cp "HTTPs：//[account]. core. net/[container]/[path/to/blob]" "/path/to/file.txt"

使用 SAS 權杖下載單一檔案：

- azcopy cp "HTTPs：//[account]. core. net/[container]/[path/to/blob]？[SAS] ""/path/to/file.txt "

使用 SAS 權杖下載單一檔案，然後將輸出輸送至檔案（僅限區塊 blob）：
  
- azcopy cp "HTTPs：//[account]. core. net/[container]/[path/to/blob]？[SAS] ">"/path/to/file.txt "

使用 SAS 權杖下載整個目錄：
  
- azcopy cp "HTTPs：//[account]. .net/[container]/[路徑/到/目錄]？[SAS] ""/path/to/dir "--recursive = true

在 Url 中使用萬用字元（*）的注意事項：

在 URL 中使用萬用字元僅有兩種支援的方式。 

- 您可以在 URL 的最後一個正斜線（/）正後方使用一個。 這樣會將目錄中的所有檔案直接複製到目的地，而不會將它們放入子目錄中。

- 您也可以在容器的名稱中使用一個，只要 URL 只會參考容器，而不是 blob。 您可以使用這個方法，從容器的子集取得檔案。

下載目錄的內容，而不復制包含的目錄本身。

- azcopy cp "HTTPs：//[srcaccount]. core. net/[container]/[path/to/folder]/*？[SAS] ""/path/to/dir "

下載整個儲存體帳戶。

- azcopy cp "HTTPs：//[srcaccount]. .net/" "/path/to/dir"--遞迴

在容器名稱中使用萬用字元符號（*），以在儲存體帳戶內下載容器的子集。

- azcopy cp "HTTPs：//[srcaccount]. .net/[container * name]" "/path/to/dir"--遞迴

使用 SAS 權杖，將單一 blob 複製到另一個 blob。

- azcopy cp "HTTPs：//[srcaccount]. net/[container]/[path/to/blob]？[SAS] "" HTTPs：//[destcontainer]. .net/[container]/[path/to/blob]？[SAS] "

使用 SAS 權杖和 OAuth 權杖，將單一 blob 複製到另一個 blob。 您必須在來源帳戶 URL 的結尾使用 SAS 權杖，但如果您使用 AzCopy login 命令登入 AzCopy，則目的地帳戶不需要一個。 

- azcopy cp "HTTPs：//[srcaccount]. net/[container]/[path/to/blob]？[SAS] "" HTTPs：//[destcontainer]. .net/[container]/[path/to/blob] "

使用 SAS 權杖將一個 blob 虛擬目錄複製到另一個：

- azcopy cp "HTTPs：//[srcaccount]. .net/[容器]/[路徑/到/目錄]？[SAS] "" HTTPs：//[destcontainer]. .net/[容器]/[路徑/到/目錄]？[SAS] "--recursive = true

使用 SAS 權杖，將所有 blob 容器、目錄和 blob 從儲存體帳戶複製到另一個：

- azcopy cp "HTTPs：//[srcaccount]. core. net？[SAS] "" HTTPs：//[destcontainer]. core. net？[SAS] "--recursive = true

使用存取金鑰和 SAS 權杖，將單一物件從 Amazon Web Services （AWS） S3 複製到 Blob 儲存體。 首先，設定 AWS S3 來源的環境變數 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。
  
- azcopy cp "https://s3.amazonaws.com/ [bucket]/[物件]" "HTTPs：//[destcontainer]. p. p. p. p. p. p. p. p. l/[container]/[path/to blob]？[SAS] "

使用存取金鑰和 SAS 權杖，將整個目錄複寫到 AWS S3 的 Blob 儲存體。 首先，設定 AWS S3 來源的環境變數 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ [bucket]/[folder]" "HTTPs：//[destcontainer]. net/[container]/[路徑/到/目錄]？[SAS] "--recursive = true

請參閱 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html ，以進一步瞭解 [資料夾] 預留位置。

使用存取金鑰和 SAS 權杖，將所有值區複製到 Amazon Web Services （AWS）中的 Blob 儲存體。 首先，設定 AWS S3 來源的環境變數 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ " "HTTPs：//[destcontainer]. .net？[SAS] "--recursive = true

使用存取金鑰和 SAS 權杖，從 Amazon Web Services （AWS）區域將所有 bucket 複製到 Blob 儲存體。 首先，設定 AWS S3 來源的環境變數 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3- [region]. amazonaws .com/" "HTTPs：//[destcontainer]. core. net？[SAS] "--recursive = true

使用值區名稱中的萬用字元符號（*），複製值區的子集。 如同先前的範例，您將需要存取金鑰和 SAS 權杖。 請務必為 AWS S3 來源設定環境變數 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

- azcopy cp "https://s3.amazonaws.com/ [bucket * name]/" "HTTPs：//[destcontainer]. .net？[SAS] "--recursive = true

## <a name="options"></a>選項

**--blob 類型**字串會定義目的地的 blob 類型。 這是用來上傳 blob，以及在帳戶之間進行複製時（預設值為 ' 偵測 '）。 有效值包括 ' 偵測 '、' BlockBlob '、' PageBlob ' 和 ' AppendBlob '。 在帳戶之間進行複製時，' 偵測 ' 的值會導致 AzCopy 使用來源 blob 的類型來決定目的地 blob 的類型。 上傳檔案時，「偵測」會根據副檔名判斷檔案是否為 VHD 或 VHDX 檔案。 如果檔案是乙太幣 VHD 或 VHDX 檔案，AzCopy 會將檔案視為分頁 blob。 （預設值為 "偵測"）

**--區塊-blob 層**字串上傳區塊 blob 到使用此 blob 層的 Azure 儲存體。 （預設值為 "None"）

--當上傳至 Azure 儲存體並從 Azure 儲存體下載時，**區塊大小-mb** float 使用此區塊大小（以 MiB 指定）。 預設值會根據檔案大小自動計算。 允許小數分數（例如：0.25）。

**--** 快取控制字元串設定了快取控制標頭。 在下載時傳回。

**--check-length**                        在傳送後檢查目的地上的檔案長度。 如果來源與目的地之間有不相符的情況，則會將傳輸標示為失敗。 （預設值為 true）

**--check-md5**字串指定在下載時應如何驗證嚴格的 md5 雜湊。 只有在下載時才可使用。 可用的選項： NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing。 （預設值為 "FailIfDifferent"）

**--內容**配置字串設定內容配置標頭。 在下載時傳回。

**--內容編碼**字串設定內容編碼標頭。 在下載時傳回。

**--content-語言**字串設定內容語言標頭。 在下載時傳回。

**--content 類型**字串指定檔案的內容類型。 意指不猜測 mime 類型。 在下載時傳回。

**--解壓縮**                          下載時，如果檔案的內容編碼指示已壓縮，則會自動解壓縮檔案。 支援的內容編碼值為 [gzip] 和 [deflate]。 '. Gz '/' gzip ' 或 '. zz ' 的副檔名不是必要項，但如果存在，將會予以移除。

**--exclude-** attribute 字串（僅限 Windows）排除其屬性符合屬性清單的檔案。 例如： A;今日R

**--exclude-blob 類型**字串可選擇性地指定從容器或帳戶複製 blob 時要排除的 blob 類型（BlockBlob/PageBlob/AppendBlob）。 使用此旗標不適用於將資料從非 azure 服務複製到服務。 一個以上的 blob 應以 '; ' 分隔。

**--exclude-** 在複製時排除這些路徑。 此選項不支援萬用字元（*）。 檢查相對路徑前置詞（例如： myFolder; myFolder/subDirName/file .pdf）。 與帳戶遍歷搭配使用時，路徑不會包含容器名稱。

**--exclude-模式**字串在複製時排除這些檔案。 此選項支援萬用字元（*）

**--跟隨-符號連結**                     從本機檔案系統上傳時，請遵循符號連結。

**--from-to**字串選擇性地指定來源目的地組合。 例如： LocalBlob、BlobLocal、LocalBlobFS。

**-h、--** 適用于複製的說明協助

**--include-屬性**字串（僅限 Windows）包含其屬性符合屬性清單的檔案。 例如： A;今日R

**--include-path**字串只包含複製時的這些路徑。 此選項不支援萬用字元（*）。 檢查相對路徑前置詞（例如： myFolder; myFolder/subDirName/file .pdf）。

**--include-模式**字串只會在複製時包含這些檔案。 此選項支援萬用字元（*）。 使用 '; ' 來分隔檔案。

**--記錄層級**字串會定義記錄檔的記錄詳細資訊、可用的層級： INFO （所有要求/回應）、警告（回應緩慢）、錯誤（僅限失敗的要求）和無（沒有輸出記錄）。 （預設值為 "INFO"）

**--中繼資料**字串上傳至以這些索引鍵/值組做為中繼資料的 Azure 儲存體。

**--不猜測-mime 類型**                  防止 AzCopy 根據檔案的副檔名或內容來偵測內容類型。

**--** 如果此旗標設定為 true，則覆寫字串會覆寫目的地上的衝突檔案和 blob。 可能的值包括 ' true '、' false '、' ifSourceNewer ' 和 ' prompt '。 （預設值為 "true"）

**--分頁-blob 層**字串上傳分頁 blob 至使用此 blob 層的 Azure 儲存體。 （預設值為 "None"）

**--保留-上次修改時間**         只有當目的地是檔案系統時才可使用。

**--put-md5**                            建立每個檔案的 MD5 雜湊，並將雜湊儲存為目的地 blob 或檔案的 Content-MD5 屬性。 （根據預設，不會建立雜湊）。只有在上傳時才可使用。

**--遞迴**                           從本機檔案系統上傳時，會以遞迴方式查看子目錄。

**--s2s-偵測-來源-已變更**          檢查列舉之後，來源是否已變更。

**--s2s-handle-無效-中繼資料**字串指定如何處理不正確中繼資料索引鍵。 可用的選項： ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid。 （預設值為 "ExcludeIfInvalid"）

**--s2s-保留-存取層**            在服務對服務複製期間保留存取層。 請參閱[Azure Blob 儲存體：經常性、非經常性和封存存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，以確保目的地儲存體帳戶支援設定存取層。 在不支援設定存取層的情況下，請使用 s2sPreserveAccessTier = false 來略過複製存取層。 （預設值為 true）

**--s2s-保留-屬性**             保留服務到服務複製期間的完整屬性。 針對 AWS S3 和 Azure 檔案非單一檔案來源，清單作業不會傳回物件和檔案的完整屬性。 若要保留完整的屬性，AzCopy 必須針對每個物件或檔案傳送一個額外的要求。 （預設值為 true）

## <a name="options-inherited-from-parent-commands"></a>繼承自父命令的選項

**--cap-mbps uint32**     上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**--輸出-** 命令輸出的類型字串格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

## <a name="see-also"></a>請參閱

- [azcopy](storage-ref-azcopy.md)
