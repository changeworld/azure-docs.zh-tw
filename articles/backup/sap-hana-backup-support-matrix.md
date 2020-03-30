---
title: SAP Hana 備份支援矩陣
description: 在本文中，瞭解使用 Azure 備份在 Azure VM 上備份 SAP HANA 資料庫時受支援的方案和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252437"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上 SAP Hana 資料庫 備份的支援矩陣

Azure 備份支援將 SAP HANA 資料庫備份到 Azure。 本文總結了使用 Azure 備份在 Azure VM 上備份 SAP HANA 資料庫時支援的方案和限制。

> [!NOTE]
> 記錄備份頻率現在可以設置為至少 15 分鐘。 記錄備份僅在資料庫成功完成完整備份後才開始流動。

## <a name="scenario-support"></a>案例支援

| **案例**               | **支援的配置**                                | **不支援的配置**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓撲**               | 僅在 Azure Linux VM 中運行的 SAP HANA                    | HANA 大型實例 （HLI）                                   |
| **地理**                   | **Ga：**<br> **美洲**– 美國中部、美國東部 2、美國東部、美國中北部、美國中南部、美國西部 2、美國中西部、美國西部、加拿大中部、加拿大東部、巴西南部 <br> **亞太地區**– 澳大利亞中部，澳大利亞中部 2，澳大利亞東部，澳大利亞東南部，日本東部，日本西部，韓國中部，韓國南部，東亞，東南亞，印度中部，印度南部，印度西部，中國東部，中國北部，中國東部2，中國北 2 <br> **歐洲**– 西歐、北歐、法國中部、英國南部、英國西部、德國北部、德國中西部、瑞士北部、瑞士西部、瑞士中部北部 <br> **非洲 / ME** - 南非北部，南非西部，阿聯酋北部，阿聯酋中部  <BR>  **zure Government 區域** | 法國南部，德國中部，德國東北部，美國政府IOWA |
| **作業系統版本**            | 帶有 SP2、SP3 或 SP4 的 SLES 12;SLES 15 帶 SP1                              | RHEL                                                |
| **HANA 版本**          | HANA 1.x 上的 SDC，HANA 2.x <上的 MDC = SPS04 Rev 46       | -                                                            |
| **HANA 部署**       | 單個 Azure VM 上的 SAP HANA - 僅向上擴展。 <br><br> 對於高可用性部署，兩個不同電腦上的節點都被視為具有單獨資料鏈的單個節點。               | 向外延展 <br><br> 在高可用性部署中，備份不會自動容錯移轉到輔助節點。 配置備份應為每個節點單獨完成。                                           |
| **HANA 實例**         | 單個 Azure VM 上的單個 SAP HANA 實例 - 僅向上擴展 | 單個 VM 上的多個 SAP HANA 實例                  |
| **HANA 資料庫類型**    | 單資料庫容器 （SDC） 上 1.x，多資料庫容器 （MDC）在 2.x 上 | HANA 1.x 中的 MDC                                              |
| **HANA 資料庫大小**     | 2-TB 完整備份大小，由 HANA 報告）                   |                                                              |
| **備份類型**           | 完整、差異和記錄備份                          | 增量快照                                       |
| **還原類型**          | 請參閱 SAP HANA 注釋[1642148](https://launchpad.support.sap.com/#/notes/1642148)以瞭解支援的還原類型 |                                                              |
| **備份限制**          | 每個 SAP HANA 實例的完整備份大小高達 2 TB         |                                                              |
| **特殊的組態** |                                                              | SAP HANA + 動態分層 <br>  通過 LaMa 進行克隆        |

------

> [!NOTE]
> 當前不支援來自 SAP HANA 本機用戶端（SAP HANA 工作室/駕駛艙/DBA 駕駛艙）的備份和還原操作。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[備份在 Azure VM 上運行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* 瞭解如何[還原在 Azure VM 上運行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
