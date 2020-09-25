---
title: 自訂 DNS
titleSuffix: Azure SQL Managed Instance
description: 本主題說明使用 Azure SQL 受控執行個體的自訂 DNS 設定選項。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 9fdd61c5799724ab3ab098584f85ce52cdd6ccf4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283253"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>設定 Azure SQL 受控執行個體的自訂 DNS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體必須部署在 Azure [虛擬網路 (VNet) ](../../virtual-network/virtual-networks-overview.md)內。 在少數案例中 (例如，db 電子郵件，連結的伺服器連線至您雲端或混合式環境中的其他 SQL Server 執行個體)，必須從 SQL 受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 

因為 SQL 受控執行個體在內部工作中使用相同的 DNS，所以請設定自訂 DNS 伺服器，使其能夠解析公用功能變數名稱。

> [!IMPORTANT]
> 請一律使用郵件伺服器的完整功能變數名稱 (FQDN) 、SQL Server 實例和其他服務，即使它們是在您的私人 DNS 區域內也一樣。 例如，將 `smtp.contoso.com` 用於您的郵件伺服器，因為 `smtp` 無法正確地解析。 建立參考相同虛擬網路內 SQL Server Vm 的連結伺服器或複寫，也需要 FQDN 和預設的 DNS 尾碼。 例如： `SQLVM.internal.cloudapp.net` 。 如需詳細資訊，請參閱 [使用您自己的 DNS 伺服器的名稱解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虛擬網路 DNS 伺服器不會立即影響 SQL 受控執行個體。 SQL 受控執行個體 DNS 設定會在 DHCP 租用過期或平臺升級之後更新（以先發生者為准）。 **建議使用者在建立其第一個受控實例之前，先設定其虛擬網路 DNS 設定。**

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱 [什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需示範如何建立新受控實例的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需有關設定受控實例 VNet 的詳細資訊，請參閱 [受控實例的 vnet](connectivity-architecture-overview.md)設定。
