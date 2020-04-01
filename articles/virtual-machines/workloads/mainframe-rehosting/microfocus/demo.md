---
title: 為 Azure 虛擬機器上的微焦點企業開發人員 4.0 設定微焦點 CICS BankDemo
description: 在 Azure 虛擬機器(VM) 上運行微焦點銀行演示應用程式,以瞭解如何使用微焦點企業伺服器和企業開發人員。
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: db9d6bab2f383102434512aa63d7566cff1f579b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411082"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>為 Azure 上的微焦點企業開發人員 4.0 設置微焦點 CICS 銀行演示

在 Azure 上設置微焦點企業伺服器 4.0 和企業開發人員 4.0 時,可以測試 IBM z/OS 工作負載的部署。 本文演示如何設置 CICS BankDemo,這是企業開發人員附帶的範例應用程式。

CIC 代表客戶資訊控制系統,這是許多在線大型機應用程式使用的交易平臺。 BankDemo 應用程式非常適合瞭解企業伺服器和企業開發人員的操作方式以及如何管理和部署使用綠屏終端完成的實際應用程式。

> [!NOTE]
> 即將推出:有關在 Azure VM 上設置[微焦點企業伺服器 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110)的說明。

## <a name="prerequisites"></a>Prerequisites

- 具有[企業開發人員的](set-up-micro-focus-azure.md)VM 。 請記住,企業開發人員在它上有一個完整的企業伺服器實例,用於開發和測試目的。 此實例是用於演示的企業伺服器實例。

- [SQL 伺服器 2017 快遞版](https://www.microsoft.com/sql-server/sql-server-editions-express). 下載並將其安裝在企業開發人員 VM 上。 企業伺服器需要一個資料庫來管理 CICS 區域,BankDemo 應用程式還使用稱為 BANKDEMO 的 SQL Server 資料庫。 本展示假定您對兩個資料庫都使用 SQL Server Express。 安裝時,選擇基本安裝。

- [SQL 伺服器管理工作室](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)(SSMS)。 SSMS 用於管理資料庫和運行 T-SQL 文稿。 下載並將其安裝在企業開發人員 VM 上。

- [Visual Studio 2019](https://azure.microsoft.com/downloads/)與最新的服務包或[視覺工作室社區](https://visualstudio.microsoft.com/vs/community/), 你可以免費下載.

- 倫巴桌面或其他3270模擬器。

## <a name="configure-the-windows-environment"></a>設定 Windows 環境

在 VM 上安裝企業開發人員 4.0 後,必須配置附帶的企業伺服器實例。 為此,您需要安裝一些其他 Windows 功能,如下所示。

1. 使用 RDP 登錄到您創建的企業伺服器 4.0 VM。

2. 點選 **「開始」** 按鈕旁邊的 **「搜尋**」圖示,然後鍵入**Windows 功能**。 伺服器管理員添加角色和功能嚮導打開。

3. 選擇**Web 伺服器 (IIS) 角色**,然後檢查以下選項:

    - Web 管理工具
    - IIS 6 管理相容性(選擇所有可用功能)
    - IIS 管理主控台
    - IIS 管理指令碼及工具
    - IIS 管理服務

4. 選擇**萬維網服務**,並檢查以下選項:

     應用程式開發功能:
    - .NET 擴充性
    - ASP.NET
    - 常見 HTTP 功能:新增所有可用功能
    - 執行狀況與診斷:新增所有可用功能
    - 安全性：
        - 基本驗證
        - Windows 驗證

5. 選擇**Windows 進程啟動服務**及其所有子級。

6. 有關**功能**,請檢查**Microsoft .NET 框架 3.5.1**,並檢查以下選項:

    - Windows 通訊基礎 HTTP 啟動
    - Windows 通訊基礎非 HTTP 啟動

7. 有關**功能**,請檢查**Microsoft .NET 框架 4.6**,並檢查以下選項:

   - 命名導管啟動
   - TCP 啟用
   - TCP 連接埠共用

     ![新增角色和功能精靈:角色服務](media/01-demo-roles.png)

8. 選擇所有選項後,按下 **「下一步**」進行安裝。

9. Windows 功能後,轉到**控制\>面板系統和\>安全管理工具**,然後選擇 **「服務**」。 往下捲動並確保以下服務執行,並設定為**自動**:

    - **NetTcpPortSharing**
    - **Net.Pipe 接聽程式配接器**
    - **Net.tcp 偵聽器配接器**

10. 要配置 IIS 和 WAS 支援,請從選單中找到**微焦點企業開發人員命令提示符 (64 位),** 然後以**管理員**身份運行。

11. **類型設定 _i,** 然後按**Enter**。

12. 腳本運行後,可以關閉視窗。

## <a name="configure-the-local-system-account-for-sql-server"></a>為 SQL 伺服器設定本地端系統帳戶

某些企業伺服器進程需要能夠登錄 SQL Server 並創建資料庫和其他物件。 這些程序使用本地系統帳戶,因此您必須授予該帳戶的系統管理員許可權。

1. 啟動**SSMS,** 然後按下「**連接**」 以使用 Windows 身份驗證連接到本地 SQLEXPRESS 伺服器。 它在 **「伺服器名稱」** 清單中應可用。

2. 在左側,展開**安全**資料夾並選擇 **「登錄」。**

3. 選擇**\\NT 授權系統**並選擇**屬性**。

4. 選擇**伺服器角色**並檢查**系統管理員**。

     ![SSMS 物件資源管理員視窗:登入屬性](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>建立 BankDemo 資料庫及其所有物件

1. 開啟**Windows 資源管理員**並瀏覽到**C:\\\\\\\\\\\\\\\\\\\\\\使用者 公共文件微焦點企業開發人員範例主機 CICS DotNet 銀行Demo SQL**.

2. 將**BankDemoCreateAll.SQL**檔案的內容複製到剪貼簿中。

3. 開啟**SSMS**。 在右側,按一下 **「伺服器」** 並選擇 **「新建查詢**」。

4. 將剪貼簿的內容貼上到 **「新建查詢」** 框中。

5. 通過按一下查詢上方的執行**選項**卡**Command**執行 SQL。

查詢應運行時沒有錯誤。 完成後,您擁有 BankDemo 應用程式的範例資料庫。

![SQLQuery1.sql 輸出](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>驗證資料庫表與物件是否已建立

1. 右鍵按**一下 BANKDEMO**資料庫並選擇 **「刷新**」 。

2. 展開**資料庫**並選擇**表**格 。 您應該會看到類似以下內容。

     ![在物件資源管理員中延伸的 BANKDEMO 表](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>在企業開發人員中建構應用程式

1. 開啟 Visual Studio 並登入。

2. 在 **「檔案**」選單選項下,選擇 **「打開專案/解決方案**」,導航到**C:\\\\使用者\\\\\\\\\\\\公開\\\\文件微焦點企業開發人員 範例 CICS DotNet BankDemo,** 然後選擇**sln**檔案。

3. 花一些時間檢查物件。 COBOL 程式與抄本 (CPY) 和 JCL 一起顯示在解決方案資源管理器中,並帶有 CBL 擴展。

4. 右鍵按一下**BankDemo2**專案,然後選擇 **「設定為啟動專案**」 。。

    > [!NOTE]
    > BankDemo 專案使用 HCOSS(SQL Server 的主機相容性選項),該選項不用於此演示。

5. 在**解決方案資源管理器**中,右鍵單擊**BankDemo2**專案並選擇 **「生成**」。

    > [!NOTE]
    > 在解決方案等級建構會導致錯誤,因為尚未配置HCOSS。

6. 生成專案時,請檢查**輸出**視窗。 它應該類似下圖。

     ![顯示成功編譯的輸出視窗](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>將 BankDemo 應用程式部署到區域資料庫

1. 打開企業開發人員命令提示符(64 位)作為管理員。

2. 瀏覽到**\\\\%PUBLIC%\\文件\\微\\焦點企業 開發\\人員 範\\\\例主機 CICS DotNet 銀行Demo**。

3. 在命令提示符下,執行**bankdemodbdeploy,** 並包含要部署到的資料庫的參數,例如:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> 請確保使用向前斜杠 (/) 而不是向後斜杠()。\\ 此腳本運行一段時間。

![管理:企業開發人員指令提示視窗](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>在企業管理員中為 .NET 建立銀行示範區域

1. 打開**企業伺服器 .NET 管理**UI。

2. 要啟動 MMC 管理單元,請從 Windows**開始**選單中選擇**微焦點企業\>開發人員\>設定企業伺服器。.NET Admin**。( 對於 Windows 伺服器,選擇**用於\>.NET 管理員的微焦點企業開發人員企業伺服器**。

3. 展開左方視窗格中的**區域**容器,然後右鍵按**下 CICS**。

4. 選擇 **「定義區域**」以建立一個名為**BANKDEMO**的新 CICS 區域,託管在(本地)資料庫中。

5. 提供資料庫伺服器實例,按下 **「下一步**」,然後輸入區域名稱**BANKDEMO**。

     ![定義區域對話框](media/07-demo-cics.png)

6. 要選擇跨區域資料庫的區域定義檔案,請在 C**中\_尋找\_區域 bankdemo db.config:****\\\\使用者\\公共\\文件微\\焦點企業\\開發人員\\範例主機\\CICS\\\\DotNet BankDemo**。

     ![定義區域 - 區域名稱:銀行](media/08-demo-cics.png)

7. 按一下 **[完成]**。

## <a name="create-xa-resource-definitions"></a>建立 XA 資源定義

1. 在**企業伺服器的左方窗格中,用於 .NET 管理**UI,展開**系統**,然後**展開 XA 資源定義**。 此設置定義區域與企業伺服器和應用程序資料庫的交互操作方式。

2. 右鍵按**下 XA 資源定義**並選擇 **「添加伺服器實例**」 。

3. 在下拉框中,選擇**資料庫服務實體 。** 它將是本地電腦 SQLEXPRESS。

4. 從**XA 資源定義(\\電腦名稱 sqlexpress)** 容器下選擇實例,然後按下「**添加**」 。。

5. 選擇**資料庫 XA 資源定義**,然後鍵入**名稱****和地區的** **BANKDEMO。**

     ![新的資料庫 XA 資源定義螢幕](media/09-demo-xa.png)

6. 按下橢圓(**...)** 以啟動連接字串精靈。 此**伺服器名稱**,鍵入 **(本地)SQLEXPRESS\\**。 對**登入**,請選擇**Windows 認證**。 對資料庫名稱鍵入**BANKDEMO**

     ![編輯連接字串螢幕](media/10-demo-string.png)

7. 測試連接。

## <a name="start-the-bankdemo-region"></a>啟動 BANKDEMO 區域

> [!NOTE]
> 第一步很重要:必須將區域設置為使用剛剛創建的 XA 資源定義。

1. 導覽到**區域容器**下的**BANDEMO CICS 區域**,然後從 **「操作」** 窗格中選擇 **「編輯區域啟動檔**」 。 向下捲軸到 SQL 屬性,然後輸入**XA 資源名稱**的**bankdemo,** 或使用省略號來選擇它。

2. 按下 **「儲存**」圖示以儲存更改。

3. 右鍵按一下**控制檯**窗格中的**BANKDEMO CICS 區域**,然後選擇 **「開始/停止區域**」。

4. 在中間窗格中顯示的 **「開始/停止區域」** 框的底部,選擇 **「開始**」 。。 幾秒鐘后,區域開始。

     ![SQL 開始/停止框](media/11-demo-sql.png)

     ![CICS 地區銀行 - 啟動螢幕](media/12-demo-cics.png)

## <a name="create-a-listener"></a>建立偵聽器

為訪問 BankDemo 應用程式的 TN3270 會話創建偵聽器。

1. 在左邊窗格中,展開 **「設定編輯器」** 並選擇**偵聽器**。

2. 按下 **「打開檔案」** 圖示並選擇 **「seelistener.exe.config」** 檔案。 每次企業伺服器啟動時,都將編輯並載入此檔。

3. 請注意之前定義的兩個區域(ESDEMO 和 JCLDEMO)。

4. 要為 BANKDEMO 建立新區域,請右鍵單擊 **「區域**」,然後選擇「**添加區域**」 。。

5. 選擇**銀行區**。

6. 通過右鍵按一下**BANKDEMO 區域**並選擇 **「新增通道**」添加 TN3270 通道。

7. 對**名稱**,輸入**TN3270**。 對於**連接埠**,輸入**9024**。 ESDEMO 應用程式使用埠 9230,因此您需要使用不同的埠。

8. 要儲存檔案,請按下 **「儲存**」圖示或選擇「**檔案**\>**儲存**」 。

9. 要啟動偵聽器,請按下 **「開始偵聽器」** 圖示**Options**\>或選擇 **「開始偵聽器」** 選項。

     ![偵聽器設定編輯器視窗](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>設定倫巴以存取 BankDemo 應用程式

您需要做的最後一件事是使用 3270 模擬器 Rumba 配置 3270 會話。 此步驟使您能夠通過創建的偵聽器訪問 BankDemo 應用程式。

1. 從「視窗**開始」** 選單中啟動 Rumba 桌面。

2. 在 **'連線'** 選單項目下,選擇**TN3270**。

3. 按一下 IP 位址的**插入**和鍵入**127.0.0.1,** 為使用者定義的連接埠鍵入**9024。**

4. 在對話框的底部,按兩下**連接**"。 將顯示一個黑色 CICS 螢幕。

5. 鍵入**銀行**以顯示 BankDemo 應用程式的初始 3270 螢幕。

6. 對於使用者 ID,鍵入**B0001,** 對於密碼,鍵入任何內容。 第一個螢幕 BANK20 打開。

![大型機顯示器歡迎螢幕](media/14-demo.png)
![大型機顯示 - 倫巴 - 子系統展示畫面](media/15-demo.png)

恭喜！ 現在,您正在使用微焦點企業伺服器在 Azure 中運行 CICS 應用程式。

## <a name="next-steps"></a>後續步驟

- [Azure 的 Docker 容器執行企業伺服器](run-enterprise-server-container.md)
- [大型機移到門戶](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [將大型機與 Azure 移轉解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
