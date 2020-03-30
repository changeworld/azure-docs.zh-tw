---
title: '電源外殼：啟用 BYOK TDE - Azure SQL 資料庫託管實例 '
description: 了解如何設定 Azure SQL 受控執行個體，以透過 PowerShell 開始使用 BYOK 透明資料加密 (TDE) 進行待用資料加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691401"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>從 Azure Key Vault 使用自己的金鑰管理受控執行個體中的透明資料加密

此 PowerShell 腳本示例使用 Azure 金鑰保存庫的金鑰配置 Azure SQL 託管實例的客戶託管金鑰的透明資料加密 （TDE）。 這通常稱為 TDE 自帶金鑰方案。 要瞭解有關使用客戶管理的金鑰的 TDE 的更多內容，請參閱[TDE 將您自己的金鑰帶到 Azure SQL](../transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites"></a>Prerequisites

- 現有的託管實例。 請參閱[使用 PowerShell 創建 Azure SQL 資料庫託管實例](sql-database-create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本地使用 PowerShell 或使用 Azure 雲外殼都需要 AZ PowerShell 2.3.2 或更高版本。 如果需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)，或運行以下示例腳本以為當前使用者安裝模組：

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-scripts"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
