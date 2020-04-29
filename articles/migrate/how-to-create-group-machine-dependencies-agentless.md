---
title: 在 Azure Migrate Server 評估中設定無代理程式相依性分析
description: 在 Azure Migrate Server 評估中設定無代理程式相依性分析。
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455701"
---
# <a name="set-up-agentless-dependency-visualization"></a>設定無代理程式相依性視覺效果 

本文說明如何在 Azure Migrate：伺服器評估中設定無代理程式相依性分析。 相依性[分析](concepts-dependency-visualization.md)可協助您找出並瞭解您想要評估並遷移至 Azure 的機器之間的相依性。


> [!IMPORTANT]
> 無代理程式相依性視覺效果目前僅供 VMware Vm 預覽，使用 Azure Migrate： Server 評估工具探索到。
> 功能可能受限或不完整。
> 此預覽涵蓋于客戶支援，並可用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



## <a name="before-you-start"></a>在您開始使用 Intune 之前

- [深入瞭解](concepts-dependency-visualization.md#agentless-analysis)無代理程式相依性分析。
- 請[參閱](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)設定 VMware vm 無代理程式相依性視覺效果的必要條件和支援需求
- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案，請確定您已[新增](how-to-assess.md)Azure Migrate：伺服器評估工具。
- 請確定您已設定[Azure Migrate 設備](migrate-appliance.md)，以探索您的內部部署機器。 瞭解如何為[VMware](how-to-set-up-appliance-vmware.md) vm 設定設備。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。


## <a name="current-limitations"></a>目前的限制

- 目前您無法在 [相依性分析] 視圖中，從群組新增或移除伺服器。
- 目前無法使用伺服器群組的相依性對應。
- 目前，無法以表格格式下載相依性資料。

## <a name="create-a-user-account-for-discovery"></a>建立用於探索的使用者帳戶

設定使用者帳戶，讓伺服器評估可以存取 VM 進行探索。 [瞭解](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)帳戶需求。


## <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

將使用者帳戶新增至設備。

1. 開啟 [裝置管理] 應用程式。 
2. 流覽至 [**提供 vCenter 詳細資料**] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇 [**作業系統**]、[提供帳戶的易記名稱] 和 [**使用者名稱**/]**密碼**
6. 按一下 **[儲存]** 。
7. 按一下 [**儲存並啟動探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>啟動相依性探索

選擇您想要啟用相依性探索的電腦。

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 按一下 [相依性**分析**] 圖示。
3. 按一下 [**新增伺服器**]。
3. 在 [**新增伺服器**] 頁面中，選擇正在探索相關機器的設備。
4. 從 [電腦] 清單中，選取電腦。
5. 按一下 [**新增伺服器**]。

    ![啟動相依性探索](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

在啟動相依性探索之後，您可以將相依性視覺化約六個小時。

## <a name="visualize-dependencies"></a>將相依性視覺化

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 搜尋您想要查看的機器。
3. 在 [**相依性] 資料**行中，按一下 [視圖相依性 **]**
4. 使用 [**持續**時間] 下拉式清單，變更您想要查看對應的時間週期。
5. 展開**用戶端**群組，列出具有所選機器相依性的電腦。
6. 展開**埠**群組，列出與所選機器具有相依性的電腦。
7. 若要流覽至任何相依電腦的地圖視圖，請按一下電腦名稱稱 > [**載入伺服器對應**]

    ![展開伺服器埠群組和載入伺服器對應](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展開 [用戶端群組] ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展開選取的電腦，以查看每個相依性的進程層級詳細資料。

    ![展開伺服器以顯示進程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 相依性的進程資訊不一定是可用的。 如果無法使用，則會以標示為「未知進程」的進程來描述相依性。

## <a name="stop-dependency-discovery"></a>停止相依性探索

選擇您想要停止相依性探索的電腦。

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 按一下 [相依性**分析**] 圖示。
3. 按一下 [**移除伺服器**]。
3. 在 [**移除伺服器**] 頁面中，選擇正在探索 vm 的**設備**，以停止相依性探索。
4. 從 [電腦] 清單中，選取電腦。
5. 按一下 [**移除伺服器**]。


## <a name="next-steps"></a>後續步驟

[將機器分組](how-to-create-a-group.md)以進行評量。
