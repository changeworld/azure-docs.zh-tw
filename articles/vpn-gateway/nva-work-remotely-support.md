---
title: 遠端工作：遠端工作的網路虛擬裝置 （NVA） 注意事項 |Azure VPN 閘道
description: 本文可説明您瞭解在 COVID-19 大流行期間，在 Azure 中使用網路虛擬裝置 （NVA） 時應考慮的事情。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337101"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>遠端工作：遠端工作的網路虛擬裝置 （NVA） 注意事項

>[!NOTE]
>本文介紹如何利用網路虛擬裝置、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作並緩解因 COVID-19 危機而面臨的網路問題。
>

某些 Azure 客戶利用 Azure 應用商店的協力廠商網路虛擬裝置 （NVA） 為其在 COVID-19 流行期間在家工作的員工提供關鍵服務，例如點對點 VPN。 本文概述了在利用 Azure 中的 NVA 提供遠端存取解決方案時需要考慮的一些高級指導。

## <a name="nva-performance-considerations"></a>NVA 性能注意事項

Azure 應用商店中的所有主要 NVA 供應商都應就 VM 大小和部署其解決方案時使用的實例數提出建議。  雖然幾乎所有 NVA 供應商都允許您選擇給定區域中可用的任何大小，但遵循有關 Azure VM 實例大小的供應商建議非常重要，因為這些建議是供應商已完成性能的 VM 大小在 Azure 中測試。  

### <a name="consider-the-following"></a>請考慮以下事項

- **容量和併發使用者數**- 此數位對於點對點 VPN 使用者尤其重要，因為每個連接的使用者將創建一個加密（IPSec 或 SSL VPN）隧道。  
- **聚合輸送量**- 您需要的聚合頻寬量，以容納提供遠端存取所需的使用者數量。
- **您需要的 VM 大小**- 應始終使用 NVA 供應商建議的 VM 大小。  對於點對點 VPN，如果您要具有大量併發使用者連接，則應使用更大的 VM 大小，如[Dv2 和 DSv2 系列](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 和 Dsv2 系列")VM。 這些 VM 往往有更多的 vCPU，並可以處理更多的併發 VPN 會話。  除了擁有更多的虛擬內核外，Azure 中較大的 VM 大小比較小的 VM 大小具有更多的聚合頻寬容量。
    > **重要提示：** 每個供應商以不同的方式利用資源。  如果不清楚應使用什麼實例大小來容納估計的使用者負載，則應直接聯繫軟體供應商並徵求他們的建議。
- **實例數**- 如果您希望擁有大量使用者和連接，則對擴展 NVA 實例大小可以實現什麼有限制。  請考慮部署多個 VM 實例。
- **IPSec VPN 與 SSL VPN -** 一般來說，IPSec VPN 實現的性能優於 SSL VPN 實現。  
- **許可**- 確保您為 NVA 解決方案購買的軟體許可證將涵蓋您在 COVID-19 流行期間可能經歷的突然增長。  許多 NVA 許可程式限制解決方案能夠實現的連接或頻寬數量。
- **加速網路**- 考慮支援加速網路的 NVA 解決方案。  加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 此高性能路徑從資料路徑繞過主機，減少延遲、抖動和 CPU 利用率，用於受支援的 VM 類型上要求最苛刻的網路工作負載。 大多數通用和計算優化的實例大小都支援加速網路，具有兩個或多個 vCPU。

## <a name="monitoring-resources"></a>監視資源

每個 NVA 解決方案都有自己的工具和資源來監視其 NVA 的性能。  請諮詢供應商文檔，確保您瞭解性能限制，並可以檢測 NVA 何時接近或達到容量。  除此之外，您還可以查看 Azure 監視器網路見解，並查看有關網路虛擬裝置的基本性能資訊，例如：

- CPU 使用率
- Network In
- Network Out
- 連入流量
- 出站流

## <a name="next-steps"></a>後續步驟

大多數主要的 NVA 合作夥伴都發佈了有關 COVID-19 期間突然意外增長的擴展指南。 下面是一些指向合作夥伴資源的有用連結。

[梭子魚在家工作，同時在 COVID-19 期間保護您的資料](https://www.barracuda.com/covid-19/work-from-home "在 COVID-19 期間啟用在家工作，同時保護您的資料")

[用於 COVID-19 準備的思科 AnyConnect 實施和性能/擴展參考](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "用於 COVID-19 準備的思科 AnyConnect 實施和性能/擴展參考")

[Citrix COVID-19 回應支援中心](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 回應支援中心")

[F5 指導解決遠端工作人員急劇增加的問題](https://www.f5.com/business-continuity "F5 指導解決遠端工作人員急劇增加的問題")

[Fortinet COVID-19 針對客戶和合作夥伴的更新](https://www.fortinet.com/covid-19.html "COVID-19 針對客戶和合作夥伴的更新")

[帕洛阿爾托網路COVID-19回應中心](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "帕洛阿爾托網路COVID-19回應中心")
