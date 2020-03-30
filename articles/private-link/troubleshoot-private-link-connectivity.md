---
title: 針對 Azure Private Link 連線能力的問題進行疑難排解
description: 診斷專用鏈路連接的分步指南
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539462"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>針對 Azure Private Link 連線能力的問題進行疑難排解

本文提供分步指南，用於驗證和診斷 Azure 專用連結設置的連接。

使用 Azure 專用連結，可以訪問 Azure 平臺作為服務 （PaaS） 服務，例如 Azure 存儲、Azure 宇宙資料庫和 Azure SQL 資料庫，以及通過虛擬網路中的專用終結點訪問 Azure 託管的客戶或合作夥伴服務。 虛擬網路和服務之間的流量通過 Microsoft 骨幹網遍歷，從而消除了公共 Internet 的暴露。 您還可以在虛擬網路中創建自己的專用連結服務，並私下將其交付給客戶。

您可以啟用在專用連結訪問的 Azure 負載等化器標準層後面運行的服務。 服務的消費者可以在虛擬網路內創建專用終結點，並將其映射到此服務以私下訪問它。

以下是專用鏈路可用的連接方案：

- 來自同一區域的虛擬網路
- 區域對等虛擬網路
- 全球對等虛擬網路
- 通過 VPN 或 Azure 快速路由電路在本地的客戶

## <a name="deployment-troubleshooting"></a>部署故障排除

查看[有關禁用專用連結服務上的網路原則](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)的資訊，以排除無法從您為專用連結服務選擇的子網中選擇的源 IP 位址的情況。

確保禁用為選擇要從的源 IP 位址的子網的**專用連結服務網路原則**設置。

## <a name="diagnose-connectivity-problems"></a>診斷連接問題

如果專用鏈路設置遇到連接問題，請查看這些步驟，以確保所有常規配置都按預期進行。

1. 通過流覽資源查看專用連結配置。

    a. 轉到**私人連結中心**。

      ![專用連結中心](./media/private-link-tsg/private-link-center.png)

    b. 在左側窗格中，選擇 **"專用連結服務**"。

      ![專用連結服務](./media/private-link-tsg/private-link-service.png)

    c. 篩選並選擇要診斷的專用連結服務。

    d. 查看專用終結點連接。
     - 確保從中尋求連接的專用終結點已列出並帶有 **"已批准連接"** 狀態。
     - 如果狀態處於**掛起狀態**，請選擇它並批准它。

       ![專用終結點連接](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 通過選擇名稱轉到要連接的專用終結點。 確保連接狀態顯示為 **"已批准**"。

       ![專用端點連接概述](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 兩側通過後，請重試連接。

    e. 在 **"屬性"** 選項卡上的"概述"選項卡和資源**Properties** **ID**上查看**別名**。
     - 確保**別名****和資源識別碼**資訊與用於創建此服務的專用終結點的**別名****和資源識別碼**匹配。

       ![驗證別名資訊](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![驗證資源識別碼 資訊](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. 查看 **"概述"** 選項卡上的**可見度**資訊。
     - 確保您的訂閱屬於**可見度**範圍。

       ![驗證可見度資訊](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. 查看 **"概覽"** 選項卡上的**負載等化器**資訊。
     - 您可以通過選擇負載等化器連結轉到負載等化器。

       ![驗證負載等化器資訊](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 確保負載等化器設置已根據您的預期進行配置。
       - 查看**前端 IP 配置**。
       - 查看**後端池**。
       - 查看**負載平衡規則**。

       ![驗證負載等化器屬性](./media/private-link-tsg/pls-ilb-properties.png)

     - 確保負載等化器按以前的設置工作。
       - 在負載等化器後端池可用的子網以外的任何子網中選擇 VM。
       - 嘗試從上一個 VM 訪問負載等化器前端。
       - 如果根據負載平衡規則連接到後端池，則負載等化器可正常工作。
       - 還可以通過 Azure 監視器查看負載等化器指標，以查看資料是否通過負載等化器流動。

1. 使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)查看資料是否流動。

    a. 在專用連結服務資源上，選擇**指標**。
     - 選擇**位元組內**或**位元組出**。
     - 查看嘗試連接到專用鏈路服務時資料是否流動。 預計延遲約 10 分鐘。

       ![驗證專用連結服務指標](./media/private-link-tsg/pls-metrics.png)

1. 如果問題仍未解決且連接問題仍然存在，請與[Azure 支援](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)團隊聯繫。

## <a name="next-steps"></a>後續步驟

 * [創建專用連結服務 （CLI）](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 專用終結點故障排除指南](troubleshoot-private-endpoint-connectivity.md)
