---
title: 在本機讀取和更新可靠的集合備份
description: 使用 Azure 中的備份 Explorer Service Fabric 讀取和更新本機可靠的集合備份。
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86034821"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>使用 Backup Explorer 讀取和更新可靠的集合備份

如果 Service Fabric 可靠的集合中的資料已損毀，Azure Service Fabric Backup Explorer 可協助進行資料更正。 目前的資料狀態可能會因應用程式中引入的任何 bug 或在即時叢集中的任何錯誤專案而損毀。

利用 Backup Explorer 的協助，您可以執行下列工作：
-   查詢集合的中繼資料。
-   在載入的備份組合中，查看目前的狀態及其專案。
-   列出自上一個檢查點之後執行的交易。
-   藉由新增、更新或刪除集合中的專案來更新集合。
-   使用更新的狀態進行全新的備份。

> [!NOTE]
> Service Fabric Backup Explorer 目前僅支援在備份中查看和編輯可靠的集合。
>

## <a name="access-the-backup"></a>存取備份

您可以使用下列任何一種方式來取用 Backup Explorer Service Fabric，以在備份中查看或更新可靠的集合：
-   **Binary**：使用 NuGet 封裝來查看和更改可靠的集合。
-   **Http/REST**：使用以 HTTP 為基礎的 rest 伺服器來查看和更改可靠的集合。
-   **bkpctl**：使用 Service Fabric Backup Explorer 命令列介面 (CLI) 來查看和變更可靠的集合備份。

Backup Explorer 有 advanced users 的 c # 程式庫。 您可以直接在應用程式中使用此程式庫來處理可靠的集合，其方式類似于客戶在其現有具狀態服務中使用狀態管理員的方式。 針對基本使用者和基本使用案例，explorer 也有獨立的 REST 伺服器，可公開用來檢查備份的 Api。 Bkpctl CLI 工具適用于 REST Api 之上，並以 Python 為基礎。 您可以使用 CLI 工具來讀取和更新備份，以及透過命令列進行新的備份。

如需詳細資訊，請參閱 [Service Fabric Backup Explorer](https://github.com/microsoft/service-fabric-backup-explorer) GitHub 存放庫。 存放庫包含來源和版本資訊，以及設定指示。

您也可以在本機建立存放庫，並處理備份。
 
Backup Explorer (ServiceFabric. ReliableCollectionBackup) 的 NuGet 將可在 [nuget.org](https://www.nuget.org/)上取得。 

## <a name="next-steps"></a>接下來的步驟

* [深入瞭解 Azure 中的可靠集合 Service Fabric 具狀態服務](service-fabric-reliable-services-reliable-collections.md)。
* 複習 [Service Fabric 的最佳做法](service-fabric-best-practices-overview.md)。
