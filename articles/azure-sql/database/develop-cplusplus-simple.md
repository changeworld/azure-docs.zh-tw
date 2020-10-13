---
title: 使用 C 和 C++ 連接到 SQL Database
description: 使用這個快速入門中的範例程式碼建置現代應用程式，這個應用程式使用 C++，並受到具有 Azure SQL Database 的雲端中之強大關聯式資料庫的支援。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 38e4839a41ad8e58e575e552e877303a5105ff36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443670"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>使用 C 和 C++ 連接到 SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

這篇文章的目標是要嘗試連接到 Azure SQL Database 的 C 和 c + + 開發人員。 它會分成區段，讓您可以跳到您最感興趣的區段。

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ 教學課程的必要條件

請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費 Azure 試用](https://azure.microsoft.com/pricing/free-trial/)。
* [Visual Studio](https://www.visualstudio.com/downloads/)。 您必須安裝 C++ 語言元件以建置並執行此範例。
* [Visual Studio Linux 開發](https://docs.microsoft.com/cpp/linux/?view=vs-2019)。 如果您要在 Linux 上進行開發，必須也安裝 Visual Studio Linux 擴充功能。

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>虛擬機器上的 Azure SQL Database 和 SQL Server

Azure SQL Database 是以 Microsoft SQL Server 為基礎，其設計目的是要提供高可用性、高效能且可擴充的服務。 在內部部署執行的專屬資料庫上使用 Azure SQL 有許多優點。 使用 Azure SQL 時，您不需要安裝、設定、維護或管理您的資料庫，而只需要安裝內容和資料庫的結構。 我們所擔心像容錯和冗餘等資料庫的一般項目皆已內建。

Azure 目前有兩個選項可裝載 SQL server 工作負載： Azure SQL Database、資料庫即服務，以及虛擬機器上的 SQL server (VM) 。 除了這兩者之間的差異外，我們也不會詳細說明這兩者之間的差異，但 Azure SQL Database 是您最適合新的雲端應用程式，以充分利用雲端服務所提供的成本節省和效能優化。 如果您考慮將您的內部應用程式移轉或擴充至雲端，Azure 虛擬機器上的 SQL Server 可能較合適您。 為了讓本文保持簡單，讓我們建立一個 Azure SQL Database。

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>資料存取技術︰ODBC 和 OLE DB

連接到 Azure SQL Database 並無不同，目前有兩種方式可以連接到資料庫： ODBC (開放式資料庫連接) 以及 OLE DB (物件連結和內嵌資料庫) 。 近年來，Microsoft 已配合 [ODBC 進行原生關聯式資料存取](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/)。 ODBC 相當簡單，而且也比 OLE DB 更快速。 唯一必須注意的是 ODBC 會使用舊的 C 樣式 API。

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>步驟 1：建立 Azure SQL Database

請參閱 [快速入門頁面](single-database-create-quickstart.md) ，以了解如何建立範例資料庫。  或者，您可以遵循這 [段簡短的兩分鐘影片](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) ，使用 Azure 入口網站來建立 Azure SQL Database。

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>步驟 2：取得連接字串

布建您的 Azure SQL Database 之後，您必須執行下列步驟來判斷連線資訊，並新增您的用戶端 IP 以進行防火牆存取。

在 [Azure 入口網站](https://portal.azure.com/)中，移至您的 Azure SQL Database ODBC 連接字串，方法是使用在資料庫的 [總覽] 區段中列出的 [ **顯示資料庫連接字串** ]：

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

複製 **ODBC (包含 Node.js) [SQL 驗證]** 字串的內容。 我們稍後要使用此字串從我們的 C++ ODBC 命令列轉譯器進行連接。 此字串會提供詳細資料，例如驅動程式、伺服器和其他資料庫連接參數。

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>步驟 3︰將您的 IP 新增至防火牆

移至您伺服器的 [防火牆] 區段，並 [使用下列步驟將用戶端 IP 新增至防火牆](firewall-configure.md) ，以確定我們可以建立成功的連接：

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

至此，您已設定 Azure SQL Database，並已準備好從您的 c + + 程式碼進行連接。

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>步驟 4：從 Windows C/C++ 應用程式連接

您可以使用以 Visual Studio 建立的這個範例，輕鬆地 [使用 Windows 上的 ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) 連接到您的 Azure SQL Database。 此範例會執行 ODBC 命令列解譯器，以用來連接到我們的 Azure SQL Database。 這個範例會採用資料庫來源名稱 (DSN) 檔案做為命令列引數，或是我們稍早從 Azure 入口網站複製的詳細資訊連接字串。 開啟此專案的屬性頁，然後貼上連接字串做為命令引數，如下所示︰

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

請確定您提供的資料庫驗證詳細資料是的正確，做為該資料庫連接字串的一部分。

啟動應用程式來建置它。 您應該會看到下列驗證成功連線的視窗。 您甚至可以執行一些基本的 SQL 命令，例如**建立資料表**來驗證您的資料庫連線︰

![SQL 命令](./media/develop-cplusplus-simple/sqlcommands.png)

或者，您可以使用當未提供任何命令列引數時會啟動的精靈來建立 DSN 檔案。 我們建議您也試用此選項。 您可以使用此 DSN 檔案進行自動化及保護您的驗證設定︰

![建立 DSN 檔案](./media/develop-cplusplus-simple/datasource.png)

恭喜！ 您現在已在 Windows 上使用 C++ 和 ODBC 成功連接至 Azure SQL。 您可以繼續閱讀，針對 Linux 平台上執行相同的作業。

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>步驟 5：從 Linux C/C++ 應用程式連接

如果您還沒聽說過新聞，Visual Studio 現在也可讓您開發 c + + Linux 應用程式。 您可以在 [Linux 開發的 Visual C++](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) 部落格中閱讀這個新的案例。 若要針對 Linux 建置，您需要正在執行您 Linux 散發版本的遠端電腦。 如果您沒有可用的帳戶，您可以使用 [Linux Azure 虛擬機器](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)快速設定一個。

針對本教學課程，讓我們假設您已設定 Ubuntu 16.04 Linux 散發套件。 以下步驟應該也適用於 Ubuntu 15.10、Red Hat 6 和 Red Hat 7。

下列步驟會安裝適用於您散發的 SQL 和 ODBC 所需的程式庫︰

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

啟動 Visual Studio。 在 [工具] -> [選項] -> [跨平台] -> [連線管理員] 下，將連線新增至 Linux 方塊︰

![工具選項](./media/develop-cplusplus-simple/tools.png)

在建立透過 SSH 的連線之後，建立一個空白專案 (Linux) 範本︰

![新增專案範本](./media/develop-cplusplus-simple/template.png)

接著，您可以新增[ C 原始程式檔，並取代為此內容](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)。 使用 ODBC API SQLAllocHandle、SQLSetConnectAttr 和 SQLDriverConnect，您應該能夠初始化並建立您資料庫的連接。
像是利用 Windows ODBC 範例，您需要將 SQLDriverConnect 呼叫取代為您先前從 Azure 入口網站複製的資料庫連接字串參數的詳細資料。

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

在編譯之前最後一件要做的事是新增 **odbc** 做為程式庫相依性︰

![新增 ODBC 做為輸入程式庫](./media/develop-cplusplus-simple/lib.png)

若要啟動您的應用程式，從**偵錯**功能表開啟 Linux 主控台︰

![Linux 主控台](./media/develop-cplusplus-simple/linuxconsole.png)

如果您的連線成功，現在應該會看到在 Linux 主控台列印的目前資料庫名稱︰

![Linux 主控台視窗輸出](./media/develop-cplusplus-simple/linuxconsolewindow.png)

恭喜！ 您已成功完成本教學課程，現在可以從 Windows 和 Linux 平臺上的 c + + 連接到您的 Azure SQL Database。

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>取得完整的 C/C++ 教學課程方案

您可以在 GitHub 中找到包含本文中所有範例的 GetStarted 方案：

* [ODBC C++ Windows 範例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)，下載 Windows C++ ODBC 範例以連接到 Azure SQL
* [ 範例](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)，下載 Linux C++ ODBC 範例以連接到 Azure SQL

## <a name="next-steps"></a>後續步驟

* 複習 [SQL Database 開發總覽](develop-overview.md)
* 更多 [ODBC API 參考](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)的相關資訊

## <a name="additional-resources"></a>其他資源

* [多租用戶 SaaS 應用程式與 Azure SQL Database 的設計模式](saas-tenancy-app-design-patterns.md)
* 瀏覽所有 [SQL Database 的能力](https://azure.microsoft.com/services/sql-database/)
