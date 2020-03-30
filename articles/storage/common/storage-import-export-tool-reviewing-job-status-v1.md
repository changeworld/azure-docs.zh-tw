---
title: 檢閱 Azure 匯入/匯出作業狀態 - v1 | Microsoft Docs
description: 了解如何使用執行匯入或匯出作業時建立的記錄檔來查看匯入/匯出作業的狀態。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978444"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>使用複製記錄檔來檢閱 Azure 匯入/匯出作業狀態
當 Microsoft Azure 匯入/匯出服務處理與匯入或匯出作業相關聯的磁碟機時，它會將複製記錄檔寫入到您匯入或匯出 blob 的儲存體帳戶。 記錄檔包含每個已匯入或匯出檔案的詳細狀態。 查詢已完成作業的狀態時會傳回每個複製記錄檔的 URL；如需詳細資訊，請參閱[取得作業](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)。  

## <a name="example-urls"></a>範例 URL

以下是包含兩個磁碟機之匯入作業的複製記錄檔的範例 URL：  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 有關複製日誌的格式和狀態碼的完整清單，請參閱[導入/匯出服務日誌檔案格式](../storage-import-export-file-format-log.md)。  

## <a name="next-steps"></a>後續步驟

 * [設置 Azure 導入/匯出工具](storage-import-export-tool-setup-v1.md)   
 * [為導入作業準備硬碟](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [修復導入作業](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [修復匯出作業](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [排除 Azure 導入/匯出工具的故障](storage-import-export-tool-troubleshooting-v1.md)
