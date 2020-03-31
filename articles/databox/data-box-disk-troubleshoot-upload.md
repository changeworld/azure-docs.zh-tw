---
title: 使用日誌對資料上傳進行故障排除
titleSuffix: Azure Data Box Disk
description: 介紹在將資料上載到 Azure 資料框磁片時，如何使用日誌和排除問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260133"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>瞭解日誌以解決 Azure 資料盒磁片中的資料上載問題

本文適用于 Microsoft Azure 資料框磁片，並介紹將資料上載到 Azure 時看到的問題。

## <a name="about-upload-logs"></a>關於上傳日誌

將資料上載到資料中心的 Azure 時，`_error.xml`為每個存儲帳戶`_verbose.xml`生成檔。 這些日誌將上載到用於上載資料的同一存儲帳戶。 

日誌的格式相同，包含將資料從磁碟複製到 Azure 存儲帳戶時發生的事件的 XML 說明。

詳細日誌包含有關每個 Blob 或檔案複製操作狀態的完整資訊，而錯誤日誌僅包含上載期間遇到的 blob 或檔的資訊。

錯誤日誌的結構與詳細日誌相同，但篩選出成功操作。

## <a name="download-logs"></a>下載記錄

執行以下步驟來查找上載日誌。

1. 如果將資料上載到 Azure 時出現任何錯誤，門戶將顯示到診斷日誌所在的資料夾的路徑。

    ![連結到門戶中的日誌](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 去**懷斯**。

    ![錯誤和詳細日誌](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

在每種情況下，您都會看到錯誤日誌和詳細日誌。 選擇每個日誌並下載本機複本。

## <a name="sample-upload-logs"></a>示例上傳日誌

的示例`_verbose.xml`如下所示。 在這種情況下，訂單已成功完成，沒有錯誤。

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

對於相同的順序，如下所示的 樣本`_error.xml`。

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

的示例`_error.xml`如下所示，訂單已完成，出現錯誤。 

在這種情況下，錯誤檔有一個部分和另`Summary`一個包含所有檔級別錯誤的部分。 

`Summary`包含 和`ValidationErrors`。 `CopyErrors` 在這種情況下，有 8 個檔或資料夾上載到 Azure，並且沒有驗證錯誤。 將資料複製到 Azure 存儲帳戶時，成功上載了 5 個檔或資料夾。 其餘 3 個檔或資料夾根據 Azure 容器命名約定重命名，然後成功上載到 Azure。

檔級別狀態用於`BlobStatus`描述上載 Blob 所執行的任何操作。 在這種情況下，將重命名三個容器，因為複製資料的資料夾不符合容器的 Azure 命名約定。 對於在這些容器中上載的 Blob，包括新的容器名稱、Azure 中 Blob 的路徑、原始無效檔路徑和 blob 大小。
    
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

將資料上載到 Azure 時生成的錯誤將匯總在下表中。

| 錯誤碼 | 描述                   |
|-------------|------------------------------|
|`None` |  已成功完成。           |
|`Renamed` | 已成功重命名 blob。   |
|`CompletedWithErrors` | 上傳已完成錯誤。 錯誤檔的詳細資訊包含在日誌檔中。  |
|`Corrupted`|在資料引入過程中計算的 CRC 與上載期間計算的 CRC 不匹配。  |  
|`StorageRequestFailed` | Azure 存儲請求失敗。   |     
|`LeasePresent` | 此專案已租用，其他使用者正在使用。 |
|`StorageRequestForbidden` |由於身份驗證問題，無法上載。 |
|`ManagedDiskCreationTerminalFailure` | 無法作為託管磁片上載。 這些檔在暫存存儲帳戶中作為頁面 blob 提供。 您可以手動將頁面 Blob 轉換為託管磁片。  |
|`DiskConversionNotStartedTierInfoMissing` | 由於 VHD 檔是在預創建的層資料夾之外複製的，因此未創建託管磁片。 檔作為頁面 blob 上載到暫存存儲帳戶，如訂單創建期間指定的那樣。 您可以手動將其轉換為託管磁片。|
|`InvalidWorkitem` | 無法上載資料，因為它不符合 Azure 命名和限制約定。|
|`InvalidPageBlobUploadAsBlockBlob` | 在具有首碼`databoxdisk-invalid-pb-`的容器中作為塊 blob 上載。|
|`InvalidAzureFileUploadAsBlockBlob` | 在具有首碼`databoxdisk-invalid-af`-的容器中作為塊 blob 上載。|
|`InvalidManagedDiskUploadAsBlockBlob` | 在具有首碼`databoxdisk-invalid-md`-的容器中作為塊 blob 上載。|
|`InvalidManagedDiskUploadAsPageBlob` |在具有首碼`databoxdisk-invalid-md-`的容器中上載為頁面 blob。 |
|`MovedToOverflowShare` |由於原始共用大小超過 Azure 最大大小限制，因此將檔上載到新共用。 新的檔共用名稱稱的原始名稱尾碼與`-2`。   |
|`MovedToDefaultAzureShare` |將不屬於任何資料夾的檔上載到預設共用。 共用名稱稱以`databox-`開頭。 |
|`ContainerRenamed` |這些檔的容器不符合 Azure 命名約定，並且重命名。 新名稱以`databox-`原始名稱的 SHA1 雜湊開頭，尾碼 |
|`ShareRenamed` |這些檔的共用不符合 Azure 命名約定，並且重命名。 新名稱以`databox-`原始名稱的 SHA1 雜湊開頭，尾碼為 |
|`BlobRenamed` |這些檔不符合 Azure 命名約定，並且已重命名。 檢查`BlobPath`新名稱的欄位。 |
|`FileRenamed` |這些檔不符合 Azure 命名約定，並且已重命名。 檢查`FileStoragePath`新名稱的欄位。 |
|`DiskRenamed` |這些檔不符合 Azure 命名約定，並且已重命名。 檢查`BlobPath`新名稱的欄位。 |


## <a name="next-steps"></a>後續步驟

- [打開資料框磁片問題的支援票證](data-box-disk-contact-microsoft-support.md)。
