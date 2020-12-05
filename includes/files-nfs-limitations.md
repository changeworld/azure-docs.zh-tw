---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620933"
---
在預覽期間，NFS 有下列限制：

- NFS 4.1 目前僅支援來自 [通訊協定規格](https://tools.ietf.org/html/rfc5661)的大部分功能。 不支援某些功能，例如所有種類的委派和回呼、鎖定升級和降級、Kerberos 驗證和加密。
- 如果大部分的要求都是以中繼資料為中心，則相較于讀取/寫入/更新作業，延遲會較差。
- 必須建立新的儲存體帳戶，才能建立 NFS 共用。
- 僅支援管理平面 REST Api。 資料平面 REST Api 無法使用，這表示儲存體總管之類的工具將無法與 NFS 共用搭配運作，也無法流覽 Azure 入口網站中的 NFS 共用資料。
- 目前不支援 AzCopy。
- 僅適用于 premium 層。
- NFS 共用只接受數值 UID/GID。 若要避免您的用戶端傳送英數位元 UID/GID，您應該停用識別碼對應。
- 使用私用連結時，只能從個別 VM 上的一個儲存體帳戶裝載共用。 嘗試從其他儲存體帳戶掛接共用將會失敗。

### <a name="azure-storage-features-not-yet-supported"></a>尚不支援 Azure 儲存體功能

此外，NFS 共用也無法使用下列 Azure 檔案儲存體功能：

- 以身分識別為基礎的驗證
- Azure 備份支援
- 快照集
- 虛刪除
- 完整加密傳輸中支援 (如需詳細資訊，請參閱 [NFS 安全性](../articles/storage/files/storage-files-compare-protocols.md#security)) 
- Azure 檔案同步 (僅適用于 Windows 用戶端，不支援 NFS 4.1) 
