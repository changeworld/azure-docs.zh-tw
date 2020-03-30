---
title: Azure VMware 解決方案（按雲簡單 - VPN 閘道）
description: 瞭解雲簡單網站到網站 VPN 和點對點 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024852"
---
# <a name="vpn-gateways-overview"></a>VPN 閘道概述

VPN 閘道用於在本地位置的 CloudSimple 區域網路之間或通過公共 Internet 的電腦發送加密流量。  每個區域可以有一個 VPN 閘道，可以支援多個連接。 當您對相同的 VPN 閘道建立多個連線時，所有 VPN 通道都會共用可用的閘道頻寬。

CloudSimple 提供兩種類型的 VPN 閘道：

* 網站到網站 VPN 閘道
* 點對點 VPN 閘道

## <a name="site-to-site-vpn-gateway"></a>網站到網站 VPN 閘道

網站到網站 VPN 閘道用於在 CloudSimple 區域網路和本地資料中心之間發送加密流量。 使用此連接可以定義子網/CIDR 範圍，用於本地網路和雲簡單區域網路之間的網路流量。

VPN 閘道允許您使用來自私有雲的本機服務，以及從本地網路使用私有雲上的服務。  CloudSimple 提供了一個基於策略的 VPN 伺服器，用於從本地網路建立連接。

網站到網站 VPN 的用例：

* 從本地網路中的任何工作站訪問私有雲 vCenter。
* 將本地活動目錄用作 vCenter 標識源。
* 方便地將 VM 範本、ISO 和其他檔從本地資源傳輸到私有雲 vCenter。
* 從本地網路訪問在私有雲上運行的工作負載。

![網站到網站 VPN 連接拓撲](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>加密參數

網站到網站 VPN 連接使用以下預設加密參數來建立安全連線。  從本地 VPN 設備創建連接時，請使用本地 VPN 閘道支援的以下任何參數。

#### <a name="phase-1-proposals"></a>第一階段提案

| 參數 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| IKE 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| 迪菲·赫爾曼集團（DH集團） | 2 | 2 | 2 |
| 存留時間 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>第二階段提案

| 參數 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| 完美前進保密集團（PFS集團） | None | None | None |
| 存留時間 | 1，800 秒 | 1，800 秒 | 1，800 秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> 在 VPN 設備上將 TCP MSS 夾緊設置為 1200。 或者，如果您的 VPN 設備不支援 MSS 夾緊，則可以將隧道介面上的 MTU 設置為 1240 位元組。

## <a name="point-to-site-vpn-gateway"></a>點對點 VPN 閘道

點對點 VPN 用於在 CloudSimple 區域網路和用戶端電腦之間發送加密流量。  點對點 VPN 是訪問私有雲網路的最簡單方法，包括私有雲 vCenter 和工作負載 VM。  如果您遠端連線到私有雲，請使用點對點 VPN 連接。

## <a name="next-steps"></a>後續步驟

* [設置 VPN 閘道](vpn-gateway.md)
