---
title: 管理 Azure 支援要求
description: 說明如何查看支援要求、傳送訊息、變更要求嚴重性層級、與 Azure 支援共用診斷資訊、重新開啟已關閉的支援要求，以及上傳檔案。
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: a9dd703dc0a3f5e8f85b1022fa2a71ff9a8c295d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745565"
---
# <a name="manage-an-azure-support-request"></a>管理 Azure 支援要求

[建立 Azure 支援要求](how-to-create-azure-support-request.md)之後，您可以在[Azure 入口網站](https://portal.azure.com)中進行管理，這會在本文中討論。 您也可以使用 [Azure 支援票證 REST API](/rest/api/support)，以程式設計方式建立和管理要求。

## <a name="view-support-requests"></a>檢視支援要求

前往 [說明 **+ 支援**  >   **所有支援要求**]，以查看支援要求的詳細資料和狀態。

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="所有支援要求":::

在此頁面上，您可以搜尋、篩選及排序支援要求。 選取支援要求以查看詳細資料，包括其嚴重性以及與要求相關聯的任何訊息。

## <a name="send-a-message"></a>傳送訊息

1. 在 [ **所有支援要求** ] 頁面上，選取支援要求。

1. 在 [ **支援要求** ] 頁面上，選取 [ **新增訊息**]。

1. 輸入您的訊息，然後選取 [ **提交**]。

## <a name="change-the-severity-level"></a>變更嚴重性層級

> [!NOTE]
> 最大嚴重性層級取決於您的 [支援方案](https://azure.microsoft.com/support/plans)。
>

1. 在 [ **所有支援要求** ] 頁面上，選取支援要求。

1. 在 [ **支援要求** ] 頁面上，選取 [ **變更**]。

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="變更支援要求嚴重性":::

1. 根據您的要求是否已指派給支援工程師，Azure 入口網站會顯示兩個畫面中的其中一個畫面：

    - 如果您尚未指派您的要求，您會看到如下所示的畫面。 選取新的嚴重性層級，然後選取 [ **變更**]。

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="選取新的嚴重性層級":::

    - 如果已指派您的要求，您會看到如下所示的畫面。 選取 **[確定]**，然後建立 [新的訊息](#send-a-message) 來要求嚴重性層級的變更。

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="無法選取新的嚴重性層級":::

## <a name="share-diagnostic-information-with-azure-support"></a>使用 Azure 支援共用診斷資訊

當您建立支援要求時，預設會選取 [ **共用診斷資訊** ] 選項。 這可讓 Azure 支援從您的 Azure 資源收集 [診斷資訊](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) ：

* 建立要求之後，您就無法清除此選項。

* 如果您在建立要求時清除了選項，就可以在建立要求之後選取它。

    1. 在 [ **所有支援要求** ] 頁面上，選取支援要求。
    
    1. 在 [ **支援要求** ] 頁面上，選取 **[授與許可權**]，然後選取 [ **是** ] 和 **[確定]**。
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="授與診斷資訊的許可權":::

## <a name="upload-files"></a>上傳檔案

您可以使用 [檔案上傳] 選項來上傳診斷檔案或您認為與支援要求相關的任何其他檔案。

1. 在 [ **所有支援要求** ] 頁面上，選取支援要求。

1. 在 [ **支援要求** ] 頁面上，流覽以尋找您的檔案，然後選取 **[上傳**]。 如果您有多個檔案，請重複此程式。

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="上傳檔案":::

### <a name="file-upload-guidelines"></a>檔案上傳指導方針

當您使用檔案上傳選項時，請遵循下列指導方針：

* 若要保護您的隱私權，請勿在您的上傳中包含任何個人資訊。
* 檔案名稱的長度不得超過 110 個字元。
* 您無法上傳一個以上的檔案。
* 檔案不能大於 4 MB。
* 所有檔案都必須有副檔名，例如 *.docx* 或 *.xlsx*。 下表顯示允許上傳的副檔名。

| 0-9、A-C    | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .hwl        | .odx  | .rar     | .tdb       | .xlam   |
| .a          | .db   | .ics        | .oft  | .rdl     | .tdf       | .xlr    |
| .abc        | .DMP  | .ini        | .old  | .rdlc    | .text      | .xls    |
| .adm        | .do_  | .java       | .one  | .re_     | .thmx      | .xlsb   |
| .aspx       | .doc  | .jpg        | .osd  | .reg     | .tif       | .xlsm   |
| .ATF        | .docm | .LDF        | .OUT  | .remove  | .trc       | .xlsx   |
| .b          | .docx | .letterhead | .p1   | .ren     | .TTD       | .xlt    |
| .ba_        | .dotm | .lnk        | .pcap | .rename  | .tx_       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .log        | .pdf  | .rpt     | .uccapilog | .xmla   |
| .blg        | .eds  | .lpk        | .piz  | .rte     | .uccplog   | .xps    |
| .CA_        | .emf  | .manifest   | .pmls | .rtf     | .udcx      | .xsd    |
| .CAB        | .eml  | .master     | .png  | .run     | .vb_       | .xsn    |
| .cap        | .emz  | .mdmp       | .potx | .saz     | .vbs_      | .xxx    |
| .catx       | .err  | .mof        | .ppt  | .sql     | .vcf       | .z_     |
| .CFG        | .etl  | .mp3        | .pptm | .sqlplan | .vsd       | .z01    |
| .compressed | .evt  | .mpg        | .pptx | .stp     | .wdb       | .z02    |
| .Config     | .evtx | .ms_        | .prn  | .svclog  | .wks       | .zi     |
| .cpk        | .EX   | .msg        | .psf  |   -       | .wma       | .zi_    |
| .cpp        | .ex_  | .msi        | .pst  |  -        | .wmv       | .zip    |
| .cs         | .ex0  | .mso        | .pub  | -         | .wmz       | .zip_   |
| .CSV        | .FRD  | .msu        | -      |-          | .wps       | .zipp   |
| .cvr        | .gif  | .nfo        | -      |-          | .wpt       | .zipped |
| -            | .guid | -            | -      | -         | .wsdl      | .zippy  |
| -            | .gz   | -            | -      | -         | .wsp       | .zipx   |
| -            | -      | -            | -      | -         | .wtl       | .zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | .zzz    |

## <a name="reopen-a-closed-request"></a>重新開啟已關閉的要求

如果您需要重新開啟已關閉的支援要求，請建立 [新的訊息](#send-a-message)，此訊息會自動重新開啟要求。

## <a name="next-steps"></a>後續步驟

[如何建立 Azure 支援要求](how-to-create-azure-support-request.md)

[Azure 支援票證 REST API](/rest/api/support)
