---
title: PowerShell：啟用攜帶您自己的金鑰（BYOK） TDE
titleSuffix: Azure SQL Managed Instance
description: 瞭解如何設定 Azure SQL 受控執行個體，以開始使用自備金鑰（BYOK）透明資料加密（TDE），以使用 PowerShell 進行待用加密。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: eba53254d58ddfa2577212723b7234bbb5939a6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708428"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>在 SQL 受控執行個體中使用您自己的金鑰從 Azure Key Vault 透明資料加密

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

此 PowerShell 腳本範例會使用 Azure Key Vault 的金鑰，為 Azure SQL 受控執行個體設定透明資料加密（TDE）與客戶管理的金鑰。 這通常稱為「攜帶您自己的金鑰」（BYOK）案例，適用于 TDE。 若要深入瞭解，請參閱[AZURE SQL 透明資料加密與客戶管理的金鑰](../../database/transparent-data-encryption-byok-overview.md)。

## <a name="prerequisites"></a>必要條件

- 現有的受控執行個體。 請參閱[使用 PowerShell 建立受控實例](create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

在本機使用 PowerShell 或使用 Azure Cloud Shell 需要 Azure PowerShell 2.3.2 或更新版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)，或執行下列範例腳本，為目前的使用者安裝模組：

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-scripts"></a>範例指令碼

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure SQL 受控執行個體 PowerShell 指令碼](../../database/powershell-script-content-guide.md)中找到其他 SQL 受控執行個體的 PowerShell 指令碼範例。
