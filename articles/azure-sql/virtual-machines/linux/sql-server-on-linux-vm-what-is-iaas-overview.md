---
title: Azure Linux 虛擬機器上的 SQL Server 概觀 | Microsoft Docs
description: 深入了解如何在 Azure Linux 虛擬機器上執行完整的 SQL Server 版本。 取得所有 Linux SQL Server VM 映像和相關內容的直接連結。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 61b8982868bf14a7b5a5441049cb7fa21cdd9d6d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266027"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 虛擬機器上的 SQL Server 概觀 (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Azure 虛擬機器上的 SQL Server 可讓您在雲端使用完整的 SQL Server 版本，而不需要管理任何內部部署硬體。 當您使用隨用隨付方案時，SQL Server VM 也會簡化授權成本。

Azure 虛擬機器會在全球許多不同的[地理區域](https://azure.microsoft.com/regions/)中執行。 此外，也提供各種[機器大小](../../../virtual-machines/windows/sizes.md)。 虛擬機器映像庫可讓您使用正確的版本、版次及作業系統建立 SQL Server VM。 這可讓虛擬機器成為許多不同 SQL Server 工作負載的適合選項。 

## <a name="get-started-with-sql-vms"></a><a id="create"></a>開始使用 SQL VM

若要開始使用，請選擇具有您所需版本、版次及作業系統的 SQL Server 虛擬機器映像。 下列各節提供 Azure 入口網站中 SQL Server 虛擬機器資源庫映像的直接連結。

> [!TIP]
> 如需了解 SQL 映像定價的詳細資訊，請參閱[ Linux SQL Server VM 的定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 若要查看可用的 Windows SQL Server 虛擬機器映像，請參閱 [Azure 虛擬機器 (Windows) 上的 SQL Server 概觀](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)。

## <a name="installed-packages"></a><a id="packages"></a>已安裝的套件

當您設定 Linux 上的 SQL Server 時，可以根據您的需求安裝資料庫引擎套件，以及數個選用的套件。 SQL Server 的 Linux 虛擬機器映像會自動為您安裝大多數的套件。 下表顯示每個發佈會安裝哪些套件。

| 散發 | [資料庫引擎](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [全文檢索搜尋](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 附加元件](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![否](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![否](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![否](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![是](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>相關產品與服務

### <a name="linux-virtual-machines"></a>Linux 虛擬機器

* [虛擬機器概觀](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>儲存體

* [Microsoft Azure 儲存體簡介](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>網路功能

* [虛擬網路概觀](../../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 位址](../../../virtual-network/public-ip-addresses.md)
* [在 Azure 入口網站中建立完整格式的網域名稱](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux) \(機器翻譯\)
* [Azure SQL Database 比較](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>後續步驟

在 Azure Linux 虛擬機器上開始使用 SQL Server：

* [在 Azure 入口網站中建立 SQL Server VM](sql-vm-create-portal-quickstart.md)

獲得有關 Linux 上 SQL VM 常見問題的答案：

* [Azure Linux 虛擬機器上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
