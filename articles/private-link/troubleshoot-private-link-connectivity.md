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
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77539462"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>針對 Azure Private Link 連線能力的問題進行疑難排解

本文提供逐步指引，以驗證及診斷 Azure 私人連結設定的連線能力。

透過 Azure 私人連結，您可以存取 Azure 平臺即服務（PaaS）服務，例如 Azure 儲存體、Azure Cosmos DB 和 Azure SQL Database，以及透過虛擬網路中私人端點的 Azure 託管客戶或合作夥伴服務。 您的虛擬網路與服務之間的流量會透過 Microsoft 骨幹網路進行，這可免除公用網際網路的暴露。 您也可以在虛擬網路中建立自己的私人連結服務，並將其私下提供給您的客戶。

您可以讓您的服務在 Azure Load Balancer 的標準層級後方執行，以進行私人連結存取。 您的服務取用者可以在其虛擬網路內建立私人端點，並將其對應到此服務以私下存取。

以下是私人連結提供的連線案例：

- 來自相同區域的虛擬網路
- 區域內對等互連虛擬網路
- 全域對等互連的虛擬網路
- 透過 VPN 或 Azure ExpressRoute 線路的客戶內部部署

## <a name="deployment-troubleshooting"></a>部署疑難排解

請參閱在[私人連結服務上停用網路原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的相關資訊，以取得疑難排解案例，讓您無法從您選擇的子網中選取私人連結服務的來源 IP 位址。

請確定您要選取來源 IP 位址的子網已停用設定**privateLinkServiceNetworkPolicies** 。

## <a name="diagnose-connectivity-problems"></a>診斷連線問題

如果您在私人連結設定時遇到連線問題，請參閱下列步驟，以確定所有一般設定都如預期般運作。

1. 流覽資源以檢查私人連結設定。

    a. 移至 [**私人連結中心**]。

      ![私人連結中心](./media/private-link-tsg/private-link-center.png)

    b. 在左窗格中，選取 [**私人連結服務**]。

      ![私人連結服務](./media/private-link-tsg/private-link-service.png)

    c. 篩選並選取您想要診斷的私人連結服務。

    d. 檢查私人端點連接。
     - 請確定您要搜尋連線的私人端點已列出且已**核准**連接狀態。
     - 如果狀態為 [**暫**止]，請選取它並核准。

       ![私人端點連接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 藉由選取名稱，移至您所連接的私人端點。 請確定 [線上狀態] 顯示為 [**已核准**]。

       ![私人端點連接總覽](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 雙方都經過核准後，再次嘗試連線。

    e. 在 [**總覽**] 索引標籤和 [**屬性**] 索引標籤上的 [**資源識別碼** **] 上，**
     - 請確定**別名**和**資源識別碼**資訊符合您用來建立此服務私人端點的**別名**和**資源識別碼**。

       ![驗證別名資訊](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![驗證資源識別碼資訊](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 [**總覽**] 索引標籤上的**可見度**資訊。
     - 請確定您的訂用帳戶落在**可見度**範圍之下。

       ![驗證可見度資訊](./media/private-link-tsg/pls-overview-pane-visibility.png)

    如 查看 [**總覽**] 索引標籤上的**負載平衡器**資訊。
     - 您可以選取 [負載平衡器] 連結來移至負載平衡器。

       ![驗證負載平衡器資訊](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 請確定已根據您的預期設定負載平衡器設定。
       - 檢查**前端 IP**設定。
       - 審查**後端**集區。
       - 檢查**負載平衡規則**。

       ![驗證負載平衡器屬性](./media/private-link-tsg/pls-ilb-properties.png)

     - 請確定負載平衡器依先前的設定運作。
       - 選取任何子網中的 VM，而非可使用負載平衡器後端集區的子網。
       - 嘗試從先前的 VM 存取負載平衡器前端。
       - 如果連線依據負載平衡規則使其成為後端集區，則您的負載平衡器會運作。
       - 您也可以透過 Azure 監視器來檢查負載平衡器計量，以查看資料是否流經負載平衡器。

1. 使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)查看資料是否流動。

    a. 在私人連結服務資源上，選取 [**計量**]。
     - 選取 [**傳入位元組**] 或 [**位元組輸出**]。
     - 當您嘗試連接到私人連結服務時，請查看資料是否流動。 預期延遲大約10分鐘。

       ![確認私人連結服務計量](./media/private-link-tsg/pls-metrics.png)

1. 如果您的問題仍然無法解決，且連線問題仍然存在，請聯絡[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)小組。

## <a name="next-steps"></a>後續步驟

 * [建立私用連結服務（CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 私用端點疑難排解指南](troubleshoot-private-endpoint-connectivity.md)
