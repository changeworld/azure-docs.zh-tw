---
title: 將 AdventureWorks 範例資料庫還原到 SQL 受控執行個體
description: 將 AdventureWorks 範例資料庫還原到 SQL 受控執行個體
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d238a889648e789087a0803f6b50288318462c7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629058"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>將 AdventureWorks 範例資料庫還原到 SQL 受控執行個體-Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=tsql&preserve-view=true) 是範例資料庫，其中包含通常用於教學課程和範例的 OLTP 資料庫。 它是由 Microsoft 提供並維護，作為 [SQL Server 範例 GitHub 存放庫](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

本檔說明將 AdventureWorks 範例資料庫還原到 SQL 受控執行個體 Azure Arc 的簡單程式。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>下載 AdventureWorks 備份檔案

將 AdventureWorks backup ( .bak) 檔案下載至您的 SQL 受控執行個體容器。 在此範例中，使用 `kubectl exec` 命令從遠端執行 SQL 受控執行個體容器內的命令，將 .bak 檔案下載到容器中。 `wget`如果您有想要提取的其他資料庫備份檔案，以在 SQL 受控執行個體容器內，請從可存取的任何位置下載此檔案。 一旦在 SQL 受控執行個體容器內，就很容易使用標準 T-sql 來還原 `RESTORE DATABASE` 。

執行如下所示的命令，下載 .bak 檔案，以取代 pod 名稱和命名空間名稱的值，然後再執行它。
> [!NOTE]
>  您的容器必須有網際網路連線能力超過443，才能從 GitHub 下載檔案

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

範例

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>還原 AdventureWorks 資料庫

同樣地，您可以執行 `kubectl` exec 命令，以使用 `sqlcmd` SQL 受控執行個體容器中所含的 CLI 工具來執行 t-sql 命令以還原資料庫。

執行如下的命令來還原資料庫。 執行之前，請先取代 pod 名稱、密碼和命名空間名稱的值。

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
範例

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
