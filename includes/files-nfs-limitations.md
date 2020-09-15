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
ms.openlocfilehash: 7e009516721473554517d1677bdef7a7451e4007
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564822"
---
在預覽期間，NFS 有下列限制：

- NFS 4.1 目前僅支援來自 [通訊協定規格](https://tools.ietf.org/html/rfc5661)的強制功能。 不支援選擇性功能，例如所有種類的委派和回呼、鎖定升級和降級，以及 Kerberos 驗證和加密。
- 如果大部分的要求都是以中繼資料為中心，則相較于開啟/關閉作業，延遲會更糟。
- 必須建立新的儲存體帳戶，才能建立 NFS 共用。
- 僅支援管理平面 REST Api。 資料平面 REST Api 無法使用，這表示像儲存體 explorer 這樣的工具無法與 NFS 共用搭配使用，也無法流覽 Azure 入口網站中的 NFS 共用資料。
- 僅適用于 premium 層。
- 目前僅適用于本機的多餘儲存體。

### <a name="azure-storage-features-not-yet-supported"></a>尚不支援 Azure 儲存體功能

此外，NFS 共用也無法使用下列 Azure 檔案儲存體功能：

- 以身分識別為基礎的驗證
- Azure 備份支援
- 快照集
- 虛刪除
- 完整加密傳輸中支援 (如需詳細資訊，請參閱 [NFS 安全性](../articles/storage/files/storage-files-compare-protocols.md#security)) 
- Azure 檔案同步 (僅適用于 Windows 用戶端，不支援 NFS 4.1) 