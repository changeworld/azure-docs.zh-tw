---
title: 使用 Azure Front Door 和 Azure Web 應用程式防火牆 (WAF) 快速調整和保護 Web 應用程式 | Microsoft Docs
description: 此文章可協助您了解如何搭配 Azure Front Door 服務使用 Web 應用程式防火牆
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 3c0b23bc7c25c56953d267e5193a63fe08e35f64
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663516"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>使用 Azure Front Door 和 Azure Web 應用程式防火牆 (WAF) 快速調整和保護 Web 應用程式

許多 Web 應用程式在 COVID-19 的最近幾週，都經歷了流量的快速增長。 此外，這些 Web 應用程式也會觀察大量湧進的惡意流量，包括拒絕服務攻擊。 處理這些需求、擴增以因應流量激增以及防範攻擊的其中一種有效方式是，使用 Azure WAF 設定 Azure Front Door，作為 Web 應用程式前方的加速、快取和安全性階層。 此文章提供的指導方針說明如何使用 Azure WAF 設定，針對在 Azure 內外執行的任何 Web 應用程式，快速取得此 Azure Front Door。 

我們將在此教學課程中使用 Azure CLI 設定 WAF，但 Azure 入口網站、Azure PowerShell、Azure ARM 與 Azure REST API 也完全支援所有這些步驟。 

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

此部落格中的指示會使用 Azure 命令列介面 (CLI)。 檢視本指南以[開始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

*秘訣：開始使用 Azure CLI 簡單而快速的方式是使用 [Azure Cloud Shell 中 Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)* \(部分機器翻譯\)

確定已將 front-door 延伸模組新增至您的 Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

注意:如需下面所列命令的詳細資訊，請參閱 [Front Door 的 Azure CLI 參考](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest) \(英文\)。

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>步驟 1:建立 Azure Front Door (AFD) 資源


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**：後端位址是您想要保護之應用程式的完整網域名稱 (FQDN)。 例如，myapplication.contoso.com

**--accepted-protocols**：接受的通訊協定會指定您希望 AFD 為您的 Web 應用程式支援的所有通訊協定。 其中一個範例是 --accepted-protocols Http Https。

**--name**：指定 AFD 資源的名稱

**--resource-group**：您希望放入此 AFD 資源的資源群組。  若要深入了解資源群組，請瀏覽在 Azure 中管理資源群組

在您從成功執行此命令取得的回應中，尋找金鑰 "hostName" 並記下其值，以用於後續步驟。 hostName 是您已建立之 AFD 資源的 DNS 名稱

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>步驟 2:建立 Azure WAF 設定檔以搭配 Azure Front Door 資源使用

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name 指定 Azure WAF 原則的名稱

--resource-group 您希望放入此 WAF 資源的資源群組。 

上述的 CLI 程式碼將會建立已啟用且處於 [預防] 模式的 WAF 原則。 

注意：您可能也會想要在 [偵測] 模式下建立 WAF，並觀察其在決定切換為 [保護] 模式之前，如何偵測並記錄惡意要求 (而非封鎖)。

在您從成功執行此命令得到的回應中，尋找金鑰 "ID" 並記下其值，以用於後續步驟。 ID 欄位應採用下列格式

/subscriptions/**訂用帳戶識別碼**/resourcegroups/**資源群組名稱**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF 原則名稱**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>步驟 3：將受控規則集新增至此 WAF 原則

在 WAF 原則中，您可以新增由 Microsoft 所建置並管理的一組規則所組成的受控規則集，並針對完整的威脅類別提供現成可用的保護。 在此範例中，我們將新增兩個此類規則集 (1) 預設規則集，可防範常見的 Web 威脅和 (2) Bot 保護規則集，可防範惡意 Bot

(1) 新增預設規則集

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) 新增 Bot Manager 規則集

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name 您為 Azure WAF 資源提供的名稱

--resource-group 您已經放入此 WAF 資源的資源群組。

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>步驟 4：建立 WAF 原則與 AFD 資源的關聯

在此步驟中，我們會將所建置的 WAF 原則與位於 Web 應用程式前方的 AFD 資源建立關聯。

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name 您為 AFD 資源指定的名稱

--resource-group 您已經放入 Azure Front Door 資源的資源群組。

--set 這是您使用新建置的 WAF 原則，更新與 AFD 資源相關聯之 frontendEndpoint 的屬性 WebApplicationFirewallPolicyLink 所在。 您可以從上述步驟 #2 所得到的回應，找到 WAF 原則的識別碼

注意：上述範例適用於您未使用自訂網域的情況 (如果您是如此)

如果您未使用任何自訂網域存取您的 Web 應用程式，可以略過步驟 #5。 在該情況下，您會將您在步驟 #1 中取得的主機名稱提供給使用者，以瀏覽至您的 Web 應用程式

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>步驟 5：為 Web 應用程式設定自訂網域

一開始，您 Web 應用程式的自訂網域名稱 (客戶用來參考您應用程式的自訂網域名稱，例如， www.contoso.com) 會指向您在推出 AFD 之前執行 Web 應用程式所在位置。 在將 AFD+WAF 新增至應用程式前方的此架構變更之後，對應至該自訂網域的 DNS 項目現在應該會指向此 AFD 資源。 將 DNS 伺服器中的這個項目重新對應至您在步驟 #1 記下的 AFD 主機名稱，即可完成此作業。

更新 DNS 記錄的特定步驟取決於您的 DNS 服務提供者，但如果您使用 Azure DNS 來裝載您的 DNS 名稱，您可以參閱相關文件，以了解[更新 DNS 記錄的步驟](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) \(部分英文翻譯\)，並指向 AFD hostName。 

這裡要注意的一個重點是，如果您需要使用者使用區域頂點瀏覽至您的網站 (例如，contoso.com)，您必須使用 Azure DNS 及其 [ALIAS 記錄類型](https://docs.microsoft.com/azure/dns/dns-alias)，以裝載您的 DNS 名稱。 

此外，您也需要將 AFD 設定更新為在其中[新增此自訂網域](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)，讓 AFD 了解此對應。

最後，如果您使用自訂網域連線到您的 Web 應用程式，而且希望啟用 HTTPS 通訊協定，則必須[在 AFD 中設定您自訂網域的憑證](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)。 

## <a name="step-6-lock-down-your-web-application"></a>步驟 6：鎖定您的 Web 應用程式

另一個要遵循的最佳做法是確保只有 AFD 邊緣可以與您的 Web 應用程式通訊。 此動作可確保沒有人可以略過 AFD 防護並直接存取您的應用程式。 您可以瀏覽 [AFD 的常見問題集一節](https://docs.microsoft.com/azure/frontdoor/front-door-faq) \(部分機器翻譯\)，並參考有關鎖定後端以僅供 AFD 存取的問題來完成此鎖定。
