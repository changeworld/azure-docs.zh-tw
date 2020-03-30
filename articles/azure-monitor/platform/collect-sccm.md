---
title: 將組態管理員連接到 Azure 監視器 |微軟文檔
description: 本文演示了將組態管理員連接到 Azure 監視器中的工作區並開始分析資料的步驟。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655251"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>將組態管理員連接到 Azure 監視器
您可以將 Microsoft 終結點組態管理員環境連接到 Azure 監視器，以同步設備集合資料，並在 Azure 監視器和 Azure 自動化中引用這些集合。  

## <a name="prerequisites"></a>Prerequisites

Azure 監視器支援組態管理員當前分支、版本 1606 及更高版本。

>[!NOTE]
>將組態管理員與日誌分析工作區連接的功能是可選的，預設情況下未啟用。 您必須先先啟用這項功能才能使用它。 如需詳細資訊，請參閱 [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)。

## <a name="configuration-overview"></a>組態概觀

以下步驟總結了配置組態管理員 與 Azure 監視器集成的步驟。  

1. 在 Azure 活動目錄中，將組態管理員註冊為 Web 應用程式和/或 Web API 應用，並確保從 Azure 活動目錄註冊時具有用戶端 ID 和用戶端金鑰。 如需如何完成此步驟的詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。

2. 在 Azure 活動目錄中，[授予組態管理員（已註冊的 Web 應用）以訪問 Azure 監視器](#grant-configuration-manager-with-permissions-to-log-analytics)的許可權。

3. 在組態管理員中，使用 Azure**服務**嚮導添加連接。

4. 在運行組態管理員 服務點網站系統角色的電腦上[下載並安裝 Windows 的日誌分析代理](#download-and-install-the-agent)。 代理將組態管理員資料發送到 Azure 監視器中的日誌分析工作區。

5. 在 Azure 監視器中，[從組態管理員導入集合](#import-collections)作為電腦群組。

6. 在 Azure 監視器中，將組態管理員中的資料視為[電腦群組](computer-groups.md)。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>對 Configuration Manager 授與 Log Analytics 的權限

在下列程序中，您會將 Log Analytics 工作區中的「參與者」** 角色授與給您稍早為 Configuration Manager 建立的 AD 應用程式和服務主體。 如果還沒有工作區，請參閱[在 Azure 監視器中創建工作區](../../azure-monitor/learn/quick-create-workspace.md)，然後再繼續。 這可讓 Configuration Manager 進行驗證並連線到 Log Analytics 工作區。  

> [!NOTE]
> 您必須在組態管理員的日誌分析工作區中指定許可權。 否則，當您在 Configuration Manager 中使用組態精靈時，將會收到錯誤訊息。
>

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]****。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選擇**日誌分析**。

2. 在 Log Analytics 工作區清單中，選取要修改的工作區。

3. 從左窗格中，選取 [存取控制 (IAM)]****。

4. 在 [存取控制 (IAM)] 頁面上，按一下 [新增角色指派]****，[新增角色指派]**** 窗格隨即出現。

5. 在 [新增角色指派]**** 窗格中，於 [角色]**** 下拉式清單中選取 [參與者]**** 角色。  

6. 在 [指派存取權的對象]**** 下拉式清單底下，選取稍早在 AD 中建立的 Configuration Manager 應用程式，然後按一下 [確定]****。  

## <a name="download-and-install-the-agent"></a>下載並安裝代理程式

查看將[Windows 電腦連接到 Azure 監視器](agent-windows.md)的文章，瞭解在承載組態管理員 服務點網站系統角色的電腦上安裝 Windows 的日誌分析代理的方法。  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>將組態管理員連接到日誌分析工作區

>[!NOTE]
> 為了添加日誌分析連接，組態管理員環境必須為連線模式配置[服務連接點](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point)。

> [!NOTE]
> 您必須將層次結構中的頂級網站連接到 Azure 監視器。 如果將獨立主網站連接到 Azure 監視器，然後將管理中心網站添加到環境，則必須刪除並重新創建新層次結構中的連接。

1. 在組態管理員**的管理**工作區中，選擇**雲服務**，然後選擇**Azure 服務**。 

2. 按右鍵**Azure 服務**，然後選擇"配置 Azure**服務**"。 將顯示 **"配置 Azure 服務"** 頁。 
   
3. 在 [一般]**** 畫面上，確認您已完成下列動作而且您有每個項目的詳細資料，然後選取 [下一步]****。

4. 在 Azure 服務精靈的 [Azure 服務]頁面上：

    1. 為 Configuration Manager 中的物件指定 [名稱]****。
    2. 指定選用 [描述]**** 以協助您識別服務。
    3. 選擇 Azure 服務**OMS 連接器**。

    >[!NOTE]
    >OMS 現在稱為日誌分析，這是 Azure 監視器的一項功能。

5. 選取 [下一步]****，繼續前往 [Azure 服務精靈] 的 Azure 應用程式內容頁面。

6. 在 Azure 服務嚮導**的應用**頁上，首先從清單中選擇 Azure 環境，然後按一下"**導入**"。

7. 在 **"導入應用"** 頁上，指定以下資訊：

    1. 為應用指定**Azure AD 租戶名稱**。

    2. 為**Azure AD 租戶 ID**指定 Azure AD 租戶。 您可以在 Azure 活動目錄**屬性**頁上找到此資訊。 

    3. 為**應用程式名稱**指定應用程式名稱。

    4. 為**用戶端 ID**指定之前創建的 Azure AD 應用的應用程式 ID。

    5. 為**金鑰**指定已創建的 Azure AD 應用的用戶端金鑰。

    6. 指定**金鑰過期，** 金鑰的到期日期。

    7. 為**應用 ID URI**指定，是前面創建的 Azure AD 應用的應用 ID URI。

    8. 選擇 **"驗證**"，並在右側顯示結果應顯示**成功驗證！**。

8. 在 **"配置**"頁上，查看資訊以驗證已預填充**Azure 訂閱****、Azure 資源組**和**操作管理套件工作區**欄位，以指示 Azure AD 應用程式在資源組中具有足夠的許可權。 如果欄位為空，則表示您的應用程式沒有所需的許可權。 選擇要收集和轉發到工作區的設備集合，然後選擇 **"添加**"。

9. 查看"**確認設置"** 頁上的選項，然後選擇 **"下一步**"以開始創建和配置連接。

10. 配置完成後，將顯示 **"完成"** 頁。 選取 [關閉]****。 

將組態管理員連結到 Azure 監視器後，可以添加或刪除集合，並查看連接的屬性。

## <a name="update-log-analytics-workspace-connection-properties"></a>更新日誌分析工作區連接屬性

如果密碼或用戶端金鑰過期或丟失，則需要手動更新日誌分析連接屬性。

1. 在組態管理員**的管理**工作區中，選擇**雲服務**，然後選擇**OMS 連接器**以打開**OMS 連接屬性**頁。
2. 在此頁面上按一下 [Azure Active Directory]**** 索引標籤，檢視 [租用戶]****、[用戶端識別碼]**** 和 [用戶端秘密金鑰到期]****。 **確認****用戶端秘密金鑰**是否已過期。

## <a name="import-collections"></a>匯入集合

將日誌分析連接添加到組態管理員並在運行組態管理員服務點網站系統角色的電腦上安裝代理後，下一步是從 Azure 中的組態管理員導入集合監視為電腦群組。

完成從層次結構匯入裝置集合的初始配置後，每 3 小時檢索一次集合資訊，以保持成員身份。 您可以隨時選擇停用此功能。

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]****。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]****。
2. 在您的 Log Analytics 工作區清單中，選取 Configuration Manager 用來註冊的工作區。  
3. 選取 [進階設定]****。
4. 依序選取 [電腦群組]**** 和 [SCCM]****。  
5. 選取 [匯入 Configuration Manager 集合成員資格]****，然後按一下 [儲存]****。  
   
    ![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>從 Configuration Manager 檢視資料

將日誌分析連接添加到組態管理員並在運行組態管理員 服務點網站系統角色的電腦上安裝代理後，代理的資料將發送到 Azure 監視器中的日誌分析工作區。 在 Azure 監視器中，組態管理員集合顯示為[電腦群組](../../azure-monitor/platform/computer-groups.md)。 您可以從 [設定\電腦群組]**** 下的 [Configuration Manager]**** 頁面檢視群組。

匯入集合之後，您可以看到已偵測到多少部具有集合成員資格的電腦。 您也可以看到已匯入的集合數目。

![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups02.png)

按一下任一組時，日誌查詢編輯器將打開，顯示所有導入的組或屬於每個組的所有電腦。 使用[日誌搜索](../../azure-monitor/log-query/log-query-overview.md)，可以對集合成員身份資料執行進一步深入的分析。

## <a name="next-steps"></a>後續步驟

請使用 [記錄檔搜尋](../../azure-monitor/log-query/log-query-overview.md)，檢視有關 Configuration Manager 資料的詳細資訊。
