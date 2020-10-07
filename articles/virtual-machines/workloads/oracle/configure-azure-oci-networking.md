---
title: 將 Azure ExpressRoute 與 Oracle 雲端基礎結構連線 |Microsoft Docs
description: 將 Azure ExpressRoute 與 Oracle 雲端基礎結構 (OCI) FastConnect，以啟用跨雲端的 Oracle 應用程式解決方案
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: 5bb26a21317401ddbd0d9b8f8a9a501c78153842
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776573"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>設定 Azure 與 Oracle 雲端基礎結構之間的直接互連  

為了建立 [整合的多重雲端體驗](oracle-oci-overview.md)，Microsoft 和 Oracle 提供 Azure 與 Oracle 雲端基礎結構之間的直接互連， (透過 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 和 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)的 OCI) 。 透過 ExpressRoute 和 FastConnect 互連，客戶可以在這兩個雲端之間體驗低延遲、高輸送量、私人直接連線能力。

> [!IMPORTANT]
> 在 2020 年 5 月前使用 Azure/Oracle 雲端互連解決方案，Oracle 即會認證下列應用程式可在 Azure 中執行。
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Oracle Retail 應用程式
> * Oracle Hyperion Financial Management

下圖顯示互連的高階總覽：

![跨雲端網路連接](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>必要條件

* 若要建立 Azure 與 OCI 之間的連線，您必須擁有作用中的 Azure 訂用帳戶和使用中的 OCI 租使用者。

* 只有在 Azure ExpressRoute 對等互連位置位於與 OCI FastConnect 相同的對等位置中或相同的對等位置時，才可能有連線能力。 請參閱 [區域可用性](oracle-oci-overview.md#region-availability)。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>設定 ExpressRoute 與 FastConnect 之間的直接連線能力

1. 在您的 Azure 訂用帳戶下，于資源群組下建立標準 ExpressRoute 線路。 
    * 建立 ExpressRoute 時，選擇 **Oracle Cloud FastConnect** 作為服務提供者。 若要建立 ExpressRoute 線路，請參閱 [逐步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 線路提供細微的頻寬選項，而 FastConnect 支援1、2、5或 10 Gbps。 因此，建議您在 ExpressRoute 下選擇其中一個相符的頻寬選項。

    ![建立 ExpressRoute 線路](media/configure-azure-oci-networking/exr-create-new.png)
1. 記下您的 ExpressRoute **服務金鑰**。 您必須在設定 FastConnect 線路時提供金鑰。

    ![ExpressRoute 服務金鑰](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦布建 ExpressRoute 線路之後，您就會向您收取 ExpressRoute 費用 (即使未) 布**建****提供者狀態**。

1. 切割/30 的兩個私人 IP 位址空間，每個都不會與您的 Azure 虛擬網路或 OCI 虛擬雲端網路 IP 位址空間重迭。 我們會將第一個 IP 位址空間稱為主要位址空間，並將第二個 IP 位址空間稱為次要位址空間。 記下您在設定 FastConnect 電路時所需的位址。
1.  (DRG.4) 建立動態路由閘道。 您將在建立 FastConnect 電路時需要此程式。 如需詳細資訊，請參閱 [動態路由閘道](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 檔。
1. 在您的 Oracle 租使用者下建立 FastConnect 線路。 如需詳細資訊，請參閱 [Oracle 檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)集。
  
    * 在 [FastConnect 設定] 下，選取 [ **Microsoft Azure： ExpressRoute** 作為提供者]。
    * 選取您在上一個步驟中布建的動態路由閘道。
    * 選取要布建的頻寬。 為了達到最佳效能，頻寬必須符合建立 ExpressRoute 線路時所選取的頻寬。
    * 在 [ **提供者服務金鑰**] 中，貼上 ExpressRoute 服務金鑰。
    * 使用上一個步驟中劃分的第一個/30 私人 IP 位址空間，作為 **主要 BGP Ip 位址** 和 **次要 bgp ip** 位址的第二/30 私人 ip 位址空間。
        * 將 Oracle BGP IP 位址的第一個可用位址指派 (主要和次要) ，並將第二個位址指派給客戶 BGP IP 位址 (從 FastConnect 的觀點) 。 第一個可用的 IP 位址是/30 位址空間中的第二個 IP 位址 (第一個 IP 位址是由 Microsoft) 保留。
    * 按一下 [建立]。
1. 使用路由表，透過動態路由閘道，完成將 FastConnect 連結至 Oracle 租使用者下的虛擬雲端網路。
1. 流覽至 Azure，並確定 ExpressRoute 線路的 **提供者狀態** 已變更為 [已布 **建** ]，且已布建 **Azure 私人** 類型的對等互連。 這是下列步驟的先決條件。

    ![ExpressRoute 提供者狀態](media/configure-azure-oci-networking/exr-provider-status.png)
1. 按一下 **Azure 私人** 對等互連。 您會看到對等互連詳細資料已根據您在設定 FastConnect 電路時所輸入的資訊自動設定。

    ![私用對等設定](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>將虛擬網路連線至 ExpressRoute

1. 如果您尚未建立虛擬網路和虛擬網路閘道，請先建立虛擬網路。 如需詳細資訊，請參閱 [逐步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 藉由執行 [Terraform 腳本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) 或執行 PowerShell 命令來設定 [expressroute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，來設定虛擬網路閘道與 ExpressRoute 線路之間的連線。

完成網路設定之後，您可以在 Azure 入口網站中，按一下 [ **取得 ARP 記錄** ]，然後在 [ExpressRoute 私用對等互連] 下 **取得路由表** ，確認設定的有效性。

## <a name="automation"></a>自動化

Microsoft 已建立 Terraform 腳本來啟用網路互連的自動部署。 Terraform 腳本必須先向 Azure 進行驗證，才能執行，因為它們需要 Azure 訂用帳戶的適當許可權。 您可以使用 [Azure Active Directory 服務主體](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 或使用 Azure CLI 來執行驗證。 如需詳細資訊，請參閱 [Terraform 檔](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

您可以在此 [GitHub 存放庫](https://aka.ms/azureociinterconnecttf)中找到部署連線連線的 Terraform 腳本和相關檔。

## <a name="monitoring"></a>監視

在這兩個雲端上安裝代理程式，您可以利用 Azure [網路效能監控 (NPM) ](../../../expressroute/how-to-npm.md) 來監視端對端網路的效能。 NPM 可協助您立即找出網路問題，並協助消除這些問題。

## <a name="delete-the-interconnect-link"></a>刪除互連連結

若要刪除互連，必須依照指定的順序，遵循下列步驟。 若無法這樣做，將會導致「失敗狀態」 ExpressRoute 線路。

1. 刪除 ExpressRoute 連接。 按一下連線頁面上的 [ **刪除** ] 圖示，以刪除連接。 如需詳細資訊，請參閱 [ExpressRoute 檔](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)集。
1. 從 Oracle Cloud 主控台刪除 Oracle FastConnect。
1. 一旦刪除 Oracle FastConnect 線路之後，您就可以刪除 Azure ExpressRoute 線路。

此時，刪除和取消布建程式已完成。

## <a name="next-steps"></a>後續步驟

* 如需 OCI 與 Azure 之間跨雲端連線的詳細資訊，請參閱 [Oracle 檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)集。
* 使用 [Terraform 腳本](https://aka.ms/azureociinterconnecttf) ，透過 Azure 為目標 Oracle 應用程式部署基礎結構，並設定網路互連。 
