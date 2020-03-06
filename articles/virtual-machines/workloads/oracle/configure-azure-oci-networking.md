---
title: 將 Azure ExpressRoute 與 Oracle 雲端基礎結構連線 |Microsoft Docs
description: 將 Azure ExpressRoute 與 Oracle 雲端基礎結構（OCI） FastConnect 連線，以啟用跨雲端 Oracle 應用程式解決方案
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302044"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>設定 Azure 與 Oracle 雲端基礎結構之間的直接互連  

為了建立[整合式多雲端體驗](oracle-oci-overview.md)（預覽），Microsoft 和 Oracle 透過[ExpressRoute](../../../expressroute/expressroute-introduction.md)和[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)提供 Azure 與 oracle 雲端基礎結構（OCI）之間的直接互連。 透過 ExpressRoute 與 FastConnect 互連，客戶可以在這兩個雲端之間體驗低延遲、高輸送量、私人直接連線能力。

> [!IMPORTANT]
> Microsoft Azure 和 OCI 之間的連線是在預覽階段。 若要建立 Azure 與 OCI 之間的低延遲連線，您的 Azure 訂用帳戶必須先啟用這項功能。 您必須完成這[份簡短問卷](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)，才能在預覽版中註冊。 當訂用帳戶註冊完成之後，您會收到電子郵件。 在收到確認電子郵件後才能使用此功能。 您也可以聯繫 Microsoft 代表，以供此預覽版啟用。 預覽功能的存取權受限於 Microsoft 自行決定的可用性和限制。 填寫問卷並不保證能夠存取。 此預覽版是在沒有服務等級協定的情況下提供，不應用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 Microsoft Azure 預覽的[補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

下圖顯示互連的高階總覽：

![跨雲端網路連線](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisites

* 若要建立 Azure 與 OCI 之間的連線，您必須擁有有效的 Azure 訂用帳戶和作用中的 OCI 租用。

* 只有在 Azure ExpressRoute 對等互連位置與 OCI FastConnect 位於相同的對等互連位置時，才可以進行連線。 請參閱[區域可用性](oracle-oci-overview.md#region-availability)。

* 您的 Azure 訂用帳戶必須啟用此預覽功能。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>設定 ExpressRoute 與 FastConnect 之間的直接連線

1. 在資源群組下的 Azure 訂用帳戶上建立標準 ExpressRoute 線路。 
    * 建立 ExpressRoute 時，請選擇 [ **Oracle Cloud FastConnect** ] 作為服務提供者。 若要建立 ExpressRoute 線路，請參閱[逐步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 線路提供更細微的頻寬選項，而 FastConnect 支援1、2、5或 10 Gbps。 因此，建議您在 ExpressRoute 底下選擇其中一個符合的頻寬選項。

    ![建立 ExpressRoute 線路](media/configure-azure-oci-networking/exr-create-new.png)
1. 記下您的 ExpressRoute**服務金鑰**。 設定 FastConnect 線路時，您必須提供金鑰。

    ![ExpressRoute 服務金鑰](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦布建 ExpressRoute 線路（即使**未布建** **提供者狀態**），您才需要支付 expressroute 費用。

1. 切割每個/30 的私人 IP 位址空間，而不會與您的 Azure 虛擬網路或 OCI 虛擬雲端網路 IP 位址空間重迭。 我們會將第一個 IP 位址空間稱為「主要位址空間」，並將第二個 IP 位址空間稱為「次要位址空間」。 記下位址，您在設定 FastConnect 線路時需要用到。
1. 建立動態路由閘道（DRG.4）。 建立 FastConnect 線路時，您將需要此程式。 如需詳細資訊，請參閱[動態路由閘道](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)檔。
1. 在您的 Oracle 租使用者底下建立 FastConnect 電路。 如需詳細資訊，請參閱[Oracle 檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)集。
  
    * 在 [FastConnect 設定] 下，選取 [ **Microsoft Azure： ExpressRoute** ] 做為提供者。
    * 選取您在上一個步驟中布建的動態路由閘道。
    * 選取要布建的頻寬。 為了達到最佳效能，頻寬必須符合建立 ExpressRoute 線路時選取的頻寬。
    * 在 [**提供者服務金鑰**] 中，貼上 ExpressRoute 服務金鑰。
    * 在上一個步驟中使用第一個/30 私人 IP 位址空間劃分，以取得**主要 BGP Ip 位址**和**次要 bgp ip**位址的第二個/30 私人 ip 位址空間。
        * 將 Oracle BGP IP 位址（主要和次要）的第一個可用位址和第二個位址指派給客戶 BGP IP 位址（從 FastConnect 的觀點來看）。 第一個可用的 IP 位址是/30 位址空間中的第二個 IP 位址（Microsoft 會保留第一個 IP 位址）。
    * 按一下 **[建立]** 。
1. 使用路由表，完成透過動態路由閘道，將 FastConnect 連結至 Oracle 租使用者下的虛擬雲端網路。
1. 流覽至 Azure，並確定 ExpressRoute 線路的**提供者狀態**已變更為 [已布**建**]，而且已布建 [ **Azure 私**用] 類型的對等互連。 這是下列步驟的必要條件。

    ![ExpressRoute 提供者狀態](media/configure-azure-oci-networking/exr-provider-status.png)
1. 按一下 [ **Azure 私**用對等]。 您會看到已根據您在設定 FastConnect 線路時所輸入的資訊，自動設定對等互連詳細資料。

    ![私用對等設定](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>將虛擬網路連線到 ExpressRoute

1. 建立虛擬網路和虛擬網路閘道（如果尚未這麼做）。 如需詳細資訊，請參閱[逐步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 藉由執行[Terraform 腳本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或執行 PowerShell 命令來設定[expressroute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，來設定虛擬網路閘道與 ExpressRoute 線路之間的連線。

完成網路設定之後，您可以按一下 [**取得 ARP 記錄**]，然後在 [ExpressRoute 私人對等互連] 分頁的 [Azure 入口網站]**底下，確認**您的設定是否有效。

## <a name="automation"></a>自動化

Microsoft 已建立 Terraform 腳本，以啟用網路互連的自動化部署。 Terraform 腳本必須在執行之前向 Azure 進行驗證，因為它們需要 Azure 訂用帳戶的適當許可權。 您可以使用[Azure Active Directory 服務主體](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或使用 Azure CLI 來執行驗證。 如需詳細資訊，請參閱[Terraform 檔](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

您可以在此[GitHub 存放庫](https://aka.ms/azureociinterconnecttf)中找到 Terraform 腳本和用來部署連線的相關檔。

## <a name="monitoring"></a>監視

在這兩個雲端上安裝代理程式，您可以利用 Azure[網路效能監控（NPM）](../../../expressroute/how-to-npm.md)來監視端對端網路的效能。 NPM 可協助您立即找出網路問題，並協助消除它們。

## <a name="delete-the-interconnect-link"></a>刪除互連連結

若要刪除互連，必須依照指定的順序，遵循下列步驟。 若未這麼做，將會導致「失敗狀態」 ExpressRoute 線路。

1. 刪除 ExpressRoute 連接。 按一下連接頁面上的 [**刪除**] 圖示，以刪除連接。 如需詳細資訊，請參閱[ExpressRoute 檔](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)。
1. 從 Oracle 雲端主控台刪除 Oracle FastConnect。
1. 一旦刪除 Oracle FastConnect 線路之後，您就可以刪除 Azure ExpressRoute 線路。

此時，刪除和解除布建程式已完成。

## <a name="next-steps"></a>後續步驟

* 如需 OCI 與 Azure 之間跨雲端連線的詳細資訊，請參閱[Oracle 檔](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)集。
* 使用[Terraform 腳本](https://aka.ms/azureociinterconnecttf)，透過 Azure 部署目標 Oracle 應用程式的基礎結構，並設定網路互連。 
