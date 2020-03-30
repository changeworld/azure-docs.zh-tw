---
title: Azure VMware 解決方案（按雲簡單 ） - 為私有雲設置工作負載 DNS 和 DHCP
description: 描述如何為雲簡單私有雲環境中運行的應用程式和工作負載設置 DNS 和 DHCP
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024682"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>在雲簡單私有雲中設置 DNS 和 DHCP 應用程式和工作負載

在私有雲環境中運行的應用程式和工作負載需要名稱解析和 DHCP 服務才能進行查找和 IP 位址分配。  需要適當的 DHCP 和 DNS 基礎結構來提供這些服務。  您可以配置虛擬機器以在私有雲環境中提供這些服務。  

## <a name="prerequisites"></a>Prerequisites

* 配置了 VLAN 的分散式埠組
* 將設置路由到本地或基於 Internet 的 DNS 伺服器
* 虛擬機器範本或 ISO 以創建虛擬機器

## <a name="linux-based-dns-server-setup"></a>基於 Linux 的 DNS 伺服器設置

Linux 提供了用於設置 DNS 伺服器的各種套裝軟體。  下面是[數位海洋的一個示例設置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)，其中包含設置開源 BIND DNS 伺服器的說明。

## <a name="windows-based-setup"></a>基於 Windows 的設置

這些 Microsoft 主題描述了如何將 Windows 伺服器設置為 DNS 伺服器和 DHCP 伺服器。

* [視窗伺服器作為 DNS 伺服器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [視窗伺服器為 DHCP 伺服器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
