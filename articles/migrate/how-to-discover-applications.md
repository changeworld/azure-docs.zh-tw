---
title: 使用 Azure Migrate 探索內部部署伺服器上的應用程式、角色和功能
description: 瞭解如何在具有 Azure Migrate Server 評估的內部部署伺服器上探索應用程式、角色和功能。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79453577"
---
# <a name="discover-machine-apps-roles-and-features"></a>探索機器應用程式、角色和功能

本文說明如何使用 Azure Migrate： Server 評定，探索內部部署伺服器上的應用程式、角色和功能。

探索應用程式的清查，以及在您的內部部署機器上執行的角色/功能，可協助您識別並規劃針對您的工作負載量身打造的 Azure 遷移路徑。

> [!NOTE]
> 應用程式探索目前僅供 VMware Vm 預覽，僅限探索。 我們尚未提供以應用程式為基礎的評量。 適用于內部部署 VMware Vm、Hyper-v Vm 和實體伺服器的機器型評估。

使用 Azure Migrate 進行應用程式探索：伺服器評估是無代理程式。 電腦和 Vm 上不會安裝任何內容。 伺服器評估會使用 Azure Migrate 設備，搭配電腦來賓認證來執行探索。 設備會使用 VMware Api 從遠端存取 VMware 機器。


## <a name="before-you-start"></a>在您開始使用 Intune 之前

1. 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
2. 請確定您已將 [Azure Migrate：伺服器評估工具][新增](how-to-assess.md)至專案。
4. 請檢查[vmware 需求](migrate-support-matrix-vmware.md#vmware-requirements)，以使用 Azure Migrate 設備探索和評定 vmware vm。
5. 檢查部署 Azure Migrate 設備的[需求](migrate-appliance.md)。
6. 確認應用程式探索的[支援和需求](migrate-support-matrix-vmware.md#application-discovery)。

## <a name="prepare-for-app-discovery"></a>準備應用程式探索

1. [準備設備部署](tutorial-prepare-vmware.md)。 準備工作包括確認設備設定，以及設定設備將用來存取 vCenter Server 的帳戶。
2. 請確定您有一個使用者帳戶（Windows 和 Linux 伺服器各一個），並具有您想要探索應用程式、角色和功能之電腦的系統管理員許可權。
3. [部署 Azure Migrate 設備](how-to-set-up-appliance-vmware.md)以開始探索。 若要部署設備，您可以下載 OVA 範本並將其匯入 VMware，以將設備建立為 VMware VM。 您可以設定設備，然後向 Azure Migrate 註冊。
2. 當您部署設備時，若要啟動連續探索，請指定下列各項：
    - 您要連接之 vCenter Server 的名稱。
    - 您為設備建立的認證，用來連線到 vCenter Server。
    - 您為設備建立的帳號憑證，用來連線到 Windows/Linux Vm。

部署設備並提供認證之後，應用裝置會開始持續探索 VM 中繼資料和效能資料，以及應用程式、功能和角色的探索。  應用程式探索的持續時間取決於您擁有的 Vm 數目。 500 Vm 的應用程式探索通常需要一小時的時間。

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
