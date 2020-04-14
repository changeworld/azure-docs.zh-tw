---
title: 將 Azure 快速路由與 Oracle 雲端基礎架構連接 |微軟文件
description: 將 Azure 快速路由與 Oracle 雲端基礎架構 (OCI) 快速連線連線,實現跨雲端 Oracle 應用程式解決方案
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263177"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>設定 Azure 和 Oracle 雲端基礎結構之間的直接互連  

為了創造[集成的多雲體驗](oracle-oci-overview.md),微軟和甲骨文通過[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)在 Azure 和甲骨文雲端基礎設施(OCI)之間提供直接互連。 通過 ExpressRoute 和 FastConnect 互連,客戶可以體驗低延遲、高吞吐量、兩個雲之間的私人直接連接。

> [!IMPORTANT]
> 在 2020 年 5 月之前,Oracle 將在 Azure/Oracle 雲端互連解決方案中驗證這些應用程式在 Azure 中運行。
> * 電子商務套件
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * 甲骨文零售應用程式
> * 甲骨文海龍財務管理

下圖顯示了互連的高級概述:

![跨雲端網路連線](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisites

* 要在 Azure 和 OCI 之間建立連接,必須具有活動 Azure 訂閱和活動 OCI 租約。

* 僅當 Azure ExpressRoute 對等互連位置靠近或與 OCI FastConnect 位於同一對等位置時,才可能連接。 請參考[區域可用性](oracle-oci-overview.md#region-availability)。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>設定快速路由並快速連線之間的直接連線

1. 在資源群組下的 Azure 訂閱上創建標準 ExpressRoute 電路。 
    * 創建 ExpressRoute 時,請選擇**Oracle 雲端快速連線**作為服務提供者。 要建立 ExpressRoute 電路,請參閱分[步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 電路提供粒度頻寬選項,而FastConnect支援1、2、5或10 Gbps。 因此,建議在 ExpressRoute 下選擇這些匹配的頻寬選項之一。

    ![建立快速路由電路](media/configure-azure-oci-networking/exr-create-new.png)
1. 將快速路由**服務金鑰**。 在設定 FastConnect 電路時,您需要提供密鑰。

    ![快速路由服務金鑰](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦預配了 ExpressRoute 電路(即使未**預配****提供商狀態**),您也會收取快遞線路費用。

1. 雕刻出兩個私有 IP 位址空間 /30,每個空間不與 Azure 虛擬網路或 OCI 虛擬雲端網路 IP 位址空間重疊。 我們將第一個 IP 位址空間稱為主位址空間,第二個 IP 位址空間稱為輔助位址空間。 記下配置 FastConnect 電路時所需的位址。
1. 創建動態路由閘道 (DRG)。 創建 FastConnect 電路時,您需要這樣做。 有關詳細資訊,請參閱[動態路由閘道](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文文文文文檔。
1. 在 Oracle 租戶下創建 FastConnect 電路。 有關詳細資訊,請參閱 Oracle[文件](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * 在 FastConnect 配置下,選擇**Microsoft Azure:快速路由**作為提供程式。
    * 選擇上一步中預配的動態路由閘道。
    * 選擇要預配的頻寬。 為了獲得最佳性能,頻寬必須與創建 ExpressRoute 電路時選擇的頻寬相匹配。
    * 在**提供程式服務金鑰**中,貼上 ExpressRoute 服務密鑰。
    * 使用上一步中劃分的第一個 /30 專用 IP 位址空間,用於**主 BGP IP 位址**,使用輔助**BGP IP**位址的第二 /30 專用 IP 位址空間。
        * 將 Oracle BGP IP 位址(主和輔助)的兩個範圍的第一個可使用位址和第二個位址分配給客戶 BGP IP 位址(從快速連接的角度來看)。 第一個可使用的 IP 位址是 /30 位址空間中的第二個 IP 位址(第一個 IP 位址由 Microsoft 保留)。
    * 按一下頁面底部的 [新增]  。
1. 使用路由表,透過動態路由閘道完成將 FastConnect 連接到 Oracle 租戶下的虛擬雲端網路。
1. 導航到 Azure,並確保已預配 ExpressRoute 電路的**提供程式狀態**更改為**預配**,並且已預配**Azure 私有**類型的對等互連。 這是以下步驟的先決條件。

    ![快速路由提供者狀態](media/configure-azure-oci-networking/exr-provider-status.png)
1. 單擊**Azure 專用**對等互連。 您將看到已根據您在設置 FastConnect 電路時輸入的資訊自動配置對等互連詳細資訊。

    ![專用對等互連設定](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>將虛擬網路連線到快速路由

1. 創建虛擬網路和虛擬網路閘道(如果尚未創建)。 有關詳細資訊,請參閱分[步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 通過執行[Terraform 文稿](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或執行 PowerShell 命令來[配置 ExpressRoute 快速路徑](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath),設定虛擬網路閘道和 ExpressRoute 電路之間的連接。

完成網路配置後,可以通過單擊 Azure 門戶中的 ExpressRoute 專用對等邊欄選項卡下的 **「獲取 ARP 記錄**」和 **「獲取路由」** 表來驗證配置的有效性。

## <a name="automation"></a>自動化

Microsoft 已創建 Terraform 文稿,以實現網路互連的自動部署。 Terraform 文稿在執行之前需要使用 Azure 進行身份驗證,因為它們需要對 Azure 訂閱授予足夠的許可權。 可以使用[Azure 活動目錄服務主體](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI 執行身份驗證。 有關詳細資訊,請參閱[Terraform 文件](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

部署互連的 Terraform 文稿和相關文件可以在此[GitHub 儲存庫](https://aka.ms/azureociinterconnecttf)中找到。

## <a name="monitoring"></a>監視

在這兩個雲端安裝代理,可以利用 Azure[網路效能監視器 (NPM)](../../../expressroute/how-to-npm.md)來監視端到端網路的性能。 NPM 可説明您輕鬆識別網路問題,並説明消除這些問題。

## <a name="delete-the-interconnect-link"></a>刪除互連結

要刪除互連,必須按照給定的特定順序執行以下步驟。 否則將導致故障狀態「ExpressRoute 電路」。

1. 刪除快速路由連接。 按下連接頁面上的 **「刪除**」圖示,刪除連接。 有關詳細資訊,請參閱[ExpressRoute 文件](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. 從 Oracle 雲端控制台中刪除 Oracle 快速連接。
1. 刪除 Oracle 快速連接電路後,可以刪除 Azure ExpressRoute 電路。

此時,刪除和取消預配過程已完成。

## <a name="next-steps"></a>後續步驟

* 有關 OCI 與 Azure 之間的跨雲端連線的詳細資訊,請參閱[Oracle 文件](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用[Terraform 文稿](https://aka.ms/azureociinterconnecttf)透過 Azure 部署針對目標的 Oracle 應用程式的基礎結構,並配置網路互連。 
