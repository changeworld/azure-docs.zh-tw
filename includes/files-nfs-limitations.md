---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 10177dd949ac531027e13cf633b11c16674fd4ab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386639"
---
在預覽期間，NFS 有下列限制：

- NFS 4.1 目前僅支援來自 [通訊協定規格](https://tools.ietf.org/html/rfc5661)的強制功能。 不支援選擇性功能，例如所有種類的委派和回呼、鎖定升級和降級，以及 Kerberos 驗證和加密。
- 如果大部分的要求都是以中繼資料為中心，則相較于讀取/寫入/更新作業，延遲會較差。
- 必須建立新的儲存體帳戶，才能建立 NFS 共用。
- 僅支援管理平面 REST Api。 資料平面 REST Api 無法使用，這表示儲存體總管之類的工具將無法與 NFS 共用搭配運作，也無法流覽 Azure 入口網站中的 NFS 共用資料。
- 僅適用于 premium 層。
- 目前僅適用于本機冗余儲存體 (LRS) 。

### <a name="azure-storage-features-not-yet-supported"></a>尚不支援 Azure 儲存體功能

此外，NFS 共用也無法使用下列 Azure 檔案儲存體功能：

- 以身分識別為基礎的驗證
- Azure 備份支援
- 快照集
- 虛刪除
- 完整加密傳輸中支援 (如需詳細資訊，請參閱 [NFS 安全性](../articles/storage/files/storage-files-compare-protocols.md#security)) 
- Azure 檔案同步 (僅適用于 Windows 用戶端，不支援 NFS 4.1) 
