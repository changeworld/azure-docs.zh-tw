---
title: 在 Azure (Resource Manager) 中的 Windows VM 上將 Key Vault 與 SQL Server 整合 | Microsoft Docs
description: 了解如何自動化 SQL Server 加密的組態，以用於 Azure 金鑰保存庫。 本主題說明如何搭配使用資源管理員建立的 SQL Server 虛擬機器使用 Azure Key Vault 整合。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7fab8db1fcc02e26d1b19d3889414565ff56351b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293539"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>在 Azure VM (資源管理員) 上設定 SQL Server 的 Azure Key Vault 整合
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和[備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 這些形式的加密需要您管理和儲存用來加密的密碼編譯金鑰。 Azure Key Vault (AKV) 服務旨在改善安全性，以及在安全且高可用性的位置管理這些金鑰。 [SQL Server 連接器](https://www.microsoft.com/download/details.aspx?id=45344) 讓 SQL Server 可以從 Azure 金鑰保存庫使用這些金鑰。

如果正在執行內部部署的 SQL Server，則可遵循一些步驟來[從內部部署 SQL Server 執行個體存取 Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx)。 但針對 Azure VM 上的 SQL Server，您可使用 *Azure Key Vault 整合*功能來節省時間。

啟用這項功能時，它會自動安裝 SQL Server 連接器、設定 EKM 提供者來存取 Azure 金鑰保存庫，並建立認證讓您存取您的保存庫。 如果您看到先前提及的內部部署文件中的步驟，您可以發現這項功能會自動執行步驟 2 和 3。 您唯一仍然需要手動進行的是建立金鑰保存庫和金鑰。 該位置會自動化 SQL VM 的整個設定。 這項功能完成設定後，即可執行 Transact-SQL (T-SQL) 陳述式，以開始如往常一般加密資料庫或備份。

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > 可延伸金鑰管理 (EKM) 提供者版本 1.0.4.0 已透過 [SQL 基礎結構即服務 (IaaS) 延伸模組](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)安裝在 SQL Server VM 上。 升級 SQL IaaS 延伸模組不會更新提供者版本。 如有需要 (例如，在移轉至 SQL 受控執行個體時)，請考慮以手動方式升級 EKM 提供者版本。


## <a name="enabling-and-configuring-key-vault-integration"></a>啟用及設定 Key Vault 整合
您可在佈建期間啟用 Key Vault 整合，或針對現有的 VM 設定這項整合。

### <a name="new-vms"></a>新的 VM
如果您要使用資源管理員佈建新的 SQL Server 虛擬機器，則 Azure 入口網站提供啟用 Azure Key Vault 整合的方式。 Azure 金鑰保存庫功能僅適用於 SQL Server 的 Enterprise、Developer 和 Evaluation 版本。

![SQL Azure 金鑰保存庫整合](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

如需佈建的詳細逐步解說，請參閱[在 Azure 入口網站中佈建 SQL 虛擬機器](create-sql-vm-portal.md)。

### <a name="existing-vms"></a>現有的 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

針對現有的 SQL 虛擬機器，請開啟 [SQL 虛擬機器資源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)，然後選取位於 [設定] 下的 [安全性]。 選取 [啟用] 以啟用 Azure Key Vault 整合。 

![現有 VM 的 SQL Key Vault 整合](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

完成後，請選取位於 [安全性] 頁面底部的 [套用] 按鈕來儲存變更。

> [!NOTE]
> 此處所建立的認證名稱會在稍後對應到 SQL 登入。 這樣能允許 SQL 登入存取金鑰保存庫。 


> [!NOTE]
> 您也可以使用範本來設定 Key Vault 整合。 如需詳細資訊，請參閱 [適用於 Azure 金鑰保存庫整合的 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)。


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
