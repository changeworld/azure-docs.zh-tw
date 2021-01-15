---
title: Azure ExpressRoute：適用于網路工程師的 Azure
description: 此頁面會向傳統網路工程師說明網路在 Azure 中的運作方式。
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234200"
---
# <a name="azure-for-network-engineers"></a>適用於網路工程師的 Azure
傳統的網路工程師是您處理的實體資產，例如路由器、交換器、纜線、防火牆，以建立基礎結構。 在邏輯層中，您已設定虛擬 LAN (VLAN) 、跨樹狀結構通訊協定 (STP) 、路由通訊協定 (RIP、OSPF、BGP) 。 您已使用管理工具和 CLI 來管理您的網路。 雲端中的網路功能不同，因為網路端點是邏輯的，且最少使用路由通訊協定。 您將使用 Azure Resource Manager API、Azure CLI 和 PowerShell 來設定和管理 Azure 中的資產。 您將瞭解 Azure 網路功能的基本租使用者，以開始使用雲端中的網路旅程。 
## <a name="virtual-network"></a>虛擬網路
當您從底部設計網路時，您會收集一些基本資訊。 這項資訊可能是主機數目、網路裝置、子網數目、子網之間的路由、隔離網域（例如 Vlan）。 這項資訊可協助您調整網路和安全性裝置的大小，以及建立支援應用程式和服務的架構。

當您打算在 Azure 中部署應用程式和服務時，您將從在 Azure 中建立邏輯界限（稱為虛擬網路）開始。 此虛擬網路類似于實體網路界限。 因為這是虛擬網路，您不需要實體齒輪，但仍需要規劃邏輯實體，例如 IP 位址、IP 子網、路由及原則。

當您在 Azure 中建立虛擬網路時，它會預先設定 IP 範圍 (10.0.0.0/16) 。 此範圍不是固定的，您可以定義自己的 IP 範圍。 您可以定義 IPv4 和 IPv6 位址範圍。 為虛擬網路定義的 IP 範圍不會向網際網路公告。 您可以從您的 IP 範圍建立多個子網。 這些子網將用來將 IP 位址指派給虛擬網路介面 (Vnic) 。 每個子網的前四個 IP 位址會保留，且無法用於 IP 配置。 公用雲端中的 Vlan 沒有任何概念。 不過，您可以根據所定義的子網，在虛擬網路內建立隔離。

您可以建立一個包含所有虛擬網路位址空間的大型子網，或選擇建立多個子網。 不過，如果您使用虛擬網路閘道，Azure 會要求您建立名為「閘道子網」的子網。 Azure 會使用此子網將 IP 位址指派給虛擬網路閘道。 

## <a name="ip-allocation"></a>IP 配置

當您將 IP 位址指派給主機時，您實際上會將 IP 指派給網路介面卡， (NIC) 。 您可以將兩種類型的 IP 位址指派給 Azure 中的 NIC：

- 公用 IP 位址-用來在沒有網路位址轉譯的情況下傳達輸入和輸出 ( (NAT) # A3 與網際網路，以及其他未連線至虛擬網路的 Azure 資源。 將公用 IP 位址指派給 NIC 是選擇性作業。 公用 IP 位址屬於 Microsoft IP 位址空間。
- 私人 IP 位址-用於虛擬網路、您的內部部署網路和網際網路 (（使用 NAT) ）內的通訊。 即使您設定了公用 IP 位址空間，在虛擬網路中定義的 IP 位址空間仍會被視為私用。 Microsoft 不會將此空間公告至網際網路。 您必須將至少一個私人 IP 位址指派給 VM。

與實體主機或裝置一樣，有兩種方式可將 IP 位址配置給資源動態或靜態。 在 Azure 中，預設配置方法為動態，其中 IP 位址會在您建立虛擬機器 (VM) 或啟動已停止的 VM 時配置。 此 IP 位址會在您停止或刪除 VM 時釋出。 若要確保 VM 的 IP 位址維持不變，您可以明確地將配置方法設定為靜態。 在此情況下會立即指派 IP 位址。 只有在您刪除 VM 或將其配置方法變更為動態時，才會釋出 IP 位址。 

私人 IP 位址是從您已在虛擬網路中定義的子網進行配置。 針對 VM，您可以選擇 IP 配置的子網。 如果 VM 包含多個 Nic，您可以為每個 NIC 選擇不同的子網。

## <a name="routing"></a>路由
當您建立虛擬網路時，Azure 會為您的網路建立路由表。 此路由表包含下列類型的路由。
- 系統路由
- 子網預設路由
- 來自其他虛擬網路的路由
- BGP 路由
- 服務端點路由
-  (UDR 的使用者定義路由) 

當您第一次建立虛擬網路而未定義任何子網時，Azure 會在路由表中建立路由專案。 這些路由稱為系統路由。 系統會在此位置定義系統路由。 您無法修改這些路由。 不過，您可以藉由設定 Udr 來覆寫系統路由。

當您在虛擬網路內建立一或多個子網時，Azure 會在路由表中建立預設專案，以啟用虛擬網路內這些子網之間的通訊。 您可以使用靜態路由來修改這些路由，也就是在 Azure 中 (UDR) 的使用者定義路由。

當您在兩個虛擬網路間建立虛擬網路對等互連時，系統會在每個建立對等連線的虛擬網路位址空間中，為每個位址範圍新增路由。

如果您的內部部署網路閘道會交換邊界閘道協定 (BGP) 路由與 Azure 虛擬網路閘道，則系統會針對每個從內部部署網路閘道散佈的每個路由新增路由。 這些路由會以 BGP 路由的形式出現在路由表中。

當您對特定服務啟用服務端點時，Azure 會將該服務的公用 IP 位址新增至路由表。 系統會為虛擬網路內的個別子網啟用服務端點。 當您啟用服務端點時，只會將路由新增至屬於此服務之子網的路由表。 當位址變更時，Azure 會自動管理路由表中的位址。

使用者定義的路由也稱為自訂路由。 您可以在 Azure 中建立 UDR 來覆寫 Azure 的預設系統路由，或將其他路由新增至子網的路由表。 在 Azure 中，您會建立路由表，然後將路由表與虛擬網路子網產生關聯。

當您在路由表中有競爭專案時，Azure 會根據與傳統路由器類似的最長首碼比對，來選取下一個躍點。 但是，如果有多個下一個躍點專案具有相同的位址首碼，則 Azure 會依下列順序選取路由。
1. 使用者定義的路由 (UDR) 
1. BGP 路由
1. 系統路由

## <a name="security"></a>安全性

您可以使用網路安全性群組來篩選虛擬網路中進出資源的網路流量。 您也可以使用網路虛擬裝置 (NVA) 例如 Azure 防火牆或其他廠商的防火牆。 您可以控制 Azure 路由傳送來自子網路流量的方法。 您也可以限制組織中能使用虛擬網路中資源的人員。

網路安全性群組 (NSG) 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路、NIC 或兩者的網路流量。 NSG 可與子網路或連接到子網路的個別 VM 相關聯。 當 NSG 與子網路相關聯時，ACL 規則便會套用至該子網路中的所有 VM。 此外，將 NSG 直接關聯至 NIC，即可限制個別 NIC 的流量。

NSG 包含兩組規則：輸入和輸出。 規則的優先順序在每一個集合中必須是唯一的。 每個規則都有通訊協定、來源和目的地連接埠範圍、位址前置詞、流量方向、優先順序和存取類型的屬性。 所有 NSG 都包含一組預設規則。 預設規則無法刪除，但因為其會指派為最低優先權，因此可以由您所建立的規則覆寫預設規則。

## <a name="verification"></a>驗證
### <a name="routes-in-virtual-network"></a>虛擬網路中的路由
您所建立的路由、Azure 的預設路由和任何路由的組合若透過邊界閘道協定 (BGP) 經由 Azure VPN 閘道 (如果您的虛擬網路連線至內部部署網路) 從您的內部部署網路進行傳播，對子網路中的所有網路介面而言都將是有效路由。 您可以透過入口網站、PowerShell 或 CLI 流覽至 NIC，查看這些有效的路由。
### <a name="network-security-groups"></a>網路安全性群組
套用至網路介面的有效安全性規則，乃是針對網路介面及該網路介面所在子網路關聯的 NSG，存在其中的規則彙總。 您可以透過入口網站、PowerShell 或 CLI 流覽至 NIC，來查看 VM 網路介面上套用的 Nsg 中的所有有效安全性規則。

## <a name="next-steps"></a>後續步驟

深入瞭解 [虛擬網路][VNet]。

深入瞭解 [虛擬網路路由][vnet-routing]。

深入瞭解 [網路安全性群組][network-security]。

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md