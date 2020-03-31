---
title: 為容錯移轉 VM 自訂網路設定 |微軟文檔
description: 提供使用 Azure 網站恢復複製 Azure VM 時容錯移轉 VM 的自訂網路設定概述。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292853"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自訂目標 Azure 虛擬機器的網路設定

本文提供有關使用[Azure 網站恢復](site-recovery-overview.md)將 Azure VM 從一個區域複製和恢復 Azure VM 時在目標 Azure 虛擬機器 （VM） 上自訂網路設定的指導。

## <a name="before-you-start"></a>開始之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。

## <a name="supported-networking-resources"></a>支援的網路資源

在複製 Azure VM 時，可以為容錯移轉 VM 提供以下關鍵資源配置：

- [內部負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [公共智慧財產權](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子網和 NIC 的[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>Prerequisites

- 確保提前規劃恢復端配置。
- 提前創建網路資源。 將其作為輸入提供，以便 Azure 網站恢復服務可以遵守這些設置並確保容錯移轉 VM 遵守這些設置。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>自訂容錯移轉和測試容錯移轉網路設定

1. 轉到**複製項**。 
2. 選擇所需的 Azure VM。
3. 選擇 **"計算和網路"** 並選擇 **"編輯**"。 請注意，NIC 配置設置在源處包含相應的資源。 

     ![自訂容錯移轉網路設定](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 選擇測試容錯移轉虛擬網路。 您可以選擇將其留空，並在測試容錯移轉時選擇一個。
5. 容錯移轉網路是在要配置的 NIC 附近選擇**編輯**。 在打開的下一個邊欄選項卡中，在測試容錯移轉和容錯移轉位置中選擇相應的預創建資源。

    ![編輯 NIC 配置](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. 選取 [確定]****。

網站恢復現在將遵守這些設置，並確保容錯移轉上的 VM 通過相應的 NIC 連接到所選資源。

通過恢復計畫觸發測試容錯移轉時，它將始終詢問 Azure 虛擬網路。 此虛擬網路將用於未預配置測試容錯移轉設置的電腦的測試容錯移轉。

## <a name="troubleshooting"></a>疑難排解

### <a name="unable-to-view-or-select-a-resource"></a>無法查看或選擇資源

如果無法選擇或查看網路資源，請通過以下檢查和條件：

- 僅當源 VM 具有相應的輸入時，才啟用網路資源的目標欄位。 這基於以下原則：對於災害復原方案，您需要源的精確版本或縮減版本。
- 對於每個網路資源，在下拉清單中應用一些篩選器，以確保容錯移轉 VM 可以將自己附加到所選資源並保持容錯移轉可靠性。 這些篩選器基於配置源 VM 時已驗證的相同網路條件。

內部負載等化器驗證：

- 負載等化器和目標 VM 的訂閱和區域應相同。
- 與內部負載等化器和目標 VM 關聯的虛擬網路應相同。
- 目標 VM 的公共 IP SKU 和內部負載等化器的 SKU 應相同。
- 如果目標 VM 配置為放置在可用性區域中，則檢查負載等化器是區域冗余還是任何可用性區域的一部分。 （基本 SKU 負載等化器不支援區域，在這種情況下不會顯示在下拉清單中。
- 確保內部負載等化器具有預先創建的後端池和前端配置。

公共 IP 位址：

- 公共 IP 和目標 VM 的訂閱和區域應相同。
- 目標 VM 的公共 IP SKU 和內部負載等化器的 SKU 應相同。

網路安全性群組︰
- 網路安全性群組和目標 VM 的訂閱和區域應相同。


> [!WARNING]
> 如果目標 VM 與可用性集相關聯，則需要將同一 SKU 的公共 IP 和內部負載等化器與其他 VM 的公共 IP 和可用性集中的內部負載等化器關聯。 如果沒有，容錯移轉可能不會成功。
