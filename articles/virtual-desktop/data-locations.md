---
title: Windows 虛擬桌面的資料位置-Azure
description: 簡短介紹 Windows 虛擬桌面的資料和中繼資料儲存在哪些位置。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010067"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Windows 虛擬桌面的資料和中繼資料位置

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/data-locations-2019.md)。

Windows 虛擬桌面目前適用于所有地理位置。 系統管理員可以在建立主機集區虛擬機器和相關聯的服務（例如檔案伺服器）時，選擇要用來儲存使用者資料的位置。 深入瞭解 azure [資料中心地圖](https://azuredatacentermap.azurewebsites.net/)上的 azure 地理位置。

>[!NOTE]
>Microsoft 不會控制或限制您或您的使用者可以存取您的使用者和應用程式特定資料的區域。

>[!IMPORTANT]
>Windows 虛擬桌面會將全域中繼資料資訊（例如租使用者名稱、主機集區名稱、應用程式組名，以及資料中心內的使用者主體名稱）儲存起來。 每當客戶建立服務物件時，他們必須輸入服務物件的位置。 他們輸入的位置會決定物件中繼資料的儲存位置。 客戶將會選擇 Azure 區域，而且中繼資料會儲存在相關的地理位置。 如需所有 Azure 區域和相關地理位置的清單，請參閱 [azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。

目前，我們只支援將中繼資料儲存在美國 (US) Azure 地理位置。 儲存的中繼資料會在待用時加密，而且地理區域冗余鏡像會在地理位置進行維護。 所有客戶資料（例如應用程式設定和使用者資料）都會位於客戶選擇的位置，而不是由服務所管理。 隨著服務成長，將會有更多地理位置可供使用。

服務中繼資料會在 Azure 地理位置中複寫，以供災難修復之用。