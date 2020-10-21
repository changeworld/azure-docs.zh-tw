---
title: 教學課程：使用 Azure Front Door 和 Azure Web 應用程式防火牆 (WAF) 調整和保護 Web 應用程式
description: 此教學課程將說明如何使用 Azure Web 應用程式防火牆搭配 Azure Front Door 服務。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: a5d51a77b1da0ae44c76d0187113105c4e53c9b4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279211"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>教學課程：使用 Azure Front Door 和 Azure Web 應用程式防火牆 (WAF) 快速調整和保護 Web 應用程式

由於 COVID-19，許多 Web 應用程式在最近幾週都經歷了流量的快速增長。 這些 Web 應用程式也會經歷大量湧進的惡意流量，包括拒絕服務攻擊。 擴增應用程式以因應流量激增並同時保護自己免受攻擊的一種有效方式：設定 Azure Front Door 以使用 Azure WAF 作為 Web 應用程式前方的加速、快取和安全性層。 此文章提供的指導方針說明如何針對在 Azure 內外執行的任何 Web 應用程式，設定包含 Azure WAF 的 Azure Front Door。 

我們將在本教學課程中使用 Azure CLI 來設定 WAF。 您可以使用 Azure 入口網站、Azure PowerShell、Azure Resource Manager 或 Azure REST API 來完成相同的動作。 

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> - 建立 Front Door。
> - 建立 Azure WAF 原則。
> - 設定 WAF 原則的規則集。
> - 將 WAF 原則與 Front Door 相關聯。
> - 設定自訂網域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 本教學課程中的指示會使用 Azure CLI。 [檢視本指南](/cli/azure/get-started-with-azure-cli?preserve-view=true&view=azure-cli-latest)以開始使用 Azure CLI。

  > [!TIP] 
  > 開始使用 Azure CLI 簡單而快速的方式是使用 [Azure Cloud Shell 中的 Bash](../cloud-shell/quickstart.md)。

- 確定已將 `front-door` 延伸模組已新增至 Azure CLI：

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> 如需本教學課程中使用之命令的詳細資訊，請參閱 [Front Door 的 Azure CLI 參考](/cli/azure/ext/front-door/?preserve-view=true&view=azure-cli-latest)。

## <a name="create-an-azure-front-door-resource"></a>建立 Azure Front Door 資源

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`：您想要保護之應用程式的完整網域名稱 (FQDN)。 例如： `myapplication.contoso.com` 。

`--accepted-protocols`：指定您想要 Azure Front Door 支援 Web 應用程式的通訊協定。 例如： `--accepted-protocols Http Https` 。

`--name`：您的 Azure Front Door 資源名稱。

`--resource-group`：您希望放入此 Azure Front Door 資源所在的資源群組。 若要深入了解資源群組，請參閱[在 Azure 中管理資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md)。

在您執行此命令時取得的回應中，尋找金鑰 `hostName`。 在稍後的步驟中您將需要此值。 `hostName` 是您所建立 Azure Front 資源的 DNS 名稱。

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>建立 Azure WAF 設定檔以搭配 Azure Front Door 資源使用

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`：新 Azure WAF 原則的名稱。

`--resource-group`：您希望放入此 WAFD 資源所在的資源群組。 

先前的 CLI 程式碼會建立已啟用且處於預防模式的 WAF 原則。 

> [!NOTE] 
> 在您決定使用保護模式之前，您可能會想要在偵測模式中建立 WAF 原則，並觀察它如何偵測並記錄惡意要求 (而不加以封鎖)。

在您執行此命令時取得的回應中，尋找金鑰 `ID`。 在稍後的步驟中您將需要此值。 

`ID` 欄位格式應該如下：

/subscriptions/**訂用帳戶識別碼**/resourcegroups/**資源群組名稱**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF 原則名稱**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>將受控規則集新增至 WAF 原則

您可以將受控規則集新增至 WAF 原則。 受控規則集是由 Microsoft 所建立及管理的一組規則，可協助保護您免於遭受某類威脅。 在此範例中，我們要新增兩個規則集：
- 預設規則集，可協助保護您免於遭受常見的 Web 威脅。 
- Bot 保護規則集，可協助保護您免於遭受惡意 Bot 的威脅。

新增預設規則集：

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

新增 Bot 保護規則集：

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`：您為 Azure WAF 資源指定的名稱。

`--resource-group`：您放入 WAF 資源所在的資源群組。

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>建立 WAF 原則與 Azure Front Door 資源的關聯

在此步驟中，我們會將我們所建立的 WAF 原則與位於 Web 應用程式前方的 Azure Front Door 資源建立關聯：

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`：您為 Azure Front Door 資源指定的名稱。

`--resource-group`：您放入 Azure Front Door 資源所在的資源群組。

`--set`：這是您使用新 WAF 原則更新與您 Azure Front 資源相關聯 `frontendEndpoint` 的 `WebApplicationFirewallPolicyLink` 屬性的位置。 您應該可從稍早在本教學課程中建立 WAF 設定檔時所收到的回應中找到 WAF 原則的識別碼。

 > [!NOTE] 
> 前述範例適用於您未使用自訂網域的情況。 如果您未使用任何自訂網域來存取您的 Web 應用程式，可以略過下一節。 在此情況下，您會為客戶提供您在建立 Azure Front Door 資源時取得的 `hostName`。 他們會使用此 `hostName` 來移至您的 Web 應用程式。

## <a name="configure-the-custom-domain-for-your-web-application"></a>為 Web 應用程式設定自訂網域

Web 應用程式的自訂網域名稱是客戶用來參考您應用程式的名稱。 例如，www.contoso.com。 一開始，這個自訂網域名稱是指向您引進 Azure Front 之前，其執行所在的位置。 將 Azure Front Door 和 WAF 新增至應用程式的前方之後，與該自訂網域對應的 DNS 項目應該指向 Azure Front Door 資源。 若要進行此變更，您可以將 DNS 伺服器中的項目重新對應至您建立 Azure Front Door 資源時所記下的 Azure Front Door `hostName`。

更新 DNS 記錄的特定步驟將取決於您的 DNS 服務提供者。 如果您使用 Azure DNS 來裝載您的 DNS 名稱，您可以參閱文件以取得[更新 DNS 記錄的步驟](../dns/dns-operations-recordsets-cli.md)，並指向 Azure Front Door `hostName`。 

如果您需要客戶使用區域頂點 (例如 contoso.com) 來進入您的網站，請注意一個重要事項。 在此情況下，您必須使用 Azure DNS 及其[別名記錄類型](../dns/dns-alias.md)來裝載您的 DNS 名稱。 

您也需要更新您的 Azure Front Door 設定，以[新增自訂網域](./front-door-custom-domain.md)至該設定，使其知道此對應。

最後，如果您使用自訂網域連線到您的 Web 應用程式，而且希望啟用 HTTPS 通訊協定。 您需要[在 Azure Front Door 中設定自訂網域的憑證](./front-door-custom-domain-https.md)。 

## <a name="lock-down-your-web-application"></a>鎖定您的 Web 應用程式

建議您確保只有 Azure Front Door 邊緣可以與您的 Web 應用程式通訊。 這麼做可確保沒有人可以略過 Azure Front Door 保護並直接存取您的應用程式。 若要完成此鎖定，請參閱[如何將對我的後端的存取鎖定在僅限 Azure Front Door？](./front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)。

## <a name="clean-up-resources"></a>清除資源

當您不再需要教學課程中所使用的資源時，請使用 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) 命令移除資源群組、Front Door 和 WAF 原則：

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`：本教學課程中使用所有資源的資源群組名稱。

## <a name="next-steps"></a>後續步驟

若要了解如何針對 Front Door 進行疑難排解，請參閱疑難排解指南：

> [!div class="nextstepaction"]
> [針對常見路由問題進行疑難排解](front-door-troubleshoot-routing.md)