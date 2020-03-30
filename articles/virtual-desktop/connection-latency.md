---
title: Windows 虛擬桌面使用者連接延遲 - Azure
description: Windows 虛擬桌面使用者的連接延遲。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128187"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>確定 Windows 虛擬桌面中的使用者連接延遲

Windows 虛擬桌面在全球可用。 管理員可以在所需的任何 Azure 區域中創建虛擬機器 （VM）。 連接延遲因使用者和虛擬機器的位置而異。 Windows 虛擬桌面服務將不斷推廣到新的地理位置，以提高延遲。 
 
[Windows 虛擬桌面體驗估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)可説明您確定最佳位置以優化 VM 的延遲。 我們建議您每兩到三個月使用該工具，以確保最佳位置在 Windows 虛擬桌面滾到新區域時未更改。 

## <a name="azure-traffic-manager"></a>Azure 流量管理員

Windows 虛擬桌面使用 Azure 流量管理器，該管理器檢查使用者的 DNS 伺服器的位置以查找最近的 Windows 虛擬桌面服務實例。 我們建議管理員在選擇 VM 的位置之前查看使用者的 DNS 伺服器的位置。

## <a name="next-steps"></a>後續步驟

- 要檢查最佳位置以獲得最佳延遲，請參閱[Windows 虛擬桌面體驗估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)。
- 有關定價計畫，請參閱[Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
- 要開始使用 Windows 虛擬桌面部署，請查看[我們的教程](tenant-setup-azure-active-directory.md)。