---
title: 使用 Azure Migrate 探索內部部署伺服器上的應用程式、角色和功能
description: 瞭解如何在內部部署伺服器上，利用 Azure Migrate Server 評量探索應用程式、角色和功能。
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 6bdc26ac3a27026183e889bf258e2e1a5dadebfb
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315202"
---
# <a name="discover-machine-apps-roles-and-features"></a>探索機器應用程式、角色和功能

本文說明如何使用 Azure Migrate：伺服器評量，在內部部署伺服器上探索應用程式、角色和功能。

探索在內部部署機器上執行的應用程式、角色和功能的清查，有助於識別和量身訂做工作負載的 Azure 遷移路徑。 應用程式探索會使用 Azure Migrate 設備，利用 VM 來賓認證來執行探索。 應用程式探索是無代理程式。 Vm 上未安裝任何專案。

> [!NOTE]
> 應用程式探索目前僅供 VMware Vm 預覽，且僅限探索。 我們尚未提供以應用程式為基礎的評量。 


## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 確認您已：
    - [建立](how-to-add-tool-first-time.md) Azure Migrate 專案。
    - [已](how-to-assess.md) 將 Azure Migrate：伺服器評定工具新增至專案。
- 請參閱 [應用程式探索支援和需求](migrate-support-matrix-vmware.md#vmware-requirements)。
- 確定您正在執行應用程式探索的 Vm 已安裝 PowerShell 2.0 版或更新版本，且安裝的 VMware 工具 (晚于 10.2.0) 。
- 檢查部署 Azure Migrate 設備的 [需求](migrate-appliance.md) 。


## <a name="deploy-the-azure-migrate-appliance"></a>部署 Azure Migrate 設備

1. 請[參閱](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 設備的需求。
2. 查看設備在 [公用](migrate-appliance.md#public-cloud-urls) 和 [政府](migrate-appliance.md#government-cloud-urls)雲端中需要存取的 Azure url。
3. [檢閱設備在探索和評量期間收集的資料](migrate-appliance.md#collected-data---vmware)。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)設備的連接埠存取需求。
5. [部署 Azure Migrate 設備](how-to-set-up-appliance-vmware.md) 以開始探索。 若要部署設備，您可以下載 OVA 範本，並將其匯入 VMware，以將設備建立為 VMware VM。 您可以設定設備，然後向 Azure Migrate 註冊。
6. 當您部署設備時，若要開始連續探索，請指定下列各項：
    - 您要連接之 vCenter Server 的名稱。
    - 您為設備所建立以連線至 vCenter Server 的認證。
    - 您為設備建立的帳號憑證，以連線至 Windows/Linux Vm。

部署設備並提供認證之後，設備會開始連續探索 VM 中繼資料和效能資料，以及探索應用程式、功能和角色。  應用程式探索的持續時間取決於您擁有多少部 Vm。 500 Vm 的應用程式探索通常需要一小時的時間。

## <a name="verify-permissions"></a>驗證權限

您已 [建立一個 vCenter Server 唯讀帳戶](./tutorial-discover-vmware.md#prepare-vmware) 以進行探索和評量。 唯讀帳戶需要啟用**虛擬機器**  >  **來賓作業**的許可權，才能與 VM 互動以進行應用程式探索。

### <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

新增使用者帳戶，如下所示：

1. 開啟設備管理應用程式。 
2. 流覽至 [ **提供 vCenter 詳細資料** ] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇**作業系統**、提供帳戶的易記名稱和**使用者名稱** / **密碼**
6. 按一下 [檔案]  。
7. 按一下 [ **儲存並開始探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>檢查和匯出清查

探索結束之後，如果您提供應用程式探索的認證，您可以在 Azure 入口網站中檢查和匯出應用程式清查。

1. 在 [ **Azure Migrate-伺服器**  >  **Azure Migrate：伺服器評**量] 中，按一下顯示的計數以開啟 [探索到的**伺服器**] 頁面。

    > [!NOTE]
    > 在這個階段，您也可以選擇性地為探索到的機器設定相依性分析，讓您可以在想要評估的機器上視覺化相依性。 [深入瞭解](concepts-dependency-visualization.md) 相依性分析。

2. 在 **探索到的應用程式**中，按一下顯示的計數。
3. 在 **應用程式清查**中，您可以檢查探索到的應用程式、角色和功能。
4. 若要匯出清查，請在探索到的 **伺服器**中，按一下 [ **匯出應用程式清查**]。

應用程式清查會以 Excel 格式匯出和下載。 **應用程式清查**工作表會顯示所有機器上探索到的所有應用程式。

## <a name="next-steps"></a>後續步驟

- 為探索到的伺服器[建立評](how-to-create-assessment.md)量。
- 使用 [Azure Migrate：資料庫評估](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)來評估 SQL Server 資料庫。