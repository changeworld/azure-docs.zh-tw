---
title: 將 Azure 快速路由與 Oracle 雲基礎架構連接 |微軟文檔
description: 將 Azure 快速路由與 Oracle 雲基礎架構 （OCI） 快速連接連接，實現跨雲 Oracle 應用程式解決方案
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 0e2e16ccc04ff6df80597d646a00c40551e4cfd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302044"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>設置 Azure 和 Oracle 雲基礎結構之間的直接互連  

為了創建[集成的多雲體驗](oracle-oci-overview.md)（預覽），微軟和甲骨文通過[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)在 Azure 和 Oracle 雲基礎架構 （OCI） 之間提供直接互連。 通過 ExpressRoute 和 FastConnect 互連，客戶可以體驗低延遲、高輸送量、兩個雲之間的私人直接連接。

> [!IMPORTANT]
> Microsoft Azure 和 OCI 之間的連接處於預覽階段。 要在 Azure 和 OCI 之間建立低延遲連接，必須首先為此功能啟用 Azure 訂閱。 您必須通過填寫此簡短[調查表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)來註冊預覽版。 當訂用帳戶註冊完成之後，您會收到電子郵件。 在收到確認電子郵件後才能使用此功能。 您也可以聯繫 Microsoft 代表以啟用此預覽。 對預覽功能的訪問取決於可用性，並受 Microsoft 自行決定的限制。 調查的完成並不能保證訪問。 此預覽版在沒有服務等級協定的情況下提供，不應用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 有關詳細資訊，請參閱 Microsoft Azure 預覽[的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

下圖顯示了互連的高級概述：

![跨雲網路連接](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisites

* 要在 Azure 和 OCI 之間建立連接，必須具有活動 Azure 訂閱和活動 OCI 租約。

* 僅當 Azure ExpressRoute 對等互連位置靠近或與 OCI FastConnect 位於同一對等位置時，才可能連接。 請參閱[區域可用性](oracle-oci-overview.md#region-availability)。

* 必須為此預覽功能啟用 Azure 訂閱。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>配置快速路由和快速連接之間的直接連接

1. 在資源組下的 Azure 訂閱上創建標準 ExpressRoute 電路。 
    * 創建 ExpressRoute 時，請選擇**Oracle 雲快速連接**作為服務提供者。 要創建 ExpressRoute 電路，請參閱分[步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 電路提供細微性頻寬選項，而 FastConnect 支援 1、2、5 或 10 Gbps。 因此，建議在 ExpressRoute 下選擇這些匹配的頻寬選項之一。

    ![創建快速路由電路](media/configure-azure-oci-networking/exr-create-new.png)
1. 記下您的快速路由**服務金鑰**。 在配置 FastConnect 電路時，您需要提供金鑰。

    ![快速路由服務金鑰](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦預配了 ExpressRoute 電路（即使未**預配****供應商狀態**），您也會收取快遞線路費用。

1. 雕刻出兩個私有 IP 位址空間 /30，每個空間不與 Azure 虛擬網路或 OCI 虛擬雲網路 IP 位址空間重疊。 我們將第一個 IP 位址空間稱為主位址空間，第二個 IP 位址空間稱為輔助位址空間。 記下配置 FastConnect 電路時所需的位址。
1. 創建動態路由閘道 （DRG）。 創建 FastConnect 電路時，您需要這樣做。 有關詳細資訊，請參閱[動態路由閘道](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文檔。
1. 在 Oracle 租戶下創建 FastConnect 電路。 有關詳細資訊，請參閱 Oracle[文檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * 在 FastConnect 配置下，選擇**Microsoft Azure：快速路由**作為提供程式。
    * 選擇上一步中預配的動態路由閘道。
    * 選擇要預配的頻寬。 為了獲得最佳性能，頻寬必須與創建 ExpressRoute 電路時選擇的頻寬相匹配。
    * 在**提供程式服務金鑰**中，粘貼 ExpressRoute 服務金鑰。
    * 使用上一步中劃分的第一個 /30 私人 IP 位址空間，用於**主 BGP IP 位址**，使用輔助**BGP IP**位址的第二 /30 私人 IP 位址空間。
        * 將 Oracle BGP IP 位址（主和輔助）的兩個範圍的第一個可使用位址和第二個位址分配給客戶 BGP IP 位址（從快速連接的角度來看）。 第一個可使用的 IP 位址是 /30 位址空間中的第二個 IP 位址（第一個 IP 位址由 Microsoft 保留）。
    * 按一下 **[建立]**。
1. 使用路由表，通過動態路由閘道完成將 FastConnect 連接到 Oracle 租戶下的虛擬雲網路。
1. 導航到 Azure，並確保已預配 ExpressRoute 電路的**提供程式狀態**更改為**預配**，並且已預配**Azure 私有**類型的對等互連。 這是以下步驟的先決條件。

    ![快速路由提供程式狀態](media/configure-azure-oci-networking/exr-provider-status.png)
1. 按一下**Azure 專用**對等互連。 您將看到已根據您在設置 FastConnect 電路時輸入的資訊自動設定對等互連詳細資訊。

    ![專用對等互連設置](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>將虛擬網路連接到快速路由

1. 創建虛擬網路和虛擬網路閘道（如果尚未創建）。 有關詳細資訊，請參閱分[步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 通過執行[Terraform 腳本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或執行 PowerShell 命令來[配置 ExpressRoute 快速路徑](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，設置虛擬網路閘道和 ExpressRoute 電路之間的連接。

完成網路設定後，可以通過按一下 Azure 門戶中的 ExpressRoute 專用對等邊欄選項卡下的 **"獲取 ARP 記錄**"和 **"獲取路由"** 表來驗證配置的有效性。

## <a name="automation"></a>自動化

Microsoft 已創建 Terraform 腳本，以實現網路互連的自動部署。 Terraform 腳本在執行之前需要使用 Azure 進行身份驗證，因為它們需要對 Azure 訂閱授予足夠的許可權。 可以使用[Azure 活動目錄服務主體](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI 執行身份驗證。 有關詳細資訊，請參閱[Terraform 文檔](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

部署互連的 Terraform 腳本和相關文檔可以在此[GitHub 存儲庫](https://aka.ms/azureociinterconnecttf)中找到。

## <a name="monitoring"></a>監視

在這兩個雲上安裝代理，可以利用 Azure[網路效能監視器 （NPM）](../../../expressroute/how-to-npm.md)來監視端到端網路的性能。 NPM 可説明您輕鬆識別網路問題，並説明消除這些問題。

## <a name="delete-the-interconnect-link"></a>刪除互連連結

要刪除互連，必須按照給定的特定循序執行以下步驟。 否則將導致"故障狀態"ExpressRoute 電路。

1. 刪除快速路由連接。 按一下連接頁面上的 **"刪除**"圖示，刪除連接。 有關詳細資訊，請參閱[ExpressRoute 文檔](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. 從 Oracle 雲主控台中刪除 Oracle 快速連接。
1. 刪除 Oracle 快速連接電路後，可以刪除 Azure ExpressRoute 電路。

此時，刪除和取消預配過程已完成。

## <a name="next-steps"></a>後續步驟

* 有關 OCI 和 Azure 之間的跨雲連接的詳細資訊，請參閱[Oracle 文檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用[Terraform 腳本](https://aka.ms/azureociinterconnecttf)通過 Azure 部署針對目標的 Oracle 應用程式的基礎結構，並配置網路互連。 
