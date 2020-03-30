---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565075"
---
[Azure 檔](../articles/storage/files/storage-files-introduction.md)通過[活動目錄 （AD） （預覽）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)和 Azure[活動目錄域服務 （Azure AD DS） （GA）](../articles/active-directory-domain-services/overview.md)支援通過伺服器訊息區 （SMB） 進行基於身份的身份驗證。 本文重點介紹 Azure 檔如何利用本地或 Azure 中的域服務來支援通過 SMB 對 Azure 檔的基於標識的訪問。 這允許您輕鬆地將現有檔案伺服器替換為 Azure 檔，並繼續使用現有目錄服務，從而保持使用者對共用的無縫訪問。 

Azure 檔強制授權使用者訪問共用和目錄/檔級別。 可以將共用級別許可權分配分配給 Azure AD 使用者或通過典型的[基於角色的存取控制 （RBAC）](../articles/role-based-access-control/overview.md)模型管理的組。 使用 RBAC 時，用於檔訪問的憑據應可用或同步到 Azure AD。 您可以將內置 RBAC 角色（如存儲檔資料 SMB 共用讀取器）分配給 Azure AD 中的使用者或組，以授予對 Azure 檔共用的讀取存取許可權。

在目錄/檔級別，Azure 檔支援保留、繼承和強制執行[Windows DACL，](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists)就像任何 Windows 檔案伺服器一樣。 如果將資料通過 SMB 從檔共用複製到 Azure 檔，反之亦然，則可以選擇保留 Windows DACL。 無論是否計畫強制實施授權，都可以利用 Azure 檔與資料一起備份 ACL。 
