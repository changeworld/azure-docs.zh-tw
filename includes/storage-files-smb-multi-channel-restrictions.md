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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995458"
---
適用于 Azure 檔案共用的 SMB 多重通道目前有下列限制：
- 只能搭配本機重複的 FileStorage 帳戶使用。
- 僅支援 Windows 用戶端。 
- 通道數目上限為四個。
- 不支援 SMB 直接存取。
- 針對內部部署 Active Directory Domain Services (AD DS) 或針對 Azure AD 啟用的 Azure 檔案儲存體 DS 身分 [識別型驗證](../articles/storage/files/storage-files-active-directory-overview.md) 的儲存體帳戶，SMB 用戶端無法使用 Windows 檔案總管設定目錄和檔案的 NTFS 許可權。
    - 請改為使用 Windows [icacls](/windows-server/administration/windows-commands/icacls) 工具或 [Set ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) 命令來設定許可權。

