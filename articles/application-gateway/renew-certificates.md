---
title: 更新 Azure 應用程式閘道憑證
description: 了解如何更新與應用程式閘道接聽程式相關聯的憑證。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622175"
---
# <a name="renew-application-gateway-certificates"></a>更新應用程式閘道憑證

如果您已將應用程式閘道設定為 TLS/SSL 加密，則在某個時間點，您必須更新憑證。

您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來更新與接聽程式相關聯的憑證：

## <a name="azure-portal"></a>Azure 入口網站

若要從入口網站更新接聽程式憑證，請瀏覽至您的應用程式閘道接聽程式。 選取具有需要更新之憑證的接聽程式，然後選取 [ **更新] 或 [編輯選取的憑證**]。

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="更新憑證":::

上傳新的 PFX 憑證、為它命名、輸入密碼，然後選取 [ **儲存**]。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 更新您的憑證，請使用下列指令碼：

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Azure 應用程式閘道來設定 TLS 卸載，請參閱[設定 Tls](./create-ssl-portal.md)卸載