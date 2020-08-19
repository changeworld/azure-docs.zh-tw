---
title: 檢閱 Azure 匯入/匯出作業狀態 - v1 | Microsoft Docs
description: 瞭解如何使用匯入或匯出作業所建立的記錄檔來查看作業的狀態。
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: c9093af25e621b3ab9a60d9894f0a875340fda45
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525664"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>使用複製記錄檔來檢閱 Azure 匯入/匯出作業狀態
當 Microsoft Azure 匯入/匯出服務處理與匯入或匯出作業相關聯的磁片磁碟機時，它會將複製記錄檔寫入您用來匯入或匯出 blob 的儲存體帳戶。 記錄檔包含每個已匯入或匯出檔案的詳細狀態。 當您查詢已完成作業的狀態時，服務會傳回每個複製記錄檔的 URL。 如需詳細資訊，請參閱 [取得工作](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)。  

## <a name="example-urls"></a>範例 URL

以下是包含兩個磁碟機之匯入作業的複製記錄檔的範例 URL：  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 如需複製記錄的格式和狀態碼的完整清單，請參閱匯 [入/匯出服務記錄檔格式](../storage-import-export-file-format-log.md) 。  

## <a name="next-steps"></a>接下來的步驟

 * [設定 Azure 匯入/匯出工具](storage-import-export-tool-setup-v1.md)   
 * [準備匯入作業的硬碟](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [修復匯入作業](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [修復匯出作業](../storage-import-export-tool-repairing-an-export-job-v1.md)
