---
title: 使用 Azure Migrate 探索內部部署伺服器上的應用程式、角色和功能
description: 瞭解如何在具有 Azure Migrate Server 評估的內部部署伺服器上探索應用程式、角色和功能。
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 535c8ae8c2d6e5d9d175e663a58d47dc76aa0529
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118642"
---
# <a name="discover-machine-apps-roles-and-features"></a>探索機器應用程式、角色和功能

本文說明如何使用 Azure Migrate： Server 評定，探索內部部署伺服器上的應用程式、角色和功能。

探索在內部部署機器上執行的應用程式、角色和功能的清查，有助於識別並量身打造適用于您工作負載的 Azure 遷移路徑。 應用程式探索會使用 VM 來賓認證，利用 Azure Migrate 設備來執行探索。 應用程式探索是無代理程式。 Vm 上未安裝任何內容。

> [!NOTE]
> 應用程式探索目前僅供 VMware Vm 預覽，僅限探索。 我們尚未提供以應用程式為基礎的評量。 


## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 確認您已：
    - [建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
    - [已](how-to-assess.md)將 Azure Migrate：伺服器評估工具加入至專案。
- 審查[應用程式探索支援和需求](migrate-support-matrix-vmware.md#vmware-requirements)。
- 請確定您執行應用程式探索的 Vm 已安裝 PowerShell 2.0 版或更新版本，且已安裝 VMware 工具（10.2.0 之後）。
- 檢查部署 Azure Migrate 設備的[需求](migrate-appliance.md)。


## <a name="deploy-the-azure-migrate-appliance"></a>部署 Azure Migrate 設備

1. [檢查](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 設備的需求。
2. 在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中，檢查設備需要存取的 Azure url。
3. [檢閱設備在探索和評量期間收集的資料](migrate-appliance.md#collected-data---vmware)。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)設備的連接埠存取需求。
5. [部署 Azure Migrate 設備](how-to-set-up-appliance-vmware.md)以開始探索。 若要部署設備，您可以下載 OVA 範本並將其匯入 VMware，以將設備建立為 VMware VM。 您可以設定設備，然後向 Azure Migrate 註冊。
6. 當您部署設備時，若要啟動連續探索，請指定下列各項：
    - 您要連接之 vCenter Server 的名稱。
    - 您為設備建立的認證，用來連線到 vCenter Server。
    - 您為設備建立的帳號憑證，用來連線到 Windows/Linux Vm。

部署設備並提供認證之後，應用裝置會開始持續探索 VM 中繼資料和效能資料，以及應用程式、功能和角色的探索。  應用程式探索的持續時間取決於您擁有的 Vm 數目。 500 Vm 的應用程式探索通常需要一小時的時間。

## <a name="verify-permissions"></a>驗證權限

您已[建立 vCenter Server 唯讀帳戶](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)來進行探索和評估。 唯讀帳戶需要已啟用**虛擬機器**  >  **來賓作業**的許可權，才能與 VM 進行應用程式探索。

### <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

新增使用者帳戶，如下所示：

1. 開啟 [裝置管理] 應用程式。 
2. 流覽至 [**提供 vCenter 詳細資料**] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇 [**作業系統**]、[提供帳戶的易記名稱] 和 [**使用者名稱**] / **密碼**
6. 按一下 [檔案] 。
7. 按一下 [**儲存並啟動探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>審查和匯出清查

探索結束後，如果您為應用程式探索提供認證，您可以在 Azure 入口網站中檢查並匯出應用程式清查。

1. 在 [ **Azure Migrate-伺服器**  >  **Azure Migrate：伺服器評估**] 中，按一下顯示的計數以開啟 [探索到的**伺服器**] 頁面。

    > [!NOTE]
    > 在這個階段中，您也可以選擇性地為探索到的機器設定相依性分析，以便將您想要評估的機器之間的相依性視覺化。 [深入瞭解](concepts-dependency-visualization.md)相依性分析。

2. 在 [探索到的**應用程式**] 中，按一下顯示的計數。
3. 在**應用程式清查**中，您可以查看探索到的應用程式、角色和功能。
4. 若要匯出清查，請在 [探索到的**伺服器**] 中按一下 [**匯出應用程式清查**]。

應用程式清查會以 Excel 格式匯出和下載。 **應用程式清查**工作表會顯示所有電腦上探索到的所有應用程式。

## <a name="next-steps"></a>後續步驟

- 為探索到的伺服器[建立評](how-to-create-assessment.md)量。
- 使用 Azure Migrate 評估 SQL Server 資料庫[：資料庫評估](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)。
