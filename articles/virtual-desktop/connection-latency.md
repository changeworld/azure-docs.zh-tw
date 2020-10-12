---
title: Windows 虛擬桌面使用者連接延遲-Azure
description: Windows 虛擬桌面使用者的連接延遲。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 403cf584c79bc0a166054ae36c9d2ea50e4b9d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008724"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>判斷 Windows 虛擬桌面中的使用者連接延遲

Windows 虛擬桌面可供全球使用。 系統管理員可以在其所需的任何 Azure 區域中，建立 (Vm) 的虛擬機器。 連接延遲將視使用者和虛擬機器的位置而異。 Windows 虛擬桌面服務會持續推出至新的地理位置，以改善延遲。

[Windows 虛擬桌面體驗估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)可協助您決定最佳的位置，以優化 vm 的延遲。 建議您每兩到三個月使用此工具，以確保在 Windows 虛擬桌面推出至新區域時，最佳位置沒有變更。

## <a name="azure-traffic-manager"></a>Azure 流量管理員

Windows 虛擬桌面使用 Azure 流量管理員，其會檢查使用者 DNS 伺服器的位置，以尋找最接近的 Windows 虛擬桌面服務實例。 建議系統管理員先檢查使用者 DNS 伺服器的位置，然後再選擇 Vm 的位置。

## <a name="next-steps"></a>後續步驟

- 若要查看最佳延遲的最佳位置，請參閱 [Windows 虛擬桌面體驗估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)。
- 如需定價方案，請參閱 [Windows 虛擬桌面定價](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
- 若要開始使用您的 Windows 虛擬桌面部署，請參閱 [我們的教學](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)課程。