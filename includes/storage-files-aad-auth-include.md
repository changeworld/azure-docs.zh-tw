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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565075"
---
[Azure 檔案儲存體](../articles/storage/files/storage-files-introduction.md)支援透過[Active Directory （AD）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) （預覽）和[Azure Active Directory Domain Services （Azure AD DS）](../articles/active-directory-domain-services/overview.md) （GA）的伺服器訊息區（SMB）進行以身分識別為基礎的驗證。 本文著重于 Azure 檔案儲存體如何運用內部部署或 Azure 中的網域服務，以支援以身分識別為基礎的 Azure 檔案儲存體透過 SMB 存取。 這可讓您輕鬆地以 Azure 檔案儲存體取代現有的檔案伺服器，並繼續使用您現有的目錄服務，以維持順暢的使用者對共用的存取權。 

Azure 檔案儲存體會在使用者對共用和目錄/檔案層級的存取權上強制執行授權。 共用層級許可權指派可指派給透過一般[角色型存取控制（RBAC）](../articles/role-based-access-control/overview.md)模型管理的 Azure AD 使用者或群組。 使用 RBAC 時，您用來存取檔案的認證應該可以使用或同步處理到 Azure AD。 您可以將內建的 RBAC 角色（例如儲存體檔案資料 SMB 共用讀取器）指派給 Azure AD 中的使用者或群組，以授與 Azure 檔案共用的讀取權限。

在目錄/檔案層級，Azure 檔案儲存體支援保留、繼承和強制執行[Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) ，就像任何 windows 檔案伺服器一樣。 如果您透過 SMB 將資料從檔案共用複製到 Azure 檔案儲存體，或反之亦然，您可以選擇保留 Windows Dacl。 無論您是否計畫強制執行授權，都可以利用 Azure 檔案儲存體來與您的資料一起備份 Acl。 
