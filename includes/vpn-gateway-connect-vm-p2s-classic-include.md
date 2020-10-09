---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173845"
---
建立遠端桌面連線來連線至部署至 VNet 的 VM。 確認您可以連線至 VM 的最佳方法是使用其私人 IP 位址 (而不是電腦名稱) 進行連線。 這樣一來，您會測試以查看您是否可以連線，而不是否已正確設定名稱解析。 

1. 找出 VM 的私人 IP 位址。 若要找出 VM 的私人 IP 位址，請在 Azure 入口網站中查看 VM 的屬性或使用 PowerShell。
2. 確認您已使用點對站 VPN 連線來連線至 VNet。 
3. 若要開啟遠端桌面連線，請在工作列上的搜尋方塊中輸入「RDP」** 或「遠端桌面連線」**，然後選取 [遠端桌面連線]****。 您也可以使用 PowerShell 中的 **mstsc** 命令來開啟遠端桌面連線。 
3. 在 **遠端桌面連線**中，輸入 VM 的私人 IP 位址。 如有必要，您可以選取 [顯示選項]**** 來調整其他設定，然後進行連線。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>針對 VM 的 RDP 連線進行疑難排解

如果您無法透過 VPN 連線與虛擬機器連線，您可以檢查幾件事。 

- 確認您的 VPN 連線成功。
- 確認您正連線至 VM 的私人 IP 位址。
- 輸入 **ipconfig** 來檢查指派給所連線電腦上乙太網路介面卡的 IPv4 位址。 如果 IP 位址位在您要連線的 VNet 位址範圍內，或在您 VPNClientAddressPool 的位址範圍內，就會發生重疊位址空間。 當您的位址空間以這種方式重疊時，網路流量不會連線到 Azure，它會保留在本機網路上。
- 如果您可以使用私人 IP 位址 (而非電腦名稱) 來連線至 VM，請確認您已正確設定 DNS。 如需 VM 的名稱解析運作方式的詳細資訊，請參閱 [VM 的名稱解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
- 請確認 VPN 用戶端設定套件是在您為 VNet 指定 DNS 伺服器 IP 位址之後產生。 如果您更新 DNS 伺服器 IP 位址，請產生並安裝新的 VPN 用戶端設定套件。

如需疑難排解詳細資訊，請參閱[針對 VM 的遠端桌面連線進行疑難排解](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。