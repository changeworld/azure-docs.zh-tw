---
title: 使用 Azure 實驗室服務設定實驗室來教授大型資料分析 |Microsoft Docs
description: 瞭解如何使用 Hortonworks Data Platform 的 Docker 部署 (HDP) 來設定實驗室，以教授大型資料分析。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 6ae4e658985a3974b311171e83e6243dfc4a1ae9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444024"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 HortonWorks Data Platform 的 Docker 部署設定適用于大型資料分析的實驗室

本文說明如何設定實驗室來教授大型資料分析類別。  藉由這種類型的課程，學員將瞭解如何處理大量資料，並套用機器和統計學習演算法來衍生資料見解。  學生的主要目標是瞭解如何使用資料分析工具，例如 [Apache Hadoop 的開放原始碼軟體套件](https://hadoop.apache.org/) ，它提供儲存、管理及處理大量資料的工具。

在此實驗室中，學生將使用 [Cloudera](https://www.cloudera.com/)所提供的熱門商用版 Hadoop，稱為 [Hortonworks DATA Platform (HDP) ](https://www.cloudera.com/products/hdp.html)。  具體來說，學生將使用 [HDP 的沙箱 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) ，這是一個簡單、容易使用的平臺版本，可免費使用並用於學習和實驗。  雖然此類別可能會使用 Windows 或 Linux 虛擬機器 (已部署 HDP 沙箱的 VM) ，本文將說明如何使用 Windows。

此實驗室的另一個有趣層面是，我們將使用 [Docker](https://www.docker.com/) 容器在實驗室 vm 上部署 HDP 沙箱。  每個 Docker 容器都提供自己的隔離環境，讓軟體應用程式在內部執行。  就概念而言，Docker 容器就像是嵌套的 Vm，而且可以用來根據 [Docker Hub](https://www.docker.com/products/docker-hub)上提供的容器映射，輕鬆地部署和執行各種不同的軟體應用程式。  適用于 HDP 沙箱的 Cloudera 部署腳本會自動從 Docker Hub 提取 [HDP 的沙箱 3.0.1 Docker 映射](https://hub.docker.com/r/hortonworks/sandbox-hdp) ，並執行兩個 Docker 容器：
  - 沙箱-hdp
  - 沙箱-proxy

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱 [設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。  您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需有關如何啟用 marketplace 映射的詳細資訊，請參閱 [指定實驗室建立者可用的 marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
|Marketplace 映像| 啟用 Windows 10 專業版映射，以在您的實驗室帳戶中使用。|

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。  如需如何建立教室實驗室的詳細資訊，請參閱 [設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| 中型 (嵌套的虛擬化) 。 此 VM 大小最適合用於關係資料庫、記憶體內部快取及分析。  此大小也支援巢狀虛擬化。|  
|虛擬機器映像| Windows 10 Pro|

> [!NOTE] 
> 我們需要使用中型 (嵌套虛擬化) ，因為使用 Docker 部署 HDP 沙箱需要：
>   - 具有嵌套虛擬化的 Windows Hyper-v
>   - 至少 10 GB 的 RAM

## <a name="template-machine-configuration"></a>範本電腦設定

為了設定範本電腦，我們將：
- 安裝 Docker
- 部署 HDP 沙箱
- 使用 PowerShell 和 Windows 工作排程器自動啟動 Docker 容器

### <a name="install-docker"></a>安裝 Docker

本節中的步驟是以 [Cloudera 使用 Docker 容器進行部署的指示為](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)基礎。 

若要使用 Docker 容器，您必須先在範本 VM 上安裝 Docker Desktop：

1. 請依照 [必要條件一節](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 中的步驟來安裝 [適用於 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。 

    > [!IMPORTANT] 
    > 確定未核取 [ **使用 Windows 容器，而非 Linux 容器** ] 設定選項。

1. 確定已開啟 **Windows 容器和 hyper-v 功能** 。
   ![開啟或關閉 Windows 功能](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 遵循 [ [Windows 的記憶體](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) ] 區段中的步驟，設定 Docker 的記憶體設定。

    > [!WARNING]
    > 如果您在安裝 Docker 時不慎勾選 [ **使用 Windows 容器，而非 Linux 容器** ] 選項，則不會看到記憶體設定。  若要修正此問題，您可以 [按一下 Windows 系統匣中的 Docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)，切換到使用 Linux 容器;當 Docker Desktop 功能表開啟時，請選取 [ **切換至 Linux 容器**]。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙箱

在本節中，您將部署 HDP 沙箱，然後使用瀏覽器來存取 HDP 沙箱。

1. 確定您已依照指南的[先決條件一節](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中所列的方式安裝[Git Bash](https://gitforwindows.org/) ，因為這是建議用來完成後續步驟的建議。

1. 使用 [Cloudera 的 Docker 部署及安裝指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)，完成下列各節中的步驟：
   
   -    部署 HDP 沙箱
   -    確認 HDP 沙箱

    > [!WARNING] 
    > 當您下載 HDP 的最新 .zip 檔案時，請確定您 *不* 會將 .zip 檔案儲存在包含空格的目錄路徑中。

    > [!NOTE] 
    > 如果您在部署期間收到的例外狀況指出尚未 **共用磁片磁碟機**，您需要與 Docker 共用您的 C 磁片磁碟機，讓 HDP 的 Linux 容器可以存取本機 Windows 檔案。  若要修正此問題，請 [按一下 Windows 系統匣中的 docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) ，以開啟 docker Desktop 功能表並選取 [ **設定**]。  當 **Docker 的 [設定** ] 對話方塊開啟時，請選取 [ **資源] > 檔案共用** ]，然後檢查 **C** 磁片磁碟機。  然後，您可以重複這些步驟來部署 HDP 沙箱。

1. 部署並執行適用于 HDP 沙箱的 Docker 容器之後，您可以啟動瀏覽器並遵循 Cloudera 的指示，開啟 [沙箱歡迎頁面](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) 並啟動 HDP 儀表板，以存取環境。

    > [!NOTE] 
    > 這些指示假設您已先將沙箱環境的本機 IP 位址對應到範本 VM 上主機檔案中的 sandbox-hdp.hortonworks.com。  如果 **您沒有這樣** 的對應，您可以流覽至，以存取沙箱的 [歡迎使用] 頁面 `http://localhost:8080` 。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>當學生登入時自動啟動 Docker 容器

為了為學生提供容易使用的體驗，我們將使用自動執行的 PowerShell 腳本：
  - 當學生啟動並聯機至其實驗室 VM 時，啟動 HDP 沙箱 Docker 容器。
  - 啟動瀏覽器並流覽至沙箱的 [歡迎使用] 頁面。
當學生登入其 VM 時，我們也會使用 Windows 工作排程器自動執行此腳本。
若要進行設定，請遵循下列步驟： [大型資料分析腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本預估

如果您想要估計此實驗室的成本，您可以使用下列範例。

針對 25 名學生的課程，其中 20 小時是已排定課程的時間，10 小時是家庭作業或指派工作的配額，此實驗室的價格為：
  - 25名學生 * (20 + 10) 小時 * 55 實驗室單位 * 0.01 美元/小時 = 412.50 美元

如需定價的詳細資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文將逐步引導您完成針對大型資料分析類別建立實驗室所需的步驟，以使用以 Docker 部署的 Hortonworks 資料平臺。  此類別類型的設定可用於類似的資料分析類別。  這項設定也適用于其他類型的類別，這些類別會使用 Docker 來進行部署。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
