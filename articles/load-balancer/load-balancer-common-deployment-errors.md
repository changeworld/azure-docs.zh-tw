---
title: 針對一般部署錯誤進行疑難排解
titleSuffix: Azure Load Balancer
description: 說明如何解決部署 Azure 負載平衡器時的常見錯誤
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791080"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>使用 Azure Load Balancer 針對常見的 Azure 部署錯誤進行疑難排解

本文說明一些常見的 Azure Load Balancer 部署錯誤，並提供解決錯誤的資訊。 如果您要尋找有關錯誤碼的資訊，但本文並未提供該資訊，請讓我們知道。 在此頁面底部，您可以留下意見反應。 意見反應會與 GitHub 問題一併追蹤。

## <a name="error-codes"></a>錯誤碼

| 錯誤碼 | 詳細資料和緩和措施 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 公用 IP SKU 和 Load Balancer SKU 必須相符。 確定 Azure Load Balancer 和公用 IP Sku 相符。 建議將標準 SKU 用於生產工作負載。 深入瞭解[sku 中的差異](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 當未指定或部署 SKU 時，虛擬機器擴展集會預設為基本負載平衡器，而不需要標準公用 Ip。 在個別實例上使用標準公用 Ip 重新部署虛擬機器擴展集，以確保已選取 Standard Load Balancer，或直接從 Azure 入口網站部署虛擬機器擴展集時選取標準 LB。 |
|MaxAvailabilitySetsInLoadBalancerReached | Load Balancer 的後端集區最多可以包含150個可用性設定組。 如果您沒有針對後端集區中的 Vm 明確定義的可用性設定組，則每個單一 VM 都會進入其本身的可用性設定組。 因此，部署150獨立 Vm 可能表示它會有150個可用性設定組，因而達到此限制。 您可以部署可用性設定組，並在其中新增額外的 Vm 作為因應措施。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 指定的負載平衡器類型（內部、公用）上不能有一個以上的規則，其具有相同的後端埠，以及相同的虛擬機器擴展集所參考的通訊協定。 更新您的規則，以變更此重複規則的建立。 |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 指定的負載平衡器類型（內部、公用）上不能有一個以上的規則，其具有相同的後端埠，以及相同的虛擬機器擴展集所參考的通訊協定。 更新您的規則參數，以變更此重複規則的建立。 |
|AnotherInternalLoadBalancerExists| 在 Load Balancer 的後端中，您只能有一個類型為 internal 的 Load Balancer 參考相同的 Vm/網路介面集。 更新您的部署，以確保您只會建立一個相同類型的 Load Balancer。 |
|CannotUseInactiveHealthProbe| 針對虛擬機器擴展集健全狀況所設定的任何規則，您都不能有未使用的探查。 請確定所設定的探查正在積極使用。 |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 您不能有多個相同類型（內部、公用）的負載平衡器。 您最多可以有一個內部和一個公用 Load Balancer。 |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 不支援對多個放置群組虛擬機器擴展集或跨可用性區域虛擬機器擴展集進行基本 Load Balancer。 請改用 Standard Load Balancer。 |
|ResourceDeploymentFailure| 如果您的負載平衡器處於失敗狀態，請遵循下列步驟，使其從失敗狀態恢復：<ol><li>移至https://resources.azure.com，並使用您的 Azure 入口網站認證登入。</li><li>選取 [**讀取/寫入**]。</li><li>展開左側的 [**訂閱**]，然後展開含有要更新之 Load Balancer 的訂用帳戶。</li><li>展開 [ **ResourceGroups**]，然後展開含有要更新之 Load Balancer 的資源群組。</li><li>選取 [ **Microsoft** > **LoadBalancers**]，然後選取要更新的 Load Balancer， **LoadBalancer_1**。</li><li>在**LoadBalancer_1**的 [顯示] 頁面上，選取 [**取得** > **編輯**]。</li><li>將**ProvisioningState**值從更新為 Failed **。** **Failed**</li><li>選取 [PUT] ****。</li></ol>|
|  |  |

## <a name="next-steps"></a>後續步驟

* 查看 Azure Load Balancer [SKU 比較表](./skus.md)
* 瞭解[Azure Load Balancer 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)
