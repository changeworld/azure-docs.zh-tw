---
title: 連接和查詢-單一伺服器 MySQL
description: Azure My SQL Database 快速入門的連結，顯示如何連線至您的伺服器並執行查詢。
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 229011f11ad6898555f59b063910d80a679070e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936628"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>適用于 MySQL 的 Azure 資料庫的連線和查詢總覽-單一伺服器
下列檔包含範例的連結，示範如何使用適用於 MySQL 的 Azure 資料庫單一伺服器進行連接和查詢。 本指南也包含 TLS 建議和程式庫，可讓您用來連接到下列支援語言的伺服器。

## <a name="quickstarts"></a>快速入門

| 快速入門 | 描述 |
|---|---|
|[MySQL 工作臺](connect-workbench.md)|本快速入門示範如何使用 MySQL 工作臺用戶端連接到資料庫。 然後，您可以使用 MySQL 語句來查詢、插入、更新和刪除資料庫中的資料。|
|[Azure Cloud Shell](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|本文說明如何在[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)中執行**mysql.exe** ，以連接到您的伺服器，然後執行語句來查詢、插入、更新和刪除資料庫中的資料。|
|[具有 Visual Studio 的 MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|您可以使用 MySQL 進行 Visual Studio，以連接到您的 MySQL 伺服器。 適用于 Visual Studio 的 MySQL 可直接整合到伺服器總管，讓您輕鬆地設定新的連接並使用資料庫物件。|
|[PHP](connect-php.md)|本快速入門示範如何使用 PHP 建立程式，以連接到資料庫並使用 MySQL 語句來查詢資料。|
|[Java](connect-java.md)|本快速入門示範如何使用 JAVA 來連接到資料庫，然後使用 MySQL 語句來查詢資料。|
|[Node.js](connect-nodejs.md)|本快速入門示範如何使用 Node.js 建立程式，以連接到資料庫並使用 MySQL 語句來查詢資料。|
|[.NET (C # ) ](connect-csharp.md)|本快速入門示範如何 use.NET (c # ) 來建立 c # 程式，以連接到資料庫並使用 MySQL 語句來查詢資料。|
|[Go](connect-go.md)|此快速入門示範如何使用「移至連接」進入資料庫。 也會示範用以查詢及修改資料的 TRANSACT-SQL 陳述式。|
|[Python](connect-python.md)|本快速入門示範如何使用 Python 來連接到資料庫，並使用 MySQL 語句來查詢資料。 |
|[Ruby](connect-ruby.md)|本快速入門示範如何使用 Ruby 建立程式，以連接到資料庫並使用 MySQL 語句來查詢資料。|
|[C++](connect-cpp.md)|本快速入門示範如何使用 c + + + 來建立程式，以連接到資料庫並使用查詢資料。|


## <a name="tls-considerations-for-database-connectivity"></a>資料庫連線的 TLS 考量

 (TLS) 的傳輸層安全性，由 Microsoft 所提供或支援的所有驅動程式用來連接到適用於 MySQL 的 Azure 資料庫中的資料庫。 不需要特殊設定，但會針對新建立的伺服器強制使用 TLS 1.2。 如果您使用 TLS 1.0 和1.1，則建議您補救伺服器的 TLS 版本。 瞭解 [ 如何設定 TLS](howto-tls-configurations.md)


## <a name="libraries"></a>程式庫

適用於 MySQL 的 Azure 資料庫使用世界上最熱門的 MySQL 資料庫社群版本。 因此，它與各種程式設計語言和驅動程式相容。 目標是支援三個最新版本的 MySQL 驅動程式，並繼續與開放原始碼社群作者一起努力，持續改善 MySQL 驅動程式的功能和可用性。

查看哪些 [驅動程式](concepts-compatibility.md) 與適用於 MySQL 的 Azure 資料庫單一伺服器相容。 


## <a name="next-steps"></a>後續步驟 
- [使用傾印和還原來遷移資料](concepts-migrate-dump-restore.md)
- [使用匯入和匯出來遷移資料](concepts-migrate-import-export.md)
