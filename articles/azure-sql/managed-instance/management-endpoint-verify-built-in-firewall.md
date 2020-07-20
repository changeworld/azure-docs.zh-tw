---
title: 確認內建防火牆中的埠安全性
description: 瞭解如何驗證 Azure SQL 受控執行個體中的內建防火牆保護。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708700"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>確認 Azure SQL 受控執行個體內建防火牆
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體[強制輸入安全性規則](connectivity-architecture-overview.md#mandatory-inbound-security-rules)需要從保護 SQL 受控執行個體的網路安全性群組（NSG）上的**任何來源**開啟管理埠9000、9003、1438、1440和1452。 雖然這些連接埠是於 NSG 層級開啟，不過會在網路層級受到內建防火牆保護。

## <a name="verify-firewall"></a>驗證防火牆

若要確認這些連接埠，請使用任何安全性掃描程式工具對這些連接埠進行測試。 下列螢幕擷取畫面顯示了使用其中一套工具的使用方式。

![驗證內建防火牆](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>後續步驟

如需 SQL 受控執行個體和連線的詳細資訊，請參閱[AZURE sql 受控執行個體連線性架構](connectivity-architecture-overview.md)。
