---
title: Windows 虛擬桌面 (傳統) 的資料位置-Azure
description: 簡短介紹 Windows 虛擬桌面 (傳統) 資料和中繼資料儲存在哪些位置。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008809"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows 虛擬桌面 (傳統) 的資料位置

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../data-locations.md)。

Windows 虛擬桌面目前適用于所有地理位置。 一開始，服務中繼資料只能儲存在美國 (US) geography。 系統管理員可以在建立主機集區虛擬機器和相關聯的服務（例如檔案伺服器）時，選擇要用來儲存使用者資料的位置。 深入瞭解 azure [資料中心地圖](https://azuredatacentermap.azurewebsites.net/)上的 azure 地理位置。

>[!NOTE]
>Microsoft 不會控制或限制您或您的使用者可以存取您的使用者和應用程式特定資料的區域。

>[!IMPORTANT]
>Windows 虛擬桌面會在位於美國的資料中心儲存全域中繼資料資訊，例如租使用者名稱、主機集區名稱、應用程式組名和使用者主體名稱。 儲存的中繼資料會在待用時加密，而地理位置冗余鏡像則會在美國內進行維護。 所有客戶資料（例如應用程式設定和使用者資料）都會位於客戶選擇的位置，而不是由服務所管理。

服務中繼資料會在美國複寫，以供災難復原之用。