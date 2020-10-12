---
title: DNS 別名
description: 您的應用程式可以連接到伺服器名稱的別名，以進行 Azure SQL Database。 同時，您可以隨時變更別名所指向的 SQL Database，以便進行測試等等。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: d208a9b9f8e1cc16e2c72aa825a2daf88ad00176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86145652"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL Database 的 DNS 別名
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database 具有網域名稱系統 (DNS) 伺服器。 PowerShell 和 REST Api 接受[呼叫來建立和管理](#anchor-powershell-code-62x)[邏輯 SQL SERVER](logical-servers.md)名稱的 DNS 別名。

*DNS 別名*可以用來取代伺服器名稱。 用戶端程式可以在其連接字串中使用別名。 DNS 別名提供的轉譯層可將用戶端程式重新導向至不同伺服器。 此轉譯層讓您免去必須尋找和編輯所有用戶端及其連接字串的難處。

DNS 別名的常見用法包含下列案例：

- 為伺服器建立容易記住的名稱。
- 在初始開發期間，您的別名可以參考測試伺服器。 當應用程式上線時，您可以將別名修改為參照生產伺服器。 從測試到生產環境的轉換不需要修改數個連接到伺服器的用戶端。
- 假設您的應用程式中唯一的資料庫已移至另一部伺服器。 您可以修改別名，而不需要修改數個用戶端的設定。
- 在區域性中斷期間，您會使用異地還原來復原不同伺服器和區域中的資料庫。 您可以修改現有的別名以指向新的伺服器，讓現有的用戶端應用程式可以重新連接到該伺服器。

## <a name="domain-name-system-dns-of-the-internet"></a>網際網路的網域名稱系統 (DNS)

網際網路會依賴 DNS。 DNS 會將您的易記名稱轉譯為您伺服器的名稱。

## <a name="scenarios-with-one-dns-alias"></a>具有一個 DNS 別名的案例

假設您需要將系統切換至新的伺服器。 在過去，您需要尋找並更新每個用戶端程式中的每個連接字串。 但是現在，如果連接字串使用 DNS 別名，就只必須更新別名屬性。

Azure SQL Database 的 DNS 別名功能適用於下列案例：

### <a name="test-to-production"></a>測試到生產

當您開始開發用戶端程式時，讓這些程式在其連接字串中使用 DNS 別名。 您可以將別名的屬性設為伺服器的測試版本。

稍後當新系統上線于生產環境時，您可以更新別名的屬性以指向實際執行伺服器。 不需要變更用戶端程式。

### <a name="cross-region-support"></a>跨區域支援

嚴重損壞修復可能會將您的伺服器移至不同的地理區域。 針對使用 DNS 別名的系統，您可以避免尋找和更新所有用戶端的所有連接字串。 相反地，您可以將別名更新為參照現在裝載您 Azure SQL Database 的新伺服器。

## <a name="properties-of-a-dns-alias"></a>DNS 別名的屬性

下列屬性適用于您伺服器的每個 DNS 別名：

- *唯一名稱：* 您所建立的每個別名名稱在所有伺服器中都是唯一的，就像伺服器名稱一樣。
- *伺服器是必要的：* 無法建立 DNS 別名，除非該名稱參照正好一部伺服器，且該伺服器必須已存在。 更新後的別名必須一律參照正好一部現有的伺服器。
  - 當您卸載伺服器時，Azure 系統也會卸載所有參考該伺服器的 DNS 別名。
- *未繫結至任何區域：* DNS 別名未繫結至任何區域。 您可以更新任何 DNS 別名，以參考位於任何地理區域的伺服器。
  - 不過，將別名更新為參照另一部伺服器時，這兩部伺服器都必須存在於相同的 Azure 訂用帳戶** 中。
- *權限：* 若要管理 DNS 別名，使用者必須擁有「伺服器參與者」** 權限或更高階的權限。 如需詳細資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](../../role-based-access-control/overview.md)。

## <a name="manage-your-dns-aliases"></a>管理 DNS 別名

REST API 和 PowerShell Cmdlet 可讓您以程式設計方式管理 DNS 別名。

### <a name="rest-apis-for-managing-your-dns-aliases"></a>用於管理 DNS 別名的 REST API

下列 Web 位置附近可取得 REST API 的文件：

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

此外，在 GitHub 中也可以看到 REST API：

- [Azure SQL Database DNS 別名 REST Api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>用於管理 DNS 別名的 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

PowerShell Cmdlet 可用於呼叫 REST API。

用來管理 DNS 別名的 PowerShell Cmdlet 程式碼範例記載於：

- [適用於 Azure SQL Database 之 DNS 別名的 PowerShell](dns-alias-powershell-create.md)

程式碼範例中使用的 Cmdlet 如下所示：

- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)：在 Azure SQL Database service 系統中建立新的 DNS 別名。 別名是指伺服器1。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)：取得並列出所有指派給伺服器1的 DNS 別名。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias)：修改別名設定為參考的伺服器名稱，從伺服器1到伺服器2。
- [AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)：使用別名的名稱移除伺服器2中的 DNS 別名。

## <a name="limitations-during-preview"></a>預覽期間的限制

目前，DNS 別名具有下列限制：

- *最多 2 分鐘的延遲：* 更新或移除 DNS 別名最多需要 2 分鐘的時間。
  - 不論是否有任何短暫的延遲，別名都會立即停止讓用戶端連線參照舊版伺服器。
- *DNS 查閱：* 現在，若要檢查指定的 DNS 別名是參照哪部伺服器，唯一的權威性方式便是執行 [DNS 查閱](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)。
- _不支援資料表審核：_ 您無法在已啟用資料庫 *資料表審核* 的伺服器上使用 DNS 別名。
  - 資料表稽核功能已淘汰。
  - 建議您改用 [Blob 稽核](../../azure-sql/database/auditing-overview.md)。

## <a name="related-resources"></a>相關資源

- [Azure SQL Database 的業務連續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)，包括災害復原。
- [Azure REST API 參考](https://docs.microsoft.com/rest/api/azure/)
- [伺服器 Dns 別名 API](https://docs.microsoft.com/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>接下來的步驟

- [適用於 Azure SQL Database 之 DNS 別名的 PowerShell](dns-alias-powershell-create.md)
