---
title: 針對 Azure Load Balancer 資源健康狀態、前端和後端可用性問題進行疑難排解
description: 使用可用的計量來診斷降級或無法使用的 Azure Standard Load Balancer。
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 6148cedbf004e3e63200ac50b91a40866c5b18db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719662"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>針對資源健康狀態、前端和後端可用性問題進行疑難排解 

本文是一份指南，可調查影響您負載平衡器前端 IP 和後端資源可用性的問題。 

## <a name="about-the-metrics-well-use"></a>關於我們將使用的計量
要使用的兩個度量是 *資料路徑可用性* 和 *健康情況探查狀態* ，而且務必瞭解它們對衍生正確見解的意義。 

## <a name="data-path-availability"></a>資料路徑可用性
在已設定負載平衡和輸入 NAT 規則的所有前端埠上，TCP ping 每隔25秒會產生一次資料路徑可用性度量。 然後，此 TCP ping 將會路由傳送至任何狀況良好的 () 後端實例上探查。 如果服務收到對 ping 的回應，則會將其視為成功，並逐一查看度量的總和（如果失敗）。 此計量的計數為每個取樣期間的 TCP ping 總數1/100。 因此，我們想要考慮平均，這會顯示時間週期的總和/計數平均值。 依平均匯總的資料路徑可用性計量，可為我們提供前端 IP 上 TCP ping 的百分比成功率：每個負載平衡和輸入 NAT 規則的埠。

## <a name="health-probe-status"></a>健康情況探查狀態
健康情況探查狀態度量是由健康情況探查中定義之通訊協定的 ping 所產生。 此 ping 會傳送至後端集區中的每個實例，以及健康情況探查中定義的埠。 對於 HTTP 和 HTTPS 探查，成功的 ping 需要 HTTP 200 OK 回應，而 TCP 探查則會將任何回應視為成功。 每次探查的連續成功或失敗，接著會判斷後端實例是否狀況良好，而且能夠接收指派後端集區之負載平衡規則的流量。 與資料路徑可用性類似，我們會使用平均匯總，這會在取樣間隔期間告訴我們平均成功/總計的 ping。 此健康情況探查狀態值會藉由探查您的後端實例，而不透過前端傳送流量，來指出從負載平衡器隔離的後端健康情況。

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>診斷降級和無法使用的負載平衡器
如同 [資源健康狀態文章](load-balancer-standard-diagnostics.md#resource-health-status)中所述，降級的負載平衡器是顯示在25% 到90% 的資料路徑可用性之間，而無法使用的負載平衡器是在兩分鐘的期間內，具有少於25% 資料路徑可用性的負載平衡器。 您可以採取這些相同步驟來調查您在任何健康情況探查狀態中所看到的失敗，或您已設定的資料路徑可用性警示。 我們會探索我們已檢查資源健康狀態的情況，併發現我們的負載平衡器無法在資料路徑可用性為0% 的情況下使用-我們的服務已關閉。

首先，我們將移至負載平衡器深入解析分頁的詳細計量視圖。 您可以透過負載平衡器資源分頁或資源健康情況訊息中的連結來完成此動作。  接下來，我們會流覽至 [前端和後端可用性] 索引標籤，並在發生降級或無法使用的狀態時，檢查時間週期的三十分鐘時間範圍。 如果我們看到資料路徑可用性為0%，我們知道有一個問題會導致所有負載平衡和輸入 NAT 規則的流量無法通過，而且可以看到這項影響持續的時間長度。 

接下來我們需要查看健康情況探查狀態計量，以判斷我們的資料路徑是否無法使用，因為我們沒有狀況良好的後端實例來提供流量。 如果我們的所有負載平衡和輸入規則都有至少一個狀況良好的後端實例，我們知道我們的設定不會導致資料路徑無法使用。 此案例表示 Azure 平臺的問題，在罕見的情況下，如果您發現這些問題，因為系統會將自動警示傳送給我們的小組，以快速解決所有平臺問題。

## <a name="diagnose-health-probe-failures"></a>診斷健康情況探查失敗
假設我們檢查健康情況探查狀態，並找出所有實例都顯示為狀況不良。 這項搜尋解釋了為什麼當流量沒有地方時，我們的資料路徑無法使用。 接著，我們應該逐一查看下列檢查清單，以排除常見的設定錯誤：
* 檢查資源的 CPU 使用率，以檢查您的實例是否事實良好
  * 您可以檢查此 
* 如果使用 HTTP 或 HTTPS 探查，請檢查應用程式的狀況是否良好且有回應
  * 透過與您後端實例相關聯的私人 IP 位址或實例層級的公用 IP 位址，直接存取應用程式，以驗證這項功能是否正常運作
* 檢查套用至後端資源的網路安全性群組。 確定沒有優先順序高於將封鎖健康情況探查的 AllowAzureLoadBalancerInBound 的規則
  * 您可以造訪後端 Vm 或虛擬機器擴展集的網路分頁來進行這項作業
  * 如果您發現此 NSG 問題，請移動現有的允許規則，或建立新的高優先順序規則以允許 AzureLoadBalancer 流量
* 檢查您的作業系統。 確定您的 Vm 正在接聽探查埠並檢查其作業系統防火牆規則，以確保它們不會封鎖來自 IP 位址的探查流量168.63.129.16
  * 您可以藉由在 Linux 終端機中執行 netstat-a Windows 命令提示字元或 netstat-l 來檢查接聽埠
* 請勿將防火牆 NVA VM 放在負載平衡器的後端集區中。請使用 [使用者定義的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) ，透過防火牆將流量路由傳送到後端實例
* 確定您使用的是正確的通訊協定，如果使用 HTTP 探查接聽非 HTTP 應用程式的埠，探查將會失敗

如果您已完成此檢查清單，但仍在尋找健康情況探查失敗，可能會有罕見的平臺問題影響到您的實例的探查服務。 在此情況下，Azure 會將您的回復，並將自動化警示傳送給我們的小組，以快速解決所有平臺問題。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure Load Balancer 健康情況探查](load-balancer-custom-probe-overview.md)
* [深入瞭解 Azure Load Balancer 計量](load-balancer-standard-diagnostics.md)


