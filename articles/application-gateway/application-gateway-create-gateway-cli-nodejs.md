---
title: 建立 Azure 應用程式閘道 - Azure 經典 CLI
description: 了解如何使用 Resource Manager 中的 Azure 傳統 CLI 建立應用程式閘道
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312637"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>使用 Azure CLI 建立應用程式閘道

Azure 應用程式閘道是第 7 層負載平衡器。 不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。 應用程式閘道具有以下應用程式交付功能:HTTP 負載平衡、基於 Cookie 的作業階段關聯和傳輸層安全 (TLS),以前稱為安全套接字層 (SSL)、卸載、自訂運行狀況探測器以及對多網站的支援。

## <a name="prerequisite-install-the-azure-cli"></a>必要條件：安裝 Azure CLI

要執行本文中的步驟,需要安裝 Azure [CLI,](../xplat-cli-install.md)並且需要在[Azure 中簽名](/cli/azure/authenticate-azure-cli)。 

> [!NOTE]
> 如果您沒有 Azure 帳戶，就需要申請一個。 請 [在此處註冊免費試用](../active-directory/fundamentals/sign-up-organization.md)。

## <a name="scenario"></a>狀況

在此案例中，您將了解如何使用 Azure 入口網站來建立應用程式閘道。

此案例將會：

* 建立含有兩個執行個體的中型應用程式閘道。
* 建立名為 ContosoVNET 且含有 10.0.0.0/16 保留 CIDR 區塊的虛擬網路。
* 建立名為 subnet01 且使用 10.0.0.0/28 作為其 CIDR 區塊的子網路。

> [!NOTE]
> 其他應用程式閘道組態 (包括自訂健康狀況探查、後端集區位址及其他規則) 會在設定應用程式閘道設定之後才進行設定，而不會在初始部署期間設定。

## <a name="before-you-begin"></a>開始之前

「Azure 應用程式閘道」需要有自己的子網路。 建立虛擬網路時，請確定您保留足夠的位址空間，以便擁有多個子網路。 將應用程式閘道部署到子網路之後，就只能將額外的應用程式閘道新增到該子網路。

## <a name="sign-in-to-azure"></a>登入 Azure

打開**Microsoft Azure 命令提示符**並登錄。

```azurecli-interactive
az login
```

輸入上述範例後會提供程式碼。 在瀏覽器https://aka.ms/devicelogin中導航以繼續登錄過程。

![顯示裝置登入的 cmd][1]

在瀏覽器中，輸入收到的程式碼。 系統會將您重新導向至 [登入] 頁面。

![要輸入程式碼的瀏覽器][2]

輸入程式碼後您已登入，請關閉瀏覽器以繼續進行案例。

![已順利登入][3]

## <a name="switch-to-resource-manager-mode"></a>切換至 Resource Manager 模式

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>建立資源群組

建立應用程式閘道之前，會建立資源群組以包含應用程式閘道。 以下顯示命令。

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立資源群組後，就會為應用程式閘道建立虛擬網路。  在下列範例中，位址空間為上述案例注意事項中所定義的 10.0.0.0/16。

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>建立子網路

建立虛擬網路之後，就會為應用程式閘道新增子網路。  如果您打算使用應用程式閘道搭配與其裝載於相同虛擬網路中的 Web 應用程式，請務必保留足夠的空間給另一個子網路使用。

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

建立虛擬網路和子網路後，便已完成應用程式閘道的先決條件。 此外下列步驟也需要先前匯出的 .pfx 憑證和憑證密碼︰用於後端的 IP 位址是後端伺服器的 IP 位址。 這些值可以是虛擬網路中的私人 IP、公用 IP 或後端伺服器的完整網域名稱。

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> 如需可在建立期間提供的參數清單，請執行下列命令︰**azure network application-gateway create --help**。

此範例會建立一個具有接聽程式、後端集區、後端 http 設定及規則之預設設定的基本應用程式閘道。 佈建成功之後，您可以依據您的部署需求修改這些設定。
如果在先前步驟中已經以後端集區定義 Web 應用程式，一旦建立之後，負載平衡即開始。

## <a name="next-steps"></a>後續步驟

參閱 [建立自訂健康狀態探查](application-gateway-create-probe-portal.md)

瞭解如何透過存[取 設定 TLS 卸載](application-gateway-ssl-arm.md)來設定 TLS 卸載,並將昂貴的 TLS 解密從 Web 伺服器中移除

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
