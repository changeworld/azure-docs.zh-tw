---
title: 針對 Azure 資料箱磁碟進行疑難排解 | Microsoft Docs
description: 說明如何針對在 Azure 資料箱磁碟中察覺的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67805716"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用記錄對 Azure 資料箱磁碟中的驗證問題進行疑難排解

本文適用于 Microsoft Azure 資料箱磁碟。 本文說明如何使用記錄，針對您在部署此解決方案時所看到的驗證問題進行疑難排解。

## <a name="validation-tool-log-files"></a>驗證工具記錄檔

當您使用[驗證工具](data-box-disk-deploy-copy-data.md#validate-data)驗證磁片上的資料時，會產生*錯誤 .xml*來記錄任何錯誤。 記錄檔位於磁片磁碟機的`Drive:\DataBoxDiskImport\logs`資料夾中。 當您執行驗證時，會提供錯誤記錄檔的連結。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果您執行多個會話進行驗證，則每個會話都會產生一個錯誤記錄檔。

- 以下是載入`PageBlob`資料夾中的資料不是 512-位元組對齊時的錯誤記錄檔範例。 任何上傳至 PageBlob 的資料，都必須是 512-位元組對齊，例如 VHD 或 VHDX。 此檔案中的錯誤位於和中`<Errors>` `<Warnings>`的警告。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 以下是容器名稱無效時的錯誤記錄檔範例。 您在磁片上的、 `BlockBlob` `PageBlob`或`AzureFile`資料夾下建立的資料夾，會變成 Azure 儲存體帳戶中的容器。 容器的名稱必須遵循[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>驗證工具錯誤

下表摘要說明包含在 error 中的*錯誤。 xml*與對應的建議動作。

| 錯誤碼| 描述                       | 建議動作               |
|------------|--------------------------|-----------------------------------|
| `None` | 已成功驗證資料。 | 您不需要執行任何動作。 |
| `InvalidXmlCharsInPath` |因為檔案路徑包含不正確字元，所以無法建立資訊清單檔。 | 請移除這些字元以繼續。  |
| `OpenFileForReadFailed`| 無法處理檔案。 這可能是因為存取問題或檔案系統損毀所造成。|因為發生錯誤，所以無法讀取檔案。 錯誤詳細資料在例外狀況中。 |
| `Not512Aligned` | 這個檔案不是有效的 PageBlob 資料夾格式。| 僅將512個位元組的資料上傳`PageBlob`到資料夾。 從 PageBlob 資料夾中移除檔案，或將它移至 BlockBlob 資料夾。 請重試驗證。|
| `InvalidBlobPath` | 根據 Azure Blob 命名慣例，檔案路徑不會對應到雲端中的有效 blob 路徑。|遵循 Azure 命名指導方針來重新命名檔案路徑。 |
| `EnumerationError` | 無法列舉檔案以進行驗證。 |發生此錯誤的原因可能有多種。 最有可能的原因是檔案的存取權。 |
| `ShareSizeExceeded` | 此檔案會導致 Azure 檔案共用大小超過 5 TB 的 Azure 限制。|減少共用中的資料大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `AzureFileSizeExceeded` | 檔案大小超過 Azure 檔案大小限制。| 縮小檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。|
| `BlockBlobSizeExceeded` | 檔案大小超過 Azure 區塊 Blob 大小限制。 | 縮小檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `ManagedDiskSizeExceeded` | 檔案大小超過 Azure 受控磁片大小限制。 | 縮小檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `PageBlobSizeExceeded` | 檔案大小超過 Azure 受控磁片大小限制。 | 縮小檔案或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `InvalidShareContainerFormat`  |目錄名稱不符合容器或共用的 Azure 命名慣例。         |在磁片上預先存在的資料夾底下建立的第一個資料夾，會成為您儲存體帳戶中的容器。 此共用或容器名稱不符合 Azure 命名慣例。 將檔案重新命名，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。   |
| `InvalidBlobNameFormat` | 根據 Azure Blob 命名慣例，檔案路徑不會對應到雲端中的有效 blob 路徑。|將檔案重新命名，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。 |
| `InvalidFileNameFormat` | 根據 Azure 檔案命名慣例，檔案路徑不會對應到雲端中的有效檔案路徑。 |將檔案重新命名，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。 |
| `InvalidDiskNameFormat` | 根據 Azure 受控磁片命名慣例，檔案路徑不會對應到雲端中的有效磁片名稱。 |將檔案重新命名，使其符合[Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。       |
| `NotPartOfFileShare` | 檔案的上傳路徑無效。 將檔案上傳至 Azure 檔案儲存體中的資料夾。   | 請移除錯誤中的檔案，並將這些檔案上傳至預先建立資料夾。 請重試驗證。 |
| `NonVhdFileNotSupportedForManagedDisk` | 非 VHD 檔案無法上傳為受控磁片。 |請移除資料夾中`ManagedDisk`的非 VHD 檔案，因為這些檔案不受支援，或將這些`PageBlob`檔案移至資料夾。 請重試驗證。 |


## <a name="next-steps"></a>後續步驟

- 針對[資料上傳錯誤](data-box-disk-troubleshoot-upload.md)進行疑難排解。
