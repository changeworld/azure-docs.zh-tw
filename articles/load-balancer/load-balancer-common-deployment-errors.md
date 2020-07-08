---
title: 針對一般部署錯誤進行疑難排解
titleSuffix: Azure Load Balancer
description: 說明如何解決部署 Azure Load Balancer 時的常見錯誤
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 527f71b1980b5a62d3db94fe89a1bce98142e31a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221001"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>針對常見的 Azure Load Balancer 部署錯誤進行疑難排解

本文說明一些常見的 Azure Load Balancer 部署錯誤，並提供解決錯誤的資訊。 如果您要尋找錯誤碼相關資訊，但本文並未提供該資訊，請讓我們知道。 您可在此頁面底部留下意見反應。 意見反應會與 GitHub 問題一併追蹤。

## <a name="error-codes"></a>錯誤碼

| 錯誤碼 | 詳細資料和緩和措施 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 公用 IP SKU 與 Load Balancer SKU 必須相符。 確保 Load Balancer 與公用 IP SKU 相符。 建議對生產環境工作負載使用標準 SKU。 深入了解 [SKU 的差異](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 若未指定 SKU 或未使用標準公用 IP 部署 SKU 時，虛擬機器擴展集會預設為 Basic Load Balancer。 在個別執行個體上使用標準公用 IP 重新部署虛擬機器擴展集，確保已選取 Standard Load Balancer，或是從 Azure 入口網站部署虛擬機器擴展集時直接選取 Standard LB。 |
|MaxAvailabilitySetsInLoadBalancerReached | Load Balancer 的後端集區最多可包含 150 個可用性設定組。 如果您沒有針對後端集區中的 VM 明確定義的可用性設定組，則每個單一 VM 都會進入其本身的可用性設定組。 因此，部署 150 部獨立 VM 表示其會有 150 個可用性設定組，因而達到限制。 您可以部署可用性設定組，並在其中新增額外的 VM 作為因應措施。 |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | 針對基本 Sku 負載平衡器，網路介面和負載平衡器必須位於相同的可用性設定組中。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 在指定的負載平衡器類型 (內部、公用) 上，不能有一個以上的規則具有相同的後端連接埠，以及相同的虛擬機器擴展集所參考的通訊協定。 更新您的規則，以變更此重複規則的建立。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 在指定的負載平衡器類型 (內部、公用) 上，不能有一個以上的規則具有相同虛擬機器擴展集所參考的相同後端連接埠和通訊協定。 更新您的規則參數，以變更此重複規則的建立。 |
|AnotherInternalLoadBalancerExists| 在 Load Balancer 的後端中，您只能有一個內部類型的 Load Balancer 參考相同的 VM/網路介面集。 更新您的部署，確保您只會建立一個相同類型的 Load Balancer。 |
|CannotUseInactiveHealthProbe| 您擁有的探查都是由針對虛擬機器擴展集健康情況所設定的規則使用。 確保正積極使用所設定的探查。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 您不能有多個相同類型 (內部、公用) 的 Load Balancer。 您最多可有一個內部和一個公用 Load Balancer。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 多重放置的群組虛擬機器擴展集或跨可用性區域的虛擬機器擴展集不支援 Basic Load Balancer。 請改為使用 Standard Load Balancer。 |
|MarketplacePurchaseEligibilityFailed | 切換至正確的系統管理帳戶才能購買，因為訂用帳戶是 EA 訂用帳戶。 您可以在[這裡](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase)取得詳細資訊。 |
|ResourceDeploymentFailure| 如果您的負載平衡器處於失敗狀態，請遵循下列步驟，使其從失敗狀態恢復：<ol><li>移至 https://resources.azure.com ，並以您的 Azure 入口網站認證登入。</li><li>選取 [讀取/寫入]。</li><li>在左側，展開 [訂用帳戶]，然後展開含有要更新之 Load Balancer 的訂用帳戶。</li><li>展開 [ResourceGroups]，然後展開含有所要更新 Load Balancer 的資源群組。</li><li>選取 [Microsoft.Network] > [LoadBalancers]，然後選取要更新的 Load Balancer (LoadBalancer_1)。</li><li>在 [LoadBalancer_1] 的顯示頁面上，選取 [取得] > [編輯]。</li><li>將 [ProvisioningState] 值從 [失敗] 更新為 [成功]。</li><li>選取 [PUT] 。</li></ol>|
|  |  |

## <a name="next-steps"></a>後續步驟

* 查看 Azure Load Balancer [SKU 比較表](./skus.md)
* 了解 [Azure Load Balancer 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
