---
title: SAP Hana 備份支援矩陣
description: 在本文中，當您使用 Azure 備份在 Azure Vm 上備份 SAP Hana 資料庫時，請瞭解支援的案例和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: 5065f4cf3d9ab75c6e3b682b6a24387ce7449b0f
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562653"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上 SAP Hana 資料庫 備份的支援矩陣

Azure 備份支援將 SAP HANA 資料庫備份至 Azure。 此文章摘要說明當您使用 Azure 備份來備份 Azure VM 上的 SAP HANA 資料庫時，所支援的案例和限制。

> [!NOTE]
> 記錄備份的頻率現在可以設定為至少 15 分鐘。 只有在成功完成一個資料庫的完整備份後，記錄備份才會開始運作。

## <a name="scenario-support"></a>案例支援

| **案例**               | **支援的組態**                                | **不支援的組態**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓撲**               | 僅在 Azure Linux VM 中執行的 SAP HANA                    | HANA 大型執行個體 (HLI)                                   |
| **區域**                   | **GA：**<br> **美洲** – 美國中部、美國東部 2、美國東部、美國中北部、美國中南部、美國西部 2、美國中西部、美國西部、加拿大中部、加拿大東部、巴西南部 <br> **亞太地區** – 澳大利亞中部、澳大利亞中部 2、澳大利亞東部、澳大利亞東南部、日本東部、日本西部、韓國中部、韓國南部、東亞、東南亞、印度中部、印度南部、印度西部、中國東部、中國北部、中國東部 2、中國北部 2 <br> **歐洲** –西歐、北歐、法國中部、英國南部、英國西部、德國北部、德國中西部、瑞士北部、瑞士西部、中央瑞士北部、挪威東部、挪威西部 <br> **非洲/ME** - 南非北部、南非西部、阿拉伯聯合大公國北部、阿拉伯聯合大公國中部  <BR>  **Azure Government 區域** | 法國南部、德國中部、德國東北部、US Gov 愛荷華州 |
| **作業系統版本**            | SLES 12 SP2、SP3、SP4 和 SP5;SLES 15 含 SP0、SP1、SP2 <br><br>  從 2020 年 8 月 1 日起，適用於 RHEL 的 SAP Hana 備份 (7.4、7.6、7.7 和 8.1) 已正式推出。                |                                             |
| **HANA 版本**          | Hana 2.x 上的 SDC、HANA 2.x 上的 MDC <= SPS04 Rev 53、SPS05 (尚未針對啟用加密的案例進行驗證)       |                                                            |
| **HANA 部署**       | 單一 Azure VM 上一個 SAP HANA - 僅限擴大。 <br><br> 針對高可用性部署，系統會將兩部不同電腦上的兩個節點視為具有不同資料鏈的個別節點。               | 向外延展 <br><br> 在高可用性部署中，備份不會自動容錯移轉至次要節點。 應分別針對每個節點來設定備份。                                           |
| **HANA 執行個體**         | 單一 Azure VM 上一個 SAP HANA 執行個體 - 僅限擴大 | 單一 VM 上的多個 SAP Hana 實例。 您一次只能保護其中一個多個實例。                  |
| **HANA 資料庫類型**    | 1\.x 上單一資料庫容器 (SDC)、2.x 上多個資料庫容器 (MDC) | HANA 1.x 上 MDC                                              |
| **HANA 資料庫大小**     | 大小 <= 2 TB 的 HANA 資料庫 (這不是 HANA 系統的記憶體大小)                |                                                              |
| **備份類型**           | 完整、差異、增量 (預覽) 和記錄備份                          |  快照集                                       |
| **還原類型**          | 若要了解支援的還原類型，請參閱 SAP Hana Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) |                                                              |
| **備份限制**          | 每 SAP Hana 實例最多 2 TB 的完整備份大小 (軟限制)          |                                                              |
| **特殊的組態** |                                                              | SAP HANA + Dynamic Tiering <br>  透過 LaMa 複製        |

------

>[!NOTE]
>在 Azure VM 中執行 SAP HANA 資料庫備份時，Azure 備份不會自動調整日光節約時間變更。
>
>請視需要手動修改原則。

> [!NOTE]
> 您現在可以將 [備份和還原](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) 作業 (到相同的電腦) 從 HANA 原生用戶端觸發， (SAP Hana Studio/環境環境/DBA 環境) Azure 入口網站。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [備份在 Azure vm 上執行的 SAP Hana 資料庫](./backup-azure-sap-hana-database.md)
* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](./sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](./backup-azure-sap-hana-database-troubleshoot.md)
