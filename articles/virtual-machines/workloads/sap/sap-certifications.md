---
title: 適用於 SAP 的 Microsoft Azure 認證 | Microsoft Docs
description: 目前 Azure 平台上 SAP 設定和認證的更新清單。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: df80113b85153f4e52bcbefa7b33dab9da296173
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084816"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>於 Microsoft Azure 上執行的 SAP 認證和設定

SAP 和 Microsoft 是已合作多年的穩固合作夥伴，能互相為彼此的客戶帶來許多好處。 Microsoft 一直持續更新其平台，並提交新的認證詳細資料給 SAP，以確保 Microsoft Azure 是執行 SAP 工作負載的最佳平台。 下表概述 Azure 所支援的設定，以及持續增加的 SAP 認證清單。 此清單是一個概覽清單,可能在此和那裡偏離官方 SAP 清單。 有關如何獲取詳細資料,本文在[文章"Azure 部署支援哪些 SAP 軟體"](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="sap-hana-certifications"></a>SAP HANA 認證
參考：

- [SAP HANA 認證的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)可用於 SAP HANA 支援原生 Azure VM 和 HANA 大型執行個體。

| SAP 產品 | 支援的 OS | Azure 供應項目 |
| --- | --- | --- |
| SAP HANA Developer Edition (包括由 SQLODBC、Windows 專用版的 ODBO、ODBC、JDBC 驅動程式、HANA Studio 及 HANA 資料庫組成的 HANA 用戶端軟體) | Red Hat Enterprise Linux、SUSE Linux Enterprise | D 系列 VM 系列 |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2、M32ts、M32ls、M64ls、M64s <br /> [SAP Hana 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | 受控制的 GS5 可用性。 完全支援 M64、M64ms、M128、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2、 <br /> SAP HANA on Azure (大型執行個體) [SAP HANA 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA (OLTP) | Red Hat Enterprise Linux、SUSE Linux Enterprise | M64s、M64ms、M128、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> M416s_v2、M416ms_v2、Azure(大型實體)SAP HANA[認證 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW (OLAP) | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64、M64ms、M128、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> M416s_v2、M416ms_v2、Azure(大型實體)SAP HANA[認證 IaaS 平臺](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64、M64ms、M128、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> azure 上的M416s_v2、M416ms_v2、SAP HANA(大型實例) <br /> [SAP Hana 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

請注意，SAP 在 [SAP HANA 認證 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中使用「叢集」一詞是作為「相應放大」的同義字，而「不是」作為高可用性「叢集」

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認證
Microsoft Azure 已獲認證可用於下列 SAP 產品，因此具備 Microsoft 和 SAP 的完整支援。
參考：

- [1928533 - Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)用於所有 SAP NetWeaver 型應用程式，包括 SAP TREX、SAP LiveCache 及 SAP 內容伺服器。 還有所有資料庫，但不包括 SAP HANA。


| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business Suite 軟體 | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 到 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP Business All-in-One | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 到 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP BusinessObjects BI | Windows |N/A |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 到 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP NetWeaver | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 到 A11、D11 到 D14、DS11 到 DS14、DS11_v2 到 DS15_v2、GS1 到 GS5、D2s_v3 到 D64s_v3、D2as_v4 到 D64as_v4、E2s_v3 到 E64s_v3、E2as_v4 到 E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Azure 上支援的其他 SAP 工作負載

| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | 所有 NetWeaver 認證的 VM 類型<br /> [SAP 附註編號 928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows 與 Linux | | 所有 NetWeaver 認證的 VM 類型<br /> SAP 附註編號 2451795 |
| SAP Business Objects BI 平台 | Windows 與 Linux | | SAP 附註編號 2145537 |
| SAP Data Services 4.2 | | | SAP 附註編號 2288344 |
| SAP Hybris 商務平臺  | Windows | SQL Server，Oracle | 所有 NetWeaver 認證的 VM 類型 <br /> [海布裡斯文件](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris 商務平臺  | SLES 12 或更多最新 | SAP HANA | 所有 NetWeaver 認證的 VM 類型 <br /> [海布裡斯文件](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris 商務平臺  | RHEL 7 或更多最新 | SAP HANA | 所有 NetWeaver 認證的 VM 類型 <br /> [海布裡斯文檔]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP(Hybris) 商務平臺 1811 及更高版本  | 視窗、SLES 或 RHEL | SQL Azure 資料庫 | 所有 NetWeaver 認證的 VM 類型 <br /> [海布裡斯文件](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
