---
title: Azure 防火牆原則 DNS 設定（預覽）
description: 您可以使用 DNS 伺服器和 DNS proxy 設定來設定 Azure 防火牆原則。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bf189e4c9853d9f2ff6e8495423f4f36f14f41d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611793"
---
# <a name="azure-firewall-policy-dns-settings-preview"></a>Azure 防火牆原則 DNS 設定（預覽）

> [!IMPORTANT]
> Azure 防火牆 DNS 設定目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以設定自訂 DNS 伺服器，並啟用 Azure 防火牆原則的 DNS proxy。 當您從 [ **DNS 設定**] 頁面部署防火牆或更新版本時，可以設定這些設定。

## <a name="dns-servers"></a>DNS 伺服器

DNS 伺服器會維護並將功能變數名稱解析成 IP 位址。 根據預設，Azure 防火牆會使用 Azure DNS 來進行名稱解析。 [ **Dns 伺服器**] 設定可讓您設定自己的 dns 伺服器，以進行 Azure 防火牆名稱解析。 您可以設定單一或多部伺服器。

### <a name="configure-custom-dns-servers"></a>設定自訂 DNS 伺服器

1. 選取您的防火牆原則。
2. 在 [**設定**] 底下，選取 [ **DNS 設定**]。
3. 在 [ **DNS 伺服器**] 底下，您可以輸入或新增先前已在虛擬網路中指定的現有 DNS 伺服器。
4. 選取 [儲存]。
5. 防火牆現在會將 DNS 流量導向至指定的 DNS 伺服器，以進行名稱解析。

## <a name="dns-proxy-preview"></a>DNS proxy （預覽）

您可以設定 Azure 防火牆作為 DNS proxy。 DNS proxy 會作為從用戶端虛擬機器到 DNS 伺服器的 DNS 要求媒介。 如果您設定自訂 DNS 伺服器，您應該啟用 DNS proxy 以避免 DNS 解析不相符，並啟用網路規則中的 FQDN 篩選。

如果您未啟用 DNS proxy，來自用戶端的 DNS 要求可能會在不同的時間傳送到 DNS 伺服器，或傳回與防火牆不同的回應。 DNS proxy 會將 Azure 防火牆放在用戶端要求的路徑中，以避免發生不一致的情況。

DNS Proxy 設定需要三個步驟：
1. 啟用 Azure 防火牆 DNS 設定中的 DNS proxy。
2. 選擇性地設定您的自訂 DNS 伺服器，或使用提供的預設值。
3. 最後，您必須將 Azure 防火牆的私人 IP 位址設定為虛擬網路 DNS 伺服器設定中的自訂 DNS 位址。 這可確保將 DNS 流量導向至 Azure 防火牆。

### <a name="configure-dns-proxy-preview"></a>設定 DNS proxy （預覽）

若要設定 DNS proxy，您必須將虛擬網路 DNS 伺服器設定設為使用防火牆私人 IP 位址。 然後，在 [Azure 防火牆原則] [ **dns 設定**] 中啟用 dns Proxy。

#### <a name="configure-virtual-network-dns-servers"></a>設定虛擬網路 DNS 伺服器

1. 選取將透過 Azure 防火牆路由傳送 DNS 流量的虛擬網路。
2. 選取 [設定] 底下的 [DNS 伺服器]。
3. 選取 [ **DNS 伺服器**] 底下的 [**自訂**]。
4. 輸入防火牆的私人 IP 位址。
5. 選取 [儲存]。

#### <a name="enable-dns-proxy-preview"></a>啟用 DNS proxy （預覽）

1. 選取您的 Azure 防火牆原則。
2. 在 [**設定**] 底下，選取 [ **DNS 設定**]。
3. 根據預設， **DNS Proxy**已停用。 啟用時，防火牆會接聽埠53，並將 DNS 要求轉送到已設定的 DNS 伺服器。
4. 請檢查**DNS 伺服器**設定，以確定適用于您環境的設定。
5. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

[網路規則中的 FQDN 篩選](fqdn-filtering-network-rules.md)