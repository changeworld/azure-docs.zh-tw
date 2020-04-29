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
ms.openlocfilehash: 741906bbe9de68459b2e4a704a243fde4771b3a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067318"
---
# <a name="sql-database-application-development-overview"></a>SQL Database 應用程式開發概觀

本文將逐步解說開發人員在撰寫程式碼以連接到 Azure SQL Database 時應注意的基本考量事項。 本文適用於 Azure SQL Database (單一資料庫、彈性集區、受控執行個體) 的所有部署模型。

> [!TIP]
> 若您需要設定 Azure SQL Database，請查看快速入門指南，以了解[單一資料庫](sql-database-single-database-quickstart-guide.md)和[受控執行個體](sql-database-managed-instance-quickstart-guide.md)。
>

## <a name="language-and-platform"></a>語言和平台

您可以使用各種[程式設計語言與平台](sql-database-connect-query.md)連線及查詢 Azure SQL Database。 您可以找到可用於連線至 Azure SQL Database 的[範例應用程式](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)。

您可以利用 [cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS Code](https://code.visualstudio.com/) 等開放原始碼工具。 此外，Azure SQL Database 使用 [Visual Studio](https://www.visualstudio.com/downloads/) 和 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) 等 Microsoft 工具。 您也可以使用 Azure 入口網站、PowerShell 和 REST API 協助您獲得額外生產力。

## <a name="authentication"></a>驗證

Azure SQL Database 的存取受到登入及防火牆的保護。 Azure SQL Database 支援 SQL Server 及 [Azure Active Directory (AAD) 驗證](sql-database-aad-authentication.md) 的使用者和登入。 AAD 登入僅適用於受控執行個體。 

深入了解[管理資料庫存取和登入](sql-database-manage-logins.md)。

## <a name="connections"></a>連接

在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。 預設 15 秒對於依賴網際網路的連線而言太短。

如果您使用的是 [連接集區](https://msdn.microsoft.com/library/8xx3tyca.aspx)，請確定在程式未主動使用時即時關閉連接，而不是準備重複使用連接。

請避免長時間執行的交易，因為任何基礎結構或連線失敗可能會復原交易。 可能的話，請將交易分割成多個較小的交易，並使用[批次處理來改善效能](sql-database-use-batching-to-improve-performance.md)。

## <a name="resiliency"></a>災害復原

Azure SQL Database 為雲端服務，因此基礎結構或雲端實體之間的通訊可能會發生暫時性錯誤。 雖然 Azure SQL Database 會從可轉移的基礎結構失敗中復原，但這些失敗可能會影響您的連線。 當連接到 SQL Database 發生暫時性錯誤時，您的程式碼應該[重試呼叫](sql-database-connectivity-issues.md)。 我們建議重試邏輯使用輪詢邏輯，因此它不會同時重試多個用戶端而讓 SQL Database 超過負荷。 重試邏輯取決於 [SQL Database 用戶端程式的錯誤訊息](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)。

如需關於如何對 Azure SQL 資料庫上的計劃性維護事件進行準備的詳細資訊，請參閱[規劃 Azure SQL Database 的 Azure 維護事件](sql-database-planned-maintenance.md)。

## <a name="network-considerations"></a>網路考量

- 在託管您的用戶端程式的電腦上，請確定防火牆允許連接埠 1433 上的傳出 TCP 通訊。  詳細資訊：[設定 Azure SQL Database 防火牆](sql-database-configure-firewall-settings.md)。
- 如果您的用戶端程式是在 Azure 虛擬機器 (VM) 上執行，而用戶端程式會連線至 SQL Database，您就必須開啟該 VM 上特定的連接埠範圍。 詳細資訊： [ADO.NET 4.5 和 SQL Database 的埠超過 1433](sql-database-develop-direct-route-ports-adonet-v12.md)。
- 與 Azure SQL Database 的用戶端連線有時會略過 proxy 並直接與資料庫互動。 1433 以外的連接埠變得重要。 如需詳細資訊，請參閱 [Azure SQL Database 連線架構](sql-database-connectivity-architecture.md)和[針對 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)一節。
- 如需受控實例的網路設定，請參閱[受控實例的網路](sql-database-howto-managed-instance.md#network-configuration)設定。

## <a name="next-steps"></a>後續步驟

探索 SQL Database 的所有[功能](sql-database-technical-overview.md)。
