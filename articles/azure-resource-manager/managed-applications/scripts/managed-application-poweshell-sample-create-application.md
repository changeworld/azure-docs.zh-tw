---
title: Azure PowerShell 指令碼範例 - 部署受控應用程式
description: 提供 Azure PowerShell 指令碼範例，以將受控應用程式定義部署至訂用帳戶。
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "86055880"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>使用 PowerShell 為服務類別目錄部署受控應用程式

此指令碼會從服務類別目錄部署受控應用程式定義。


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來部署受控應用程式。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | 建立受控應用程式。 提供範本的定義識別碼和參數。 |


## <a name="next-steps"></a>後續步驟

* 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](../overview.md)。
* 如需 PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/get-started-azureps)。
