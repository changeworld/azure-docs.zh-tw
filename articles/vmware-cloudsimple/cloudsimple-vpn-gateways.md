---
title: Azure VMware Solution by CloudSimple-VPN 閘道
description: 深入瞭解 CloudSimple 站對站和點對站 VPN 閘道，這些閘道可用來在 CloudSimple 區域與其他資源之間傳送加密的流量。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140627"
---
# <a name="vpn-gateways-overview"></a>VPN 閘道總覽

VPN 閘道可用來在內部部署位置的 CloudSimple 區域網路或透過公用網際網路的電腦之間傳送加密的流量。  每個區域可以有一個 VPN 閘道，可支援多個連接。 當您對相同的 VPN 閘道建立多個連線時，所有 VPN 通道都會共用可用的閘道頻寬。

CloudSimple 提供兩種類型的 VPN 閘道：

* 站對站 VPN 閘道
* 點對站 VPN 閘道

## <a name="site-to-site-vpn-gateway"></a>站對站 VPN 閘道

站對站 VPN 閘道可用來在 CloudSimple 區域網路與內部部署資料中心之間傳送加密的流量。 使用此連線來定義您內部部署網路與 CloudSimple 區域網路之間的網路流量的子網/CIDR 範圍。

VPN 閘道可讓您在私人雲端上使用內部部署的服務，並從內部部署網路取用私人雲端上的服務。  CloudSimple 提供以原則為基礎的 VPN 伺服器，用來建立從內部部署網路的連線。

站對站 VPN 的使用案例：

* 您的私人雲端 vCenter 在內部部署網路中的任何工作站的協助工具。
* 使用內部部署 Active Directory 作為 vCenter 身分識別來源。
* 方便將 VM 範本、Iso 和其他檔案從您的內部部署資源傳輸至私人雲端 vCenter。
* 從內部部署網路在私人雲端上執行之工作負載的協助工具。

![站對站 VPN 連線拓撲](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>密碼編譯參數

站對站 VPN 連線會使用下列預設的密碼編譯參數來建立安全連線。  當您從內部部署 VPN 裝置建立連線時，請使用內部部署 VPN 閘道所支援的下列任何參數。

#### <a name="phase-1-proposals"></a>階段1提案

| 參數 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| IKE 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| 將 diffie-hellman 群組 (DH 群組)  | 2 | 2 | 2 |
| 存留時間 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>階段2提案

| 參數 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 雜湊演算法| SHA 256 | SHA 256 | SHA 1 |
| 完美轉寄密碼群組 (PFS 群組)  | 無 | 無 | 無 |
| 存留時間 | 1800秒 | 1800秒 | 1800秒 |
| 資料大小 | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> 在 VPN 裝置上設定1200的 TCP MSS 固定。 或者，如果您的 VPN 裝置不支援 MSS 固定，您也可以將通道介面上的 MTU 改設為1240個位元組。

## <a name="point-to-site-vpn-gateway"></a>點對站 VPN 閘道

點對站 VPN 是用來在 CloudSimple 區域網路與用戶端電腦之間傳送加密的流量。  點對站 VPN 是存取私人雲端網路最簡單的方式，包括私用雲端 vCenter 和工作負載 Vm。  如果您是從遠端連線到私人雲端，請使用點對站 VPN 連線能力。

## <a name="next-steps"></a>後續步驟

* [設定 VPN 閘道](vpn-gateway.md)
