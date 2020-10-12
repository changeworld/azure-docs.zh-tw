---
title: 使用備份中心監視和操作備份
description: 本文說明如何使用備份中心大規模監視和操作備份
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 29e09e502e83ea67af290f206ee0e68b847b2069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994514"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>使用備份中心監視和操作備份

作為備份系統管理員，您可以使用 [備份中心] 作為單一的單一窗格，以每日一天監視您的作業和備份清查。 您也可以使用備份中心來執行一般作業，例如回應隨選備份要求、還原備份、建立備份原則等等。

## <a name="supported-scenarios"></a>支援的案例

* Azure VM 備份和適用於 PostgreSQL 的 Azure 資料庫 Server 備份目前支援「備份中心」。
* 如需支援和不支援案例的詳細清單，請參閱 [支援矩陣](backup-center-support-matrix.md) 。

## <a name="backup-instances"></a>備份實例

備份中心可讓您輕鬆搜尋及探索備份資產上的備份實例。

選取 [備份中心] 中的 [ **備份實例** ] 索引標籤，可讓您查看您有權存取之所有備份實例的詳細資料。

 您可以查看每個備份實例的下列資訊：

* Datasource 訂用帳戶
* Datasource 資源群組
* 最新復原點
* 與備份實例相關聯的保存庫
* 保護狀態

 您也可以篩選下列參數的備份實例清單：

* Datasource 訂用帳戶
* Datasource 資源群組
* Datasource 位置
* Datasource 類型
* 保存庫
* 保護狀態
* Datasource 標記

以滑鼠右鍵按一下方格中的任何專案，可讓您在指定的備份實例上執行動作，例如流覽至資源、觸發隨選備份和還原，或停止備份。

![備份中心-實例](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>備份作業

備份中心可讓您查看備份資產中所建立之所有作業的詳細資訊，並針對失敗的工作採取適當的動作。

選取 [備份中心] 中的 [ **備份作業** ] 功能表項目，可讓您查看所有的作業。 每項作業都包含下列資訊：

* 與作業相關聯的備份實例
* Datasource 訂用帳戶
* Datasource 資源群組
* Datasource 位置
* 作業作業
* 工作狀態
* 工作開始時間
* 工作持續時間

選取方格中的專案可讓您查看更多有關指定作業的詳細資料。 以滑鼠右鍵按一下專案可協助您流覽至資源，以採取必要的動作。

![備份中心-作業](./media/backup-center-monitor-operate/backup-center-jobs.png)

您可以使用 [ **備份作業** ] 索引標籤來查看最多過去七天的工作。 若要查看較舊的工作，請使用 [備份報表](backup-center-obtain-insights.md)。

## <a name="vaults"></a>保存庫

在 [備份中心] 中選取 [保存 **庫** ] 功能表項目，可讓您查看所有復原 [服務保存庫](backup-azure-recovery-services-vault-overview.md) 的清單，以及您有權存取的 [備份](backup-vault-overview.md) 保存庫清單。 您可以使用下列參數篩選清單：

* 保存庫訂用帳戶
* 保存庫資源群組
* 保存庫名稱
* 與原則相關聯的資料來源類型

選取清單中的任何專案，可讓您流覽至指定的保存庫。

![備份中心-保存庫](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>備份原則

備份中心可讓您查看及編輯任何備份原則的金鑰資訊。

選取 [ **備份原則** ] 功能表項目，可讓您查看在整個備份資產中建立的所有原則。 您可以依保存庫訂用帳戶、資源群組、資料來源類型和保存庫來篩選清單。 以滑鼠右鍵按一下方格中的專案，可讓您查看該原則的相關專案、編輯原則，或甚至在必要時將其刪除。

![備份中心-原則](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>後續步驟

* [管理您的備份資產](backup-center-govern-environment.md)
* [使用備份中心執行動作](backup-center-actions.md)
* [取得您的備份見解](backup-center-obtain-insights.md)
