---
title: 保護 Azure SQL Edge
description: 瞭解 Azure SQL Edge 中的安全性
keywords: SQL Edge，安全性
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 17e3e8dca1c03f9783c0ca94350bb8a4ba5aca64
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934134"
---
# <a name="securing-azure-sql-edge"></a>保護 Azure SQL Edge

隨著跨產業採用 IoT 和邊緣運算的增加，裝置數量和從這些裝置產生的資料也會增加。 增加的資料量和裝置端點數目在資料和裝置的安全性方面都有很大的挑戰。 

Azure SQL Edge 提供多項特性和功能，可讓您更輕鬆地保護 SQL Server 資料庫內的 IoT 資料。 Azure SQL Edge 是使用相同的 SQL 引擎（可 Microsoft SQL Server 和 Azure SQL）來建立，並共用相同的安全性功能，可讓您更輕鬆地將相同的安全性原則和實務從雲端延伸至邊緣。

就像 Microsoft SQL Server 和 Azure SQL 一樣，您可以將 Azure SQL Edge 部署的安全性視為一系列涉及四個區域的步驟：平臺、驗證、物件 (包括資料) 以及存取系統的應用程式。 

## <a name="platform-and-system-security"></a>平臺和系統安全性

Azure SQL Edge 的平臺包含實體 docker 主機、主機上的作業系統，以及將實體裝置連接至應用程式和用戶端的網路系統。 

執行平臺安全性的起點是讓未經授權的使用者離開網路。 某些最佳作法包括（但不限於）：
- 執行防火牆規則，以確保組織安全性原則。 
- 確定實體裝置上的作業系統已套用所有最新的安全性更新。 
- 指定和限制使用於 Azure SQL Edge 的主機埠
- 確定會將適當的存取控制套用至裝載 Azure SQL Edge 資料的所有資料磁片區。 

如需 Azure SQL Edge 網路通訊協定和 TDS 端點的詳細資訊，請參閱 [網路通訊協定和 Tds 端點](https://docs.microsoft.com//previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105))。

## <a name="authentication-and-authorization"></a>驗證與授權 

### <a name="authentication"></a>驗證  
驗證是證明使用者宣告身分的程序。 Azure SQL Edge 目前僅支援此 `SQL Authentication` 機制。

- *SQL 驗證*：

    SQL 驗證指的是使用使用者名稱和密碼連接到 Azure SQL Edge 時的使用者驗證。 Sql Edge 部署期間必須指定 SQL **sa** 登入密碼。 在那之後，伺服器管理員可以建立其他 SQL 登入和使用者，可讓使用者使用使用者名稱和密碼進行連線。

    如需有關在 SQL Edge 中建立和管理登入和使用者的詳細資訊，請參閱 [建立登](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) 入和 [建立資料庫使用者](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-database-user)。

### <a name="authorization"></a>授權   

授權是指指派給 Azure SQL Edge 資料庫中之使用者的許可權，並決定允許使用者執行的動作。 許可權的控制方式是將使用者帳戶加入至 [資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) ，並將資料庫層級許可權指派給這些角色，或授與使用者某些 [物件層級許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)。 如需詳細資訊，請參閱登入 [和使用者](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)。

最佳做法是在需要時建立自訂角色。 將使用者新增至具有工作功能所需最低許可權的角色。 請勿直接將許可權指派給使用者。 伺服器系統管理員帳戶是內建 db_owner 角色的成員，其具有廣泛的許可權，而且只能授與給具有系統管理責任的少數使用者。 若為應用程式，請使用 [EXECUTE AS](https://docs.microsoft.com/sql/t-sql/statements/execute-as-clause-transact-sql) 來指定被呼叫模組的執行內容，或使用具有有限許可權的 [應用程式角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) 。 這種作法可確保連接至資料庫的應用程式具有應用程式所需的最低許可權。 遵循這些最佳作法也會促進責任的分隔。

## <a name="database-object-security"></a>資料庫物件安全性

主體是授與 SQL Edge 存取權的個人、群組和流程。 「安全性實體」則是指伺服器、資料庫及資料庫所包含的物件。 每個都有一組可設定的許可權，以協助減少介面區。 下表包含主體和安全性實體的相關資訊。

|如需下列資訊|請參閱|  
|---------------------------|---------|  
|伺服器與資料庫使用者、角色和處理序|[主體資料庫引擎](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|伺服器與資料庫物件安全性|[安全性實體](https://docs.microsoft.com/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>加密和憑證  
 
加密並不能解決存取控制問題。 但是，若發生存取控制失靈的罕見情形，加密則可限縮資料遺失的風險以增強安全性。 例如，只要資料已加密，即使資料庫主機電腦設定不當而遭惡意使用者取得敏感性資料 (如信用卡號)，失竊的資訊就可能毫無用處。 下表包含 Azure SQL Edge 加密的詳細資訊。  
  
|如需下列資訊|請參閱|  
|---------------------------|---------|  
|實作安全連接|[加密連接](https://docs.microsoft.com/sql/linux/sql-server-linux-encrypted-connections)|  
|加密函數|[密碼編譯函數 &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|待用資料加密|[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[一律加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> 針對 [Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-security-overview) 所述的安全性限制也適用于 Azure SQL Edge。 


> [!NOTE]
> Azure SQL Edge 不包含 mssql 檢測公用程式。 包括加密相關設定在內的所有設定都必須透過 [mssql](configure.md#configure-by-using-an-mssqlconf-file) 檔案或 [環境變數](configure.md#configure-by-using-environment-variables)來執行。 


與 Azure SQL 和 Microsoft SQL Server 類似，Azure SQL Edge 也提供相同的機制來建立和使用憑證，以增強物件和連線安全性。 如需詳細資訊，請參閱 [建立 (transact-sql) 的憑證 ](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql)。


## <a name="application-security"></a>應用程式安全性

### <a name="client-programs"></a>用戶端程式

Azure SQL Edge 安全性最佳作法包括撰寫安全的用戶端應用程式。 如需如何在網路層維護用戶端應用程式安全的詳細資訊，請參閱 [用戶端網路組態](https://docs.microsoft.com/sql/database-engine/configure-windows/client-network-configuration)。

### <a name="sql-server-security-catalog-views-and-functions"></a>SQL Server 安全性目錄的觀點和功能  
 安全性資訊會在數個針對效能和公用程式優化的視圖和功能中公開。 下表包含安全性檢視和函數的相關資訊。  
  
|函數和 views|連結|  
|---------------------------|---------|  
|安全性目錄檢視，會傳回資料庫層級和伺服器層級許可權、主體、角色等等的相關資訊。 此外，另有若干目錄檢視提供了加密金鑰、憑證與認證的相關資訊。|[安全性目錄檢視 &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|安全性函數，傳回目前使用者、許可權和架構的相關資訊。|[安全性函數 &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/t-sql/functions/security-functions-transact-sql)|  
|安全性動態管理的觀點。|[安全性相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>稽核 

Azure SQL Edge 提供與 SQL Server 相同的審核機制。 如需詳細資訊，請參閱 [SQL Server Audit (資料庫引擎) ](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)。


## <a name="next-steps"></a>下一步

- [具有安全性功能的消費者入門](https://docs.microsoft.com/sql/linux/sql-server-linux-security-get-started)
- [以非根使用者的身份執行 Azure SQL Edge](configure.md#run-azure-sql-edge-as-non-root-user)
- [適用於 IoT 的 Azure 資訊安全中心](https://docs.microsoft.com/azure/asc-for-iot/overview)

