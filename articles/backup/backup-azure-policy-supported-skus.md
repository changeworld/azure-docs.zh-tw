---
title: Azure 原則支援的 VM SKU
description: 描述備份提供的內置 Azure 策略支援的 VM SKU（按發佈伺服器、映射提供和映射 SKU）的文章
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980110"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure 原則支援的 VM SKU

Azure 備份提供一個內置策略（使用 Azure 策略），可以分配給**訂閱或資源組中指定位置中的所有 Azure VM。** 當此策略分配給給定作用域時，該作用域中創建的所有新 VM 將自動設定為備份到**同一位置和訂閱中的現有保存庫**。 下表列出了此策略支援的所有 VM SKU。

### <a name="supported-vms"></a>**支援的 VM**

**策略名稱：** 將位置 VM 上的備份配置為同一位置的現有中央保存庫

圖像發行者 | 圖像優惠 | 映像 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2008 R2 SP1 （2008-R2-SP1）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2008 R2 SP （2008-R2-SP1 小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2012 資料中心 （2012-資料中心）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2012 資料中心 （2012-資料中心-小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2012 R2 資料中心 （2012-R2-資料中心）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2012 R2 資料中心 （2012-R2-資料中心-小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2016 資料中心 （2016-資料中心）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2016 資料中心 - 伺服器核心 （2016-資料中心-伺服器核心）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2016 資料中心 - 伺服器核心 （2016-資料中心-伺服器-核心小磁片）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2016 資料中心 （2016-資料中心-小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2019 資料中心伺服器核心與容器 （2016-資料中心與容器）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2016 遠端桌面工作階段主機 2016 （2016-資料中心與 RDSH）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2019 資料中心 （2019-資料中心）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2019 資料中心伺服器核心 （2019-資料中心核心）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2019 資料中心伺服器核心 （2019-資料中心-核心小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2019 資料中心伺服器核心與容器（2019-資料中心核心與容器）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2019 資料中心伺服器核心與容器 （2019-資料中心-核心與容器小磁片）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2019 資料中心 （2019-資料中心-小磁片）
MicrosoftWindowsServer | WindowsServer | Windows 伺服器 2019 資料中心與容器 （2019-資料中心帶容器）
MicrosoftWindowsServer | WindowsServer | [小磁片]Windows 伺服器 2019 資料中心與容器 （2019-資料中心與容器小磁片）
MicrosoftWindowsServer | WindowsServer | 視窗伺服器 2019 資料中心 （zh-cn） （2019-資料中心-zhcn）
MicrosoftWindowsServer | 視窗伺服器半年 | 資料中心-核心-1709-小磁片
MicrosoftWindowsServer | 視窗伺服器半年 | 資料中心-核心-1709-帶容器的小磁片
MicrosoftWindowsServer | 視窗伺服器半年 | 資料中心-核心-1803-帶容器的小磁片
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 所有圖像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | 所有圖像 SKU
MicrosoftSQLServer | SQL2016-WS2016  | 所有圖像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 所有圖像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 所有圖像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | 所有圖像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 所有圖像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 所有圖像 SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 所有圖像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 所有圖像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 所有圖像 SKU
MicrosoftRServer | MLServer-WS2016 | 所有圖像 SKU
微軟視覺工作室 | VisualStudio | 所有圖像 SKU
微軟視覺工作室 | Windows | 所有圖像 SKU
微軟動力AX | Dynamics  | 預召回-AX7-一盒-U8
microsoft-ads | 視窗-資料-科學-vm | 所有圖像 SKU
MicrosoftWindowsDesktop | Windows-10 | 所有圖像 SKU
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-優先順序 | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES 優先 | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES 標準 | 12.X
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
Canonical | UbuntuServer | 18.04-每日-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X， 7.X
OpenLogic | CentOS_LVM | 6.X， 7.X
OpenLogic | 森托斯·斯裡羅夫 | 6.X， 7.X
cloudera | cloudera-centos-os | 7.X
