---
title: 在 Azure Migrate Server 評估中設定無代理程式相依性分析
description: 在 Azure Migrate Server 評估中設定無代理程式相依性分析。
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: dc2ea0656198927cc8ae58533d296a2bedc37c13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771371"
---
# <a name="analyze-machine-dependencies-agentless"></a>分析機器相依性 (無代理程式)

本文說明如何在 Azure Migrate：伺服器評估中設定無代理程式相依性分析。 相依性[分析](concepts-dependency-visualization.md)可協助您找出並瞭解機器之間的相依性，以便評估和遷移至 Azure。


> [!IMPORTANT]
> 透過 Azure Migrate： Server 評估工具探索到的 VMware Vm，無代理程式相依性視覺效果目前為預覽狀態。
> 功能可能受限或不完整。
> 此預覽涵蓋于客戶支援，並可用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="current-limitations"></a>目前的限制

- 在 [相依性分析] 視圖中，您目前無法從群組新增或移除伺服器。
- 目前無法使用伺服器群組的相依性對應。
- 無法以表格格式下載相依性資料。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- [檢查](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)支援的作業系統和必要的許可權。
- 請確定您已執行下列動作：
    - 具有 Azure Migrate 專案。 如果您沒有這麼做，請立即[建立](how-to-add-tool-first-time.md)一個。
    - 請確認您已將 [Azure Migrate：伺服器評估工具][新增](how-to-assess.md)至專案。
    - 設定[Azure Migrate 設備](migrate-appliance.md)以探索內部部署機器。 為 VMware Vm[設定設備](how-to-set-up-appliance-vmware.md)。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。
- 檢查您要分析的每個 VM 上是否已安裝 VMware 工具（10.2 以後的版本）。


## <a name="create-a-user-account-for-discovery"></a>建立用於探索的使用者帳戶

設定使用者帳戶，讓伺服器評估可以存取 VM 以探索相依性。 [瞭解](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)Windows 和 Linux vm 的帳戶需求。


## <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

將使用者帳戶新增至設備。

1. 開啟 [裝置管理] 應用程式。 
2. 流覽至 [**提供 vCenter 詳細資料**] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇 [**作業系統**]、[提供帳戶的易記名稱] 和 [**使用者名稱**] / **密碼**
6. 按一下 [檔案] 。
7. 按一下 [**儲存並啟動探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>啟動相依性探索

選擇您想要啟用相依性探索的電腦。

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 按一下 [相依性**分析**] 圖示。
3. 按一下 [**新增伺服器**]。
4. 在 [**新增伺服器**] 頁面中，選擇正在探索相關機器的設備。
5. 從 [電腦] 清單中，選取電腦。
6. 按一下 [**新增伺服器**]。

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

## <a name="export-dependency-data"></a>匯出相依性資料

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 按一下 [相依性**分析**] 圖示。
3. 按一下 [**匯出應用程式**相依性]。
4. 在 [**匯出應用程式**相依性] 頁面中，選擇正在探索相關機器的設備。
5. 選取 [開始時間] 和 [結束時間]。 請注意，您只可以下載過去30天的資料。
6. 按一下 [**匯出**相依性]。

相依性資料是以 CSV 格式匯出和下載。 下載的檔案包含針對相依性分析啟用的所有電腦上的相依性資料。 

![匯出相依性](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>相依性資訊

匯出之 CSV 中的每個資料列都會對應到在指定的時間位置觀察到的相依性。 

下表摘要說明匯出之 CSV 中的欄位。 請注意，只會針對已啟用無代理程式相依性分析的伺服器填入 [伺服器名稱]、[應用程式] 和 [處理] 欄位

**欄位名稱** | **詳細資料**
--- | --- 
Timeslot | 觀察到相依性的 timeslot。 <br/> 相依性資料目前會在6個小時的位置上捕捉。
來源伺服器名稱 | 來源電腦的名稱 
來源應用程式 | 來源電腦上的應用程式名稱 
來源程序 | 來源電腦上的進程名稱 
目的地伺服器名稱 | 目的地電腦的名稱
目的地 IP | 目的地電腦的 IP 位址
目的地應用程式 | 目的地電腦上的應用程式名稱
目的地進程 | 目的地電腦上的進程名稱 
目的地連接埠 | 目的地電腦上的埠號碼


## <a name="stop-dependency-discovery"></a>停止相依性探索

選擇您想要停止相依性探索的電腦。

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 按一下 [相依性**分析**] 圖示。
3. 按一下 [**移除伺服器**]。
3. 在 [**移除伺服器**] 頁面中，選擇正在探索 vm 的**設備**，以停止相依性探索。
4. 從 [電腦] 清單中，選取電腦。
5. 按一下 [**移除伺服器**]。


## <a name="next-steps"></a>後續步驟

[群組機器](how-to-create-a-group.md)以進行評量。
