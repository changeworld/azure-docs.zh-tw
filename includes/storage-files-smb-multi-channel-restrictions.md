---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052874"
---
適用于 Azure 檔案共用的 SMB 多重通道目前有下列限制：
- 只能搭配本機重複的 FileStorage 帳戶使用。
- 僅支援 Windows 用戶端。 
- 通道數目上限為四個。
- 不支援 SMB 直接存取。
- 不支援儲存體帳戶的私人端點。
- 針對內部部署 Active Directory Domain Services (AD DS) 或針對 Azure AD 啟用的 Azure 檔案儲存體 DS 身分 [識別型驗證](../articles/storage/files/storage-files-active-directory-overview.md) 的儲存體帳戶，SMB 用戶端無法使用 Windows 檔案總管設定目錄和檔案的 NTFS 許可權。
    - 請改為使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) 命令來設定許可權。

