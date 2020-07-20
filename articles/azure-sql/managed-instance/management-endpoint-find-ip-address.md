---
title: 探索管理端點 IP 位址
titleSuffix: Azure SQL Managed Instance
description: 瞭解如何取得 Azure SQL 受控執行個體管理端點公用 IP 位址，並確認其內建防火牆保護
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 40a44fe46cf38c633380c4c353960cc4e11f2f3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708717"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>判斷管理端點 IP 位址-Azure SQL 受控執行個體 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體虛擬叢集包含 Azure 用於管理作業的管理端點。 管理端點會受到網路層級內建防火牆和應用程式層級的相互憑證驗證所保護。 您可以判斷管理端點的 IP 位址，但您無法存取此端點。

若要判斷管理 IP 位址，請在您的 SQL 受控執行個體 FQDN：上進行[DNS 查閱](/windows-server/administration/windows-commands/nslookup) `mi-name.zone_id.database.windows.net` 。 這會傳回類似的 DNS 專案 `trx.region-a.worker.vnet.database.windows.net` 。 接著，您可以在此 FQDN 上執行 DNS 查閱，並移除 ". vnet"。 這會傳回管理 IP 位址。 

如果您將取代 \<MI FQDN\> 為 SQL 受控執行個體的 DNS 專案，則此 PowerShell 程式碼會為 `mi-name.zone_id.database.windows.net` 您執行所有動作：
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

如需 SQL 受控執行個體和連線的詳細資訊，請參閱[AZURE sql 受控執行個體連線性架構](connectivity-architecture-overview.md)。
