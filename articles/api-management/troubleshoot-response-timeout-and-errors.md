---
title: 針對 API 管理的用戶端回應超時和錯誤進行疑難排解
description: 針對間歇性連線錯誤和 API 管理中的相關延遲問題進行疑難排解
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 770a8191b1b07a7ebc779b84f443ae96d66d1c97
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841395"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>針對 API 管理的用戶端回應超時和錯誤進行疑難排解

本文可協助您針對間歇性連線錯誤和 [AZURE API 管理](./api-management-key-concepts.md)中的相關延遲問題進行疑難排解。 具體而言，本文將提供來源位址網路轉譯 (SNAT) 埠的耗盡資訊和疑難排解。 如果您需要更多協助，請與 azure 支援 [中心](https://azure.microsoft.com/support/community/) 的 azure 專家聯繫，或提出支援要求與 [azure 支援](https://azure.microsoft.com/support/options/)。

## <a name="symptoms"></a>徵兆

透過 API 管理呼叫 Api (APIM) 服務的用戶端應用程式可能會出現下列一或多個徵兆：

* 間歇的 HTTP 500 錯誤
* 逾時錯誤訊息

這些徵兆資訊清單是 `BackendConnectionFailure` [Azure 監視器資源記錄](../azure-monitor/platform/resource-logs.md)檔中的實例。

## <a name="cause"></a>原因

這種徵兆的模式通常是因為網路位址轉譯 (SNAT) 埠限制與 APIM 服務。

每當用戶端呼叫其中一個 APIM Api 時，Azure API 管理服務就會開啟 SNAT 埠來存取您的後端 API。 如同 Azure 中的 [輸出](../load-balancer/load-balancer-outbound-connections.md)連線所述，azure 會使用來源網路位址轉譯 (SNAT) 和 Load Balancer (不會向客戶公開，) 在公用 IP 位址空間中與 azure 外部的端點進行通訊，也可以使用未使用 [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)的 azure 內部端點。 這種情況只適用于公用 Ip 上公開的後端 Api。

每個 API 管理服務實例一開始都會提供預先配置的 SNAT 埠數目。 該限制會影響對相同主機和埠組合的開啟連接。 當您重複呼叫相同的位址和埠組合時，就會使用 SNAT 埠。 一旦發行 SNAT 埠之後，就可以視需要重複使用埠。 Azure 網路負載平衡器只會在等候四分鐘後，從關閉的連線回收 SNAT 埠。

如果這些埠未關閉且回收速度夠快，讓您的 Api 的用戶端要求快速連續，可能會耗盡預先配置的 SNAT 埠配額，以防止您的 APIM 服務及時處理用戶端要求。

## <a name="mitigations-and-solutions"></a>緩和措施與解決方案

解決 SNAT 埠耗盡的問題首先需要診斷和優化後端服務的效能。

若要減少 SNAT 埠耗盡的一般策略，請參閱 *Azure Load Balancer* 檔中的 [輸出連接失敗疑難排解](../load-balancer/troubleshoot-outbound-connection.md)。 下列策略適用于 API 管理。

### <a name="scale-your-apim-instance"></a>調整您的 APIM 實例

每個 API 管理實例都會根據 APIM 單位配置一些 SNAT 埠。 您可以使用額外的單位來調整您的 API 管理實例，以配置額外的 SNAT 埠。 如需詳細資訊，請參閱 [調整您的 API 管理服務](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> SNAT 埠使用量目前無法作為自動調整 API 管理單位的度量。

### <a name="use-multiple-ips-for-your-backend-urls"></a>針對後端 Url 使用多個 Ip

從您的 APIM 實例到相同目的地 IP 和後端服務目的地埠的每個連線都會使用 SNAT 埠，以維護不同的流量。 若沒有不同的 SNAT 埠可讓您從背景服務傳回流量，APIM 將無法區分另一個回應。

因為在目的地 IP 或目的地埠不同的情況下，可以重複使用 SNAT 埠，所以避免 SNAT 埠耗盡的另一種方法，就是針對後端服務 Url 使用多個 Ip。

如需詳細資訊，請參閱 [輸出 proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md)。

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>將您的 APIM 和後端服務放在相同的 VNet 中

如果您的後端 API 裝載于支援 *服務端點* （例如 App Service）的 Azure 服務上，您可以將 APIM 實例和後端服務放在相同的虛擬網路中，並透過 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md) 或 [私人端點](../private-link/private-endpoint-overview.md)公開，以避免 SNAT 埠耗盡問題。 當您使用常見的 VNet，並將服務端點放置於整合子網上時，從您的 APIM 實例到這些服務的輸出流量會略過網際網路，進而避免 SNAT 埠限制。 同樣地，如果您使用 VNet 和私人端點，則不會有該目的地的任何輸出 SNAT 埠問題。

如需詳細資訊，請參閱 [如何搭配使用 AZURE API 管理與虛擬網路](api-management-using-with-vnet.md) ，並將 [App Service 與 Azure 虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)。

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>將您的 APIM 放在虛擬網路中，並將輸出呼叫路由至 Azure 防火牆

類似于將 APIM 和後端服務放在虛擬網路中，您可以在 VNet 中使用 Azure 防火牆搭配 APIM 服務，然後將輸出 APIM 呼叫路由傳送至 Azure 防火牆。 在 APIM 與 Azure 防火牆 (位於相同的 VNet) 中時，不需要 SNAT 埠。 針對後端服務的 SNAT 連線，Azure 防火牆具有64000可用的埠，其數量遠高於配置給 APIM 實例的數量。

如需詳細資訊，請參閱 [Azure 防火牆](../firewall/overview.md) 檔。

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>考慮回應快取和其他後端效能微調

另一個要考慮的可能緩和措施是改善後端 Api 的處理時間。 其中一種方式是使用回應快取來設定特定 Api，以減少用戶端應用程式呼叫您的 API 和 APIM 後端負載之間的延遲。

如需詳細資訊，請參閱新增快取 [以改善 AZURE API 管理中的效能](api-management-howto-cache.md)。

### <a name="consider-implementing-access-restriction-policies"></a>考慮實施存取限制原則

如果對您的商務案例而言很合理，您可以為您的 API 管理產品執行存取限制原則。 例如， `rate-limit-by-key` 原則可用來限制每個索引鍵的 API 使用尖峰，方法是限制每個指定時間週期的呼叫率。

如需詳細資訊，請參閱 [API 管理存取限制原則](api-management-access-restriction-policies.md) 。

## <a name="see-also"></a>另請參閱

* [Azure Load Balancer：針對輸出連接失敗進行疑難排解](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service：針對間歇性連出連線錯誤進行疑難排解](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
