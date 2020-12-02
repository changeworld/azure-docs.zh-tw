---
title: 連接到 Azure Analysis Services 伺服器 | Microsoft Docs
description: 了解如何在 Azure 連線至 Analysis Services 伺服器並從該伺服器中取得資料。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499419"
---
# <a name="connecting-to-servers"></a>連線至伺服器

本文說明如何使用資料模型化和管理應用程式（例如 SQL Server Management Studio (SSMS) 或使用 Analysis Services 專案的 Visual Studio，或用戶端報表應用程式（例如 Microsoft Excel、Power BI Desktop 或自訂應用程式）來連接到伺服器。 連到 Azure Analysis Services 的連線會使用 HTTPS。

## <a name="client-libraries"></a>用戶端程式庫

[取得最新的用戶端程式庫](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

所有連到伺服器的連線 (不論是哪一種類型) 都需要已更新的 AMO、ADOMD.NET 及 OLEDB 用戶端程式庫，才能連接到 Analysis Services 伺服器並與其銜接。 針對 SSMS、Visual Studio、Excel 2016 和更新版本，以及 Power BI，最新的用戶端程式庫會隨著每月版本安裝或更新。 不過，在某些情況下，應用程式的版本可能不會是最新的。 例如，當原則延遲更新，或 Microsoft 365 更新在延遲通道上時。

> [!NOTE]
> 用戶端程式庫無法透過需要使用者名稱和密碼的 proxy 伺服器連線到 Azure Analysis Services。 

## <a name="server-name"></a>伺服器名稱

當您在 Azure 中建立 Analysis Services 伺服器時，您可以指定唯一的名稱和要建立伺服器的區域。 在連線中指定伺服器名稱時，伺服器命名配置為：

```
<protocol>://<region>/<servername>
```
 其中 protocol 是字串 **asazure**，region 是伺服器建立位置的 Uri (例如 westus.asazure.windows.net)，而 servername 則是該區域內您唯一伺服器的名稱。

### <a name="get-the-server-name"></a>取得伺服器名稱

在 [Azure 入口網站] > 伺服器 > [概觀]  >  [伺服器名稱] 中，複製整個伺服器名稱。 如果您組織中的其他使用者也會連線到這部伺服器，您可以將此伺服器名稱告訴他們。 指定伺服器名稱時，必須使用完整路徑。

![在 Azure 中取得伺服器名稱](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> 美國東部2區域的通訊協定為 **aspaaseastus2**。

## <a name="connection-string"></a>連接字串

使用表格式物件模型連線至 Azure Analysis Services 時，請使用下列連接字串格式：

###### <a name="integrated-azure-active-directory-authentication"></a>整合型 Azure Active Directory 驗證

整合型驗證會挑選 Azure Active Directory 認證快取 (若有的話)。 如果沒有，則會顯示 Azure 登入視窗。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>以使用者名稱與密碼進行的 Azure Active Directory 驗證

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows 驗證 (整合式安全性)

請使用執行目前程序的 Windows 帳戶。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>使用 .odc 檔案進行連接

在使用舊版 Excel 的情況下，使用者可以使用「Office 資料連線」(.odc) 檔案來連接到 Azure Analysis Services 伺服器。 若要深入了解，請參閱[建立 Office 資料連線 (.odc) 檔案](analysis-services-odc.md)。

## <a name="connect-as-a-linked-server-from-sql-server"></a>從 SQL Server 連接到連結伺服器

SQL Server 可以將 MSOLAP 指定為數據源提供者，以 [連結伺服器](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) 的形式連接至 Azure Analysis Services 資源。 設定連結的伺服器連線之前，請務必安裝最新的 [MSOLAP 用戶端程式庫](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (提供者) 。 

針對與 Azure Analysis Services 的連結伺服器連接，MSOLAP 提供者必須在 SQL Server 進程之外具現化。 設定連結的伺服器選項時，請確定 **未選取**[**允許進程進程**] 選項。

如果已選取 [ **允許進程進程** ]，並且在 SQL Server 進程中具現化提供者，則會傳回下列錯誤：

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>後續步驟

[使用 Excel 連接](analysis-services-connect-excel.md)    
[使用 Power BI 連接](analysis-services-connect-pbi.md)   
[管理您的伺服器](analysis-services-manage.md)