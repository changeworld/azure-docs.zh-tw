---
title: 針對 Azure 私用端點連線問題進行疑難排解
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
ms.openlocfilehash: 1c3fc4ebaffe2347a9c46b626b94042d1c9cfe11
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031851"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>針對私用端點連線問題進行疑難排解

本指南提供逐步指引，以驗證及診斷您的私人端點連線設定。 

Azure 私用端點是一種網路介面，可讓您私下且安全地連接到私人連結服務。 此解決方案藉由提供從您的虛擬網路到 Azure 服務資源的私人連線，協助您保護您在 Azure 中的工作負載。 這可有效地將這些服務帶入您的虛擬網路。 

以下是私人端點提供的連線案例 
- 來自相同區域的虛擬網路 
- 區域內對等互連虛擬網路
- 全域對等互連的虛擬網路
- 透過 VPN 或 Express Route 線路的客戶內部部署

## <a name="diagnosing-connectivity-problems"></a>診斷連線問題 
請執行下列步驟，以確定所有一般設定都如預期般解決私人端點安裝的連線問題。

1. 流覽資源以檢查私人端點設定 

    a）前往**私人連結中心**

      ![私人連結中心](./media/private-endpoint-tsg/private-link-center.png)

    b）從左側流覽窗格選取 [私人端點]
    
      ![私人端點](./media/private-endpoint-tsg/private-endpoints.png)

    c）篩選並選取您想要診斷的私用端點

    d）檢查虛擬網路和 DNS 資訊
    
     - 驗證連接狀態是否已**核准**
     - 確定 VM 可以連線到裝載私人端點的 VNet
     - 已指派 FQDN 資訊（複製）和私人 IP 位址
    
       ![VNet 和 DNS 設定](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. 使用[**Azure 監視器**](https://docs.microsoft.com/azure/azure-monitor/overview)來審查資料的流動

    a）在私人端點資源上，選取 [**監視**]
     - 選取 [資料傳入] 或 [資料輸出]，並檢查當嘗試連接到私人端點時，資料是否流動。 預期大約10分鐘的延遲。
    
       ![確認私人端點遙測](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. 使用來自**網路監看員**的 VM 連接疑難排解

    a）選取用戶端 VM

    b）選取 [**連接疑難排解**] 區段 [**輸出**連線] 索引標籤
    
      ![網路監看員-測試輸出連線](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c）選取 **使用網路監看員以進行詳細的連接追蹤**
    
      ![網路監看員-連接疑難排解](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d）選取 [**依 FQDN 測試**]
     - 從私人端點資源貼上 FQDN
     - 提供埠（*通常是443，適用于 Azure 儲存體或 COSMOS，1336 For Sql ...* ）

    e）按一下 [**測試**]，然後驗證測試結果
    
      ![網路監看員測試結果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. 來自測試結果的 DNS 解析必須具有指派給私人端點的相同私人 IP 位址

    a）如果 DNS 設定不正確，請執行下列動作
     - 使用私人區域： 
       - 請確定用戶端 VM VNet 與私人區域相關聯
       - 檢查私人 DNS 區域記錄，如果不存在則建立
    
     - 使用自訂 DNS：
       - 請檢查您的客戶 DNS 設定，並驗證 DNS 設定是否正確。
       如需指引，請參閱[私人端點總覽-DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)設定。

    b）連線失敗的原因是 NSG/Udr
     - 請參閱 NSG 輸出規則，並建立適當的輸出規則以允許流量
    
       ![NSG 輸出規則](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. 如果連接已驗證結果，連線問題可能與應用層上的秘密、權杖、密碼等其他層面相關。
   - 在此情況下，請檢查與私人端點相關聯之私人連結資源的設定。 請參閱[私用連結疑難排解指南](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)。 

6. 如果您的問題仍然無法解決，且連線問題仍然存在，請聯絡[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)小組。 

## <a name="next-steps"></a>後續步驟

 * [在更新的子網上建立私用端點（Azure 入口網站）](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [私人連結疑難排解指南](https://docs.microsoft.com/azure/private-link/private-link-connectivity-troubleshooting)
