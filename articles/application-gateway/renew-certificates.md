---
title: 更新 Azure 應用程式閘道憑證
description: 了解如何更新與應用程式閘道接聽程式相關聯的憑證。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311954"
---
# <a name="renew-application-gateway-certificates"></a>更新應用程式閘道憑證

在某個時候,如果您為 TLS/SSL 加密配置了應用程式閘道,則需要續訂證書。

您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來更新與接聽程式相關聯的憑證：

## <a name="azure-portal"></a>Azure 入口網站

若要從入口網站更新接聽程式憑證，請瀏覽至您的應用程式閘道接聽程式。 按一下有憑證需要更新的接聽程式，然後按一下 [更新或編輯選取的憑證]****。

![更新憑證](media/renew-certificate/ssl-cert.png)

上傳新的 PFX 憑證並為其命名，輸入密碼，然後按一下 [儲存]****。

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

要瞭解如何使用 Azure 應用程式閘道設定 TLS 卸載,請參考[設定 TLS 卸載](application-gateway-ssl-portal.md)
