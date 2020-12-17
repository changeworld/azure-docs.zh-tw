---
title: 什麼是適用于 Azure NetApp Files Azure 應用程式一致的快照集工具 |Microsoft Docs
description: 提供可與 Azure NetApp Files 搭配使用 Azure 應用程式一致快照集工具的簡介。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632608"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>什麼是 Azure 應用程式一致的快照集工具 (預覽) 

Azure 應用程式一致的快照集工具 (AzAcSnap) 是一種命令列工具，可讓您在 Linux 環境中簡化協力廠商資料庫 (SAP Hana) 的資料保護 (例如，SUSE 和 RHEL) 。  

## <a name="benefits-of-using-azacsnap"></a>使用 AzAcSnap 的優點

AzAcSnap 會利用 Azure NetApp Files 和 Azure 大型實例中的磁片區快照和複寫功能。  其提供下列優點：

- **應用程式一致的資料保護** AzAcSnap 是備份重要資料庫檔案的集中式解決方案。 在執行儲存體磁片區快照集之前，它可確保資料庫一致性。 如此一來，就能確保儲存體磁片區快照集可以用於資料庫復原。
- **資料庫目錄管理** 當您將 AzAcSnap 與具有內建備份目錄的資料庫搭配使用時，目錄內的記錄會保留在儲存體快照集的最新狀態。  這項功能可讓資料庫管理員查看備份活動。
- **特定磁片區保護** 這項功能對於不需要應用程式停止的非資料庫磁片區很有説明，必須先建立儲存體快照集。  範例包括 SAP Hana 記錄備份磁片區或 SAPTRANS 磁片區。
- **複製存放磁片區** 這項功能可針對開發和測試目的提供空間有效率的儲存體磁片區複製。
- **支援** 嚴重損壞修復AzAcSnap 利用存放裝置磁片區複寫來提供選項，以在遠端網站上復原已複寫的應用程式一致快照集。

AzAcSnap 是單一二進位檔。  它不需要額外的代理程式或外掛程式，就能透過 Azure Resource Manager 和透過 SSH) 的 Azure 大型實例，與資料庫或儲存體 (的 Azure NetApp Files 進行互動。  AzAcSnap 必須安裝在可連接到資料庫和儲存體的系統上。  但是，安裝和設定的彈性可讓您進行單一集中式安裝或完全分散式安裝，並在每個資料庫安裝上安裝複本。

## <a name="architecture-overview"></a>架構概觀

AzAcSnap 可以安裝在與資料庫 (SAP Hana) 相同的主機上，也可以安裝在集中式系統上。  但是，必須有與資料庫伺服器的網路連線，以及適用于 azure 大型實例) 的 Azure NetApp Files 或 SSH 的儲存體後端 (Azure Resource Manager。

AzAcSnap 是輕量的應用程式，通常是從外部排程器執行。  在大部分的 Linux 系統上，這項作業是 `cron` 檔將著重的內容。  但排程器可能是替代工具，只要它可以匯入 `azacsnap` 使用者的 shell 設定檔即可。  匯入使用者的環境設定可確保檔案路徑和許可權已正確初始化。

## <a name="command-synopsis"></a>命令摘要

命令的一般格式如下： `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` 。

## <a name="command-options"></a>命令選項

命令選項如下所示，並將命令當作主要的專案符號，以及關聯的子命令做為縮排的專案符號：

- **`-h`** 提供擴充的命令列說明，包含 AzAcSnap 使用方式的範例。
- **`-c configure`** 此命令提供互動式 Q&樣式介面，以建立或修改 `azacsnap` 設定檔 (default = `azacsnap.json`) 。
  - **`--configuration new`** 將建立新的設定檔。
  - **`--configuration edit`** 將會編輯現有的設定檔。
  - 請參閱 [設定命令參考](azacsnap-cmd-ref-configure.md)。
- **`-c test`** 驗證設定檔和測試連線能力。
  - **`--test hana`**  測試與 SAP Hana 實例的連接。
  - **`--test storage`** 藉由在所有設定的磁片區上建立暫存儲存體快照集 `data` ，然後將它們移除，來測試與基礎儲存介面的通訊。
  - **`--test all`** 會依序執行 `hana` 和 `storage` 測試。
  - 請參閱 [測試命令參考](azacsnap-cmd-ref-test.md)。
- **`-c backup`** 是針對資料 (SAP Hana 資料磁片區執行資料庫一致儲存體快照集的主要命令) & 其他 (例如共用、記錄備份或開機) 磁片區。
  - **`--volume data`** 將設定檔 stanza 中的所有磁片區快照集 `dataVolume` 。
  - **`--volume other`** 將設定檔 stanza 中的所有磁片區快照集 `otherVolume` 。
  - 請參閱 [備份命令參考](azacsnap-cmd-ref-backup.md)。
- **`-c details`** 提供快照集或複寫的相關資訊。
  - **`--details snapshots`** 針對已設定的每個磁片區，提供快照集的基本詳細資料清單。
  - **`--details replication`** 提供有關從生產網站到災難復原網站的複寫狀態基本詳細資料。
  - 請參閱 [詳細資料命令參考](azacsnap-cmd-ref-details.md)。
- **`-c delete`** 此命令會刪除儲存體快照集或一組快照集。 您可以使用在 HANA Studio 中找到的 SAP Hana 備份識別碼或儲存體快照集名稱。 備份識別碼只會系結至 `hana` 為數據和共用磁片區建立的快照集。 否則，如果輸入快照集名稱，則會搜尋符合所輸入快照集名稱的所有快照集。
  - 請參閱 [刪除](azacsnap-cmd-ref-delete.md)。
- **`-c restore`** 提供兩種方法，可將快照集還原到磁片區，方法是根據快照集建立新的磁片區，或將磁片區回復為預覽狀態。
  - **`--restore snaptovol`** 根據目標磁片區上的最新快照集建立新的磁片區。
  - **`-c restore --restore revertvolume`** 根據最新的快照集，將目標磁片區還原為先前的狀態。
  - 請參閱 [還原命令參考](azacsnap-cmd-ref-restore.md)。
- **`[--configfile <configfilename>]`** 選用的命令列參數，以提供不同的 JSON 設定檔案名。  這特別適用于為每個 SID 建立個別的設定檔 (例如 `--configfile H80.json`) 。

## <a name="next-steps"></a>下一步

- [開始使用 Azure 應用程式一致的快照集工具](azacsnap-get-started.md)
