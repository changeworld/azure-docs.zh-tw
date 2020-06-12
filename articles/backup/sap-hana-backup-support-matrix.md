---
title: SAP Hana 備份支援矩陣
description: 在此文章中，了解當您使用 Azure 備份來備份 Azure VM 上的 SAP HANA 資料庫時，所支援的案例和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 5dcbfa67ce69a3dad1c263427ea3a0e34e8cef18
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747345"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上 SAP Hana 資料庫 備份的支援矩陣

Azure 備份支援將 SAP HANA 資料庫備份至 Azure。 此文章摘要說明當您使用 Azure 備份來備份 Azure VM 上的 SAP HANA 資料庫時，所支援的案例和限制。

> [!NOTE]
> 記錄備份的頻率現在可以設定為至少 15 分鐘。 只有在成功完成一個資料庫的完整備份後，記錄備份才會開始運作。

## <a name="scenario-support"></a>案例支援

| **案例**               | **支援的組態**                                | **不支援的組態**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓撲**               | 僅在 Azure Linux VM 中執行的 SAP HANA                    | HANA 大型執行個體 (HLI)                                   |
| **地區**                   | **GA：**<br> **美洲** – 美國中部、美國東部 2、美國東部、美國中北部、美國中南部、美國西部 2、美國中西部、美國西部、加拿大中部、加拿大東部、巴西南部 <br> **亞太地區** – 澳大利亞中部、澳大利亞中部 2、澳大利亞東部、澳大利亞東南部、日本東部、日本西部、韓國中部、韓國南部、東亞、東南亞、印度中部、印度南部、印度西部、中國東部、中國北部、中國東部 2、中國北部 2 <br> **歐洲** – 西歐、北歐、法國中部、英國南部、英國西部、德國北部、德國中西部、瑞士北部、瑞士西部、瑞士中北部 <br> **非洲/ME** - 南非北部、南非西部、阿拉伯聯合大公國北部、阿拉伯聯合大公國中部  <BR>  **Azure Government 區域** | 法國南部、德國中部、德國東北部、US Gov 愛荷華州 |
| **作業系統版本**            | SLES 12 SP2、SP3 或 SP4；SLES 15 SP1                              | RHEL                                                |
| **HANA 版本**          | HANA 1.x 上 SDC、HANA 2.x 上 MDC <= SPS04 Rev 46       | -                                                            |
| **HANA 部署**       | 單一 Azure VM 上一個 SAP HANA - 僅限擴大。 <br><br> 針對高可用性部署，系統會將兩部不同電腦上的兩個節點視為具有不同資料鏈的個別節點。               | 向外延展 <br><br> 在高可用性部署中，備份不會自動容錯移轉至次要節點。 應分別針對每個節點來設定備份。                                           |
| **HANA 執行個體**         | 單一 Azure VM 上一個 SAP HANA 執行個體 - 僅限擴大 | 單一 VM 上多個 SAP HANA 執行個體                  |
| **HANA 資料庫類型**    | 1\.x 上單一資料庫容器 (SDC)、2.x 上多個資料庫容器 (MDC) | HANA 1.x 上 MDC                                              |
| **HANA 資料庫大小**     | 2 TB 完整備份大小，如 HANA 回報)                   |                                                              |
| **備份類型**           | 完整、差異和記錄備份                          | 增量、快照                                       |
| **還原類型**          | 若要了解支援的還原類型，請參閱 SAP Hana Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) |                                                              |
| **備份限制**          | 每個 SAP HANA 執行個體最多 2 TB 的完整備份大小         |                                                              |
| **特殊的組態** |                                                              | SAP HANA + Dynamic Tiering <br>  透過 LaMa 複製        |

------

>[!NOTE]
>在 Azure VM 中執行 SAP HANA 資料庫備份時，Azure 備份不會自動調整日光節約時間變更。
>
>請視需要手動修改原則。


> [!NOTE]
> 您現在可以在 Azure 入口網站中[監視從 HANA 原生用戶端 (SAP HANA Studio/Cockpit/DBA Cockpit) 觸發的備份和還原](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) (到同一部電腦中)。

## <a name="next-steps"></a>後續步驟

* 了解如何[備份在 Azure VM 上執行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) \(部分機器翻譯\)
* 了解如何[還原在 Azure VM 上執行的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) \(部分機器翻譯\)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
