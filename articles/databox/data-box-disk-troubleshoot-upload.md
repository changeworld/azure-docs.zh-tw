---
title: 使用記錄檔進行資料上傳疑難排解
titleSuffix: Azure Data Box Disk
description: 描述如何使用記錄檔，並針對將資料上傳至 Azure 資料箱磁碟時所看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380331"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>瞭解記錄以對 Azure 資料箱磁碟中的資料上傳問題進行疑難排解

本文適用于 Microsoft Azure 資料箱磁碟，並說明您將資料上傳至 Azure 時所看到的問題。

## <a name="about-upload-logs"></a>關於上傳記錄

當資料在資料中心上傳至 Azure 時，會為每個儲存體帳戶產生 `_error.xml` 和 `_verbose.xml` 檔案。 這些記錄會上傳至用來上傳資料的相同儲存體帳戶。 

這兩個記錄都採用相同的格式，並包含將資料從磁碟複製到 Azure 儲存體帳戶時所發生事件的 XML 描述。

詳細資訊記錄檔包含有關每個 blob 或檔案的複製作業狀態的完整資訊，而錯誤記錄檔只包含上傳期間發生錯誤之 blob 或檔案的資訊。

錯誤記錄檔具有與詳細資訊記錄相同的結構，但會篩選出成功的作業。

## <a name="download-logs"></a>下載記錄

請採取下列步驟來找出上傳記錄檔。

1. 如果將資料上傳至 Azure 時發生任何錯誤，入口網站會顯示診斷記錄所在資料夾的路徑。

    ![連結至入口網站中的記錄](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 移至**waies**。

    ![錯誤和詳細資訊記錄檔](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每個案例中，您會看到錯誤記錄檔和詳細資訊記錄檔。 選取每個記錄檔，並下載本機複本。

## <a name="sample-upload-logs"></a>範例上傳記錄

`_verbose.xml` 的範例如下所示。 在此情況下，訂單已順利完成，沒有錯誤。

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

依照相同的順序，`_error.xml` 的範例如下所示。

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

以下顯示 `_error.xml` 的範例，其中的訂單已完成，但發生錯誤。 

在此情況下，錯誤檔案有一個 `Summary` 區段，另一個區段則包含所有檔案層級錯誤。 

`Summary` 包含 `ValidationErrors` 和 `CopyErrors`。 在此情況下，已將8個檔案或資料夾上傳至 Azure，但沒有任何驗證錯誤。 當資料複製到 Azure 儲存體帳戶時，已成功上傳5個檔案或資料夾。 其餘3個檔案或資料夾已根據 Azure 容器命名慣例重新命名，然後成功上傳至 Azure。

檔案層級的狀態為 `BlobStatus`，其中描述上傳 blob 所採取的任何動作。 在此情況下，會重新命名三個容器，因為複製資料的目的檔案夾不符合適用于容器的 Azure 命名慣例。 針對在這些容器中上傳的 blob，會包含新的容器名稱、Azure 中的 blob 路徑、原始的無效檔案路徑，以及 blob 大小。
    
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

下表摘要說明將資料上傳至 Azure 時所產生的錯誤。

| 錯誤碼 | 描述                   |
|-------------|------------------------------|
|`None` |  已順利完成。           |
|`Renamed` | 已成功重新命名 blob。   |
|`CompletedWithErrors` | 上傳已完成，但發生錯誤。 錯誤中的檔案詳細資料包含在記錄檔中。  |
|`Corrupted`|資料內嵌期間計算的 CRC，與上傳期間計算的 CRC 不符。  |  
|`StorageRequestFailed` | Azure 儲存體要求失敗。   |     
|`LeasePresent` | 此專案已租用，且正由另一位使用者使用。 |
|`StorageRequestForbidden` |無法上傳，因為發生驗證問題。 |
|`ManagedDiskCreationTerminalFailure` | 無法上傳為受控磁片。 檔案會以分頁 blob 的形式存在於暫存的儲存體帳戶中。 您可以手動將分頁 blob 轉換成受控磁片。  |
|`DiskConversionNotStartedTierInfoMissing` | 因為 VHD 檔案已複製到預先建立層資料夾之外，所以未建立受控磁片。 檔案會以分頁 blob 的形式上傳至暫存的儲存體帳戶，如建立順序期間所指定。 您可以手動將它轉換為受控磁片。|
|`InvalidWorkitem` | 無法上傳資料，因為它不符合 Azure 命名和限制慣例。|
|`InvalidPageBlobUploadAsBlockBlob` | 在具有前置詞 `databoxdisk-invalid-pb-`的容器中，上傳為區塊 blob。|
|`InvalidAzureFileUploadAsBlockBlob` | 在具有前置詞 `databoxdisk-invalid-af`-的容器中，上傳為區塊 blob。|
|`InvalidManagedDiskUploadAsBlockBlob` | 在具有前置詞 `databoxdisk-invalid-md`-的容器中，上傳為區塊 blob。|
|`InvalidManagedDiskUploadAsPageBlob` |以分頁 blob 的形式上傳至具有前置詞 `databoxdisk-invalid-md-`的容器中。 |
|`MovedToOverflowShare` |已將檔案上傳到新的共用，因為原始共用大小超過最大 Azure 大小限制。 新的檔案共用名稱的原始名稱尾碼為 `-2`。   |
|`MovedToDefaultAzureShare` |已將不屬於任何資料夾的檔案上傳至預設共用。 共用名稱的開頭為 `databox-`。 |
|`ContainerRenamed` |這些檔案的容器不符合 Azure 命名慣例，而且已重新命名。 新名稱的開頭為 `databox-`，並以原始名稱的 SHA1 雜湊做為尾碼 |
|`ShareRenamed` |這些檔案的共用不符合 Azure 命名慣例，而且已重新命名。 新名稱的開頭為 `databox-`，並以原始名稱的 SHA1 雜湊做為尾碼。 |
|`BlobRenamed` |這些檔案不符合 Azure 命名慣例，而且已重新命名。 檢查 [`BlobPath`] 欄位中的新名稱。 |
|`FileRenamed` |這些檔案不符合 Azure 命名慣例，而且已重新命名。 檢查 [`FileStoragePath`] 欄位中的新名稱。 |
|`DiskRenamed` |這些檔案不符合 Azure 命名慣例，而且已重新命名。 檢查 [`BlobPath`] 欄位中的新名稱。 |


## <a name="next-steps"></a>後續步驟

- [開啟資料箱磁碟問題的支援票證](data-box-disk-contact-microsoft-support.md)。
