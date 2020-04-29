---
title: 遠端工作：遠端工作的網路虛擬裝置（NVA）考慮 |Azure VPN 閘道
description: 本文可協助您瞭解在 COVID-19 流感期間，使用 Azure 中的網路虛擬裝置（Nva）時應考慮的事項。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337101"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>遠端工作：遠端工作的網路虛擬裝置（NVA）考慮

>[!NOTE]
>本文說明如何運用網路虛擬裝置、Azure、Microsoft 網路和 Azure 合作夥伴生態系統，從遠端工作，並減輕因 COVID-19 危機而面臨的網路問題。
>

某些 Azure 客戶會利用來自 Azure Marketplace 的協力廠商網路虛擬裝置（Nva），為其在 COVID-19 epidemic 期間在家工作的員工提供重要服務，例如點對站 VPN。 本文概述在 Azure 中利用 Nva 來提供遠端存取解決方案時，要考慮的一些高階指引。

## <a name="nva-performance-considerations"></a>NVA 效能考慮

Azure Marketplace 中的所有主要 NVA 廠商都應該有 VM 大小的建議，以及部署其解決方案時要使用的實例數目。  雖然幾乎所有的 NVA 廠商都可讓您選擇在指定區域中可用的任何大小，但請務必遵循 Azure VM 實例大小的廠商建議，因為這些建議是廠商已在 Azure 中完成效能測試的 VM 大小。  

### <a name="consider-the-following"></a>請考慮下列事項

- **並行使用者的容量和數目**-這對於點對站 VPN 使用者而言特別重要，因為每個連線的使用者會建立一個加密（IPSEC 或 SSL VPN）通道。  
- **匯總輸送量**-您需要多少的匯總頻寬，才能容納需要提供遠端存取的使用者數目。
- **您需要的 vm 大小**-您應該一律使用 NVA 廠商建議的 vm 大小。  針對點對站 VPN，如果您將會有許多並行的使用者連線，您應該使用較大的 VM 大小，例如[Dv2 和 DSv2 系列](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2 和 Dsv2 系列")vm。 這些 Vm 通常會有更多的個 vcpu，而且可以處理更多的並行 VPN 會話。  除了擁有更多虛擬核心之外，Azure 中較大的 VM 大小也比較小的 VM 大小擁有更多的匯總頻寬容量。
    > **重要事項：** 每個廠商都會以不同的方式來使用資源。  如果不清楚您應該使用何種實例大小來配合估計的使用者負載，您應該直接洽詢軟體廠商，並要求他們提供建議。
- **實例數目**-如果您預期會有大量的使用者和連線，您的 NVA 實例大小所能達到的範圍會有所限制。  請考慮部署多個 VM 實例。
- **IPSEC vpn 與 SSL vpn** -在一般的 ipsec vpn 部署中，其效能優於 SSL vpn 的執行。  
- **授權**-請確定您為 NVA 解決方案購買的軟體授權，將涵蓋您在 COVID-19 epidemic 期間可能遇到的突然成長。  許多 NVA 授權方案會限制解決方案能夠連線的數目或頻寬。
- **加速網路**-考慮支援加速網路的 NVA 解決方案。  加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 此高效能路徑會從資料路徑略過主機，以降低延遲、抖動和 CPU 使用率，以便在支援的 VM 類型上使用最嚴苛的網路工作負載。 具有兩個或多個個 vcpu 的大部分一般用途和計算優化實例大小，都支援加速網路。

## <a name="monitoring-resources"></a>監視資源

每個 NVA 解決方案都有自己的工具和資源，可用來監視其 NVA 的效能。  請洽詢您的廠商檔，以確定您瞭解效能限制，並且可以偵測 NVA 接近或達到容量的時間。  此外，您還可以查看 Azure 監視器的網路深入解析，並查看關於網路虛擬裝置的基本效能資訊，例如：

- CPU 使用率
- Network In
- Network Out
- 連入流量
- 輸出流程

## <a name="next-steps"></a>後續步驟

大部分的主要 NVA 合作夥伴都已張貼在 COVID-19 期間，針對突然、非預期成長進行調整的指導方針。 以下是一些適用于合作夥伴資源的實用連結。

[Barracuda 在 COVID-19 期間保護您的資料時，從家裡啟用工作](https://www.barracuda.com/covid-19/work-from-home "在 COVID-19 期間，于保護您的資料時，從家裡啟用工作")

[適用于 COVID-19 準備的 Cisco AnyConnect 執行和效能/調整參考](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "適用于 COVID-19 準備的 Cisco AnyConnect 執行和效能/調整參考")

[Citrix COVID-19 回應支援中心](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 回應支援中心")

[F5 指導方針，以解決遠端工作者的大幅增加](https://www.f5.com/business-continuity "F5 指導方針，以解決遠端工作者的大幅增加")

[Fortinet COVID-適用于客戶和合作夥伴的19個更新](https://www.fortinet.com/covid-19.html "COVID-適用于客戶和合作夥伴的19個更新")

[Palo Alto Networks COVID-19 回應中心](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 回應中心")
