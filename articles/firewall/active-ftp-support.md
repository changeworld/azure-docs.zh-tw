---
title: Azure 防火牆主動式 FTP 支援
description: 根據預設，Azure 防火牆上的主動式 FTP 已停用。 您可以使用 PowerShell、CLI 和 ARM 範本來啟用它。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: victorh
ms.openlocfilehash: 2ff61d06885c182454c99ee7e982a3a1a1f5013c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690331"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure 防火牆主動式 FTP 支援

使用 Active FTP 時，FTP 伺服器會起始對指定 FTP 用戶端資料埠的資料連線。 用戶端網路上的防火牆通常會封鎖內部用戶端埠的外部連線要求。 如需詳細資訊，請參閱 [主動式 ftp 與被動式 ftp，這是一種明確的說明](https://slacksite.com/other/ftp.html)。

根據預設，Azure 防火牆上的主動式 FTP 支援會停用，以防止使用 FTP 命令的 FTP 彈跳攻擊 `PORT` 。 不過，當您使用 Azure PowerShell、Azure CLI 或 Azure ARM 範本進行部署時，您可以啟用 Active FTP。

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 部署，請使用 `AllowActiveFTP` 參數。 如需詳細資訊，請參閱 [建立具有允許 ACTIVE FTP 的防火牆](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-)。

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 部署，請使用 `--allow-active-ftp` 參數。 如需詳細資訊，請參閱 [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters)。 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) 範本

若要使用 ARM 範本進行部署，請使用 `AdditionalProperties` 欄位：

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
如需詳細資訊，請參閱 [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="next-steps"></a>下一步

若要瞭解如何部署 Azure 防火牆，請參閱 [使用 Azure PowerShell 部署和設定 Azure 防火牆](deploy-ps.md)。