---
title: Azure DDoS 保護參考架構
description: 瞭解 Azure DDoS 保護參考架構。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 3371b9cc0848e387c0150ca9aa7e7a971cecba1a
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905132"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS 保護參考架構

DDoS Protection Standard 是 [針對在虛擬網路中部署的服務](/azure/virtual-network/virtual-network-for-azure-services)所設計。 對於其他服務，將適用預設的基本 DDoS 保護服務。 以下的參考架構依案例排列，並與架構模式組合在一起。

## <a name="virtual-machine-windowslinux-workloads"></a>虛擬機器 (Windows/Linux) 工作負載

### <a name="application-running-on-load-balanced-vms"></a>在負載平衡的 VM 上執行的應用程式

此參考架構顯示一組經過證實的作法，可用來在負載平衡器後方的擴展集中執行多個 Windows VM，以提升可用性和延展性。 此架構可以用於任何無狀態的工作負載，例如網頁伺服器。

![在負載平衡的 VM 上執行的應用程式架構參考圖](./media/ddos-best-practices/image-9.png)

在此架構中，會將工作負載散發到多個 VM 執行個體。 有單一公用 IP 位址，且網際網路流量是透過負載平衡器散發到 VM。 標準 DDoS 保護會在有相關聯公用 IP 的 Azure (網際網路) 負載平衡器其虛擬網路上啟用。

負載平衡器會將連入網際網路要求散發到 VM 執行個體。 虛擬機器擴展集允許以手動方式或自動根據預先定義的規則，相應縮小或放大 VM 數目。 如果資源受到 DDoS 攻擊，這就很重要。 如需此參考架構的詳細資訊，請參閱[這篇文章](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)。

### <a name="application-running-on-windows-n-tier"></a>在 Windows 多層式架構 (N-Tier) 上執行的應用程式

有許多方式可實作多層式架構。 下圖顯示的是一般的三層式 Web 應用程式。 此架構是根據[執行負載平衡的 VM 以獲得延展性和可用性](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)一文所建置。 Web 及 Business 層會使用負載平衡的 VM。

![在 Windows 多層式架構 (N-Tier) 上執行的應用程式架構參考圖](./media/ddos-best-practices/image-10.png)

在此架構中，標準 DDoS 保護會在虛擬網路上啟用。 虛擬網路中的所有公用 IP 都會得到第 3 層和第 4 層的 DDoS 保護。 至於第 7 層保護，則應在 WAF SKU 中部署應用程式閘道。 如需此參考架構的詳細資訊，請參閱[這篇文章](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)。

> [!NOTE]
> 不支援單一 VM 在公用 IP 後方執行的案例。

### <a name="paas-web-application"></a>PaaS Web 應用程式

此參考架構示範在單一區域中執行的 Azure App Service 應用程式。 針對使用 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) 和 [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) 的 Web 應用程式，此架構會示範一組經過證實的作法。
針對容錯移轉案例設定待命區域。

![PaaS Web 應用程式的架構參考圖](./media/ddos-best-practices/image-11.png)

Azure 流量管理員會將連入要求路由到其中一個區域中的應用程式閘道。 正常作業期間，它會將要求路由到主要區域的應用程式閘道。 如果該區域變得無法使用，流量管理員就會容錯移轉到待命區域中的應用程式閘道。

所有來自網際網路且預定送往 Web 應用程式的流量都會透過流量管理員，路由到[應用程式閘道的公用 IP 位址](/azure/application-gateway/application-gateway-web-app-overview)。 在此案例中，應用程式服務 (Web 應用程式) 本身不會直接面向外部，且會受到應用程式閘道保護。 

建議您設定應用程式閘道 WAF SKU (防止模式) 來提供保護，以避免受到第 7 層 (HTTP/HTTPS/WebSocket) 攻擊。 此外，請將 Web 應用程式設定為[只接受來自應用程式閘道 IP 位址的流量](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) (英文)。

如需此參考架構的詳細資訊，請參閱這篇[文章](/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

## <a name="mitigation-for-non-web-paas-services"></a>適用於非 Web PaaS 服務的防護功能

### <a name="hdinsight-on-azure"></a>Azure 上的 HDInsight

此參考架構示範如何針對 [Azure HDInsight 叢集](/azure/hdinsight/)設定標準 DDoS 保護。 請確定 HDInsight 叢集會連結到虛擬網路，且該虛擬網路上的 DDoS 保護已啟用。

![含虛擬網路設定的 [HDInsight] 和 [進階設定] 窗格](./media/ddos-best-practices/image-12.png)

![啟用 DDoS 保護的選取項目](./media/ddos-best-practices/image-13.png)

在此架構中，來自網際網路且預定送往 HDInsight 叢集的流量，會路由到與 HDInsight 閘道負載平衡器相關聯的公用 IP。 閘道負載平衡器接著會將流量直接傳送到前端節點或背景工作節點。 由於已在 HDInsight 虛擬網路上啟用標準 DDoS 保護，因此虛擬網路中的所有公用 IP 都會受到第 3 層和第 4 層 DDoS 保護。 此參考架構可以與多層式 (N-Tier)/多區域參考架構結合。

如需此參考架構的詳細資訊，請參閱[使用 Azure 虛擬網路延伸 Azure HDInsight](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。


> [!NOTE]
> 在使用公用 IP 的虛擬網路中，Azure App Service 環境未能原生支援對於 PowerApps 或 API 的管理。

## <a name="next-steps"></a>下一步

- 瞭解如何 [建立 DDoS 保護計劃](manage-ddos-protection.md)。