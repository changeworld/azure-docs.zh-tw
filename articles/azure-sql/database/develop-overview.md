---
title: 應用程式開發概觀
description: 深入了解連接至 SQL Database 的應用程式的可用連線庫和最佳作法。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: b099158261de55c829ab2b89a2f994b35b3e50d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254033"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>應用程式開發總覽-SQL Database & SQL 受控執行個體

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文將逐步解說開發人員在撰寫程式碼以連接到 Azure 中的資料庫時，應注意的基本考慮。 本文適用于 Azure SQL Database 和 Azure SQL 受控執行個體。

## <a name="language-and-platform"></a>語言和平台

您可以使用各種[程式設計語言與平台](connect-query-content-reference-guide.md)連線及查詢 Azure SQL Database。 您可以尋找可以用來連接到資料庫的 [範例應用程式](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) 。

您可以利用 [cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS Code](https://code.visualstudio.com/) 等開放原始碼工具。 此外，Azure SQL Database 使用 [Visual Studio](https://www.visualstudio.com/downloads/) 和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) 等 Microsoft 工具。 您也可以使用 Azure 入口網站、PowerShell 和 REST API 協助您獲得額外生產力。

## <a name="authentication"></a>驗證

Azure SQL Database 的存取受到登入及防火牆的保護。 Azure SQL Database 支援 SQL Server 和 [Azure Active Directory 驗證](authentication-aad-overview.md) 使用者和登入。 Azure Active Directory 登入僅適用于 SQL 受控執行個體。 

深入了解[管理資料庫存取和登入](logins-create-manage.md)。

## <a name="connections"></a>連接

在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。 預設 15 秒對於依賴網際網路的連線而言太短。

如果您使用的是 [連接集區](https://msdn.microsoft.com/library/8xx3tyca.aspx)，請確定在程式未主動使用時即時關閉連接，而不是準備重複使用連接。

請避免長時間執行的交易，因為任何基礎結構或連線失敗可能會復原交易。 可能的話，請將交易分割成多個較小的交易，並使用[批次處理來改善效能](../performance-improve-use-batching.md)。

## <a name="resiliency"></a>災害復原

Azure SQL Database 為雲端服務，因此基礎結構或雲端實體之間的通訊可能會發生暫時性錯誤。 雖然 Azure SQL Database 會從可轉移的基礎結構失敗中復原，但這些失敗可能會影響您的連線。 當連接到 SQL Database 發生暫時性錯誤時，您的程式碼應該[重試呼叫](troubleshoot-common-connectivity-issues.md)。 我們建議重試邏輯使用輪詢邏輯，如此一來，就不會同時重試多個用戶端的服務。 重試邏輯取決於 [SQL Database 用戶端程式的錯誤訊息](troubleshoot-common-errors-issues.md)。

如需有關如何為 Azure SQL Database 上規劃的維護事件做準備的詳細資訊，請參閱 [Azure SQL Database 中的規劃 Azure 維護事件](planned-maintenance.md)。

## <a name="network-considerations"></a>網路考量

- 在託管您的用戶端程式的電腦上，請確定防火牆允許連接埠 1433 上的傳出 TCP 通訊。  詳細資訊：[設定 Azure SQL Database 防火牆](firewall-configure.md)。
- 如果您的用戶端程式是在 Azure 虛擬機器 (VM) 上執行，而用戶端程式會連線至 SQL Database，您就必須開啟該 VM 上特定的連接埠範圍。 詳細資訊： [超過1433的埠，適用于 ADO.NET 4.5 及 SQL Database](adonet-v12-develop-direct-route-ports.md)。
- 與 Azure SQL Database 的用戶端連線有時會略過 proxy 並直接與資料庫互動。 1433 以外的連接埠變得重要。 如需詳細資訊，請參閱 [Azure SQL Database 連線架構](connectivity-architecture.md)和[針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](adonet-v12-develop-direct-route-ports.md)一節。
- 如需 SQL 受控執行個體實例的網路設定，請參閱 [sql 受控執行個體的網路](../managed-instance/how-to-content-reference-guide.md#network-configuration)設定。

## <a name="next-steps"></a>後續步驟

探索 [SQL Database](sql-database-paas-overview.md) 和 [SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)的所有功能。

若要開始使用，請參閱 [Azure SQL Database](quickstart-content-reference-guide.md) 和 [Azure SQL 受控實例](../managed-instance/quickstart-content-reference-guide.md)的指南。
