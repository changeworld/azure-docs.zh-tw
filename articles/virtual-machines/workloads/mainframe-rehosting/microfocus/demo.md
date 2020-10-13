---
title: 設定微焦點 CICS BankDemo，以在 Azure 虛擬機器上進行微焦點企業開發人員4。0
description: 在 (Vm) 的 Azure 虛擬機器上執行微焦點 BankDemo 應用程式，以瞭解如何使用企業伺服器和企業開發人員的微型焦點。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7fb72b9a7d0d655f99d1e5cf194f7c6f26976a37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508044"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>設定微焦點 CICS BankDemo，以在 Azure 上取得微型企業開發人員4。0

當您在 Azure 上設定微焦點企業伺服器4.0 和企業開發人員4.0 時，可以測試 IBM z/OS 工作負載的部署。 本文說明如何設定 CICS BankDemo，這是 Enterprise Developer 隨附的範例應用程式。

CICs 代表客戶資訊控制系統，也就是許多線上大型主機應用程式所使用的交易平臺。 BankDemo 應用程式非常適合用來學習企業伺服器和企業開發人員的運作方式，以及如何管理和部署實際的應用程式，並以綠色螢幕終端機完成。

> [!NOTE]
> 即將推出：在 Azure Vm 上設定 [微焦點企業伺服器 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 的指示。

## <a name="prerequisites"></a>Prerequisites

- 具有 [企業開發人員](set-up-micro-focus-azure.md)的 VM。 請記住，企業開發人員有企業伺服器的完整實例，可供開發和測試之用。 這個實例是用於示範的 Enterprise Server 實例。

- [SQL Server 2017 Express 版](https://www.microsoft.com/sql-server/sql-server-editions-express)。 在企業開發人員 VM 上下載並安裝它。 企業伺服器需要管理 CICS 區域的資料庫，而 BankDemo 應用程式也會使用名為 BANKDEMO 的 SQL Server 資料庫。 此示範假設您使用的是兩個資料庫的 SQL Server Express。 安裝時，請選取基本安裝。

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS) 。 SSMS 可用來管理資料庫和執行 T-sql 腳本。 在企業開發人員 VM 上下載並安裝它。

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) 與最新的 service pack 或 [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)，您可以免費下載。

- Rumba 桌面或另一個3270模擬器。

## <a name="configure-the-windows-environment"></a>設定 Windows 環境

在 VM 上安裝 Enterprise Developer 4.0 之後，您必須設定隨附的 Enterprise Server 實例。 若要這樣做，您需要安裝一些額外的 Windows 功能，如下所示。

1. 使用 RDP 登入您所建立的 Enterprise Server 4.0 VM。

2. 按一下 [**開始**] 按鈕旁的 [**搜尋**] 圖示，然後輸入 [ **Windows 功能**]。 隨即開啟 [新增角色及功能] 伺服器管理員。

3. 選取 [ **Web 服務器 (IIS) 角色**]，然後選取下列選項：

    - Web 管理工具
    - IIS 6 管理相容性 (選取所有可用功能) 
    - IIS 管理主控台
    - IIS 管理指令碼及工具
    - IIS 管理服務

4. 選取 [ **World Wide Web 服務**]，並檢查下列選項：

     應用程式開發功能：
    - .NET 擴充性
    - ASP.NET
    - 一般 HTTP 功能：新增所有可用的功能
    - 健康情況和診斷：新增所有可用的功能
    - 安全性：
        - 基本驗證
        - Windows 驗證

5. 選取 [ **Windows 進程啟用服務** ] 及其所有子系。

6. 如需 **功能**，請檢查 **Microsoft .net framework 3.5.1**，並檢查下列選項：

    - Windows Communication Foundation HTTP 啟用
    - Windows Communication Foundation 非 HTTP 啟用

7. 如需 **功能**，請檢查 **Microsoft .net framework 4.6**，並檢查下列選項：

   - 具名管道啟用
   - TCP 啟用
   - TCP 連接埠共用

     ![新增角色及功能 Wizard：角色服務](media/01-demo-roles.png)

8. 當您選取所有選項之後，請按 **[下一步]** 安裝。

9. 在 Windows 功能之後，請移至 **主控台 \> 系統和安全性系統 \> 管理工具**]，然後選取 [ **服務**]。 向下滾動並確定下列服務正在執行，並設定為 **自動**：

    - **NetTcpPortSharing**
    - **Net.Pipe 接聽程式配接器**
    - **Net.tcp 接聽程式介面卡**

10. 若要設定 IIS 和 WAS 支援，請從功能表找到 **微型焦點企業開發人員命令提示字元 (64 位) ** 然後以 **系統管理員**身分執行。

11. 輸入 **wassetup – i** ，然後按 **enter**。

12. 腳本執行之後，您可以關閉視窗。

## <a name="configure-the-local-system-account-for-sql-server"></a>設定 SQL Server 的本機系統帳戶

某些企業伺服器處理常式必須能夠登入 SQL Server 並建立資料庫和其他物件。 這些進程會使用本機系統帳戶，因此您必須將系統管理員授權授與該帳戶。

1. 啟動 **SSMS** ，然後按一下 **[** 連線]，使用 Windows 驗證連接到本機 SQLEXPRESS 伺服器。 它應該會出現在 [ **伺服器名稱** ] 清單中。

2. 展開左側的 [ **安全性** ] 資料夾， **然後選取 [** 登入]。

3. 選取 [ **NT 授權單位 \\ 系統** ]，然後選取 [ **屬性**]。

4. 選取 [ **伺服器角色** ] 並檢查 **系統管理員（sysadmin**）。

     ![SSMS 物件總管視窗：登入屬性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>建立 BankDemo 資料庫及其所有物件

1. 開啟 **Windows 檔案總管** 並流覽至 **C： \\ Users \\ Public \\ Documents \\ 微焦點 \\ 企業開發人員 \\ 範例 \\ 大型主機 \\ CICS \\ DotNet \\ BankDemo \\ SQL**。

2. 將 **BankDemoCreateAll** 的內容複寫到剪貼簿。

3. 開啟 **SSMS**。 按一下右側的 [ **伺服器** ]，然後選取 [追加 **查詢**]。

4. 將剪貼簿的內容貼到 **新的查詢** 方塊中。

5. 從查詢上方的**命令**索引標籤中按一下 [**執行**]，以執行 SQL。

查詢應該會執行，且不會發生任何錯誤。 完成時，您會有 BankDemo 應用程式的範例資料庫。

![SQLQuery1 .sql 輸出](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>確認已建立資料庫資料表和物件

1. 以滑鼠右鍵按一下 **BANKDEMO** 資料庫，然後 **選取 [** 重新整理]。

2. 展開 **資料庫** ，然後選取 [ **資料表]**。 您應該會看到如下所示的內容。

     ![物件總管中展開的 BANKDEMO 資料表](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>在企業開發人員中建立應用程式

1. 開啟 Visual Studio 並登入。

2. **在 [檔案**] 功能表選項下，選取 [**開啟專案/方案**]，流覽至**C： \\ 使用者 \\ 公用 \\ 檔 \\ 微焦點 \\ 企業開發人員 \\ 範例 \\ 大型主機 \\ CICS \\ DotNet \\ BankDemo**，然後選取 **.sln**檔案。

3. 請花一些時間檢查物件。 COBOL 程式會顯示在使用 CBL 擴充功能的方案總管中，以及 CopyBooks (CPY) 和 JCL。

4. 以滑鼠右鍵按一下 [ **BankDemo2** ] 專案，然後選取 [ **設定為啟始專案**]。

    > [!NOTE]
    > BankDemo 專案會使用 SQL Server) 的 HCOSS (Host 相容性選項，這不適用於此示範。

5. 在 **方案總管**中，以滑鼠右鍵按一下 **BankDemo2** 專案，然後選取 [ **建立**]。

    > [!NOTE]
    > 由於尚未設定 HCOSS，因此在解決方案層級建立會導致錯誤。

6. 建立專案時，請檢查 [ **輸出** ] 視窗。 該應用程式應該類似下圖。

     ![顯示成功組建的輸出視窗](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>將 BankDemo 應用程式部署到區域資料庫

1. 以系統管理員身分)  (64 位來開啟企業開發人員命令提示字元。

2. 流覽至 **% PUBLIC% \\ Documents \\ 微焦點 \\ 企業開發人員 \\ 範例 \\ 大型主機 \\ CICS \\ DotNet \\ BankDemo**。

3. 在命令提示字元中，執行 **bankdemodbdeploy** 並包含要部署之資料庫的參數，例如：

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 請務必使用正斜線 (/) 不是反斜線 (\\) 。 此腳本會執行一段時間。

![管理：企業開發人員命令提示字元視窗](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在適用于 .NET 的企業系統管理員中建立 BankDemo 區域

1. 開啟 **適用于 .net 系統管理 UI 的 Enterprise Server** 。

2. 若要啟動 MMC 嵌入式管理單元，請從 Windows [ **開始** ] 功能表中，選擇 [ **適用于 .net 系統管理員的微焦點 enterprise Developer \> Configuration \> enterprise Server**]。針對 [Windows Server (] 選擇 [ **微焦點 Enterprise developer \> enterprise server for .net 系統管理員** ]) 。

3. 展開左窗格中的 [ **區域** ] 容器，然後以滑鼠右鍵按一下 [ **CICS**]。

4. 選取 [ **定義區域** ]，以建立名為 **BANKDEMO**的新 CICS 區域（裝載于 (本機) 資料庫中）。

5. 提供資料庫伺服器實例，然後按 [ **下一步]**，再輸入區功能變數名稱稱 **BANKDEMO**。

     ![定義區域對話方塊](media/07-demo-cics.png)

6. 若要選取跨區域資料庫的區域定義檔，請在**C： \\ Users \\ Public \\ Documents \\ 微專注于 \\ 企業開發人員 \\ 範例 \\ 大型主機 \\ CICS \\ DotNet \\ bankdemo**中，找出**region \_ bankdemo \_db.config** 。

     ![定義區域區功能變數名稱稱： BANKDEMO](media/08-demo-cics.png)

7. 按一下 [完成] 。

## <a name="create-xa-resource-definitions"></a>建立 XA 資源定義

1. 在 **適用于 .Net 系統管理 UI 的 Enterprise Server** 的左窗格中，展開 [ **系統**]，然後依序展開 [ **XA 資源定義**]。 此設定會定義區域與企業伺服器和應用程式資料庫之間的互通性。

2. 以滑鼠右鍵按一下 **XA 資源定義** ，然後選取 [ **新增伺服器實例**]。

3. 在下拉式清單方塊中，選取 [ **資料庫服務實例**]。 它將會是本機電腦 SQLEXPRESS。

4. 從 XA 資源定義底下選取實例， ** (machinename \\ sqlexpress) ** 容器，然後按一下 [ **新增**]。

5. 選取 [ **資料庫 XA 資源定義** ]，然後輸入 **BANKDEMO** 作為 [ **名稱** ] 和 [ **區域**]。

     ![新增資料庫 XA 資源定義畫面](media/09-demo-xa.png)

6. 按一下省略號 (**...**) 以顯示連接字串 wizard。 在 [ **伺服器名稱**] 中，輸入 ** (local) \\ SQLEXPRESS**。 若為 **登**入，請選取 [ **Windows 驗證**]。 針對 [資料庫名稱]，輸入 **BANKDEMO**

     ![編輯連接字串畫面](media/10-demo-string.png)

7. 測試連接。

## <a name="start-the-bankdemo-region"></a>啟動 BANKDEMO 區域

> [!NOTE]
> 第一個步驟很重要：您必須將區域設定為使用您剛才建立的 XA 資源定義。

1. 流覽至 [**區域] 容器**底下的 [ **BANDEMO CICS] 區域**，然後從 [**動作**] 窗格中選取 [**編輯區域啟動**檔案]。 向下滾動至 SQL 屬性，並輸入 **bankdemo** 作為 **XA 資源名稱**，或使用省略號來選取它。

2. 按一下 [ **儲存** ] 圖示以儲存您的變更。

3. 以滑鼠右鍵按一下**主控台**窗格中的 [ **BANKDEMO CICS region** ]，然後選取 [**啟動/停止區域**]。

4. 在中間窗格中的 [ **啟動/停止區域** ] 方塊底部，選取 [ **開始**]。 幾秒鐘之後，就會啟動區域。

     ![SQL 啟動/停止方塊](media/11-demo-sql.png)

     ![CICS Region BANKDEMO-已啟動畫面](media/12-demo-cics.png)

## <a name="create-a-listener"></a>建立接聽程式

為存取 BankDemo 應用程式的 TN3270 會話建立接聽程式。

1. 在左窗格中，展開 [設定 **編輯器** ]，然後選取 [接聽程式 **]**。

2. 按一下 [ **開啟** 檔案] 圖示，然後選取 **seelistener.exe.config** 檔案。 系統會編輯此檔案，並在每次企業伺服器啟動時載入。

3. 請注意之前定義的兩個區域 (ESDEMO 和 JCLDEMO) 。

4. 若要建立 BANKDEMO 的新區域，請以滑鼠右鍵按一下 [ **區域**]，然後選取 [ **新增區域**]。

5. 選取 [ **BANKDEMO 區域**]。

6. 以滑鼠右鍵按一下 [ **BANKDEMO 區域** ]，然後選取 [ **新增通道**]，以新增 TN3270 頻道。

7. 針對 [ **名稱**]，輸入 **TN3270**。 針對 [ **埠**]，輸入 **9024**。 ESDEMO 應用程式會使用埠9230，因此您需要使用不同的埠。

8. 若要儲存檔案，請按一下 [ **儲存** ] 圖示 **或選擇 [** \> **儲存**盤案]。

9. 若要啟動接聽程式，請按一下 [啟動接聽程式 **]** 圖示，或選擇 [ **選項**啟動接聽程式 \> **]**。

     ![接聽程式設定編輯器視窗](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>設定 Rumba 以存取 BankDemo 應用程式

您需要做的最後一件事，就是使用3270模擬器的 Rumba 來設定3270會話。 此步驟可讓您透過您所建立的接聽程式來存取 BankDemo 應用程式。

1. 從 Windows [ **開始** ] 功能表啟動 [Rumba Desktop]。

2. **在 [連線**] 功能表項目下，選取 [ **TN3270**]。

3. 按一下 [ **插入** ]，然後輸入 **127.0.0.1** 作為 IP 位址，並輸入 **9024** 作為使用者定義的埠。

4. 按一下對話方塊底部的 **[連接]**。 黑色 CICS 畫面隨即出現。

5. 輸入 **bank** 以顯示 BankDemo 應用程式的初始3270畫面。

6. 針對 [使用者識別碼]，輸入 **B0001** ，並在 [密碼] 中輸入任何密碼。 第一個畫面 BANK20 隨即開啟。

![大型主機顯示器歡迎畫面 ](media/14-demo.png)
 ![ 大型主機顯示器-Rumba-子系統示範畫面](media/15-demo.png)

恭喜！ 您現在正在 Azure 中使用微型焦點企業伺服器來執行 CICS 應用程式。

## <a name="next-steps"></a>接下來的步驟

- [在 Azure 上的 Docker 容器中執行企業伺服器](run-enterprise-server-container.md)
- [大型主機遷移-入口網站](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [虛擬機器](../../../linux/overview.md)
- [疑難排解](../../../troubleshooting/index.yml)
- [將大型主機揭開至 Azure 遷移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
