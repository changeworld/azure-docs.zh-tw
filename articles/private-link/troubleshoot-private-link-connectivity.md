---
title: 針對 Azure Private Link 連線能力的問題進行疑難排解
description: 診斷私人連結連線能力的逐步指引
services: private-link
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
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544304"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>針對 Azure Private Link 連線能力的問題進行疑難排解

本文提供逐步指引，可驗證及診斷 Azure Private Link 設定的連線能力。

使用 Azure Private Link，您可以透過虛擬網路中的私人端點，存取 Azure 平臺即服務 (PaaS) 服務，例如 Azure 儲存體、Azure Cosmos DB 和 Azure SQL Database，以及 Azure 託管客戶或合作夥伴服務。 您的虛擬網路與服務之間的流量會透過 Microsoft 骨幹網路進行，以消除公用網際網路的洩漏。 您也可以在虛擬網路中建立自己的私用連結服務，並將其私下提供給您的客戶。

您可以啟用您的服務，以在 Azure Load Balancer 的標準層級後方執行，以進行 Private Link 存取。 您服務的取用者可以在其虛擬網路內建立私人端點，並將其對應到此服務，以私下進行存取。

以下是 Private Link 提供的連接案例：

- 來自相同區域的虛擬網路
- 地區對等互連虛擬網路
- 全域對等互連的虛擬網路
- 透過 VPN 或 Azure ExpressRoute 線路的客戶內部部署

## <a name="deployment-troubleshooting"></a>部署疑難排解

如果您無法從您選擇的私人連結服務的子網中選取來源 IP 位址，請參閱在 [私用連結服務上停用網路原則](./disable-private-link-service-network-policy.md) 的相關資訊，以進行疑難排解。

請確定您要從中選取來源 IP 位址的子網已停用設定 **privateLinkServiceNetworkPolicies** 。

## <a name="diagnose-connectivity-problems"></a>診斷連線能力問題

如果您遇到私人連結設定的連線問題，請參閱下列步驟，以確定所有一般設定都如預期般運作。

1. 藉由流覽資源來審核 Private Link 設定。

    a. 移至 **Private Link Center**。

      ![Private Link 中心](./media/private-link-tsg/private-link-center.png)

    b. 在左窗格中，選取 [ **私用連結服務**]。

      ![Private link 服務](./media/private-link-tsg/private-link-service.png)

    c. 篩選並選取您要診斷的私人連結服務。

    d. 檢查私人端點連接。
     - 確定您要進行連線的私人端點會以 **已核准** 的連接狀態列出。
     - 如果狀態為 **暫** 止，請選取它並加以核准。

       ![私人端點連接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 選取名稱，移至您要連接的私人端點。 請確定連接狀態顯示為 [ **已核准**]。

       ![私人端點連接總覽](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 雙方都經過核准後，請再次嘗試連接。

    e. **[內容] 索引** 標籤上的 [**總覽**] 索引標籤和 **資源識別碼** 的審核 **別名**。
     - 請確定 **別名** 和 **資源識別碼** 資訊符合您用來建立此服務私人端點的 **別名** 和 **資源識別碼。**

       ![確認別名資訊](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![確認資源識別碼資訊](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 [**總覽**] 索引標籤上的 **可見度** 資訊。
     - 請確定您的訂用帳戶落在 **可見度** 範圍內。

       ![確認可見度資訊](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. 請參閱 [**總覽**] 索引標籤上的 **負載平衡器** 資訊。
     - 您可以選取負載平衡器連結來移至負載平衡器。

       ![確認負載平衡器資訊](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 確定已根據您的預期設定負載平衡器設定。
       - 檢查 **前端 IP** 設定。
       - 檢查 **後端** 集區。
       - 檢查 **負載平衡規則**。

       ![確認負載平衡器屬性](./media/private-link-tsg/pls-ilb-properties.png)

     - 請確定負載平衡器正在依照先前的設定運作。
       - 在負載平衡器後端集區可用的子網以外的任何子網中選取 VM。
       - 嘗試從先前的 VM 存取負載平衡器前端。
       - 如果連線將它設為每個負載平衡規則的後端集區，則您的負載平衡器可正常運作。
       - 您也可以透過 Azure 監視器查看負載平衡器度量，以查看資料是否流經負載平衡器。

1. 使用 [Azure 監視器](../azure-monitor/overview.md) 查看資料是否流動。

    a. 在私人連結服務資源上，選取 [ **計量**]。
     - 選取 [ **位元組** ] 或 [ **位元組輸出**]。
     - 當您嘗試連線至私用連結服務時，請查看資料是否流動。 預期的延遲大約為10分鐘。

       ![確認私用連結服務計量](./media/private-link-tsg/pls-metrics.png)

1. 如果您的問題仍未解決，但仍有連線問題，請洽詢 [Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 小組。

## <a name="next-steps"></a>後續步驟

 * [ (CLI) 建立 private link 服務 ](./create-private-link-service-cli.md)
 * [Azure 私人端點疑難排解指南](troubleshoot-private-endpoint-connectivity.md)