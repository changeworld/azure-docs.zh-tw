---
title: Azure VMware 解決方案（AVS）-設定適用于 AVS 私人雲端的工作負載 DNS 和 DHCP
description: 說明如何為您的 AVS 私用雲端環境中執行的應用程式和工作負載設定 DNS 和 DHCP
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024682"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>在您的 AVS 私人雲端中設定 DNS 和 DHCP 應用程式和工作負載

在 AVS 私人雲端環境中執行的應用程式和工作負載需要名稱解析和 DHCP 服務，才能進行查閱和 IP 位址指派。 必須要有適當的 DHCP 和 DNS 基礎結構，才能提供這些服務。 您可以設定虛擬機器，以在您的 AVS 私人雲端環境中提供這些服務。 

## <a name="prerequisites"></a>必要條件

* 已設定 VLAN 的分散式通訊埠群組
* 將設定路由至內部部署或以網際網路為基礎的 DNS 伺服器
* 建立虛擬機器的虛擬機器範本或 ISO

## <a name="linux-based-dns-server-setup"></a>以 Linux 為基礎的 DNS 伺服器設定

Linux 提供各種封裝來設定 DNS 伺服器。 以下是[從 DigitalOcean 設定的範例](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)，以及設定開放原始碼系結 DNS 伺服器的指示。

## <a name="windows-based-setup"></a>以 Windows 為基礎的安裝

這些 Microsoft 主題說明如何將 Windows server 設定為 DNS 伺服器和 DHCP 伺服器。

* [Windows Server 做為 DNS 伺服器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [做為 DHCP 伺服器的 Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
