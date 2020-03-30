---
title: 託管實例自訂 DNS
description: 本主題描述 Azure SQL Database 受控執行個體的自訂 DNS 設定選項。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247073"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的自訂 DNS

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 在少數案例中 (例如，db 電子郵件，連結的伺服器連線至您雲端或混合式環境中的其他 SQL 執行個體)，必須從受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 

由於託管實例對其內部工作使用相同的 DNS，因此配置自訂 DNS 伺服器，以便它可以解析公有功能變數名稱。

> [!IMPORTANT]
> 始終對郵件伺服器、SQL Server 實例和其他服務使用完全限定的功能變數名稱 （FQDN），即使這些服務在專用 DNS 區域內也是如此。 例如，用於`smtp.contoso.com`郵件伺服器，因為`smtp`無法正確解析。 創建連結伺服器或複製以引用同一虛擬網路中的 SQL VM 還需要 FQDN 和預設 DNS 尾碼。 例如： `SQLVM.internal.cloudapp.net` 。 有關詳細資訊，請參閱[使用您自己的 DNS 伺服器的名稱解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虛擬網路 DNS 伺服器不會立即影響託管實例。 託管實例 DNS 配置在 DHCP 租約過期或平臺升級後（以先發生者為准）後更新。 **建議使用者在創建第一個託管實例之前設置其虛擬網路 DNS 配置。**

## <a name="next-steps"></a>後續步驟

- 有關概述，請參閱[什麼是託管實例](sql-database-managed-instance.md)
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需有關設定受控執行個體 VNet 的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)
