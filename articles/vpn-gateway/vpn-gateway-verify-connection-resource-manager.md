---
title: Azure VPN 閘道：確認閘道連線
description: 本文說明如何確認虛擬網路「VPN 閘道」連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: fef9eb49b10008c86ee044a199ae69a43585f4f3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217934"
---
# <a name="verify-a-vpn-gateway-connection"></a>確認 VPN 閘道連線

本文說明如何驗證傳統和 Resource Manager 部署模型的 VPN 閘道連線。

## <a name="azure-portal"></a>Azure 入口網站

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

若要使用 PowerShell 驗證 Resource Manager 部署模型的 VPN 閘道連線，請安裝最新版的 [Azure Resource Manager PowerShell Cmdlet](/powershell/azure/)。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 驗證 Resource Manager 部署模型的 VPN 閘道連線，請安裝最新版的 [CLI 命令](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 或更新版本)。

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Azure 入口網站 (傳統)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (傳統)

若要使用 PowerShell 驗證傳統部署模型的 VPN 閘道連線，請安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 請務必下載並安裝 [Service Management](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) 模組。 使用 'Add-AzureAccount' 登入傳統部署模型。

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>後續步驟

* 您可以將虛擬機器加入您的虛擬網路。 請參閱 [建立網站的虛擬機器](../virtual-machines/windows/quick-create-portal.md) 以取得相關步驟。
