---
title: 使用記錄針對資料上傳進行疑難排解
titleSuffix: Azure Data Box Disk
description: 描述如何使用記錄，以及針對將資料上傳至 Azure 資料箱磁碟時所看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605270"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>瞭解記錄以針對 Azure 資料箱磁碟中的資料上傳問題進行疑難排解

本文適用于 Microsoft Azure 資料箱磁碟，並說明您將資料上傳至 Azure 時所看到的問題。

## <a name="about-upload-logs"></a>關於上傳記錄

當資料在資料中心上傳至 Azure 時， `_error.xml` `_verbose.xml` 系統會為每個儲存體帳戶產生檔案。 這些記錄會上傳至用來上傳資料的相同儲存體帳戶。 

這兩個記錄都採用相同的格式，並包含將資料從磁碟複製到 Azure 儲存體帳戶時所發生之事件的 XML 描述。

詳細資訊記錄檔包含每個 blob 或檔案的複製作業狀態的完整資訊，而錯誤記錄檔只會包含在上傳期間發生錯誤之 blob 或檔案的相關資訊。

錯誤記錄檔的結構與詳細資訊記錄相同，但會篩選出成功的作業。

## <a name="download-logs"></a>下載記錄

請採取下列步驟來找出上傳記錄。

1. 如果將資料上傳至 Azure 時發生任何錯誤，入口網站會顯示診斷記錄所在資料夾的路徑。

    ![連結至入口網站中的記錄](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 移至 **waies**。

    ![錯誤和詳細資訊記錄檔](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每個案例中，您都會看到錯誤記錄檔和詳細資訊記錄。 選取每個記錄檔，並下載本機複本。

## <a name="sample-upload-logs"></a>範例上傳記錄

的範例 `_verbose.xml` 如下所示。 在此情況下，訂單已順利完成，且沒有任何錯誤。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

針對相同的順序，的範例 `_error.xml` 如下所示。

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

的範例 `_error.xml` 如下所示，其中的訂單已完成，但發生錯誤。 

此案例中的錯誤檔有一個 `Summary` 區段，而另一個區段包含所有檔案層級的錯誤。 

`Summary`包含 `ValidationErrors` 和 `CopyErrors` 。 在此情況下，已將8個檔案或資料夾上傳至 Azure，而且沒有任何驗證錯誤。 當資料複製到 Azure 儲存體帳戶時，已成功上傳5個檔案或資料夾。 其餘3個檔案或資料夾已根據 Azure 容器命名慣例重新命名，然後成功上傳至 Azure。

檔案層級狀態為 `BlobStatus` ，描述上傳 blob 所採取的任何動作。 在此情況下，由於複製資料的資料夾不符合容器的 Azure 命名慣例，因此會重新命名三個容器。 針對這些容器中上傳的 blob，會包含新的容器名稱、Azure 中的 blob 路徑、原始不正確檔案路徑和 blob 大小。
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>資料上傳錯誤

下表摘要說明將資料上傳至 Azure 時產生的錯誤。

| 錯誤碼 | 描述                   |
|-------------|------------------------------|
|`None` |  已順利完成。           |
|`Renamed` | 已成功重新命名 blob。   |
|`CompletedWithErrors` | 上傳已完成，但發生錯誤。 錯誤中檔案的詳細資料會包含在記錄檔中。  |
|`Corrupted`|資料內嵌期間計算的 CRC 不符合上傳期間所計算的 CRC。  |  
|`StorageRequestFailed` | Azure 儲存體要求失敗。   |     
|`LeasePresent` | 此專案已租用，且正由另一位使用者使用。 |
|`StorageRequestForbidden` |因為發生驗證問題，所以無法上傳。 |
|`ManagedDiskCreationTerminalFailure` | 無法上傳為受控磁片。 檔案會以分頁 blob 的形式在暫存儲存體帳戶中提供。 您可以手動將分頁 blob 轉換為受控磁片。  |
|`DiskConversionNotStartedTierInfoMissing` | 由於 VHD 檔案是複製在預先建立層資料夾之外，因此不會建立受控磁片。 檔案會以分頁 blob 的形式上傳至暫存儲存體帳戶，如訂單建立期間所指定。 您可以手動將它轉換成受控磁片。|
|`InvalidWorkitem` | 無法上傳資料，因為它不符合 Azure 命名和限制慣例。|
|`InvalidPageBlobUploadAsBlockBlob` | 上傳為具有前置詞之容器中的區塊 blob `databoxdisk-invalid-pb-` 。|
|`InvalidAzureFileUploadAsBlockBlob` | 在前置詞為的容器中，以區塊 blob 的形式上傳 `databoxdisk-invalid-af` 。|
|`InvalidManagedDiskUploadAsBlockBlob` | 在前置詞為的容器中，以區塊 blob 的形式上傳 `databoxdisk-invalid-md` 。|
|`InvalidManagedDiskUploadAsPageBlob` |上傳為具有前置詞之容器中的分頁 blob `databoxdisk-invalid-md-` 。 |
|`MovedToOverflowShare` |因為原始共用大小超過最大的 Azure 大小限制，所以已將檔案上傳到新的共用。 新的檔案共用名稱的原始名稱會加上尾碼 `-2` 。   |
|`MovedToDefaultAzureShare` |將不屬於任何資料夾的檔案上傳至預設共用。 共用名稱的開頭為 `databox-` 。 |
|`ContainerRenamed` |這些檔案的容器不符合 Azure 命名慣例，且已重新命名。 新名稱的開頭為 `databox-` ，且尾碼為原始名稱的 SHA1 雜湊 |
|`ShareRenamed` |這些檔案的共用不符合 Azure 命名慣例，且已重新命名。 新名稱的開頭為 `databox-` ，且尾碼為原始名稱的 SHA1 雜湊。 |
|`BlobRenamed` |這些檔案不符合 Azure 命名慣例，且已重新命名。 檢查 `BlobPath` 新名稱的欄位。 |
|`FileRenamed` |這些檔案不符合 Azure 命名慣例，且已重新命名。 檢查 `FileStoragePath` 新名稱的欄位。 |
|`DiskRenamed` |這些檔案不符合 Azure 命名慣例，且已重新命名。 檢查 `BlobPath` 新名稱的欄位。 |


## <a name="next-steps"></a>後續步驟

- [開啟資料箱磁碟問題的支援票證](data-box-disk-contact-microsoft-support.md)。
