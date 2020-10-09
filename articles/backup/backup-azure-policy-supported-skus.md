---
title: Azure 原則支援的 VM SKU
description: '描述發行者、映射供應專案和映射 SKU 所支援的支援 VM Sku (的文章，可支援備份所提供的內建 Azure 原則) '
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183614"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure 原則支援的 VM SKU

Azure 備份提供內建原則 (使用 Azure 原則) ，可指派給訂用帳戶 **或資源群組內指定位置中的所有 Azure vm**。 將此原則指派給指定的範圍時，在該範圍中建立的所有新 Vm 都會自動設定為備份至 **相同位置和訂用帳戶中現有**的保存庫。 下表列出此原則支援的所有 VM Sku。

## <a name="supported-vms"></a>支援的 Vm *

**原則名稱：** 將某個位置的 Vm 備份設定至相同位置中的現有中央保存庫

映射發行者 | 映射供應專案 | 映像 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2008 R2 SP (2008-R2-SP1-smalldisk) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2012 Datacenter (2012-Datacenter-smalldisk) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter-Server Core (2016-Datacenter-Server-核心) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2016 Datacenter-Server Core (2016-Datacenter-伺服器-核心-smalldisk) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2016 Datacenter (2016-Datacenter-smalldisk) 
MicrosoftWindowsServer | WindowsServer | 具有容器的 Windows Server 2019 Datacenter Server Core (2016-資料中心-容器) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 遠端桌面工作階段主機 2016 (2016-Datacenter-含-RDSH) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-核心) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2019 Datacenter Server Core (2019-Datacenter-核心-smalldisk) 
MicrosoftWindowsServer | WindowsServer | 具有容器的 Windows Server 2019 Datacenter Server Core (2019-Datacenter-含容器的核心) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2019 Datacenter Server Core 搭配容器 (2019-Datacenter-smalldisk) 
MicrosoftWindowsServer | WindowsServer | smalldiskWindows Server 2019 Datacenter (2019-Datacenter-smalldisk) 
MicrosoftWindowsServer | WindowsServer | 具有容器的 Windows Server 2019 Datacenter (2019-資料中心-容器) 
MicrosoftWindowsServer | WindowsServer | smalldisk具有容器的 Windows Server 2019 Datacenter (2019-Datacenter-含容器-smalldisk) 
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn)  (2019-Datacenter-zhcn) 
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-核心-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-核心-1709-含容器-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-核心-1803-含容器-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 所有映射 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016 | 所有映射 Sku
MicrosoftSQLServer | SQL2016-WS2016 | 所有映射 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 所有映射 Sku
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 所有映射 Sku
MicrosoftSQLServer | SQL2016-WS2012R2 | 所有映射 Sku
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 所有映射 Sku
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 所有映射 Sku
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 所有映射 Sku
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 所有映射 Sku
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 所有映射 Sku
MicrosoftRServer | MLServer-WS2016 | 所有映射 Sku
MicrosoftVisualStudio | VisualStudio | 所有映射 Sku
MicrosoftVisualStudio | Windows | 所有映射 Sku
MicrosoftDynamicsAX | Dynamics | 預先需求-AX7-Onebox-U8
microsoft-ads | windows-資料科學-vm | 所有映射 Sku
MicrosoftWindowsDesktop | Windows-10 | 所有映射 Sku
RedHat | RHEL | 6.7、6.8、6.9、6.10、7.2、7.3、7.4、7.5、7.6、7。7
RedHat | RHEL-SAP-HANA | 6.7、7.2、7。3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-優先順序 | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES-BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8、6.9、6.10、7.3、7.4、7.5、7。6
OpenLogic | CentOS | 6.X、7。X
OpenLogic | CentOS – LVM | 6.X、7。X
OpenLogic | CentOS – SRIOV | 6.X、7。X
cloudera | cloudera-centos-os | 7。X
