---
title: 安全性-適用於 MySQL 的 Azure 資料庫
description: 概要說明適用於 MySQL 的 Azure 資料庫中的安全性功能。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5caed64beb43cbb1e884a3dde6bc0052c617f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378775"
---
# <a name="security-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫安全性

有多層的安全性可用來保護您適用於 MySQL 的 Azure 資料庫伺服器上的資料。 本文將概述這些安全性選項。

## <a name="information-protection-and-encryption"></a>資訊保護和加密

### <a name="in-transit"></a>傳輸中
適用於 MySQL 的 Azure 資料庫透過傳輸層安全性來加密傳輸中的資料，藉此保護您的資料。 預設會強制執行加密 (SSL/TLS) 。

### <a name="at-rest"></a>靜止
適用於 MySQL 的 Azure 資料庫服務針對待用資料儲存體加密是使用符合 FIPS 140-2 的加密模組。 包含備份在內的資料會在磁片上加密，包括執行查詢時所建立的暫存檔案。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。


## <a name="network-security"></a>網路安全性
適用於 MySQL 的 Azure 資料庫伺服器的連接會先透過區域閘道進行路由。 閘道具有可公開存取的 IP，而伺服器 IP 位址則受到保護。 如需閘道的詳細資訊，請流覽連線 [架構文章](concepts-connectivity-architecture.md)。  

新建立的適用於 MySQL 的 Azure 資料庫伺服器具有封鎖所有外部連線的防火牆。 雖然它們會觸達閘道，但不允許連接至伺服器。 

### <a name="ip-firewall-rules"></a>IP 防火牆規則
IP 防火牆規則會根據每個要求的原始 IP 位址來授與伺服器的存取權。 如需詳細資訊，請參閱 [防火牆規則總覽](concepts-firewall-rules.md) 。

### <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則
虛擬網路服務端點會透過 Azure 骨幹擴充您的虛擬網路連線能力。 您可以使用虛擬網路規則，讓您的適用於 MySQL 的 Azure 資料庫伺服器允許從虛擬網路中選取的子網進行連線。 如需詳細資訊，請參閱 [虛擬網路服務端點總覽](concepts-data-access-and-security-vnet.md)。

### <a name="private-ip"></a>私人 IP
Private Link 可讓您透過私人端點連接至 Azure 中的適用於 MySQL 的 Azure 資料庫。 Azure Private Link 基本上會將 Azure 服務帶入私人虛擬網路 (VNet) 內部。 PaaS 資源可使用私人 IP 位址來存取，就像 VNet 中的任何其他資源一樣。 如需詳細資訊，請參閱 [private link 總覽](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>存取管理

建立適用於 MySQL 的 Azure 資料庫伺服器時，您會提供系統管理員使用者的認證。 此系統管理員可以用來建立其他的 MySQL 使用者。


## <a name="threat-protection"></a>威脅保護

您可以選擇使用 [Advanced 威脅防護](concepts-data-access-and-security-threat-protection.md) 來偵測異常活動，指出有不尋常且可能有害的存取或惡意探索伺服器的嘗試。

您可以使用[Audit 記錄](concepts-audit-logs.md)來追蹤資料庫中的活動。 


## <a name="next-steps"></a>後續步驟
- 啟用[ip](concepts-firewall-rules.md)或[虛擬網路](concepts-data-access-and-security-vnet.md)的防火牆規則
