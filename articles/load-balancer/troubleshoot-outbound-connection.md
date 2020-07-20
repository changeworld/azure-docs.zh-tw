---
title: 針對 Azure Load Balancer 中的輸出連線進行疑難排解
description: 透過 Azure Load Balancer 輸出連線常見問題的解決方法。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: cd98d5b8d2d4a959a48bfb04fe2eb9e16c4113c9
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851133"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a>針對輸出連接失敗進行疑難排解

本文旨在提供來自 Azure Load Balancer 的輸出連線可能會發生的常見問題的解決方法。 客戶體驗到輸出連線的大部分問題，都是因為 SNAT 埠耗盡，以及導致丟棄封包的連接逾時。 本文提供減輕這些問題的步驟。

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>管理 SNAT (PAT) 連接埠耗盡
用於[PAT](load-balancer-outbound-connections.md)的[暫時埠](load-balancer-outbound-connections.md)是可耗盡資源，如[不含公用 ip 位址的獨立 vm](load-balancer-outbound-connections.md)和[沒有公用 ip 位址的負載平衡 vm](load-balancer-outbound-connections.md)中所述。 您可以監視暫時埠的使用量，並與目前的配置進行比較，以判斷或使用[本](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation)指南來確認 SNAT 耗盡的風險。

如果您知道將會對相同的目的地 IP 位址和連接埠起始許多輸出 TCP 或 UDP 連線，並且觀察到失敗的輸出連線，或是支援人員告知您 SNAT 連接埠 ([PAT](load-balancer-outbound-connections.md) 使用的預先配置[暫時連接埠](load-balancer-outbound-connections.md#preallocatedports)) 將耗盡，您有數個可緩和這些問題的一般選項。 請檢閱這些選項並判斷哪一個可用且最適合您的案例。 可能會有一或多個選項有助於管理此案例。

如果您在了解輸出連線行為方面遇到問題，您可以使用 IP 堆疊統計資料 (netstat)。 或是使用封包擷取來觀察連線行為，也會很有幫助。 您可以在您執行個體的客體 OS 中執行這些封包擷取，或使用[網路監看員來進行封包擷取](../network-watcher/network-watcher-packet-capture-manage-portal.md)。 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>手動配置 SNAT 連接埠以將每部 VM 的 SNAT 連接埠最大化
如[預先配置的連接埠](load-balancer-outbound-connections.md#preallocatedports)中所定義，負載平衡器會根據後端中的 VM 數目自動配置連接埠。 根據預設，這會以保守的方式進行，以確保擴充性。 如果您知道後端將擁有的 Vm 數目上限，您可以在每個輸出規則中手動設定 SNAT 埠。 例如，如果您知道最多可有 10 部 VM，即可為每部 VM 配置 6,400 個 SNAT 連接埠，而不是預設的 1,024 個連接埠。 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>將應用程式修改成重複使用連線 
您可以在應用程式中重複使用連線，以降低對用於 SNAT 之暫時連接埠的需求。 連接重複使用與 HTTP/1.1 之類的通訊協定特別相關，其中的連線重複使用是預設值。 而其他使用 HTTP 作為其傳輸方式的通訊協定 (例如 REST) 也會因而受益。 

重複使用一律比每個要求的獨立且不可部分完成的 TCP 連線來得好。 重複使用可提供效能更佳又非常有效率的 TCP 傳輸。

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>將應用程式修改成使用連線共用
您可以在應用程式中採用連線集區配置，這樣要求會在內部分布到一固定的連線集合 (每個都盡可能地重複使用)。 這個配置會限制使用中的暫時連接埠數目，而建立較可預測的環境。 這個配置也可在單一連線阻斷某個作業的回應時，藉由允許多個作業同時進行，來增加要求輸送量。  

連線共用可能已存在於您用來開發應用程式的架構中，或是您應用程式的組態設定中。 您可以將連線共用與連線重複使用搭配使用。 這樣，您的多個要求就會將數目固定且可預測的連接埠取用至相同的目的地 IP 位址和連接埠。 這些要求也會因為系統有效率地使用 TCP 交易來降低延遲和資源使用量而受益。 UDP 交易也有幫助，因為管理 UDP 流程數目可接著避免發生耗盡狀況，並管理 SNAT 連接埠使用量。

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>將應用程式修改成使用較不積極的重試邏輯
當用於 [PAT](load-balancer-outbound-connections.md) 的[預先配置暫時連接埠](load-balancer-outbound-connections.md#preallocatedports)耗盡或發生應用程式失敗時，不含衰減和降速邏輯的積極或暴力重試會造成耗盡的情況發生或持續存在。 您可以使用較不積極的重試邏輯，以降低對暫時連接埠的需求。 

暫時連接埠有 4 分鐘的閒置逾時 (無法調整)。 如果重試太過積極，耗盡情況就沒有機會進行自我清理。 因此，考慮應用程式重試交易的方式和頻率，是設計的一個重要部分。

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>將公用 IP 指派給每部 VM
指派公用 IP 位址會將案例變更為[VM 的公用 IP](load-balancer-outbound-connections.md)。 用於每個 VM 的所有公用 IP 暫時連接埠都可供 VM 使用。 (與公用 IP 暫時連接埠會與個別後端集區之所有相關 VM 共用的案例相反)。有一些要考慮的取捨，例如公用 IP 位址的額外成本，以及將大量個別 IP 位址加入允許清單所造成的潛在影響。

>[!NOTE] 
>此選項不適用於 Web 背景工作角色。

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>使用多個前端
使用公用 Standard Load Balancer 時，您會指派[多個前端 IP 位址用於輸出連線](load-balancer-outbound-connections.md)以及[乘以可用的 SNAT 連接埠數目](load-balancer-outbound-connections.md#preallocatedports)。  建立前端 IP 組態、規則以及後端集區，以觸發 SNAT 到前端公用 IP 的程式設計。  此規則不需要運作，且健康情況探查不需要成功。  如果您也將多個前端用於輸入 (而非只用於輸出)，您應使用自訂健康情況探查以確保可靠性。

>[!NOTE]
>在大部分情況下，SNAT 連接埠耗盡是設計不良的徵兆。  請確定您了解為什麼您在使用更多前端以新增 SNAT 連接埠之前耗盡連接埠。  您有可能會掩蓋之後會導致失敗的問題。

## <a name="scale-out"></a><a name="scaleout"></a>擴增
[預先配置的連接埠](load-balancer-outbound-connections.md#preallocatedports)會根據後端集區大小進行指派並分組到各層中，以在某些連接埠必須重新配置以便容納下一個較大後端集區大小層時，將中斷時間降至最低。  您可以透過將後端集區調整為指定層的大小上限，選擇增加指定前端的 SNAT 埠使用率。  請記住，應用程式需要預設埠配置，才能有效率地相應放大，而不會有任何風險 SNAT 耗盡。

例如，後端集區中的兩部虛擬機器有 1024 個 SNAT 連接埠可供每個 IP 組態使用，總計允許 2048 個 SNAT 連接埠用於部署。  如果部署增加到 50 部虛擬機器，即使每部虛擬機器預先配置的連接埠數目維持不變，部署還是可以使用總計 51,200 (50 x 1024) 個 SNAT 連接埠。  如果您想要相應放大您的部署，請檢查每一層預先配置的[埠](load-balancer-outbound-connections.md#preallocatedports)數目，以確定您將向外延展調整為各自層級的最大值。  在先前範例中，如果您已選擇擴增到 51 個而不是 50 個執行個體，您會進到下一層且最終在每部虛擬機器及總計上具有較少的 SNAT 連接埠。

如果您擴增到下一個較大後端集區大小的層級，且必須將已配置的連接埠重新配置，則部分輸出連線可能會逾時。  如果您只使用部分 SNAT 連接埠，則相應放大到下一個較大後端集區大小並無意義。  每當您移至下一個後端集區層級時，會有一半的現有連接埠重新配置。  如果您不希望發生這種情形，則必須讓部署符合層大小。  或確保您的應用程式可以視需要偵測及重試。  TCP 存留可協助在 SNAT 連接埠因為重新配置而無法運作時進行偵測。

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>使用 Keepalive 來重設輸出閒置逾時
連出連線有 4 分鐘的閒置逾時。 此逾時可透過[輸出規則](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout)調整。 您也可以使用傳輸 (例如 TCP Keepalive) 或應用程式層 Keepalive 來重新整理閒置流程，然後視需要重設此閒置逾時。  

使用 TCP 存留時，在連線的一端啟用它們就已足夠。 例如，只在伺服器端啟用它們來重設流程的閒置計時器就已足夠，不需要在兩端都起始 TCP 存留。  應用程式層也有類似概念，包括資料庫用戶端-伺服器組態。  檢查伺服器端是否有應用程式特定 keepalive 的可用選項。

## <a name="next-steps"></a>後續步驟
我們一定會想要改善客戶的體驗。 如果您遇到本文未列出或解決的輸出連線問題，請透過此頁面底部的 GitHub 提交意見反應，我們會儘快處理您的意見反應。
