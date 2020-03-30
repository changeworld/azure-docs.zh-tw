---
title: 安全託管實例公共終結點
description: 在 Azure 中使用具有託管實例的公共終結點
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821747"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>使用 Azure SQL 資料庫託管實例與公共終結點一起安全

Azure SQL 資料庫託管實例可以在[公共終結點](../virtual-network/virtual-network-service-endpoints-overview.md)上提供使用者連接。 本文介紹如何使此配置更安全。

## <a name="scenarios"></a>案例

SQL 資料庫託管實例提供專用終結點，以允許來自其虛擬網路內部的連接。 預設選項是提供最大隔離。 但是，在某些情況下，您需要提供公共終結點連接：

- 託管實例必須與多租戶平臺即服務 （PaaS） 產品集成。
- 與使用 VPN 時相比，您需要更高的資料交換輸送量。
- 公司政策禁止在公司網路內使用 PaaS。

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>部署公共終結點訪問的託管實例

儘管不是強制性的，但具有公共終結點存取權限的託管實例的常見部署模型是在專用的隔離虛擬網路中創建實例。 在此配置中，虛擬網路僅用於虛擬叢集隔離。 託管實例的 IP 位址空間是否與商業網路的 IP 位址空間重疊並不重要。

## <a name="secure-data-in-motion"></a>保護動態資料

如果用戶端驅動程式支援加密，則託管實例資料流量始終加密。 託管實例和其他 Azure 虛擬機器或 Azure 服務之間發送的資料永遠不會離開 Azure 的主幹。 如果託管實例和本地網路之間存在連接，我們建議您在 Microsoft 對等互連中使用 Azure ExpressRoute。 ExpressRoute 可説明您避免在公共互聯網上移動資料。 對於託管實例專用連線，只能使用專用對等互連。

## <a name="lock-down-inbound-and-outbound-connectivity"></a>鎖定入站和出站連接

下圖顯示了建議的安全配置：

![用於鎖定入站和出站連接的安全配置](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

託管實例具有[專用的公共終結點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。 在用戶端出站防火牆和網路安全性群組規則中，設置此公共終結點 IP 位址以限制出站連接。

為了確保託管實例的流量來自受信任的源，我們建議從具有已知 IP 位址的源進行連接。 使用網路安全性群組限制對埠 3342 上的託管實例公共終結點的訪問。

當用戶端需要從本地網路啟動連接時，請確保原始位址已轉換為一組已知的 IP 位址。 如果您無法執行此操作（例如，移動員工是典型方案），我們建議您使用[點對點 VPN 連接和專用終結點](sql-database-managed-instance-configure-p2s.md)。

如果連接是從 Azure 啟動的，我們建議流量來自眾所周知的分配的[虛擬 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md)（例如虛擬機器）。 為了簡化虛擬 IP （VIP） 位址的管理，您可能需要使用公共[IP 位址首碼](../virtual-network/public-ip-address-prefix.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解如何為管理實例配置公共終結點：[配置公共終結點](sql-database-managed-instance-public-endpoint-configure.md)
