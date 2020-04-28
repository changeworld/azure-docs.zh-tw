---
title: 在 Azure 專用主機上 SQL Server VM
description: 深入瞭解在 Azure 專用主機上執行 SQL Server VM 的詳細資料。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834358"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>在 Azure 專用主機上 SQL Server VM 

本文詳細說明搭配使用 SQL Server VM 與[Azure 專用主機](/azure/virtual-machines/windows/dedicated-hosts)的細節。 如需 azure 專用主機的其他資訊，請參閱[Azure 專用主機簡介](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)中的 blog 文章。 

## <a name="overview"></a>概觀
「 [Azure 專用主機](/azure/virtual-machines/windows/dedicated-hosts)」是一種服務，可提供實體伺服器來裝載一或多個 Azure 訂用帳戶專用的虛擬機器。 專用主機是 Microsoft 資料中心內所使用的相同實體伺服器，以資源的形式提供。 您可以在區域、可用性區域和容錯網域中布建專用主機。 然後，您可以將 Vm 直接放入已布建的主機中，不論何種設定最符合您的需求。

## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。
- 支援下列 VM 系列： DSv3 和 ESv3。 

## <a name="licensing"></a>授權

將您的 SQL Server VM 新增至 Azure 專用主機時，您可以選擇兩個不同的授權選項。 

  - **SQL VM 授權**：這是現有的授權選項，您可以在其中個別支付每個 SQL Server VM 授權。 
  - **專用主機授權**：適用于 Azure 專用主機的新授權模型，其中 SQL Server 授權會在主機層級配套和付費。 


使用現有 SQL Server 授權的主機層級選項： 
  - SQL Server Enterprise Edition Azure Hybrid Benefit
    - 適用于具有 SA 或訂用帳戶的客戶。
    - 授權所有可用的實體核心並享有不受限制的虛擬化（最多可達主機支援的最大個 vcpu）。
        - 如需將 Azure Hybrid Benefit 套用至 Azure 專用主機的詳細資訊，請參閱[AZURE HYBRID BENEFIT 常見問題](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - 10月1日前取得的 SQL Server 授權
      - SQL Server Enterprise edition 同時具有主機層級和 VM 授權選項。 
      - SQL Server Standard edition 僅提供 VM 授權選項。 
          - 如需詳細資訊，請參閱[Microsoft 的產品條款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未選取任何 SQL Server 專用主機層級選項，則可以在個別 Vm 層級選取 SQL Server AHB，就像使用多租使用者 Vm 一樣。



## <a name="provisioning"></a>佈建  
將 SQL Server VM 布建至專用主機與其他任何 Azure 虛擬機器不同。 您可以使用[Azure PowerShell](../dedicated-hosts-powershell.md)、 [Azure 入口網站](../dedicated-hosts-portal.md)和[Azure CLI](../../linux/dedicated-hosts-cli.md)來執行此動作。

將現有 SQL Server VM 新增至專用主機的程式需要停機時間，但不會影響資料，而且不會遺失資料。 儘管如此，所有的資料庫（包括系統資料庫）都應該在移動之前備份。

## <a name="virtualization"></a>虛擬化 

專用主機的其中一個優點是不受限制的虛擬化。 例如，您可以擁有64虛擬核心的授權，但是您可以設定讓主機具有128虛擬核心，因此您會得到兩倍的虛擬核心，但是只需支付一半的 SQL Server 授權。 

因為這是您的主機，所以您有資格使用1:2 比來設定虛擬化。 

## <a name="faq"></a>常見問題集

**問： Azure Hybrid Benefit 如何在 Azure 專用主機上適用于 Windows Server/SQL Server 授權？**

答：客戶可以使用其現有 Windows Server 的值，並 SQL Server 具有軟體保證的授權，或合格的訂用帳戶授權，使用 Azure Hybrid Benefit 在 Azure 專用主機上支付較低的費率。 Windows Server Datacenter 和 SQL Server Enterprise Edition 客戶會在授權整個主機並使用 Azure Hybrid Benefit 時，取得無限制的虛擬化（根據基礎伺服器的實體容量，在主機上盡可能部署多部 Windows Server 虛擬機器）。  Azure 專用主機中的所有 Windows Server 和 SQL Server 工作負載也適用于 Windows Server 和 SQL Server 2008/R2 的擴充安全性更新，並不需要額外付費。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


