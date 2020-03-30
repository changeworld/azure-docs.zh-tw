---
title: 如何配置託管實例
description: 了解如何設定與管理 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256909"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>如何在 Azure SQL Database 中使用受控執行個體

本文提供各種指南、指令碼和講解說明，協助您管理和設定您的受控執行個體。

## <a name="migration"></a>移轉

- [移轉至受控執行個體](sql-database-managed-instance-migrate.md) – 了解移轉至受控執行個體的建議移轉程序與工具。

- [將 TDE 憑證移轉至受控執行個體](sql-database-managed-instance-migrate-tde-certificate.md) – 如果您的 SQL Server 資料庫是由透明資料加密 (TDE) 加以保護，您將必須移轉受控執行個體可用來對您要在 Azure 中還原的備份進行解密的憑證。

## <a name="network-configuration"></a>網路組態

- [決定受控執行個體子網路的大小](sql-database-managed-instance-determine-size-vnet-subnet.md) – 受控執行個體放置在專用的子網路中，在子網路中新增資源後，便無法再調整子網路的大小。 因此需視您要在子網路中部署的執行個體數目與類型，來計算子網路所需的 IP 位址範圍。
- [建立受控執行個體的新 VNet 和子網路](sql-database-managed-instance-create-vnet-subnet.md) – 請務必根據[此處說明的網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)來設定要部署受控執行個體的 Azure VNet 和子網路。 本指南會提供最簡單的方法，說明如何建立新 VNet 與子網路，並針對受控執行個體將 VNet 與子網路設定妥當。
- [針對受控執行個體設定現有的 VNet 與子網路](sql-database-managed-instance-configure-vnet-subnet.md) – 如果您想要設定現有的 VNet 與子網路，以便在內部部署受控執行個體，此處提供了可用來檢查[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)的指令碼，並可根據需求設定您的子網路。
- [設定自訂 DNS](sql-database-managed-instance-custom-dns.md) – 如果您想要透過 db 郵件設定檔連結的伺服器，從受控執行個體存取自訂網域上的外部資源，則需要設定自訂 DNS。
- [同步網路設定](sql-database-managed-instance-sync-network-configuration.md) – 雖然您已[將應用程式與 Azure 虛擬網路整合在一起](../app-service/web-sites-integrate-with-vnet.md)，仍可能發生您無法與受控執行個體建立連線的狀況。 您可以嘗試重新整理服務方案的網路組態。
- [查找管理終結點 IP 位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)– 託管實例使用公共終結點進行管理。 您可以使用此處所述的指令碼來決定管理端點的 IP 位址。
- [確認內建防火牆保護](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – 藉由僅允許必要連接埠上的流量的內建防火牆來保護受控執行個體。 您可以使用本指南所述的指令碼來檢查與驗證內建的防火牆。
- [連線應用程式](sql-database-managed-instance-connect-app.md) – 受控執行個體置於私人 Azure VNet 且具有私人 IP 位址。 了解將應用程式連線到受控執行個體的不同模式。

## <a name="feature-configuration"></a>功能組態

- [異動複寫](replication-with-sql-database-managed-instance.md)可讓您在受控執行個體之間複寫資料，或從內部部署 SQL Server 複製至受控執行個體，反之亦然。 在本指南中可深入了解如何使用與設定異動複寫的相關說明。
- [設定威脅偵測](sql-database-managed-instance-threat-detection.md)：[威脅偵測](sql-database-threat-detection-overview.md)是內建的 Azure SQL Database 功能，可偵測各種潛在的攻擊，例如 SQL 插入式攻擊或是從可疑位置進行的存取。 在本指南中，您可以了解如何啟用與設定受控執行個體的[威脅偵測](sql-database-threat-detection-overview.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解[單一資料庫的操作指南](sql-database-howto-single-database.md)