---
title: 針對 Azure 匯入/匯出中的匯入和匯出問題進行疑難排解 |Microsoft Docs
description: 瞭解如何在使用 Azure 匯入/匯出時處理常見的問題。
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706300"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>針對 Azure 匯入/匯出中的問題進行疑難排解
本文說明如何針對在 Azure 匯入/匯出中匯入和匯出資料時遇到的常見問題進行疑難排解。

## <a name="a-copy-session-failed-what-i-should-do"></a>複製會話失敗。 我該怎麼做？  

當複製會話失敗時，您有兩個選項：  
* 如果可以重試此錯誤（例如，如果網路共用已離線一段時間，而現在已回到線上），您可以繼續複製會話。
* 如果無法重試此錯誤（例如，如果您在命令列參數中指定了錯誤的來源檔案目錄），則需要中止複製會話。
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>我無法繼續或中止複製工作階段。

如果複製工作階段是磁碟機的第一個複製工作階段，則錯誤訊息應如下所示：「第一個複製工作階段無法繼續或中止。」 在此情況下，您可以刪除舊的日誌檔，然後重新執行命令。  

如果複製會話不是磁片磁碟機的第一個會話，則會話一律可以繼續或中止。  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>我遺失了日誌檔。 我仍然可以建立作業嗎？

磁片磁碟機的日誌檔案包含資料複本的完整會話資訊。 當您將檔案新增至磁片磁碟機時，會使用日誌檔案來建立匯入作業。 如果遺失日誌檔案，您必須重做磁片磁碟機的所有複製會話。

## <a name="next-steps"></a>後續步驟

* [設定 Azure 匯入/匯出工具](storage-import-export-tool-setup-v1.md)
* [準備匯入作業的硬碟](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [使用複製記錄檔檢查作業狀態](storage-import-export-tool-reviewing-job-status-v1.md)
* [修復匯入作業](storage-import-export-tool-repairing-an-import-job-v1.md)
* [修復匯出作業](storage-import-export-tool-repairing-an-export-job-v1.md)