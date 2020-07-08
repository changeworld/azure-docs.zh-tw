---
title: 安全性考慮 |Microsoft Docs
description: 本主題提供保護在 Azure 虛擬機器中執行之 SQL Server 的一般指引。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4421b30d672cc026a033febb34b8b31afa0ef3c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668790"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上 SQL Server 的安全性考慮
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本主題包含整體安全性指導方針，可協助制定 Azure 虛擬機器 (VM) 中 SQL Server 執行個體的存取安全。

Azure 符合多種業界規範及標準，可讓您使用在虛擬機器中執行的 SQL Server，建置相容的解決方案。 如需 Azure 的法規相符性資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>控制 SQL 虛擬機器的存取權

當您建立 SQL Server 虛擬機器時，請考慮如何仔細控制誰可以存取電腦和 SQL Server。 一般而言，您應該執行下列作業：

- 將 SQL Server 的存取只限於需要它的應用程式和用戶端。
- 遵循用來管理使用者帳戶和密碼的最佳做法。

下列各節提供逐一考量這些點的建議。

## <a name="secure-connections"></a>安全連線

當您使用資源庫映像建立 SQL Server 虛擬機器時，[SQL Server 連線] 選項可讓您選擇 [本機 (在 VM 內)][私人 (在虛擬網路內)] 或 [公用 (網際網路)]。

![SQL Server 連線](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

如需最佳的安全性，請為您的案例選擇最嚴格的選項。 例如，如果您執行的應用程式可存取相同 VM 上的 SQL Server，則 [本機] 是最安全的選擇。 如果您執行的 Azure 應用程式需要存取 SQL Server，則**私**用只能保護在指定的[Azure 虛擬網路](../../../virtual-network/virtual-networks-overview.md)內 SQL Server 的通訊。 如果您需要 SQL Server VM的 [公用] \(網際網路) 存取，請務必遵循本主題中的其他最佳做法，以縮寫受攻擊面。

入口網站中選取的選項會使用 VM [網路安全性群組](../../../active-directory/identity-protection/security-overview.md) (NSG) 上的輸入安全性規則來允許或拒絕虛擬機器的網路流量。 您可以修改或建立新的輸入 NSG 規則，以允許 SQL Server 連接埠 (預設值 1433) 的流量。 您也可以指定允許透過此連接埠通訊的特定 IP 位址。

![網路安全性群組規則](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

除了可限制網路流量的 NSG 規則，您也可以在虛擬機器上使用 Windows 防火牆。

使用您使用端點搭配傳統部署模型，如果虛擬機器上有任何不使用的端點，請將它們全部移除。 如需有關在端點中使用 ACL 的指示，請參閱 [在端點上管理 ACL](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)。 使用 Azure Resource Manager 的 Vm 不需要這麼做。

最後，請考慮對 Azure 虛擬機器中的 SQL Server Database Engine 執行個體啟用已加密的連線。 使用簽署的憑證設定 SQL Server 執行個體。 如需詳細資訊，請參閱[啟用 Database Engine 的加密連接](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)和[連接字串語法](https://msdn.microsoft.com/library/ms254500.aspx)。

## <a name="encryption"></a>加密

受控磁片提供伺服器端加密，並 Azure 磁碟加密。 [伺服器端加密](/azure/virtual-machines/windows/disk-encryption)提供待用加密，並保護您的資料，以符合您的組織安全性和合規性承諾。 [Azure 磁碟加密](/azure/security/fundamentals/azure-disk-encryption-vms-vmss)會使用 Bitlocker 或 DM Crypt 技術並與 Azure Key Vault 整合，以加密 OS 和資料磁碟。 

## <a name="use-a-non-default-port"></a>使用非預設連接埠

根據預設，SQL Server 會在已知的通訊埠 1433 上接聽。 為了提高安全性，將 SQL Server 設定為在非預設連接埠 (例如 1401) 上接聽。 如果您在 Azure 入口網站中佈建 SQL Server 資源庫映像，您可以在 [SQL Server 設定] 刀鋒視窗中指定此連接埠。

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

若要在佈建後進行此設定，您有兩個選項：

- 針對 Resource Manager VM，您可從 [SQL 虛擬機器資源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)中選取 [安全性]。 這可提供變更連接埠的選項。

  ![在入口網站中變更 TCP 連接埠](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- 對於傳統 VM 或不是透過入口網站的 SQL Server VM，您可以從遠端連線至 VM 來手動設定連接埠。 如需設定步驟，請參閱[設定伺服器以在特定 TCP 通訊埠上接聽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)。 如果您使用此手動技巧，您也需要新增 Windows 防火牆規則，以允許該 TCP 連接埠的連入流量。

> [!IMPORTANT]
> 如果您的 SQL Server 連接埠已對公用網際網路連線開啟，則指定非預設連接埠是個不錯的主意。

當 SQL Server 在非預設連接埠上接聽時，您必須在連線時指定連接埠。 例如，考量伺服器 IP 位址為 13.55.255.255，且 SQL Server 在連接埠 1401 上接聽的案例。 若要連線到 SQL Server，您會在連接字串中指定 `13.55.255.255,1401`。

## <a name="manage-accounts"></a>管理帳戶

您不希望攻擊者容易猜到帳戶名稱或密碼。 使用下列秘訣來協助：

- 建立不是名為 **Administrator**的唯一本機系統管理員帳戶。

- 為您的所有帳戶使用複雜的強式密碼。 如需如何建立強式密碼的詳細資訊，請參閱 [建立強式密碼](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)一文。

- 根據預設，Azure 會在 SQL Server 虛擬機器安裝期間選取 Windows 驗證。 因此，系統會停用 **SA** 登入，並由安裝程式指派密碼。 我們建議不要使用或啟用 **SA** 登入。 如果您必須具有 SQL 登入，請使用下列其中一個策略：

  - 使用具有 **sysadmin** 成員資格的唯一名稱建立 SQL 帳戶。 在佈建期間啟用 **SQL 驗證**，即可從入口網站執行此作業。

    > [!TIP] 
    > 如果您未在佈建期間啟用 SQL 驗證，您必須將驗證模式手動變更為 **SQL Server 和 Windows 驗證模式**。 如需詳細資訊，請參閱 [變更伺服器驗證模式](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)。

  - 如果您必須使用 **SA** 登入，請在佈建後啟用此登入，然後指派新的強式密碼。

## <a name="additional-best-practices"></a>其他最佳做法

除了本主題中所描述的做法之外，建議檢閱並實作傳統內部部署安全性做法的安全性最佳做法，以及虛擬機器的安全性最佳做法。 

如需內部部署安全性做法的詳細資訊，請參閱 [SQL Server 安裝的安全性考量](/sql/sql-server/install/security-considerations-for-a-sql-server-installation)及[資訊安全中心](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)。 

如需虛擬機器安全性的詳細資訊，請參閱[虛擬機器安全性概觀](/azure/security/fundamentals/virtual-machines-overview)。


## <a name="next-steps"></a>後續步驟

如果您也想瞭解關於效能的最佳作法，請參閱[Azure 虛擬機器上 SQL Server 的效能最佳作法](performance-guidelines-best-practices.md)。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)。 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](frequently-asked-questions-faq.md)。

