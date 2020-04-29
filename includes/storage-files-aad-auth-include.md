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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536571"
---
[Azure 檔案儲存體](../articles/storage/files/storage-files-introduction.md)透過內部[部署 Active Directory Domain Services （AD DS）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) （預覽）和[Azure Active Directory Domain Services （Azure AD DS）](../articles/active-directory-domain-services/overview.md)，在伺服器訊息區（SMB）上支援以身分識別為基礎的驗證。 本文著重于 Azure 檔案共用如何在內部部署或 Azure 中使用網域服務，以支援以身分識別為基礎的方式存取 SMB 上的 Azure 檔案共用。 針對您的 Azure 檔案共用啟用身分識別型存取，可讓您以 Azure 檔案共用取代現有的檔案伺服器，而不需要取代現有的目錄服務，以維持順暢的使用者對共用的存取權。 

Azure 檔案儲存體會在使用者存取共用和目錄/檔案層級時強制執行授權。 共用層級許可權指派可以在透過[角色型存取控制（RBAC）](../articles/role-based-access-control/overview.md)模型管理的 Azure Active Directory （Azure AD）使用者或群組上執行。 使用 RBAC 時，您用來存取檔案的認證應該可以使用或同步處理到 Azure AD。 您可以將內建的 RBAC 角色（例如儲存體檔案資料 SMB 共用讀取器）指派給 Azure AD 中的使用者或群組，以授與 Azure 檔案共用的讀取權限。

在目錄/檔案層級，Azure 檔案儲存體支援保留、繼承和強制執行[Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) ，就像任何 windows 檔案伺服器一樣。 您可以選擇在現有檔案共用和 Azure 檔案共用之間透過 SMB 複製資料時，保留 Windows Dacl。 無論您是否計畫強制執行授權，您都可以使用 Azure 檔案共用來備份 Acl 以及您的資料。 
