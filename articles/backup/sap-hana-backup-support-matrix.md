---
title: SAP Hana 備份支援矩陣
description: 在本文中，您將瞭解當您使用 Azure 備份來備份 Azure Vm 上的 SAP Hana 資料庫時，所支援的案例和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 2063da4c5210cace41454d8bdc5b12e636ba76cd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705644"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上 SAP Hana 資料庫 備份的支援矩陣

Azure 備份支援將 SAP Hana 資料庫備份至 Azure。 本文摘要說明當您使用 Azure 備份來備份 Azure Vm 上的 SAP Hana 資料庫時，所支援的案例和所提供的限制。

## <a name="onboard-to-the-public-preview"></a>上線至公開預覽

上線至公開預覽，如下所示：

* 在入口網站中，[依照本文的指示](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)將您的訂用帳戶識別碼註冊至「復原服務」服務提供者。
* 在 PowerShell 中，請執行下列 Cmdlet。 其結果應為「已註冊」。

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> 記錄備份的頻率現在可以設定為至少15分鐘。 記錄備份只會在資料庫的成功完整備份完成後開始流動。

## <a name="scenario-support"></a>案例支援

| **案例**               | **支援的設定**                                | **不支援的設定**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓撲**               | 僅 SAP Hana 在 Azure Linux Vm 中執行                    | HANA 大型實例（B-HLI）                                   |
| **地區**                   | **GA**<br />**歐洲**–西歐、北歐、法國中部、法國南部、英國南部、英國西部、德國北部、德國中西部、瑞士北部、瑞士西部<br />**亞太地區**–澳大利亞中部、澳大利亞中部2、澳大利亞東部、澳大利亞東南部、日本東部、日本西部、韓國中部、南韓南部<br /><br>**預覽：**<br />**美洲**–美國中部、美國東部2、美國東部、美國中北部、美國中南部、美國西部2、美國中西部、美國西部、加拿大中部、加拿大東部、巴西南部<br />**亞太地區**–東亞、東南亞、印度中部、印度南部 | 中國東部、中國北部、中國東部2、中國北部2、印度西部、中央瑞士北部、南非北部、南非西部、阿拉伯聯合大公國北部、阿拉伯聯合大公國中部、Azure Government 地區 |
| **作業系統版本**            | SLES 12 SP2、SP3 或 SP4                                | SLES 15，RHEL                                                |
| **HANA 版本**          | Hana 1.x 上的 SDC、HANA 2. x 上的 MDC < = SPS04 Rev 44            | -                                                            |
| **HANA 部署**       | 單一 Azure VM 上的 SAP Hana-僅限相應增加               | 向外延展                                                    |
| **HANA 實例**         | 單一 Azure VM 上的單一 SAP Hana 實例-僅相應增加 | 單一 VM 上的多個 SAP Hana 實例                  |
| **HANA 資料庫類型**    | 2\.x 上的單一資料庫容器（SDC），2.x 上的多資料庫容器（MDC） | HANA 1.x 中的 MDC                                              |
| **HANA 資料庫大小**     | 2 TB 完整備份大小，由 HANA 回報）                   |                                                              |
| **備份類型**           | 完整、差異和記錄備份                          | 增量，快照集                                       |
| **還原類型**          | 請參閱 SAP Hana 附注[1642148](https://launchpad.support.sap.com/#/notes/1642148) ，以瞭解支援的還原類型 |                                                              |
| **備份限制**          | 每個 SAP Hana 實例最多 2 TB 的完整備份大小         |                                                              |
| **特殊設定** |                                                              | SAP Hana + 動態分層 <br>  透過 LaMa 複製        |

------

> [!NOTE]
> 目前不支援 SAP Hana native client （SAP Hana Studio/駕駛中心/DBA 環境）的備份與還原作業。



## <a name="next-steps"></a>後續步驟

* 瞭解如何[備份在 Azure vm 上執行 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* 瞭解如何[還原在 Azure vm 上執行 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
