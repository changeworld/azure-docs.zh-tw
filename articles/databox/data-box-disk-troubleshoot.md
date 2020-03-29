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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805716"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>使用日誌解決 Azure 資料框磁片中的驗證問題

本文適用于 Microsoft Azure 資料盒磁片。 本文介紹如何使用日誌來排除部署此解決方案時看到的驗證問題。

## <a name="validation-tool-log-files"></a>驗證工具日誌檔

當您使用[驗證工具](data-box-disk-deploy-copy-data.md#validate-data)驗證磁片上的資料時，將生成*一個錯誤.xml*來記錄任何錯誤。 日誌檔位於磁碟機的`Drive:\DataBoxDiskImport\logs`資料夾中。 運行驗證時提供指向錯誤日誌的連結。

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

如果運行多個會話進行驗證，則每個會話生成一個錯誤日誌。

- 當載入到資料夾中的資料未對齊 512 位元組時`PageBlob`，下面是錯誤日誌的示例。 上載到 PageBlob 的任何資料都必須是 512 位元組對齊的，例如 VHD 或 VHDX。 此檔中的錯誤在`<Errors>`和 中處於 和`<Warnings>`中的警告中。

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

- 當容器名稱無效時，下面是錯誤日誌的示例。 在 的`BlockBlob``PageBlob`下創建的資料夾 或`AzureFile`磁片上的資料夾將成為 Azure 存儲帳戶中的容器。 容器的名稱必須遵循[Azure 命名約定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

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

下表匯總了*error.xml*中包含的錯誤以及相應的建議操作。

| 錯誤碼| 描述                       | 建議動作               |
|------------|--------------------------|-----------------------------------|
| `None` | 成功驗證資料。 | 您不需要執行任何動作。 |
| `InvalidXmlCharsInPath` |無法創建清單檔，因為檔路徑具有不正確字元。 | 刪除這些字元以繼續。  |
| `OpenFileForReadFailed`| 無法處理該檔。 這可能是由於訪問問題或檔案系統損壞。|由於錯誤，無法讀取該檔。 錯誤詳細資訊位於例外情況中。 |
| `Not512Aligned` | 此檔不是 PageBlob 資料夾的有效格式。| 僅上載與`PageBlob`資料夾對齊的 512 位元組的資料。 從 PageBlob 資料夾中刪除該檔或將其移動到塊 Blob 資料夾。 重試驗證。|
| `InvalidBlobPath` | 檔路徑不像 Azure Blob 命名約定那樣映射到雲中的有效 Blob 路徑。|按照 Azure 命名準則重命名檔路徑。 |
| `EnumerationError` | 無法枚舉檔進行驗證。 |此錯誤的原因可能有多種。 最有可能的原因是訪問該檔。 |
| `ShareSizeExceeded` | 此檔導致 Azure 檔共用大小超過 Azure 限制 5 TB。|減小共用中資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `AzureFileSizeExceeded` | 檔案大小超過 Azure 檔案大小限制。| 減小檔或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。|
| `BlockBlobSizeExceeded` | 檔案大小超過 Azure 塊 Blob 大小限制。 | 減小檔或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `ManagedDiskSizeExceeded` | 檔案大小超過 Azure 託管磁片大小限制。 | 減小檔或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `PageBlobSizeExceeded` | 檔案大小超過 Azure 託管磁片大小限制。 | 減小檔或資料的大小，使其符合[Azure 物件大小限制](data-box-disk-limits.md#azure-object-size-limits)。 重試驗證。 |
| `InvalidShareContainerFormat`  |目錄名稱不符合容器或共用的 Azure 命名約定。         |在磁片上預先存在的資料夾下創建的第一個資料夾將成為存儲帳戶中的容器。 此共用或容器名稱不符合 Azure 命名約定。 重命名檔，使其符合[Azure 命名約定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。   |
| `InvalidBlobNameFormat` | 檔路徑不像 Azure Blob 命名約定那樣映射到雲中的有效 Blob 路徑。|重命名檔，使其符合[Azure 命名約定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。 |
| `InvalidFileNameFormat` | 檔路徑不像 Azure 檔命名約定那樣映射到雲中的有效檔路徑。 |重命名檔，使其符合[Azure 命名約定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。 |
| `InvalidDiskNameFormat` | 檔路徑不像 Azure 託管磁片命名約定那樣映射到雲中的有效磁片名稱。 |重命名檔，使其符合[Azure 命名約定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。 重試驗證。       |
| `NotPartOfFileShare` | 檔的上載路徑無效。 將檔上載到 Azure 檔中的資料夾。   | 刪除錯誤的檔並將這些檔上載到預先創建的資料夾。 重試驗證。 |
| `NonVhdFileNotSupportedForManagedDisk` | 無法將非 VHD 檔上載為託管磁片。 |從`ManagedDisk`資料夾中刪除非 VHD 檔，因為不支援這些檔，或者將這些檔移動到`PageBlob`資料夾。 重試驗證。 |


## <a name="next-steps"></a>後續步驟

- 排除[資料上傳錯誤](data-box-disk-troubleshoot-upload.md)。
