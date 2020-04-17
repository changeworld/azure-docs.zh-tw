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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536571"
---
[Azure 檔案](../articles/storage/files/storage-files-introduction.md)透過[本地活動目錄網域服務 (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (預覽) 和[Azure 活動目錄域服務 (Azure AD DS)](../articles/active-directory-domain-services/overview.md)支援透過伺服器訊息區塊 (SMB) 進行基於身份的身份驗證。 本文重點介紹 Azure 檔共用如何使用本地或 Azure 中的網域服務來支援透過 SMB 對 Azure 檔共享的基於身份的訪問。 為 Azure 檔案共享啟用基於識別的存取允許您使用 Azure 檔共享替換現有檔案伺服器,而無需替換現有目錄服務,從而保持使用者對共享的無縫存取。 

Azure 檔案強制授權使用者存取共享和目錄/檔案等級。 可以在 Azure 活動目錄 (Azure AD) 使用者或透過[基於角色的存取控制 (RBAC)](../articles/role-based-access-control/overview.md)模型管理的組上執行共享級許可權分配。 使用 RBAC 時,用於檔案存取的認證的認證可用或同步到 Azure AD。 您可以將內建 RBAC 角色(如儲存檔案資料 SMB 共用讀取器)分配給 Azure AD 中的使用者或組,以授予對 Azure 檔案共享的讀取存取權限。

在目錄/檔案等級,Azure 檔案支援保留、繼承和強制執行[Windows DACL,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists)就像任何 Windows 檔伺服器一樣。 在現有檔案共享和 Azure 檔案共享之間透過 SMB 複製資料時,可以選擇保留 Windows DACL。 無論是否計劃強制實施授權,都可以使用 Azure 檔共享備份 ACL 以及數據。 
