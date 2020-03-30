---
title: 安全性 - MySQL 的 Azure 資料庫
description: MySQL Azure 資料庫中的安全功能概述。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: aac2641913331095550c0e19cc587257a996fcce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537018"
---
# <a name="security-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫安全性

有多層安全可用於保護 MySQL 伺服器 Azure 資料庫上的資料。 本文概述了這些安全選項。

## <a name="information-protection-and-encryption"></a>資訊保護和加密

### <a name="in-transit"></a>在途
MySQL 的 Azure 資料庫通過使用傳輸層安全性加密傳輸中的資料來保護資料。 預設情況下，將強制執行加密 （SSL/TLS）。

### <a name="at-rest"></a>休息
適用於 MySQL 的 Azure 資料庫服務針對待用資料儲存體加密是使用符合 FIPS 140-2 的加密模組。 資料（包括備份）在磁片上加密，但執行查詢時創建的暫存檔案除外。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。


## <a name="network-security"></a>網路安全性
與 MySQL 伺服器的 Azure 資料庫的連接首先通過區域閘道路由。 閘道具有可公開訪問的 IP，而伺服器 IP 位址受到保護。 有關閘道的詳細資訊，請訪問[連接體系結構文章](concepts-connectivity-architecture.md)。  

新創建的 MySQL 伺服器 Azure 資料庫具有阻止所有外部連接的防火牆。 儘管它們到達閘道，但它們不允許連接到伺服器。 

### <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則根據每個請求的原始 IP 位址授予對伺服器的存取權限。 有關詳細資訊，請參閱[防火牆規則概述](concepts-firewall-rules.md)。

### <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則
虛擬網路服務終結點通過 Azure 主幹擴展虛擬網路連接。 使用虛擬網路規則，可以使 MySQL 伺服器的 Azure 資料庫允許從虛擬網路中的選定子網進行連接。 有關詳細資訊，請參閱[虛擬網路服務終結點概述](concepts-data-access-and-security-vnet.md)。

### <a name="private-ip"></a>私人 IP
專用連結允許您通過專用終結點連接到 Azure 中 MySQL 的 Azure 資料庫。 Azure 專用連結實質上將 Azure 服務引入專用虛擬網路 （VNet）。 可以使用私人 IP 位址訪問 PaaS 資源，就像 VNet 中的任何其他資源一樣。 有關詳細資訊，請參閱[專用連結概述](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>存取管理

為 MySQL 伺服器創建 Azure 資料庫時，可以為管理員使用者提供憑據。 此管理員可用於創建其他 MySQL 使用者。


## <a name="threat-protection"></a>威脅保護

您可以加入宣告[高級威脅保護](concepts-data-access-and-security-threat-protection.md)，該保護可檢測異常活動，指示訪問或利用伺服器的異常和潛在有害嘗試。

[稽核記錄記錄](concepts-audit-logs.md)可用於跟蹤資料庫中的活動。 


## <a name="next-steps"></a>後續步驟
- 為[IP](concepts-firewall-rules.md)或[虛擬網路](concepts-data-access-and-security-vnet.md)啟用防火牆規則