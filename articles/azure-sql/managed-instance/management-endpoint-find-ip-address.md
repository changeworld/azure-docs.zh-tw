---
title: 探索管理端點 IP 位址
titleSuffix: Azure SQL Managed Instance
description: 瞭解如何取得 Azure SQL 受控執行個體管理端點公用 IP 位址，並確認其內建防火牆保護
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617361"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>判斷管理端點 IP 位址-Azure SQL 受控執行個體 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體虛擬叢集包含 Azure 用來管理作業的管理端點。 管理端點會受到網路層級內建防火牆和應用程式層級的相互憑證驗證所保護。 您可以判斷管理端點的 IP 位址，但您無法存取此端點。

若要判斷管理 IP 位址，請在您的 SQL 受控執行個體 FQDN 上進行 [DNS 查閱](/windows-server/administration/windows-commands/nslookup) ： `mi-name.zone_id.database.windows.net` 。 這會傳回類似的 DNS 專案 `trx.region-a.worker.vnet.database.windows.net` 。 然後，您可以對此 FQDN 進行 DNS 查閱，並將 "vnet" 移除。 這會傳回管理 IP 位址。 

如果您將取代 \<MI FQDN\> 為 SQL 受控執行個體的 DNS 專案，則此 PowerShell 程式碼會為您完成此作業： `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

如需 SQL 受控執行個體和連線能力的詳細資訊，請參閱 [AZURE sql 受控執行個體連線能力架構](connectivity-architecture-overview.md)。
