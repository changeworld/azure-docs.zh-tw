---
title: 針對 Azure 資料箱磁碟進行疑難排解 | Microsoft Docs
description: 瞭解如何使用記錄來針對部署 Azure 資料箱磁碟時可能產生的驗證問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: fa8a6643f1b7bd60fbf6e5950234e0381666177e
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605219"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用記錄針對 Azure 資料箱磁碟中的驗證問題進行疑難排解

本文適用于 Microsoft Azure 資料箱磁碟。 本文描述如何使用記錄來針對您在部署此解決方案時所看到的驗證問題進行疑難排解。

## <a name="validation-tool-log-files"></a>驗證工具記錄檔

當您使用 [驗證工具](data-box-disk-deploy-copy-data.md#validate-data)驗證磁片上的資料時，會產生 *error.xml* 來記錄任何錯誤。 記錄檔位於  `Drive:\DataBoxDiskImport\logs` 您磁片磁碟機的資料夾中。 當您執行驗證時，會提供錯誤記錄檔的連結。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果您執行多個會話進行驗證，則每個會話都會產生一個錯誤記錄檔。

- 以下是載入資料夾中的資料 `PageBlob` 不是 512-位元組對齊時，錯誤記錄檔的範例。 上傳至 PageBlob 的任何資料都必須是 512-位元組對齊，例如 VHD 或 VHDX。 此檔案中的錯誤位於 `<Errors>` 和警告中 `<Warnings>` 。

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

- 以下是當容器名稱無效時的錯誤記錄檔範例。 您在 `BlockBlob` 磁片上的、或資料夾下建立的資料夾， `PageBlob` `AzureFile` 會成為 Azure 儲存體帳戶中的容器。 容器的名稱必須遵循 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

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

下表摘要說明 *error.xml* 中包含對應建議動作的錯誤。

| 錯誤碼| 描述                       | 建議動作               |
|------------|--------------------------|-----------------------------------|
| `None` | 已成功驗證資料。 | 您不需要執行任何動作。 |
| `InvalidXmlCharsInPath` |因為檔案路徑包含不正確字元，所以無法建立資訊清單檔。 | 請移除這些字元以繼續。  |
| `OpenFileForReadFailed`| 無法處理檔案。 這可能是因為存取問題或檔案系統損毀所致。|因為發生錯誤，所以無法讀取檔案。 錯誤詳細資料位於例外狀況中。 |
| `Not512Aligned` | 此檔案不是 PageBlob 資料夾的有效格式。| 只上傳與資料夾對齊的512位元組資料 `PageBlob` 。 從 PageBlob 資料夾中移除檔案，或將它移至 BlockBlob 資料夾。 請重試驗證。|
| `InvalidBlobPath` | 檔案路徑不會根據 Azure Blob 命名慣例，對應至雲端中的有效 blob 路徑。|遵循 Azure 命名方針來重新命名檔案路徑。 |
| `EnumerationError` | 無法列舉檔案進行驗證。 |此錯誤可能有許多原因。 最可能的原因是存取檔案。 |
| `ShareSizeExceeded` | 此檔案會導致 Azure 檔案共用大小超過 5 TB 的 Azure 限制。|減少共用中的資料大小，使其符合 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `AzureFileSizeExceeded` | 檔案大小超過 Azure 檔案大小限制。| 減少檔案或資料的大小，使其符合 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。|
| `BlockBlobSizeExceeded` | 檔案大小超過 Azure 區塊 Blob 大小限制。 | 減少檔案或資料的大小，使其符合 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `ManagedDiskSizeExceeded` | 檔案大小超過 Azure 受控磁片大小的限制。 | 減少檔案或資料的大小，使其符合 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `PageBlobSizeExceeded` | 檔案大小超過 Azure 受控磁片大小的限制。 | 減少檔案或資料的大小，使其符合 [Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 請重試驗證。 |
| `InvalidShareContainerFormat`  |目錄名稱不符合容器或共用的 Azure 命名慣例。         |在磁片上預先存在的資料夾下建立的第一個資料夾，會變成儲存體帳戶中的容器。 此共用或容器名稱不符合 Azure 命名慣例。 將檔案重新命名，使其符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。   |
| `InvalidBlobNameFormat` | 檔案路徑不會根據 Azure Blob 命名慣例，對應至雲端中的有效 blob 路徑。|將檔案重新命名，使其符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。 |
| `InvalidFileNameFormat` | 檔案路徑不會根據 Azure 檔案命名慣例，對應至雲端中的有效檔案路徑。 |將檔案重新命名，使其符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。 |
| `InvalidDiskNameFormat` | 檔案路徑不會根據 Azure 受控磁片命名慣例，對應至雲端中的有效磁片名稱。 |將檔案重新命名，使其符合 [Azure 命名慣例](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 請重試驗證。       |
| `NotPartOfFileShare` | 檔案的上傳路徑無效。 將檔案上傳至 Azure 檔案儲存體中的資料夾。   | 移除錯誤中的檔案，並將這些檔案上傳至預先建立資料夾。 請重試驗證。 |
| `NonVhdFileNotSupportedForManagedDisk` | 非 VHD 檔案無法上傳為受控磁片。 |移除資料夾中的非 VHD 檔案， `ManagedDisk` 因為這些檔案不受支援，或將這些檔案移至 `PageBlob` 資料夾。 請重試驗證。 |


## <a name="next-steps"></a>後續步驟

- 針對 [資料上傳錯誤](data-box-disk-troubleshoot-upload.md)進行疑難排解。
