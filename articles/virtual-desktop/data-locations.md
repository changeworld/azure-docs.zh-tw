---
title: Windows 虛擬桌面的資料位置-Azure
description: 簡要介紹哪些位置 Windows 虛擬桌面的資料和中繼資料儲存在中。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611529"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows 虛擬桌面的資料位置

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的春季2020更新。 如果您使用的是 Windows 虛擬桌面不含 Azure Resource Manager 物件的2019版，請參閱[這篇文章](./virtual-desktop-fall-2019/data-locations-2019.md)。
>
> Windows 虛擬桌面春季2020更新目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議針對生產環境工作負載使用。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虛擬桌面目前適用于所有地理位置。 系統管理員可以在建立主機集區虛擬機器和相關聯的服務（例如檔案伺服器）時，選擇要儲存使用者資料的位置。 深入瞭解 azure[資料中心地圖](https://azuredatacentermap.azurewebsites.net/)上的 azure 地理位置。

>[!NOTE]
>Microsoft 不會控制或限制您或您的使用者可以存取您的使用者和應用程式特定資料的區域。

>[!IMPORTANT]
>Windows 虛擬桌面會儲存全球中繼資料資訊，例如租使用者名稱、主機集區名稱、應用程式組名，以及資料中心內的使用者主體名稱。 每當客戶建立服務物件時，他們必須輸入服務物件的位置。 他們輸入的位置會決定將儲存物件之中繼資料的位置。 客戶會選擇 Azure 區域，並將中繼資料儲存在相關的地理位置。 如需所有 Azure 區域和相關地理位置的清單，請參閱[azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。

目前，我們只支援將中繼資料儲存在美國（US） Azure 地理位置。 儲存的中繼資料會在待用時加密，而異地冗余鏡像則會在地理位置進行維護。 所有客戶資料（例如應用程式設定和使用者資料）都位於客戶選擇的位置，而不是由服務所管理。 隨著服務成長，將會有更多的地理位置可供使用。

服務中繼資料會在 Azure 地理位置中進行複寫，以供嚴重損壞修復之用。