---
title: 針對 Azure 私人連結連線問題進行疑難排解
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
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191045"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>針對私人連結服務連線問題進行疑難排解

本指南提供逐步指引，以驗證及診斷私人連結服務設定的連線能力。 

Azure 私人連結可讓您透過虛擬網路中的私人端點，存取 Azure PaaS 服務（例如 Azure 儲存體、Azure Cosmos DB 和 SQL Database）和 Azure 託管的客戶/合作夥伴服務。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您也可以在虛擬網路（VNet）中建立您自己的私人連結服務，並將其私下提供給您的客戶。 

您可以讓您的服務在 Azure Standard Load Balancer 後方執行，以進行私人連結存取。 您的服務取用者可以在其虛擬網路內建立私人端點，並將其對應到此服務以私下存取。

以下是私人連結服務提供的連線案例
- 來自相同區域的虛擬網路 
- 區域內對等互連虛擬網路
- 全域對等互連的虛擬網路
- 透過 VPN 或 Express Route 線路的客戶內部部署

## <a name="deployment-troubleshooting"></a>部署疑難排解

請參閱在[私人連結服務上停用網路原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的相關資訊，以取得疑難排解案例，讓您無法從您選擇的子網中選取私人連結服務的來源 IP 位址。

請確定您選取來源 IP 位址的子網已停用 [設定] **privateLinkServiceNetworkPolicies** 。

## <a name="diagnosing-connectivity-problems"></a>診斷連線問題

如果您的私人連結設定發生連線問題，請移至下列步驟，以確定所有一般設定都如預期般執行。

1. 流覽資源以檢查私人連結服務設定 

    a）前往**私人連結中心**

      ![私人連結中心](./media/private-link-tsg/private-link-center.png)

    b）從左側流覽窗格中選取 [**私人連結服務**]

      ![私人連結服務](./media/private-link-tsg/private-link-service.png)

    c）篩選並選取您想要診斷的私人連結服務

    d）檢查私人端點連接
     - 請確定您要搜尋連線的私人端點已列出並**已核准**線上狀態。 
     - 如果**擱置**中，請選取它並核准。 

       ![私人端點連接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 按一下 [名稱]，流覽至您要連接的私人端點。 請確定 [線上狀態] 顯示為 [**已核准**]。

       ![私人端點連接總覽](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 雙方都經過核准後，再次嘗試連線。

    e）從 [屬性] 索引標籤中的 [總覽] 索引標籤和**資源識別碼**審查**別名** 
     - 請確定**別名/資源識別碼**與您用來建立此服務私人端點的**別名/資源識別碼**相符。 

       ![驗證別名](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![驗證資源識別碼](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f）查看可見度（總覽區段）資訊
     - 請確定您的訂用帳戶落在**可見度**範圍內

       ![確認可見度](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g）審查 Load Balancer （總覽）資訊
     - 您可以按一下 [負載平衡器] 連結，以流覽至 [負載平衡器]

       ![確認 Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 請確定已根據您的預期設定 Load Balancer 設定
       - 審查前端 IP 設定
       - 審查後端集區
       - 檢查負載平衡規則

       ![確認 Load Balancer 屬性](./media/private-link-tsg/pls-ilb-properties.png)

     - 請確定 Load Balancer 依上述設定運作
       - 在可用 Load Balancer 後端集區的子網以外的任何子網中選取 VM
       - 嘗試從上述 VM 存取負載平衡器前端
       - 如果連線是根據負載平衡規則來連線到後端集區，則您的負載平衡器可運作
       - 您也可以透過 Azure 監視器查看 Load Balancer 計量，以查看資料是否流經負載平衡器

2. 使用[**Azure 監視器**](https://docs.microsoft.com/azure/azure-monitor/overview)來審查資料的流動

    a）在私人連結服務資源上，選取 [**計量**]
     - 選取**位元組-傳入**或**位元組-輸出**
     - 嘗試連接到私人連結服務時，審核資料正在流動。 預期大約10分鐘的延遲。

       ![確認私人連結服務計量](./media/private-link-tsg/pls-metrics.png)

3. 如果您的問題仍然無法解決，且連線問題仍然存在，請聯絡[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)小組。 

## <a name="next-steps"></a>後續步驟

 * [建立私用連結服務（CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [私人端點疑難排解指南](troubleshoot-private-endpoint-connectivity.md)
