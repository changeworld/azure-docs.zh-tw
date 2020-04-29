---
title: 開始使用 Azure 自動化狀態設定
description: Azure Automation State Configuration (DSC) 中最常見工作的說明和範例
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392161"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>開始使用 Azure 自動化狀態設定

本文說明如何使用 Azure Automation State Configuration 來執行最常見的工作，例如建立、匯入和編譯組態、將要管理的機器上架，以及檢視報告。 如需 Azure Automation State Configuration 的概觀，請參閱 [Azure Automation State Configuration 概觀](automation-dsc-overview.md)。 如需 Desired State Configuration (DSC) 文件，請參閱 [Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)。

本文提供使用 Azure Desired State Configuration 的逐步指南。 如果您不想遵循本主題中所述的步驟，但想要已經設定好的範例環境，可以使用下列 Resource Manager 範本：[Azure 自動化受控節點範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)。 此範本會設定完整的 Azure Automation State Configuration 環境，包括由 Azure Automation State Configuration 管理的 Azure VM。

## <a name="prerequisites"></a>先決條件

若要完成本文中的範例，需要有下列項目：

- Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
- 執行[支援的作業系統](automation-dsc-overview.md#operating-system-requirements)的 Azure Resource Manager VM （非傳統）。 如需建立 VM 的指示，請參閱 [在 Azure 入口網站中建立第一個 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>建立 DSC 組態

您會建立一個簡單的 [DSC 設定](/powershell/scripting/dsc/configurations/configurations)，以根據您指派節點的方式，確定是否有 **Web 伺服器** Windows 功能 (IIS) 存在。

1. 開始 [VSCode](https://code.visualstudio.com/docs) (或任何文字編輯器)。
1. 輸入下列文字：

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. 將檔案儲存為**TestConfig**。

此設定會呼叫每個節點區塊中的一項資源，即在該[資源](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)中。 此資源可確保**Web 服務器**功能是否存在。

## <a name="importing-a-configuration-into-azure-automation"></a>將組態匯入 Azure 自動化

接下來，您會將設定匯入自動化帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，選取 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC **）] 頁面**上，按一下 [設定] 索引標籤，然後按一下 [**新增**]。
1. 在 [匯入設定] 窗格中， `TestConfig.ps1`流覽至您電腦上的檔案。

   ![[匯入組態] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/AddConfig.png)

1. 按一下 [確定]  。

## <a name="viewing-a-configuration-in-azure-automation"></a>在 Azure 自動化中檢視組態

在匯入組態之後，您可以在 Azure 入口網站中檢視它。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，選取 [設定**管理**] 底下的 **[狀態設定（DSC）** ]。
1. 在 [State configuration （DSC **）] 頁面**上，按一下 [設定] 索引標籤，然後按一下 [ **TestConfig**]。 這是您在上一個程式中匯入的設定名稱。
1. 在 [TestConfig 設定] 窗格中，按一下 [ **View Configuration source**]。

   ![TestConfig 組態刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/ViewConfigSource.png)

   [TestConfig 設定來源] 窗格隨即開啟，其中顯示設定的 PowerShell 程式碼。

## <a name="compiling-a-configuration-in-azure-automation"></a>在 Azure 自動化中編譯組態

定義預期狀態的 DSC 組態必須先編譯成一或多個節點組態 (MOF 文件)，並放在自動化 DSC 提取伺服器上，才可以將該預期狀態套用至節點。 如需在 Azure Automation State Configuration 中編譯組態的詳細說明，請參閱[在 Azure Automation State Configuration 中編譯組態](automation-dsc-compile.md)。
如需編譯設定的詳細資訊，請參閱 [DSC 設定](/powershell/scripting/dsc/configurations/configurations)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC **）] 頁面**上，按一下 [設定] 索引標籤，然後按一下 [ **TestConfig**]。 這是先前匯入設定的名稱。
1. 在 [TestConfig 設定] 窗格中，按一下 [**編譯**]，然後按一下 **[是]**。 這會啟動編譯作業。

   ![醒目提示 [編譯] 按鈕之 TestConfig 設定頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> 當您在 Azure 自動化中編譯設定時，它會自動將任何已建立的節點設定 MOF 檔案部署至提取伺服器。

## <a name="viewing-a-compilation-job"></a>檢視編譯作業

啟動編譯之後，您可以**在 [設定] 頁面的**[**編譯作業**] 磚中加以查看。 [編譯作業]**** 圖格會顯示目前執行中、已完成及失敗的工作。 當您開啟 [編譯作業] 窗格時，它會顯示該工作的相關資訊，包括遇到的任何錯誤或警告、設定中使用的輸入參數，以及編譯記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC **）] 頁面**上，按一下 [設定] 索引標籤，然後按一下 [ **TestConfig**]。 這是先前匯入設定的名稱。
1. 在 [**編譯作業**] 底下，選取要查看的編譯作業。 [編譯作業] 窗格隨即開啟，並標示編譯作業的啟動日期。

   ![[編譯工作] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/CompilationJob.png)

1. 按一下 [編譯工作] 窗格中的任何圖格，以查看作業的進一步詳細資料。

## <a name="viewing-node-configurations"></a>檢視節點組態

成功完成編譯作業會建立一或多個新的節點組態。 節點組態是已部署到提取伺服器且準備由一或多個節點提取並套用的 MOF 文件。 您可以在 [State configuration （DSC）] 頁面上，查看自動化帳戶中的節點設定。 節點設定具有格式`ConfigurationName.NodeName`的名稱。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State Configuration (DSC)] 頁面上，按一下 [已編譯組態]**** 索引標籤。

   ![[已編譯組態] 索引標籤的螢幕擷取畫面](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>將 Azure VM 上架交由 Azure Automation State Configuration 管理

您可以使用 Azure 自動化狀態設定來管理 Azure Vm （傳統和 Resource Manager）、內部部署 Vm、Linux 機器、AWS Vm 和內部部署實體機器。 在本文中，您將了解如何只將 Azure Resource Manager VM 上線。 如需將其他類型的機器上架的詳細資訊，請參閱[將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>將 Azure Resource Manager VM 上架交由 Azure Automation State Configuration 管理

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC）] 頁面上，選取 [**節點**] 索引標籤，然後按一下 [ **+ 新增**]。

   ![醒目提示 [加入 Azure VM] 按鈕之 [DSC 節點] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/OnboardVM.png)

1. 在 [虛擬機器] 窗格中，選取您的 VM。
1. 在 [虛擬機器詳細資料] 窗格中，按一下 [ **+** 連線]。

   > [!IMPORTANT]
   > VM 必須是執行[支援之作業系統](automation-dsc-overview.md#operating-system-requirements)的 Azure Resource Manager vm。

2. 在 [註冊] 頁面上，于 [**節點設定名稱**] 欄位中選取要套用至 VM 的節點設定名稱。 在此時提供名稱是選擇性的。 您可以在節點上架後，變更指派的節點組態。

3. 勾選 [必要時重新啟動節點]****，然後按一下 [確定]****。

   ![[註冊] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/RegisterVM.png)

   您指定的節點設定會依提供給設定**模式頻率**的值所指定的間隔套用至 VM。 VM 會依 [重新整理**頻率**] 值所指定的間隔，檢查節點設定的更新。 如需有關如何使用這些值的詳細資訊，請參閱 [設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

Azure 會啟動 VM 上線的程序。 完成時，VM 會顯示在自動化帳戶中 [State Configuration (DSC)] 頁面的 [節點]**** 索引標籤中。

## <a name="viewing-the-list-of-managed-nodes"></a>檢視受控節點的清單

您可以在 [State Configuration (DSC)] 頁面的 [節點]**** 索引標籤中，檢視自動化帳戶中已上架以供管理的所有機器清單。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State Configuration (DSC)] 頁面上，按一下 [節點]**** 索引標籤。

## <a name="viewing-reports-for-managed-nodes"></a>檢視受控節點的報告

每次 Azure Automation State Configuration 在受控節點上執行一致性檢查時，此節點會將狀態報告傳回到提取伺服器。 您可以在該節點的頁面上檢視這些報告。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC）] 頁面上，按一下 [**節點**] 索引標籤。在這裡，您可以看到設定狀態的總覽，以及每個節點的詳細資料。

   ![[節點] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/NodesTab.png)

1. 當您位於 [節點]**** 索引標籤時，按一下節點記錄以開啟報告。 按一下報告，以檢視其他報告詳細資料。

   ![[報告] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/NodeReport.png)

在個別報告的刀鋒視窗上，您可以看到相對應一致性檢查的下列狀態資訊︰

- 報表狀態。 可能的值包括：
    * 相容-節點與檢查相容。
   * 失敗-設定檢查失敗。
   * 不符合規範-節點處於模式`ApplyandMonitor`中，且機器不是處於預期的狀態。
- 一致性檢查的開始時間。
- 一致性檢查的總執行時間。
- 一致性檢查的類型。
- 任何錯誤，包括錯誤碼和錯誤訊息。
- 在設定中使用的任何 DSC 資源，以及每個資源的狀態（節點是否為該資源的預期狀態）。 您可以按一下每個資源，以取得該資源的詳細資訊。
- 節點的名稱、IP 位址和組態模式。

您也可以按一下 [檢視原始報告] **** ，查看節點傳送至伺服器的實際資料。
如需有關使用該資料的詳細資訊，請參閱 [使用 DSC 報告伺服器](/powershell/scripting/dsc/pull-server/reportserver)。

在節點上架後，可能需要一些時間才可取得第一份報告。 在節點上架後，您可能必須等待多達 30 分鐘的時間才可取得第一份報告。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>將節點重新指派至不同的節點組態

您可以指派節點以使用不同於您最初指派的節點組態。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State Configuration (DSC)] 頁面上，按一下 [節點]**** 索引標籤。
1. 在 [節點]**** 索引標籤上，按一下您要重新指派的節點名稱。
1. 在該節點的頁面上，按一下 [指派節點組態]****。

    ![醒目提示 [指派節點組態] 按鈕的 [節點] 詳細資料頁面螢幕擷取畫面](./media/automation-dsc-getting-started/AssignNode.png)

1. 在 [指派節點設定] 頁面上，選取您要指派節點的節點設定，然後按一下 [確定]****。

    ![[指派節點組態] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>取消註冊節點

如果您不想再由 Azure 自動化 DSC 管理節點，您可以將該節點取消註冊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，按一下 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State Configuration (DSC)] 頁面上，按一下 [節點]**** 索引標籤。
1. 在 [節點]**** 索引標籤上，按一下您要取消註冊的節點名稱。
1. 在該節點的窗格中，按一下 [**取消註冊**]。

    ![醒目提示 [取消註冊] 按鈕的 [節點] 詳細資料頁面螢幕擷取畫面](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>相關文章

- [Azure 自動化狀態設定總覽](automation-dsc-overview.md)
- [將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)
- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [Azure Automation State Configuration 定價](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)