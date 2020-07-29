---
title: 在 Azure 專用主機上執行 SQL Server VM
description: 了解如何在 Azure 專用主機上執行 SQL Server VM。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 40c851e5ff5fc83ccf6b6d67e319bb97bd860bd5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669098"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>在 Azure 專用主機上執行 SQL Server VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

此文章詳細說明搭配 [Azure 專用主機](/azure/virtual-machines/windows/dedicated-hosts)使用 SQL Server 虛擬機器 (VM) 的細節。 如需有關 Azure 專用主機的其他相關資訊，請參閱部落格文章 [Azure 專用主機簡介](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/) \(英文\)。 

## <a name="overview"></a>概觀
[Azure 專用主機](/azure/virtual-machines/windows/dedicated-hosts)是一個服務，可讓實體伺服器裝載一或多個虛擬機器 - 專用於一個 Azure 訂用帳戶。 專用主機是與 Microsoft 的資料中心內所使用的伺服器相同的實體伺服器，以資源的形式提供。 您可以在區域、可用性區域與容錯網域中佈建專用主機。 然後，您可以依照最能滿足您需要的設定，將 VM 直接放入已佈建的主機中。

## <a name="limitations"></a>限制

- 專用主機目前不支援虛擬機器擴展集。
- 支援下列 VM 系列：DSv3 與 ESv3。 

## <a name="licensing"></a>授權

當您將 SQL Server VM 放在 Azure 專用主機中時，可以在兩個不同的授權選項之間選擇。 

  - **SQL VM 授權**：這是現有的授權選項，您可以個別支付每個 SQL Server VM 授權費用。 
  - **專用主機授權**：這是適用於 Azure 專用主機的新授權模型，此模型是在主機層級搭售 SQL Server 授權並支付費用。 


使用現有 SQL Server 授權的主機層級選項： 
  - SQL Server Enterprise Edition Azure Hybrid Benefit (AHB)
    - 適用於擁有軟體保證或訂用帳戶的客戶。
    - 授權所有可用的實體核心並享有數目不受限制的虛擬化 (最多可達主機支援的 vCPU 數上限)。
        - 如需將 AHB 套用到 Azure 專用主機的詳細資訊，請參閱 [Azure Hybrid Benefit 常見問題集](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - 在 10 月 1 日之前取得的 SQL Server 授權
      - SQL Server Enterprise Edition 同時提供主機層級與個別 VM 授權選項。 
      - SQL Server Standard Edition 只提供個別 VM 授權選項。 
          - 如需詳細資訊，請參閱 [Microsoft 產品條款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未選取任何 SQL Server 專用主機層級選項，可以選取個別 VM 層級的 SQL Server AHB，就像使用多租用戶 VM 時一樣。



## <a name="provisioning"></a>佈建  
將 SQL Server VM 佈建到專用主機的方式與佈建其他任何 Azure 虛擬機器的方式是一樣的。 您可以使用 [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md)、[Azure 入口網站](../../../virtual-machines/windows/dedicated-hosts-portal.md)與 [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md) 來佈建。

將現有 SQL Server VM 新增至專用主機的程序需要停機，但將不會影響資料或造成資料遺失。 儘管如此，仍應該在移動之前備份所有資料庫，包括系統資料庫。

## <a name="virtualization"></a>虛擬化 

專用主機的其中一個優點是數目不受限制的虛擬化。 例如，您可以擁有 64 個虛擬核心的授權，但是您可以將主機設定為具有 128 個虛擬核心，因此您會獲得兩倍的虛擬核心，但只需支付一半虛擬核心的 SQL Server 授權費用。 

因為這是您的主機，所以您有權使用 1:2 比例來設定虛擬化。 

## <a name="faq"></a>常見問題集

**問：Azure Hybrid Benefit 如何用於 Azure 專用主機上的 Windows Server/SQL Server 授權？**

A：客戶可以運用其現有具備軟體保證的 Windows Server 與 SQL Server 授權的價值，或合格的訂用帳戶授權，使用 Azure Hybrid Benefit 以較低的費率付費使用 Azure 專用主機。 Windows Server Datacenter 與 SQL Server Enterprise Edition 客戶可在授權整個主機並使用 Azure Hybrid Benefit 時，取得數目不受限制的虛擬化 (根據底層伺服器的實體容量，在主機上盡可能部署最多的 Windows Server 虛擬機器)。  Azure 專用主機中的所有 Windows Server 與 SQL Server 工作負載也能享有 Windows Server 與 SQL Server 2008/R2 的延伸安全性更新，不需要額外付費。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定價指導方針](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](doc-changes-updates-release-notes.md)


