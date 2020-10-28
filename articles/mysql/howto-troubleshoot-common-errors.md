---
title: 針對常見錯誤進行疑難排解 - 適用於 MySQL 的 Azure 資料庫
description: 了解如何針對新使用者在使用「適用於 MySQL 的 Azure 資料庫」服務時，所遇到的常見移轉錯誤進行疑難排解
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: f64d4d2b9acbe0e6585ca546c915b82d2d1dbbc4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737192"
---
# <a name="common-errors"></a>常見錯誤

適用於 MySQL 的 Azure 資料庫是完全受控的服務，由 MySQL 的社群版本提供技術支援。 在受管理的服務環境中，MySQL 體驗可能與您在自己環境中執行 MySQL 的體驗不同。 在本文中，您會看到使用者第一次在適用於 MySQL 的 Azure 資料庫服務上進行移轉或開發時，可能會遇到的一些常見錯誤。

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>因缺少 SUPER 權限和 DBA 角色而產生的錯誤

服務上不支援 SUPER 權限和 DBA 角色。 因此，您可能會遇到下列常見錯誤：

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERROR 1419：您沒有 SUPER 權限且二進位記錄已啟用 (您「可以」使用較不安全的 log_bin_trust_function_creators 變數)

上述錯誤可能發生在建立函式、觸發程序 (如下所示)，或匯入結構描述時。 CREATE FUNCTION 或 CREATE TRIGGER 之類的 DDL 陳述式會寫入二進位記錄中，因此次要複本可以執行這些陳述式。 複本 SQL 執行緒具有完整權限，可以利用其來提升權限。 若要在啟用二進位記錄的伺服器上抵禦此危險，MySQL 引擎的已儲存函式建立者除了需要一般的 CREATE ROUTINE 權限之外，還需要具有 SUPER 權限。 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**解決方法** ：若要解決此錯誤，請從入口網站的 [ [伺服器參數](howto-server-parameters.md)] 分頁中，將 llog_bin_trust_function_creators 設定為 1，執行 DDL 陳述式或匯入結構描述以建立所需的物件，並在建立之後將 log_bin_trust_function_creators 參數還原為先前的值。

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>第 101 行上發生 ERROR 1227 (42000)：拒絕存取；您需要此作業的 (至少其中一個) SUPER 權限。 作業失敗，結束代碼為 1

匯入傾印檔案或建立包含[定義者 (definer)](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) 的程式時，可能會發生上述錯誤。 

**解決方法** ：若要解決此錯誤，管理使用者可以藉由執行 GRANT 命令來對建立或執行程序授與權限，如下列範例所示：

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
或者，您可以用執行匯入程序的管理使用者名稱來取代定義者，如下所示。

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>後續步驟
如果您找不到所要尋找的答案，請考慮下列事項：
- 將問題張貼到 [Microsoft 問與答頁面](/answers/topics/azure-database-mysql.html)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)。
- 將電子郵件傳送給適用於 MySQL 的 Azure 資料庫小組 [@Ask 適用於 MySQL 的 Azure DB](mailto:AskAzureDBforMySQL@service.microsoft.com)。 此電子郵件地址不是技術支援的別名。
- 連絡 Azure 支援，[請從 Azure 入口網站提出票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 若要修正您的帳戶問題，請在 Azure 入口網站中提出[支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
- 若要提供意見反應或要求新功能，請透過 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 建立項目。
