---
title: 保護 Azure SQL 受控執行個體公用端點
description: 安全地使用 Azure SQL 受控執行個體中的公用端點
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: a3339d03607c2286dabbac73fd0b683c61552dc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708598"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>以公用端點安全地使用 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體可以透過[公用端點](../../virtual-network/virtual-network-service-endpoints-overview.md)提供使用者連線能力。 本文說明如何讓此設定更安全。

## <a name="scenarios"></a>案例

Azure SQL 受控執行個體提供私人端點，以允許來自其虛擬網路內的連線能力。 預設選項是提供最大隔離。 不過，在某些情況下，您需要提供公用端點連接：

- 受控實例必須與多租使用者專用的平臺即服務（PaaS）供應專案整合。
- 當您使用 VPN 時，您所需的資料交換輸送量會高於可能。
- 公司原則禁止 PaaS 內部的公司網路。

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>部署公用端點存取的受控實例

雖然不是強制性的，但具有公用端點存取權的受控實例通用部署模型，是在專用的隔離虛擬網路中建立實例。 在此設定中，虛擬網路僅用於虛擬叢集隔離。 受控實例的 IP 位址空間與公司網路的 IP 位址空間是否重迭，並不重要。

## <a name="secure-data-in-motion"></a>保護移動中的資料

如果用戶端驅動程式支援加密，SQL 受控執行個體資料流量一律會加密。 在受控實例和其他 Azure 虛擬機器或 Azure 服務之間傳送的資料，絕不會離開 Azure 的骨幹。 如果受控實例與內部部署網路之間有連線，建議您使用 Azure ExpressRoute。 ExpressRoute 可協助您避免透過公用網際網路移動資料。 針對受控實例私人連線能力，只能使用私人對等互連。

## <a name="lock-down-inbound-and-outbound-connectivity"></a>鎖定輸入和輸出連線能力

下圖顯示建議的安全性設定：

![用於鎖定輸入和輸出連線能力的安全性設定](./media/public-endpoint-overview/managed-instance-vnet.png)

受控實例具有專用的[公用端點位址](management-endpoint-find-ip-address.md)。 在用戶端輸出防火牆和網路安全性群組規則中，將此公用端點 IP 位址設定為限制輸出連線能力。

若要確保受控實例的流量來自受信任的來源，建議您從具有已知 IP 位址的來源進行連接。 使用網路安全性群組來限制對埠3342上受控實例公用端點的存取。

當用戶端需要起始來自內部部署網路的連線時，請確定原始位址已轉譯成一組知名的 IP 位址。 如果您無法這麼做（例如，行動工作人員是典型的案例），建議您使用[點對站 VPN 連線和私人端點](point-to-site-p2s-configure.md)。

如果從 Azure 啟動連線，我們建議流量來自知名的已指派[虛擬 IP 位址](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)（例如虛擬機器）。 為了簡化管理虛擬 IP （VIP）位址的作業，您可能會想要使用[公用 IP 位址首碼](../../virtual-network/public-ip-address-prefix.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解如何設定管理實例的公用端點：[設定公用端點](public-endpoint-configure.md)
