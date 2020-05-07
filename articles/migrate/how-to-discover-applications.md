---
title: 使用 Azure Migrate 探索內部部署伺服器上的應用程式、角色和功能
description: 瞭解如何在具有 Azure Migrate Server 評估的內部部署伺服器上探索應用程式、角色和功能。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744622"
---
# <a name="discover-machine-apps-roles-and-features"></a>探索機器應用程式、角色和功能

本文說明如何使用 Azure Migrate： Server 評定，探索內部部署伺服器上的應用程式、角色和功能。

探索應用程式的清查，以及在您的內部部署機器上執行的角色/功能，可協助您識別並規劃針對您的工作負載量身打造的 Azure 遷移路徑。

> [!NOTE]
> 應用程式探索目前僅供 VMware Vm 預覽，僅限探索。 我們尚未提供以應用程式為基礎的評量。 適用于內部部署 VMware Vm、Hyper-v Vm 和實體伺服器的機器型評估。

使用 Azure Migrate 進行應用程式探索：伺服器評估是無代理程式。 電腦和 Vm 上不會安裝任何內容。 伺服器評估會使用 Azure Migrate 設備，搭配電腦來賓認證來執行探索。 設備會使用 VMware Api 從遠端存取 VMware 機器。


## <a name="before-you-start"></a>開始之前

1. 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
2. 請確定您已將 [Azure Migrate：伺服器評估工具][新增](how-to-assess.md)至專案。
4. 請檢查[vmware 需求](migrate-support-matrix-vmware.md#vmware-requirements)，以使用 Azure Migrate 設備探索和評定 vmware vm。
5. 檢查部署 Azure Migrate 設備的[需求](migrate-appliance.md)。
6. 確認應用程式探索的[支援和需求](migrate-support-matrix-vmware.md#application-discovery)。



## <a name="deploy-the-azure-migrate-appliance"></a>部署 Azure Migrate 設備

1. [檢查](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 設備的需求。
2. 在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中，檢查設備需要存取的 Azure url。
3. [檢閱設備在探索和評量期間收集的資料](migrate-appliance.md#collected-data---vmware)。
4. [注意](migrate-support-matrix-vmware.md#port-access)設備的連接埠存取需求。
5. [部署 Azure Migrate 設備](how-to-set-up-appliance-vmware.md)以開始探索。 若要部署設備，您可以下載 OVA 範本並將其匯入 VMware，以將設備建立為 VMware VM。 您可以設定設備，然後向 Azure Migrate 註冊。
6. 當您部署設備時，若要啟動連續探索，請指定下列各項：
    - 您要連接之 vCenter Server 的名稱。
    - 您為設備建立的認證，用來連線到 vCenter Server。
    - 您為設備建立的帳號憑證，用來連線到 Windows/Linux Vm。

部署設備並提供認證之後，應用裝置會開始持續探索 VM 中繼資料和效能資料，以及應用程式、功能和角色的探索。  應用程式探索的持續時間取決於您擁有的 Vm 數目。 500 Vm 的應用程式探索通常需要一小時的時間。

## <a name="prepare-a-user-account"></a>準備使用者帳戶

建立用於探索的帳戶，並將它新增至設備。

### <a name="create-a-user-account-for-discovery"></a>建立用於探索的使用者帳戶

設定使用者帳戶，讓伺服器評估可以存取 VM 進行探索。 [瞭解](migrate-support-matrix-vmware.md#application-discovery)帳戶需求。


### <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶新增至設備

將使用者帳戶新增至設備。

1. 開啟 [裝置管理] 應用程式。 
2. 流覽至 [**提供 vCenter 詳細資料**] 面板。
3. 在 [**探索 vm 上的應用程式和**相依性] 中，按一下 [**新增認證**]
3. 選擇 [**作業系統**]、[提供帳戶的易記名稱] 和 [**使用者名稱**/]**密碼**
6. 按一下 [檔案]  。
7. 按一下 [**儲存並啟動探索**]。

    ![新增 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>審查和匯出清查

探索結束後，如果您為應用程式探索提供認證，您可以在 Azure 入口網站中檢查並匯出應用程式清查。

1. 在 [ **Azure Migrate-伺服器** > **Azure Migrate：伺服器評估**] 中，按一下顯示的計數以開啟 [探索到的**伺服器**] 頁面。

    > [!NOTE]
    > 在這個階段中，您也可以選擇性地為探索到的機器設定相依性對應，以便將您想要評估的機器之間的相依性視覺化。 [深入了解](how-to-create-group-machine-dependencies.md)。

2. 在 [探索到的**應用程式**] 中，按一下顯示的計數。
3. 在**應用程式清查**中，您可以查看探索到的應用程式、角色和功能。
4. 若要匯出清查，請在 [探索到的**伺服器**] 中按一下 [**匯出應用程式清查**]。

應用程式清查會以 Excel 格式匯出和下載。 **應用程式清查**工作表會顯示所有電腦上探索到的所有應用程式。

## <a name="next-steps"></a>後續步驟

- 針對探索到的伺服器，建立隨即轉移的[評估](how-to-create-assessment.md)。
- 使用 Azure Migrate 評估 SQL Server 資料庫[：資料庫評估](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)。
