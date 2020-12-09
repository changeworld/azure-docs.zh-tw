---
title: 確認內建防火牆中的埠安全性
description: 瞭解如何在 Azure SQL 受控執行個體中確認內建防火牆保護。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853266"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>驗證 Azure SQL 受控執行個體的內建防火牆
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體 [強制輸入安全性規則](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) 需要從網路安全性群組的 **任何來源** 開啟管理埠9000、9003、1438、1440和1452， (NSG) 保護 SQL 受控執行個體。 雖然這些連接埠是於 NSG 層級開啟，不過會在網路層級受到內建防火牆保護。

## <a name="verify-firewall"></a>驗證防火牆

若要確認這些連接埠，請使用任何安全性掃描程式工具對這些連接埠進行測試。 下列螢幕擷取畫面顯示了使用其中一套工具的使用方式。

![驗證內建防火牆](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>後續步驟

如需 SQL 受控執行個體和連線能力的詳細資訊，請參閱 [AZURE sql 受控執行個體連線能力架構](connectivity-architecture-overview.md)。
