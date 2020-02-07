---
title: 使用無代理程式相依性視覺效果將電腦群組 Azure Migrate
description: 說明如何以無代理程式的方式，使用機器相依性建立群組。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: c8ddd343cd00b24506382521361ebad33ad112a7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049765"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>設定無代理程式相依性視覺效果以進行評量

本文說明如何在 Azure Migrate：伺服器評估中設定無代理程式相依性對應。 

> [!IMPORTANT]
> 無代理程式相依性視覺效果目前為使用 Azure Migrate 設備探索到的 Azure VMware Vm 預覽。
> 可能不支援特定功能，或可能已經限制功能。 此預覽涵蓋于客戶支援，並可用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="about-dependency-mapping"></a>關於相依性對應

相依性對應可協助您將要評估和遷移之電腦的相依性視覺化。 當您想要評估具有較高層級信賴度的電腦時，通常會使用相依性對應。

- 在 Azure Migrate：伺服器評估中，您會將機器一起收集成群組以進行評量。 群組通常是由您要一起遷移的機器組成，而相依性對應可協助您交叉檢查機器相依性，讓您可以精確地將機器分組。
- 使用對應時，您可以探索需要一起遷移的相互相依系統。 您也可以識別執行中的系統是否仍為使用者提供服務，或者是否為解除委任而非遷移的候選。
- 視覺化相依性有助於確保不會留下任何內容，並避免在遷移期間發生意外的中斷。

## <a name="about-agentless-visualization"></a>關於無代理程式視覺效果

無代理程式相依性視覺效果不會要求您在電腦上安裝任何代理程式。 其運作方式是從已啟用它的機器中，捕獲 TCP 連接資料。

- 相依性探索開始之後，應用裝置會以五分鐘的輪詢間隔從機器收集資料。
- 收集的資料如下：
    - TCP 連線
    - 具有使用中連接的進程名稱
    - 執行上述進程的已安裝應用程式名稱
    - 否。 在每個輪詢間隔偵測到的連線數

## <a name="current-limitations"></a>目前的限制

- 無代理程式相依性視覺效果目前僅適用于 VMware Vm。
- 目前您無法在 [相依性分析] 視圖中，從群組新增或移除伺服器。
- 伺服器群組的相依性對應目前無法使用。
- 目前，無法以表格格式下載相依性資料。

## <a name="before-you-start"></a>開始之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 無代理程式相依性分析目前僅適用于 VMware 機器。
- 如果您已經建立專案，請確定您已[新增](how-to-assess.md)Azure Migrate：伺服器評估工具。
- 請確定您已在 Azure Migrate 中探索到 VMware 電腦;您可以藉由設定適用于[VMware](how-to-set-up-appliance-vmware.md)的 Azure Migrate 設備來完成這項操作。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [詳細資訊](migrate-appliance.md)。
- 檢查設定無代理程式相依性視覺效果[的需求](migrate-support-matrix-vmware.md#agentless-dependency-visualization)。



## <a name="create-a-user-account-for-discovery"></a>建立用於探索的使用者帳戶

設定具有必要許可權的使用者帳戶，讓伺服器評估可以存取 VM 進行探索。 您可以指定一個使用者帳戶。

- **Windows vm 上的必要許可權**：使用者帳戶必須是本機或網域系統管理員。
- **Linux vm 上的必要許可權**：帳戶上需要根許可權。 或者，使用者帳戶需要/bin/netstat 和/bin/ls 檔案上的這兩項功能： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。

## <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

您必須將使用者帳戶新增至設備。

新增帳戶，如下所示：

1. 開啟 [裝置管理] 應用程式。 流覽至 [**提供 vCenter 詳細資料**] 面板。
2. 在 [**探索 vm 上的應用程式和**相依性] 區段中，按一下 [**新增認證**]
3. 選擇**作業系統**。
4. 提供帳戶的易記名稱。
5. 提供**使用者名稱**和**密碼**
6. 按一下 [檔案]。
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

啟動相依性探索之後，您將能夠以視覺化方式呈現6小時的相依性。

## <a name="visualize-dependencies"></a>將相依性視覺化

1. 在 [ **Azure Migrate：伺服器評定**] 中，按一下 [探索到的**伺服器**]。
2. 搜尋您要查看其相依性對應的電腦。
3. 按一下 **[相依性] 資料**行中的 [查看相依性 **]** 。
4. 使用 [**持續**時間] 下拉式清單，變更您想要查看對應的時間週期。
5. 展開**用戶端**群組，列出相依于所選電腦的電腦。
6. 展開**埠**群組，列出與所選機器具有相依性的電腦。
7. 若要流覽至任何相依電腦的地圖視圖，請按一下電腦名稱稱，然後按一下 [**載入伺服器對應**]

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

[將機器分組](how-to-create-a-group.md)
