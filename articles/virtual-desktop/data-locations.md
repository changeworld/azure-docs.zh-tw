---
title: Windows 虛擬桌面的資料位置 - Azure
description: Windows 虛擬桌面的資料和中繼資料存儲在哪些位置的簡要概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128050"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows 虛擬桌面的資料位置

Windows 虛擬桌面當前適用于所有地理位置。 最初，服務中繼資料只能存儲在美國 （美國）地理中。 管理員可以選擇在創建主機池虛擬機器和相關服務（如檔案伺服器）時存儲使用者資料的位置。 在[Azure 資料中心映射](https://azuredatacentermap.azurewebsites.net/)中瞭解有關 Azure 地理位置的更多詳細資訊。

>[!NOTE]
>Microsoft 不控制或限制您或您的使用者可以訪問使用者和應用特定資料的區域。

>[!IMPORTANT]
>Windows 虛擬桌面將全域中繼資料資訊（如租戶名稱、主機池名稱、應用組名稱和使用者主體名稱）存儲在位於美國的資料中心中。 存儲的中繼資料在靜態時加密，並且地理冗余鏡像在美國境內維護。 所有客戶資料（如應用設置和使用者資料）都駐留在客戶選擇的位置，並且不受服務管理。

出於災害復原目的，服務中繼資料將在美國複製。