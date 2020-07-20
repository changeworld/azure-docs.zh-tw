---
title: 針對 Azure 私人端點連線問題進行疑難排解
description: 診斷私人端點連線性的逐步指引
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77538529"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>針對 Azure 私人端點連線問題進行疑難排解

本文提供逐步指引，以驗證及診斷您的 Azure 私人端點連線能力設定。

Azure 私用端點是一種網路介面，可讓您私下且安全地連接到私人連結服務。 此解決方案藉由提供從您的虛擬網路到 Azure 服務資源的私人連線，協助您保護您在 Azure 中的工作負載。 此解決方案會將這些服務有效地帶入您的虛擬網路。

以下是私人端點提供的連線案例：

- 來自相同區域的虛擬網路
- 區域內對等互連虛擬網路
- 全域對等互連的虛擬網路
- 透過 VPN 或 Azure ExpressRoute 線路的客戶內部部署

## <a name="diagnose-connectivity-problems"></a>診斷連線問題 

請參閱下列步驟，以確定所有一般設定都如預期般解決私人端點安裝的連線問題。

1. 流覽資源以檢查私人端點設定。

    a. 移至 [**私人連結中心**]。

      ![私人連結中心](./media/private-endpoint-tsg/private-link-center.png)

    b. 在左窗格中選取 [**私人端點**]。
    
      ![私人端點](./media/private-endpoint-tsg/private-endpoints.png)

    c. 篩選並選取您想要診斷的私人端點。

    d. 檢查虛擬網路和 DNS 資訊。
     - 驗證連接狀態是否已**核准**。
     - 請確定 VM 可以連線到裝載私人端點的虛擬網路。
     - 檢查是否已指派 FQDN 資訊（複製）和私人 IP 位址。
    
       ![虛擬網路和 DNS 設定](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. 使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)查看資料是否流動。

    a. 在 [私人端點資源] 上，選取 [**監視**]。
     - 選取**資料**或**輸出資料**。 
     - 當您嘗試連接至私用端點時，請查看資料是否流動。 預期延遲大約10分鐘。
    
       ![確認私人端點遙測](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  使用 Azure 網路監看員的**VM 連線疑難排解**。

    a. 選取用戶端 VM。

    b. 選取 [連線] [**疑難排解**]，然後選取 [**輸出**連線] 索引標籤。
    
      ![網路監看員-測試輸出連線](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. 選取 [**使用網路監看員以進行詳細的連接追蹤**。
    
      ![網路監看員-連接疑難排解](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. 選取 [**依 FQDN 測試**]。
     - 從私人端點資源貼上 FQDN。
     - 提供埠。 通常，使用443來 Azure 儲存體或 Azure Cosmos DB 和1336（適用于 SQL）。

    e. 選取 [**測試**]，然後驗證測試結果。
    
      ![網路監看員測試結果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. 來自測試結果的 DNS 解析必須具有指派給私人端點的相同私人 IP 位址。

    a. 如果 DNS 設定不正確，請遵循下列步驟：
     - 如果您使用私人區域： 
       - 請確定用戶端 VM 虛擬網路與私人區域相關聯。
       - 查看私人 DNS 區域記錄是否存在。 如果不存在，請加以建立。
     - 如果您使用自訂 DNS：
       - 檢查您的自訂 DNS 設定，並驗證 DNS 設定是否正確。
       如需指引，請參閱[私用端點總覽： DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)設定。

    b. 如果連線因為網路安全性群組（Nsg）或使用者定義的路由而失敗：
     - 請檢查 NSG 輸出規則，並建立適當的輸出規則以允許流量。
    
       ![NSG 輸出規則](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. 如果連接已驗證結果，連線問題可能與應用層的秘密、權杖和密碼等其他層面有關。
   - 在此情況下，請檢查與私人端點相關聯之私人連結資源的設定。 如需詳細資訊，請參閱[Azure 私人連結疑難排解指南](troubleshoot-private-link-connectivity.md)。

1. 如果您的問題仍然無法解決，且連線問題仍然存在，請聯絡[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)小組。

## <a name="next-steps"></a>後續步驟

 * [在更新的子網上建立私用端點（Azure 入口網站）](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Azure 私人連結疑難排解指南](troubleshoot-private-link-connectivity.md)
