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
ms.openlocfilehash: 1da1cfff05418219fdf5217b612103e50efca05d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87841862"
---
[Azure 檔案儲存體](../articles/storage/files/storage-files-introduction.md) 支援透過伺服器訊息區進行以身分識別為基礎的驗證， (SMB) 透過 [內部部署 Active Directory Domain Services (](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) AD DS) 和 [Azure Active Directory Domain Services (DS Azure AD ](../articles/active-directory-domain-services/overview.md)。 本文著重于 Azure 檔案共用如何在內部部署或 Azure 中使用網域服務，以支援透過 SMB 對 Azure 檔案共用進行以身分識別為基礎的存取。 針對 Azure 檔案共用啟用以身分識別為基礎的存取，可讓您將現有的檔案伺服器取代為 Azure 檔案共用，而不需要取代現有的目錄服務，以保持無縫的使用者存取共用。 

Azure 檔案儲存體會對共用和目錄/檔案層級的使用者存取強制執行授權。 共用層級許可權指派可以在 Azure Active Directory (Azure AD) 透過 [azure 角色型存取控制 (AZURE RBAC) ](../articles/role-based-access-control/overview.md) 模型所管理的使用者或群組來執行。 使用 RBAC 時，您的檔案存取所使用的認證應可供使用或同步至 Azure AD。 您可以指派 Azure 內建角色（例如儲存體檔案資料 SMB 共用讀者）給 Azure AD 中的使用者或群組，以授與 Azure 檔案共用的讀取權限。

在目錄/檔案層級，Azure 檔案儲存體支援保留、繼承和強制執行 [Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) ，就像任何 Windows 檔案伺服器一樣。 您可以選擇在現有的檔案共用與 Azure 檔案共用之間，透過 SMB 複製資料時保留 Windows Dacl。 無論您是否打算強制執行授權，您都可以使用 Azure 檔案共用來備份 Acl 和您的資料。 
