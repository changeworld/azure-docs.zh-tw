---
title: Azure 專用主機上的 SQL 伺服器 VM
description: 瞭解在 Azure 專用主機上運行 SQL Server VM 的詳細資訊。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834358"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure 專用主機上的 SQL 伺服器 VM 

本文詳細介紹了將 SQL Server VM 與 Azure[專用主機](/azure/virtual-machines/windows/dedicated-hosts)一起使用的細節。 有關 Azure 專用主機的其他資訊，請參閱[博客文章介紹 Azure 專用主機](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)。 

## <a name="overview"></a>總覽
[Azure 專用主機](/azure/virtual-machines/windows/dedicated-hosts)是一種服務，它提供物理伺服器（能夠承載一個或多個虛擬機器）專用於一個 Azure 訂閱。 專用主機是 Microsoft 資料中心中使用的物理伺服器，作為資源提供。 您可以在區域、可用性區域和容錯域中預配專用主機。 然後，您可以將 VM 直接放入預配主機中，無論配置最適合您的需要。

## <a name="limitations"></a>限制

- 專用主機當前不支援虛擬機器縮放集。
- 支援以下 VM 系列：DSv3 和 ESv3。 

## <a name="licensing"></a>授權

將 SQL Server VM 添加到 Azure 專用主機時，可以選擇兩種不同的許可選項。 

  - **SQL VM 許可**：這是現有的許可選項，您可以單獨為每個 SQL Server VM 許可證付費。 
  - **專用主機許可**：適用于 Azure 專用主機的新許可模型，其中 SQL Server 許可證在主機分級捆綁並支付。 


用於使用現有 SQL Server 許可證的主機級選項： 
  - SQL 伺服器企業版 Azure 混合優勢
    - 可供具有 SA 或訂閱的客戶使用。
    - 許可所有可用的物理內核，並享受無限制的虛擬化（最多由主機支援的最大 vCPU）。
        - 有關將 Azure 混合權益應用於 Azure 專用主機的詳細資訊，請參閱[Azure 混合權益常見問題解答](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - 10 月 1 日之前獲取的 SQL 伺服器許可證
      - SQL Server 企業版同時具有主機級和按 VM 許可證選項。 
      - SQL 伺服器標準版只有可用的 VM 許可證選項。 
          - 有關詳細資訊，請參閱[微軟的產品條款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未選擇 SQL Server 專用主機級選項，則 SQL Server AHB 可以在單個 VM 級別選擇，就像使用多租戶 VM 一樣。



## <a name="provisioning"></a>佈建  
將 SQL Server VM 預配到專用主機與任何其他 Azure 虛擬機器沒有什麼不同。 可以使用[Azure PowerShell](../dedicated-hosts-powershell.md) [、Azure 門戶](../dedicated-hosts-portal.md)和 Azure [CLI](../../linux/dedicated-hosts-cli.md)執行此操作。

將現有 SQL Server VM 添加到專用主機的過程需要停機，但不會影響資料，並且不會遺失資料。 儘管如此，所有資料庫（包括系統資料庫）都應在移動之前進行備份。

## <a name="virtualization"></a>虛擬化 

專用主機的優點之一是無限虛擬化。 例如，您可以擁有 64 個 vCore 的許可證，但您可以將主機配置為具有 128 個 vCore，因此您可以獲得 vCore 的兩倍，但只需支付一半的 SQL Server 許可證。 

由於它是您的主機，因此您有資格以 1：2 的比例設置虛擬化。 

## <a name="faq"></a>常見問題集

**問：Azure 混合權益如何適用于 Azure 專用主機上的 Windows 伺服器/SQL 伺服器許可證？**

答：客戶可以使用其現有 Windows 伺服器和 SQL Server 許可證的值以及軟體保證或符合條件的訂閱許可證，使用 Azure 混合權益在 Azure 專用主機上支付較低的費率。 Windows 伺服器資料中心和 SQL Server 企業版客戶在許可整個主機時，可獲得無限制的虛擬化（在主機上部署盡可能多的 Windows Server 虛擬機器，但需視基礎伺服器的物理容量而定）並使用 Azure 混合權益。  Azure 專用主機中的所有 Windows 伺服器和 SQL 伺服器工作負荷也有資格免費享受 Windows 伺服器和 SQL Server 2008/R2 的擴展安全更新。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


