---
title: 將 Configuration Manager 連接到 Azure 監視器 |Microsoft Docs
description: 本文說明在 Azure 監視器中將 Configuration Manager 連線至工作區，並開始分析資料的步驟。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655251"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>將 Configuration Manager 連接到 Azure 監視器
您可以將您的 Microsoft 端點 Configuration Manager 環境連接到 Azure 監視器，以同步處理裝置集合資料，並在 Azure 監視器和 Azure 自動化中參考這些集合。  

## <a name="prerequisites"></a>必要條件

Azure 監視器支援 Configuration Manager 最新分支1606版和更高版本。

>[!NOTE]
>與 Log Analytics 工作區連線 Configuration Manager 的功能是選擇性的，而且預設不會啟用。 您必須先啟用這項功能，才能使用它。 如需詳細資訊，請參閱[從更新啟用選擇性功能](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)。

## <a name="configuration-overview"></a>組態概觀

下列步驟摘要說明使用 Azure 監視器設定 Configuration Manager 整合的步驟。  

1. 在 Azure Active Directory 中，將 Configuration Manager 註冊為 Web 應用程式和/或 Web API 應用程式，並確定您有來自 Azure Active Directory 註冊的用戶端識別碼和用戶端秘密金鑰。 如需如何完成此步驟的詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。

2. 在 Azure Active Directory 中，[授與 Configuration Manager （已註冊的 web 應用程式）具有存取 Azure 監視器的許可權](#grant-configuration-manager-with-permissions-to-log-analytics)。

3. 在 Configuration Manager 中，使用**Azure 服務**嚮導新增連接。

4. 在執行 Configuration Manager 服務連接點網站系統角色的電腦上，[下載並安裝適用于 Windows 的 Log Analytics 代理程式](#download-and-install-the-agent)。 代理程式會將 Configuration Manager 資料傳送至 Azure 監視器中的 Log Analytics 工作區。

5. 在 Azure 監視器中，將[Configuration Manager 的集合匯入](#import-collections)為電腦群組。

6. 在 Azure 監視器中，將資料從 Configuration Manager 查看為[電腦群組](computer-groups.md)。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>對 Configuration Manager 授與 Log Analytics 的權限

在下列程序中，您會將 Log Analytics 工作區中的「參與者」角色授與給您稍早為 Configuration Manager 建立的 AD 應用程式和服務主體。 如果您還沒有工作區，請參閱[在 Azure 監視器中建立工作區](../../azure-monitor/learn/quick-create-workspace.md)，再繼續進行。 這可讓 Configuration Manager 進行驗證並連線到 Log Analytics 工作區。  

> [!NOTE]
> 您必須在 Log Analytics 工作區中指定 Configuration Manager 的許可權。 否則，當您在 Configuration Manager 中使用組態精靈時，將會收到錯誤訊息。
>

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。

2. 在 Log Analytics 工作區清單中，選取要修改的工作區。

3. 從左窗格中，選取 [存取控制 (IAM)]。

4. 在 [存取控制 (IAM)] 頁面上，按一下 [新增角色指派]，[新增角色指派] 窗格隨即出現。

5. 在 [新增角色指派] 窗格中，於 [角色] 下拉式清單中選取 [參與者] 角色。  

6. 在 [指派存取權的對象] 下拉式清單底下，選取稍早在 AD 中建立的 Configuration Manager 應用程式，然後按一下 [確定]。  

## <a name="download-and-install-the-agent"></a>下載並安裝代理程式

請參閱將[Windows 電腦連線到 Azure 中的 Azure 監視器](agent-windows.md)一文，以瞭解在裝載 Configuration Manager 服務連接點網站系統角色的電腦上，可用於安裝適用于 Windows 的 Log Analytics 代理程式的方法。  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>將 Configuration Manager 連線到 Log Analytics 工作區

>[!NOTE]
> 若要新增 Log Analytics 連線，您的 Configuration Manager 環境必須已針對線上模式設定[服務連接點](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point)。

> [!NOTE]
> 您必須將階層中的頂層網站連線到 Azure 監視器。 如果您將獨立主要網站連線到 Azure 監視器，然後將管理中心網站新增至您的環境，則必須在新階層內刪除並重新建立連線。

1. 在 Configuration Manager 的 [系統**管理**] 工作區中，選取 [雲端**服務**]，然後選取 [ **Azure 服務**]。 

2. 以滑鼠右鍵按一下 [ **Azure 服務**]，然後選取 [**設定 azure 服務**]。 [**設定 Azure 服務**] 頁面隨即出現。 
   
3. 在 [一般] 畫面上，確認您已完成下列動作而且您有每個項目的詳細資料，然後選取 [下一步]。

4. 在 Azure 服務嚮導的 [Azure 服務] 頁面上：

    1. 在 Configuration Manager 中指定物件的**名稱**。
    2. 指定可協助您識別服務的選擇性**描述**。
    3. 選取 [Azure 服務**OMS 連接器**]。

    >[!NOTE]
    >OMS 現在稱為 Log Analytics，這是 Azure 監視器的功能。

5. 選取 **[下一步]** ，繼續前往 Azure 服務嚮導的 [azure 應用程式屬性] 頁面。

6. 在 [Azure 服務] 嚮導的 [**應用程式**] 頁面上，先從清單中選取 [Azure 環境]，然後按一下 [匯**入**]。

7. 在 [匯**入應用程式**] 頁面上，指定下列資訊：

    1. 指定應用程式的 [ **Azure AD 租使用者名稱**]。

    2. 為 Azure AD 租使用者指定**Azure AD 租使用者識別碼**。 您可以在 [Azure Active Directory**屬性**] 頁面上找到此資訊。 

    3. 為 [**應用程式名稱**] 指定應用程式名稱。

    4. 針對 [**用戶端識別碼**]，指定先前建立之 Azure AD 應用程式的 [應用程式識別碼]。

    5. 針對 [**秘密金鑰**]，指定所建立 Azure AD 應用程式的用戶端秘密金鑰。

    6. 針對 [**秘密金鑰到期**日] 指定金鑰的到期日。

    7. 針對 [**應用程式識別碼 uri**]，指定稍早建立之所建立 Azure AD 應用程式的 [應用程式識別碼 uri]。

    8. 選取 [**驗證**]，然後在右側顯示 [**成功驗證**的結果！]。

8. **在 [設定**] 頁面上，檢查 [確認**azure**訂用帳戶]、[ **azure 資源群組**] 和 [ **Operations Management Suite 工作區**] 欄位是否已預先填入資訊，指出 Azure AD 應用程式在資源群組中具有足夠的許可權。 如果欄位是空的，則表示您的應用程式沒有必要的許可權。 選取要收集並轉寄到工作區的裝置集合，然後選取 [**新增**]。

9. 檢查 [**確認設定**] 頁面上的選項，然後選取 **[下一步]** 以開始建立和設定連線。

10. 設定完成時，[**完成**] 頁面隨即出現。 選取 [關閉]。 

將 Configuration Manager 連結到 Azure 監視器之後，您就可以新增或移除集合，以及查看連接的屬性。

## <a name="update-log-analytics-workspace-connection-properties"></a>更新 Log Analytics 工作區連接屬性

如果密碼或用戶端秘密金鑰過期或遺失，您必須手動更新 Log Analytics 連線屬性。

1. 在 Configuration Manager 的 [系統**管理**] 工作區中，選取 [**雲端服務**]，然後選取 [ **oms 連接器**] 以開啟 [ **oms 連線屬性**] 頁面。
2. 在此頁面上按一下 [Azure Active Directory] 索引標籤，檢視 [租用戶]、[用戶端識別碼] 和 [用戶端秘密金鑰到期]。 **確認** **用戶端秘密金鑰**是否已過期。

## <a name="import-collections"></a>匯入集合

將 Log Analytics 連線新增至 Configuration Manager 並在執行 Configuration Manager 服務連接點網站系統角色的電腦上安裝代理程式之後，下一個步驟是從 Azure 中的 Configuration Manager 匯入集合監視為電腦群組。

當您完成初始設定以從階層匯入裝置集合之後，每3小時會抓取一次集合資訊，以保持成員資格的最新。 您可以隨時選擇停用此功能。

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。
2. 在您的 Log Analytics 工作區清單中，選取 Configuration Manager 用來註冊的工作區。  
3. 選取 [進階設定]。
4. 依序選取 [電腦群組] 和 [SCCM]。  
5. 選取 [匯入 Configuration Manager 集合成員資格]，然後按一下 [儲存]。  
   
    ![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>從 Configuration Manager 檢視資料

將 Log Analytics 連線新增至 Configuration Manager，並在執行 Configuration Manager 服務連線點網站系統角色的電腦上安裝代理程式之後，代理程式的資料會傳送至 Azure 監視器中的 Log Analytics 工作區。 在 Azure 監視器中，您的 Configuration Manager 集合會顯示為 [[電腦群組](../../azure-monitor/platform/computer-groups.md)]。 您可以從 [設定\電腦群組] 下的 [Configuration Manager] 頁面檢視群組。

匯入集合之後，您可以看到已偵測到多少部具有集合成員資格的電腦。 您也可以看到已匯入的集合數目。

![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups02.png)

當您按一下任一項時，記錄查詢編輯器會開啟，顯示所有匯入的群組或屬於每個群組的所有電腦。 使用[記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)，您可以進一步深入分析集合成員資格資料。

## <a name="next-steps"></a>後續步驟

請使用 [記錄檔搜尋](../../azure-monitor/log-query/log-query-overview.md)，檢視有關 Configuration Manager 資料的詳細資訊。
