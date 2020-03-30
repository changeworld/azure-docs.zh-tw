---
title: 針對 Azure 私人端點連線問題進行疑難排解
description: 診斷專用終結點連接的分步指南
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538529"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>針對 Azure 私人端點連線問題進行疑難排解

本文提供分步指導，以驗證和診斷 Azure 專用終結點連接設置。

Azure 專用終結點是一個網路介面，可私下安全地連接到專用連結服務。 此解決方案通過從虛擬網路向 Azure 服務資源提供專用連線，説明您保護 Azure 中的工作負荷。 此解決方案有效地將這些服務引入虛擬網路。

以下是專用終結點可用的連接方案：

- 來自同一區域的虛擬網路
- 區域對等虛擬網路
- 全球對等虛擬網路
- 通過 VPN 或 Azure 快速路由電路在本地的客戶

## <a name="diagnose-connectivity-problems"></a>診斷連接問題 

查看這些步驟，以確保所有常規配置都按預期解決專用終結點設置的連接問題。

1. 通過流覽資源查看專用終結點配置。

    a. 轉到**私人連結中心**。

      ![專用連結中心](./media/private-endpoint-tsg/private-link-center.png)

    b. 在左側窗格中，選擇 **"專用終結點**"。
    
      ![專用終結點](./media/private-endpoint-tsg/private-endpoints.png)

    c. 篩選並選擇要診斷的專用終結點。

    d. 查看虛擬網路和 DNS 資訊。
     - 驗證連接狀態是否**已批准**。
     - 確保 VM 與承載專用終結點的虛擬網路具有連接。
     - 檢查已分配 FQDN 資訊（副本）和私人 IP 位址。
    
       ![虛擬網路和 DNS 配置](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. 使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)查看資料是否流動。

    a. 在專用終結點資源上，選擇 **"監視器**"。
     - 選擇**資料內**或**資料出**。 
     - 查看嘗試連接到專用終結點時資料是否流動。 預計延遲約 10 分鐘。
    
       ![驗證專用終結點遙測](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  使用 Azure 網路觀察程式的**VM 連接故障排除**。

    a. 選擇用戶端 VM。

    b. 選擇**連接疑難排解**，然後選擇**出站連接**選項卡。
    
      ![網路觀察程式 - 測試出站連接](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. 選擇 **"使用網路觀察程式"進行詳細的連接跟蹤**。
    
      ![網路觀察程式 - 連接故障排除](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. 選擇**按 FQDN 進行的測試**。
     - 從專用終結點資源粘貼 FQDN。
     - 提供埠。 通常，將 443 用於 Azure 存儲或 Azure 宇宙資料庫，將 1336 用於 SQL。

    e. 選擇 **"測試**"並驗證測試結果。
    
      ![網路觀察程式 - 測試結果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. 測試結果中的 DNS 解析必須具有相同的私人 IP 位址分配給專用終結點。

    a. 如果 DNS 設置不正確，請按照以下步驟操作：
     - 如果您使用私人區域： 
       - 確保用戶端 VM 虛擬網路與私人區域關聯。
       - 檢查是否存在專用 DNS 區域記錄。 如果不存在，請創建它。
     - 如果使用自訂 DNS：
       - 查看自訂 DNS 設置，並驗證 DNS 配置是否正確。
       有關指南，請參閱[專用終結點概述：DNS 配置](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)。

    b. 如果由於網路安全性群組 （NSG） 或使用者定義的路由而連接失敗：
     - 查看 NSG 出站規則，並創建適當的出站規則以允許流量。
    
       ![NSG 出站規則](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. 如果連接已驗證結果，則連接問題可能與應用程式層中的秘密、權杖和密碼等其他方面有關。
   - 在這種情況下，請查看與專用終結點關聯的專用連結資源的配置。 有關詳細資訊，請參閱 Azure[專用連結故障排除指南](troubleshoot-private-link-connectivity.md)。

1. 如果問題仍未解決且連接問題仍然存在，請與[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)團隊聯繫。

## <a name="next-steps"></a>後續步驟

 * [在更新的子網（Azure 門戶）上創建專用終結點](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Azure 專用連結故障排除指南](troubleshoot-private-link-connectivity.md)
