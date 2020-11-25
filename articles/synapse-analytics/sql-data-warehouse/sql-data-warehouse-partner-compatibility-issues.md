---
title: 協力廠商應用程式和 Azure Synapse Analytics 的相容性問題
description: 說明協力廠商應用程式可使用 Azure Synapse 發現的已知問題
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 861b963922d64926266b42a23f85e9dc30fda7a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95903979"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>協力廠商應用程式和 Azure Synapse Analytics 的相容性問題

針對 SQL Server 所建立的應用程式可與 Azure Synapse 專用的 SQL 集區緊密搭配運作。 不過，在某些情況下，SQL Server 中常用的功能和語言元素可能無法在 Azure Synapse 中使用，或它們的行為可能不同。

本文列出搭配 Azure Synapse Analytics 使用協力廠商應用程式時，可能遇到的已知問題。 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau 錯誤：「嘗試完成交易失敗。 找不到對應的交易」

從 Azure Synapse 專用的 SQL 集區版本10.0.11038.0 開始，某些進行預存程序呼叫的 Tableau 查詢可能會失敗，並出現下列錯誤訊息： "**[Microsoft] [ODBC Driver 17 for SQL Server] [SQL Server] 111214;嘗試完成交易失敗。找不到對應的交易。**」

### <a name="cause"></a>原因

這是 Azure Synapse 專用 SQL 集區中的問題，因為引進了 ODBC 和 JDBC 驅動程式會自動呼叫的新系統預存程式。 這些系統預存程式的其中一個可能會在執行失敗時中止開啟的交易。 視用戶端應用程式邏輯而定，可能會發生此問題。

### <a name="solution"></a>解決方法
當您使用連線到 Azure Synapse 專用 SQL 集區的 Tableau 時，如果客戶看到此特定問題，則應在 SQL 連線中將 FMTONLY 設定為 YES。 針對 Tableau Desktop 和 Tableau 伺服器，您應該使用 Tableau 資料來源自訂 (TDC) 檔，以確保 Tableau 將此參數傳遞至驅動程式。  

> [!NOTE] 
> Microsoft 不提供協力廠商工具的支援。 雖然我們已測試過此解決方案與 Tableau Desktop 2020.3.2 搭配運作，但您應該將此因應措施用於您自己的容量。
>

* [若要瞭解如何在 Tableau 桌面上使用 TDC 檔案進行全域自訂，請參閱 Tableau Desktop 檔。](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [若要瞭解如何在 Tableau 伺服器上使用 TDC 檔案進行全域自訂，請參閱使用。具有 Tableau 伺服器的 TDC 檔。](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

下列範例顯示 Tableau 的 TDC 檔案，該檔案會將 FMTONLY = YES 參數傳遞至 SQL 連接字串：

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
如需有關使用 TDC 檔案的詳細資訊，請洽詢 Tableau 支援人員。 

## <a name="see-also"></a>另請參閱

* [Azure Synapse Analytics 中專用 SQL 集區的 t-sql 語言元素。](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics 中的專用 SQL 集區支援 t-sql 語句。](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
