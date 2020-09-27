---
title: 使用 Azure CLI 註冊對等互連服務預覽連接
description: 瞭解如何使用 Azure CLI 來註冊對等互連服務連線
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: ef573817927cf732da3426d802f8f26e2e9cd4ec
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398984"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>使用 Azure CLI 註冊對等互連服務連線

Azure 對等互連服務是一種網路服務，可加強客戶與 Microsoft 雲端服務的連線能力，例如 Microsoft 365、Dynamics 365、軟體即服務 (SaaS) services、Azure，或任何可透過公用網際網路存取的 Microsoft 服務。 在本文中，您將瞭解如何使用 Azure CLI 來註冊對等互連服務連接。

如果您沒有 Azure 訂用帳戶，請立即建立[帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求 Azure CLI 2.0.28 版版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必要條件 

您必須滿足以下條件：

### <a name="azure-account"></a>Azure 帳戶

您必須具有有效且作用中的 Microsoft Azure 帳戶。 需要此帳戶才能設定對等互連服務連線。 對等互連服務是 Azure 訂用帳戶內的資源。

### <a name="connectivity-provider"></a>連線提供者

您可以使用網際網路服務提供者或網際網路交換合作夥伴來取得對等互連服務，以將您的網路與 Microsoft 網路連線。

請確定連線提供者已與 Microsoft 合作。

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. 登入您的 Azure 帳戶，然後選取您的訂用帳戶

若要開始您的組態，請登入您的 Azure 帳戶。 如果您使用 Cloud Shell **試用** ] 選項，您就會自動登入。 您可以使用下列範例來協助您連接。

```azurecli-interactive
az login
```

檢查帳戶的訂用帳戶。

```azurecli-interactive
az account list
```

選取您要註冊對等互連服務連接的訂用帳戶。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

如果您還沒有資源群組，您必須先建立一個資源群組，然後再註冊對等互連服務連線。 您可以藉由使用下列命令來建立資源群組：

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. 使用資源提供者和功能旗標注冊您的訂用帳戶

在您繼續進行使用 Azure CLI 註冊對等互連服務連線的步驟之前，請使用 Azure CLI，向資源提供者和功能旗標注冊您的訂用帳戶。 在這裡指定 Azure CLI 命令：

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. 註冊對等互連服務連線

透過 Azure CLI 使用下列命令集來註冊對等互連服務連線。 此範例會註冊名為 myPeeringService 的對等互連服務連接。

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. 註冊前置詞

透過 Azure CLI 執行下列命令，以註冊連接提供者所提供的前置詞。 此範例會註冊名為 myPrefix 的字首。

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解對等互連服務連接，請參閱對 [等互連服務](connection.md)連線。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 註冊連線，請參閱[註冊對等互連服務連線 - Azure PowerShell](powershell.md)。
- 若要使用 Azure 入口網站註冊連接，請參閱 [註冊對等互連服務連線-Azure 入口網站](azure-portal.md)。
