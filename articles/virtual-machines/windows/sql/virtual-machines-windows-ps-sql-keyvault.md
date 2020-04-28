---
title: 在 Azure (Resource Manager) 中的 Windows VM 上將 Key Vault 與 SQL Server 整合 | Microsoft Docs
description: 了解如何自動化 SQL Server 加密的組態，以用於 Azure 金鑰保存庫。 本主題說明如何搭配「資源管理員」建立之 SQL Server 虛擬機器使用 Azure 金鑰保存庫整合。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70102139"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>在 Azure 虛擬機器上設定 SQL Server 的 Azure Key Vault 整合 (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [傳統](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和[備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 這些形式的加密需要您管理和儲存用來加密的密碼編譯金鑰。 Azure 金鑰保存庫 (AKV) 服務是設計來改善這些金鑰在安全且高度可用位置的安全性和管理。 [SQL Server 連接器](https://www.microsoft.com/download/details.aspx?id=45344)可讓 SQL Server 從 Azure Key Vault 使用這些金鑰。

如果您使用內部部署機器執行 SQL Server，[您可以遵循下列步驟，從內部部署 SQL Server 機器存取 Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx)。 但是對於 Azure VM 中的 SQL server，您可以使用 *Azure 金鑰保存庫整合* 功能來節省時間。

啟用這項功能時，它會自動安裝 SQL Server 連接器、設定 EKM 提供者來存取 Azure 金鑰保存庫，並建立認證讓您存取您的保存庫。 如果您看到先前提及的內部部署文件中的步驟，您可以發現這項功能會自動執行步驟 2 和 3。 您唯一仍然需要手動進行的是建立金鑰保存庫和金鑰。 從那裡開始，會自動化 SQL VM 的整個設定。 這項功能完成此設定之後，您可以執行 T-SQL 陳述式以開始如往常一般加密您的資料庫或備份。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM 提供者 1.0.4.0 版會透過 [SQL IaaS 延伸模組](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)安裝在 SQL Server VM 上。 升級 SQL IaaS 延伸模組並不會更新提供者版本。 如有需要 (例如，在移轉至 SQL 受控執行個體時)，請考慮以手動方式升級 EKM 提供者版本。


## <a name="enabling-and-configuring-akv-integration"></a>啟用及設定 AKV 整合
您可以在佈建期間啟用 AKV 整合，或針對現有的 VM 設定這項整合。

### <a name="new-vms"></a>新的 VM
如果您要使用資源管理員佈建新的 SQL Server 虛擬機器，則 Azure 入口網站會提供一個方式來啟用 Azure 金鑰保存庫整合。 Azure 金鑰保存庫功能僅適用於 SQL Server 的 Enterprise、Developer 和 Evaluation 版本。

![SQL Azure 金鑰保存庫整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

如需布建的詳細逐步解說，請參閱 Azure 入口網站中的布建[SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>現有的 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

針對現有的 SQL Server 虛擬機器，開啟您的[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)，然後選取 [**設定**] 底下的 [**安全性**]。 選取 [**啟用**] 以啟用 Azure Key Vault 整合。 

![現有 VM 的 SQL AKV 整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

完成時，請選取 [**安全性**] 頁面底部**的 [套用**] 按鈕，以儲存您的變更。

> [!NOTE]
> 我們在這裡建立的認證名稱稍後會對應到 SQL 登入。 這樣能允許 SQL 登入存取金鑰保存庫。 


> [!NOTE]
> 您也可以使用範本來設定 AKV 整合。 如需詳細資訊，請參閱 [適用於 Azure 金鑰保存庫整合的 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)。


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
