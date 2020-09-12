---
title: 遠端工作：網路虛擬裝置 (NVA 遠端工作的) 考慮 |Azure VPN 閘道
description: 本文可協助您瞭解在 COVID-19-19 流感期間，在 Azure 中使用網路虛擬裝置 (Nva) 所應考慮的事項。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 4783016e472907392f2d379efa0fed2d90ed21bc
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595354"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>遠端工作：網路虛擬裝置 (NVA 遠端工作的) 考慮

>[!NOTE]
>本文說明如何利用網路虛擬裝置、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19-19 危機所面臨的網路問題。
>

有些 Azure 客戶會使用協力廠商網路虛擬裝置 (從 Azure Marketplace Nva) ，以針對在 COVID-19-19 epidemic 期間在家工作的員工，提供點對站 VPN 等重要服務。 本文概述在 Azure 中利用 Nva 來提供遠端存取解決方案時，應考慮的一些高階指引。

## <a name="nva-performance-considerations"></a>NVA 效能考慮

Azure Marketplace 中的所有主要 NVA 廠商都應該有 VM 大小的建議，以及部署其解決方案時要使用的實例數目。  雖然幾乎所有的 NVA 廠商都能讓您選擇可在指定區域中使用的任何大小，但請務必遵循 Azure VM 實例大小的廠商建議，因為這些建議是廠商已在 Azure 中完成效能測試的 VM 大小。  

### <a name="consider-the-following"></a>請考慮下列事項

- **容量和並行使用者數目** -此數目對於點對站 VPN 使用者來說特別重要，因為每個連線的使用者都會建立一個加密 (IPSEC 或 SSL VPN) 通道。  
- **匯總輸送量** -您需要哪些匯總頻寬來容納您需要提供遠端存取的使用者數目。
- **您將需要的 vm 大小** ，您應該一律使用 NVA 廠商建議的 vm 大小。  針對點對站 VPN，如果您會有大量的並行使用者連線，您應該使用較大的 VM 大小，例如 [Dv2 和 DSv2 系列](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 和 Dsv2 系列") vm。 這些 Vm 通常會有更多個 vcpu，而且可以處理更多的並行 VPN 會話。  除了有更多虛擬核心，Azure 中較大的 VM 大小具有比較小 VM 大小更多的匯總頻寬容量。
    > **重要事項：** 每個廠商都利用不同的資源。  如果不清楚應使用何種實例大小來容納您的預估使用者負載，您應該直接洽詢軟體廠商，並要求他們提供建議。
- **實例數目** -如果您預期會有大量的使用者和連接，則調整 NVA 實例大小所能達到的限制會有所限制。  請考慮部署多個 VM 實例。
- **IPSEC vpn 與 SSL vpn** -一般 ipsec vpn 的執行效能優於 ssl vpn 的實施。  
- **授權** -請確定您為 NVA 解決方案購買的軟體授權將涵蓋您在 covid-19-19 epidemic 期間可能遇到的突然成長。  許多 NVA 授權方案會限制解決方案所能達到的連接或頻寬數目。
- **加速網路** -考慮具有加速網路支援的 NVA 解決方案。  加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 這個高效能路徑會略過資料路徑中的主機，以降低延遲、抖動和 CPU 使用率，以搭配支援的 VM 類型上最嚴苛的網路工作負載使用。 有兩個或多個個 vcpu 的一般用途和計算優化的實例大小可支援加速網路。

## <a name="monitoring-resources"></a>監視資源

每個 NVA 方案都有自己的工具和資源，可用來監視其 NVA 的效能。  請參閱您的廠商檔，以確定您瞭解效能限制，並且可以偵測到您的 NVA 接近或達到產能。  此外，您還可以查看 Azure 監視器網路深入解析，並查看網路虛擬裝置的基本效能資訊，例如：

- CPU 使用率
- Network In
- Network Out
- 連入流量
- 連出流量

## <a name="next-steps"></a>後續步驟

大部分的主要 NVA 合作夥伴都有針對 COVID-19-19 期間突然的非預期成長進行調整的已發佈指引。 以下是一些適用于合作夥伴資源的實用連結。

[Barracuda 可讓公司在家工作，同時在 COVID-19 時保護您的資料-19](https://www.barracuda.com/covid-19/work-from-home "在 COVID-19 期間保護您的資料時，從家裡啟用工作")

[在 Coronavirus 期間 Check Point 安全的遠端員工](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Coronavirus 期間的安全遠端員工")

[適用于 COVID-19 的 Cisco AnyConnect 執行和效能/調整參考-19 個準備](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "適用于 COVID-19 的 Cisco AnyConnect 執行和效能/調整參考-19 個準備")

[Citrix COVID-19-19 回應支援中心](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19-19 回應支援中心")

[可解決遠端工作者大幅增加的 F5 指導方針](https://www.f5.com/business-continuity "可解決遠端工作者大幅增加的 F5 指導方針")

[適用于客戶和合作夥伴的 Fortinet COVID-19-19 個更新](https://www.fortinet.com/covid-19.html "適用于客戶和合作夥伴的 COVID-19 19 更新")

[Palo Alto Networks COVID-19-19 回應中心](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19-19 回應中心")
