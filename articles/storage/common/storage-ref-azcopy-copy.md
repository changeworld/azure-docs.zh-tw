---
title: 茲抄本*微軟文檔
description: 本文為 azcopy 複製命令提供了參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933781"
---
# <a name="azcopy-copy"></a>azcopy 複製

將來源資料複製到目標位置。

## <a name="synopsis"></a>概要

將來源資料複製到目標位置。 支援的方向包括：

  - 本地<> Azure Blob（SAS 或身份驗證）
  - 本地<> Azure 檔（共用/目錄 SAS 身份驗證）
  - 本地<> ADLS 第 2 代（SAS、OAuth 或共用金鑰身份驗證）
  - Azure Blob（SAS 或公共） -> Azure Blob（SAS 或身份驗證）
  - Azure Blob（SAS 或公共） -> Azure 檔 （SAS）
  - Azure 檔 （SAS） -> Azure 檔 （SAS）
  - Azure 檔 （SAS） -> Azure Blob（SAS 或身份驗證）
  - AWS S3（訪問金鑰） -> Azure 塊 Blob（SAS 或身份驗證）

有關詳細資訊，請參閱示例。

## <a name="related-conceptual-articles"></a>相關概念文章

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存儲傳輸資料](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)

## <a name="advanced"></a>進階

根據檔副檔名或內容（如果未指定副檔名），從本地磁片上載時，AzCopy 會自動檢測檔的內容類型。

內置查閱資料表很小，但在 Unix 上，它由本地系統的 mime.type 檔（如果可在以下一個或多個名稱下）增強：

- /等/mime.類型
- /等/apache2/mime.類型
- /等/apache/mime.類型

在 Windows 上，從註冊表中提取 MIME 類型。 此功能可以在標誌的説明下關閉。 請參閱標誌部分。

如果使用命令列設置環境變數，則該變數將在命令列歷史記錄中可讀。 請考慮清除包含命令列歷史記錄中憑據的變數。 要防止變數出現在歷史記錄中，可以使用腳本提示使用者輸入其憑據，並設置環境變數。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>範例

使用 OAuth 身份驗證上載單個檔。 如果您尚未登錄到 AzCopy，請在運行以下命令之前運行 azcopy 登錄命令。

- azcopy cp"/路徑/到/file.txt" "HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/blob]"

與上述相同，但這次還計算檔內容的 MD5 雜湊並將其保存為 blob 的 Content-MD5 屬性：

- azcopy cp"/路徑/到/file.txt" "HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/blob]" -- put-md5

使用 SAS 權杖上載單個檔：

- 茲複製 cp "/路徑/到/file.txt" "HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/blob]？[SAS]"

使用 SAS 權杖和管道上載單個檔（僅限阻止 blob）：
  
- 貓"/路徑/到/檔.txt" |azcopy cp"HTTPs：//_帳戶]blob.core.net/[容器]/[路徑/到/blob]？[SAS]"

使用 SAS 權杖上載整個目錄：
  
- 茲比比 cp "/路徑/到/dir" "HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" -- 遞迴_true

或

- 茲比比 cp "/路徑/到/dir" "HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" -- 遞迴=真實 -put-md5

使用 SAS 權杖和萬用字元 （*） 上傳一組檔：

- 茲比比 cp "/路徑 */foo/bar/\.pdf"HTTPs：//* 帳戶_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]"

使用 SAS 權杖和萬用字元 （*） 來上載檔和目錄：

- 茲比比 cp "/路徑 */foo/bar_"* HTTPs：//帳戶_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" -- 遞迴_true

使用 OAuth 身份驗證下載單個檔。 如果您尚未登錄到 AzCopy，請在運行以下命令之前運行 azcopy 登錄命令。

- azcopy cp"HTTPs：//_account_blob.core.net/[容器]/[路徑/到/blob]""/路徑/到/檔.txt"

使用 SAS 權杖下載單個檔：

- azcopy cp"HTTPs：//_帳戶]blob.core.net/[容器]/[路徑/到/blob]？[SAS]" "/路徑/到/檔.txt"

使用 SAS 權杖下載單個檔，然後將輸出管道到檔（僅限阻止 Blob）：
  
- azcopy cp"HTTPs：//_帳戶]blob.core.net/[容器]/[路徑/到/blob]？[SAS]">"/路徑/檔.txt"

使用 SAS 權杖下載整個目錄：
  
- azcopy cp"HTTPs：//_帳戶]blob.core.net/[容器]/[路徑/到/目錄]？[SAS]" "/路徑/到/dir" -- 遞迴=真實

有關在 URL 中使用萬用字元 （*） 的說明：

在 URL 中使用萬用字元的方法只有兩種支援。 

- 您可以在 URL 的最終前斜杠 （/） 之後使用。 這將將目錄中的所有檔直接複製到目標，而不會將它們放入子目錄中。

- 只要 URL 僅引用容器而不是 Blob，也可以在容器的名稱中使用一個容器。 可以使用此方法從容器子集獲取檔。

下載目錄的內容，而不復制包含目錄本身。

- azcopy cp"HTTPs：//srcaccount_blob.core.windows.net/[容器]/[路徑/到/資料夾]？[SAS]" "/路徑/到/dir"

下載整個存儲帳戶。

- azcopy cp"HTTPs：//srcaccount_blob.core.net/" /路徑/到/dir" -- 遞迴

使用容器名稱中的萬用字元符號 （*） 下載存儲帳戶中的容器子集。

- azcopy cp"HTTPs：//srcaccount_blob.core.net/[容器]名稱""/路徑/到/dir" -- 遞迴

使用 SAS 權杖將單個 Blob 複製到另一個 Blob。

- azcopy cp"HTTPs：//srcaccount_blob.core.windows.net/[容器]/[路徑/到/blob]？[SAS]" "HTTPs：//_destaccount_blob.core.windows.net/[容器]/[路徑/到/blob]？[SAS]"

使用 SAS 權杖和 OAuth 權杖將單個 Blob 複製到另一個 Blob。 您必須在源帳戶 URL 的末尾使用 SAS 權杖，但如果使用 azcopy 登錄命令登錄到 AzCopy，則目標帳戶不需要。 

- azcopy cp"HTTPs：//srcaccount_blob.core.windows.net/[容器]/[路徑/到/blob]？[SAS]" "HTTPs：//_destaccount_blob.core.windows.net/[容器]/[路徑/到/blob]"

使用 SAS 權杖將一個 Blob 虛擬目錄複製到另一個 blob 虛擬目錄：

- azcopy cp"HTTPs：//srcaccount_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" "HTTPs：//_destaccount_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" -- 遞迴_true

使用 SAS 權杖將所有 Blob 容器、目錄和 Blob 從存儲帳戶複製到另一個帳戶：

- 茲比比 cp "HTTPs：//srcaccount_blob.core.windows.net？[SAS]" "HTTPs：//_destaccount_blob.core.windows.net？[SAS]" -- 遞迴_true

使用訪問金鑰和 SAS 權杖從 Amazon Web 服務 （AWS） S3 將單個物件複製到 Blob 存儲。 首先，設置 AWS S3 源的環境變數AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY。
  
- azcopy cphttps://s3.amazonaws.com/" [存儲桶]/[物件]"HTTPs：//_destaccount_blob.core.windows.net/[容器]/[路徑/到/blob]？[SAS]"

使用訪問金鑰和 SAS 權杖從 AWS S3 將整個目錄複寫到 Blob 存儲。 首先，設置 AWS S3 源的環境變數AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY。

- azcopy cphttps://s3.amazonaws.com/" [存儲桶]/[資料夾]"HTTPs：//_destaccount_blob.core.windows.net/[容器]/[路徑/到/目錄]？[SAS]" -- 遞迴_true

請參考https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html以更好地瞭解 [資料夾] 預留位置。

使用訪問金鑰和 SAS 權杖將所有存儲桶複製到從 Amazon Web 服務 （AWS） 到 Blob 存儲。 首先，設置 AWS S3 源的環境變數AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY。

- 茲比比https://s3.amazonaws.com/cp" "HTTPs：//_destaccount_blob.core.windows.net？[SAS]" -- 遞迴_true

使用訪問金鑰和 SAS 權杖，將所有存儲桶複製到來自 Amazon Web 服務 （AWS） 區域的 Blob 存儲。 首先，設置 AWS S3 源的環境變數AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY。

- azcopy cphttps://s3-" [地區]amazonaws.com/" HTTPs：//_destaccount_blob.core.net？[SAS]" -- 遞迴_true

使用存儲桶名稱中的萬用字元符號 （*） 複製存儲桶子集。 與前面的示例一樣，您需要訪問金鑰和 SAS 權杖。 請確保為 AWS S3 源設置環境變數AWS_ACCESS_KEY_ID和AWS_SECRET_ACCESS_KEY。

- azcopy cphttps://s3.amazonaws.com/" [bucket]name}/" HTTPs：//_destaccount_blob.core.net？[SAS]" -- 遞迴_true

## <a name="options"></a>選項。

**--blob 類型**字串 定義目標上的 blob 類型。 這用於上載 blob 和在帳戶之間複製時（預設的"檢測"）。 有效值包括"檢測"、"塊 Blob"、"PageBlob"和"附加Blob"。 在帳戶之間複製時，值"檢測"會導致 AzCopy 使用源 blob 的類型來確定目標 blob 的類型。 上傳檔時，"檢測"根據檔副檔名確定檔是 VHD 還是 VHDX 檔。 如果檔是 VHD 或 VHDX 檔，AzCopy 會將該檔視為頁面 blob。 （預設"檢測"）

**--塊 blob 層**字串 將塊直接上載到您選擇的[訪問層](../blobs/storage-blob-storage-tiers.md)。 （預設為"無"）。 有效值包括"無"、"熱"、"酷"和"存檔"。 如果傳遞"無"或未傳遞層，Blob 將繼承存儲帳戶的層。

**--塊大小-mb**浮動 在上載到 Azure 存儲並從 Azure 存儲下載時使用此塊大小（在 MiB 中指定）。 預設值根據檔案大小自動計算。 允許小數分數（例如：0.25）。

**--緩存控制**字串 設置緩存控制標頭。 下載時返回。

**--檢查長度**                        傳輸後檢查目標上檔的長度。 如果源和目標不匹配，則傳輸將標記為失敗。 （預設為 true）

**--檢查 md5**字串 指定下載時應嚴格驗證 MD5 雜湊。 僅在下載時可用。 可用選項：無檢查、僅日誌、失敗、失敗或缺失。 （預設"失敗"）

**--內容處置**字串 設置內容處置標頭。 下載時返回。

**--內容編碼**字串 設置內容編碼標頭。 下載時返回。

**--內容語言**字串 設置內容語言標題。 下載時返回。

**--內容類型**字串 指定檔的內容類型。 暗示無猜測-mime 類型。 下載時返回。

**--解壓縮**                          如果下載檔案的內容編碼指示檔被壓縮，則自動解壓縮檔。 支援的內容編碼值為"gzip"和"deflate"。 不需要".gz'/'.gzip"或".zz"的檔副檔名，但如果存在，將被刪除。

**--排除屬性**字串（僅限 Windows） 排除其屬性與屬性清單匹配的檔。 例如：A;S;R

**--排除 blob 類型**字串 可選地指定從容器或帳戶複製 Blob 時要排除的 blob 類型（塊 Blob/PageBlob/AppendBlob）。 使用此標誌不適用於將資料從非 azure 服務複製到服務。 多個 Blob 應由";"分隔。

**--外**字串複製時排除這些路徑。 此選項不支援萬用字元 （*）。 檢查相對路徑首碼（例如：我的資料夾;我的資料夾/子 DirName/file.pdf）。 當與帳戶遍歷結合使用時，路徑不包括容器名稱。

**--外模式**字串複製時排除這些檔。 此選項支援萬用字元 （*）

**--後續符號連結**                     從本地檔案系統上傳時遵循符號連結。

**--從到**字串可選地指定源目標群組合。 例如：本地 Blob、Blob 本地、本地 BlobFS。

**-h， -- 説明**複製

**--包括屬性**字串（僅限 Windows） 包括其屬性與屬性清單匹配的檔。 例如：A;S;R

**--包含路徑**字串在複製時僅包含這些路徑。 此選項不支援萬用字元 （*）。 檢查相對路徑首碼（例如：我的資料夾;我的資料夾/子 DirName/file.pdf）。

**--包括模式**字串在複製時僅包含這些檔。 此選項支援萬用字元 （*）。 使用";"分隔檔。

**--日誌級**字串 定義日誌檔的日誌詳細性、可用級別：INFO（所有請求/回應）、警告（慢速回應）、ERROR（僅失敗的請求）和 NONE（無輸出日誌）。 （預設"INFO"）

**--中繼資料**字串將這些鍵值對上載到 Azure 存儲，作為中繼資料。

**--無猜測-默劇型**                  防止 AzCopy 根據檔副檔名或內容檢測內容類型。

**--** 覆蓋字串覆蓋目標處的衝突檔和 blob，如果此標誌設置為 true。 可能的值包括"true"，"假"，"如果來源更新"和"提示"。 （預設"true"）

**--使用**此 Blob 層將頁面 blob 上載到 Azure 存儲。 （預設"無"）

**--保留最後修改時間**         僅當目標為檔案系統時才可用。

**--put-md5**                            創建每個檔的 MD5 雜湊，並將雜湊保存為目標 blob 或檔的內容-MD5 屬性。 （預設情況下不會創建雜湊。僅在上載時可用。

**--遞迴**                           從本地檔案系統上載時，可重複地查看子目錄。

**--s2s 檢測源更改**          在枚舉後檢查源是否已更改。

**--s2s 控制碼無效中繼資料**字串指定如何處理不正確中繼資料金鑰。 可用選項：排除無效、失敗無效、重命名無效。 （預設"排除無效"）

**--s2s-保留-訪問層**            在服務期間保留對服務副本的訪問層。 請參閱[Azure Blob 存儲：熱、酷和存檔訪問層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)，以確保目標存儲帳戶支援設置訪問層。 如果不支援設置訪問層，請使用 s2sPreserveAccessTier_false 繞過複製訪問層。 （預設為 true）

**--s2s-保留屬性**             在服務期間保留完整的屬性以服務副本。 對於 AWS S3 和 Azure 檔非單一檔源，清單操作不會返回物件和檔的完整屬性。 要保留完整屬性，AzCopy 需要每個物件或檔發送一個附加請求。 （預設為 true）

## <a name="options-inherited-from-parent-commands"></a>從父命令繼承的選項

**--蓋-mbps uint32**     以每秒百萬位元表示傳輸速率上限。 逐時輸送量可能與上限略有不同。 如果此選項設置為零，或者省略此選項，則輸送量不會上限。

**--輸出類型**字串 命令輸出的格式。 選項包括：文本，json。 預設值為"文本"。 （預設"文本"）

## <a name="see-also"></a>另請參閱

- [阿茲比貝](storage-ref-azcopy.md)
