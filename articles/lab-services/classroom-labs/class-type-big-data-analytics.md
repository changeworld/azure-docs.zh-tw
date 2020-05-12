---
title: 使用 Azure 實驗室服務設定實驗室來教授 big data analytics |Microsoft Docs
description: 瞭解如何使用 Hortonworks Data Platform （HDP）的 Docker 部署，設定實驗室來教授 big data analytics。
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: 3035b9debdd46c1a8ba246882312e28d1a2087e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118425"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 HortonWorks 資料平臺的 Docker 部署，將實驗室設定為海量資料分析

本文說明如何設定實驗室來教授 big data analytics 類別。  透過這種類型的類別，學生將瞭解如何處理大量資料，並套用機器和統計學習演算法來衍生資料見解。  學生的主要目標是瞭解如何使用資料分析工具，例如[Apache Hadoop 的開放原始碼軟體套件](https://hadoop.apache.org/)，其中提供儲存、管理及處理 big data 的工具。

在此實驗室中，學生會使用[Cloudera](https://www.cloudera.com/)所提供的熱門商業版本 Hadoop，稱為[Hortonworks DATA Platform （HDP）](https://www.cloudera.com/products/hdp.html)。  具體來說，學生會使用[HDP 沙箱 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) ，這是一個簡單、容易使用的平臺版本，免費且可供學習和實驗之用。  雖然此類別可能會使用已部署 HDP 沙箱的 Windows 或 Linux 虛擬機器（VM），本文將說明如何使用 Windows。

此實驗室的另一個有趣層面是，我們將使用[Docker](https://www.docker.com/)容器在實驗室 vm 上部署 HDP 沙箱。  每個 Docker 容器都會提供自己的隔離環境，讓軟體應用程式在內部執行。  就概念而言，Docker 容器就像是嵌套 Vm，可以用來根據[Docker Hub](https://www.docker.com/products/docker-hub)上提供的容器映射，輕鬆地部署和執行各種軟體應用程式。  適用于 HDP 沙箱的 Cloudera 部署腳本會自動從 Docker Hub 提取[HDP 沙箱 3.0.1 Docker 映射](https://hub.docker.com/r/hortonworks/sandbox-hdp)，並執行兩個 docker 容器：
  - 沙箱-hdp
  - 沙箱-proxy

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。  您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需如何啟用 marketplace 映射的詳細資訊，請參閱[指定可供實驗室建立者使用的 marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 實驗室帳戶設定 | 指示 |
| ------------------- | ------------ |
|Marketplace 映射| 啟用 Windows 10 Pro 映射以在實驗室帳戶中使用。|

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。  如需如何建立教室實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| 中（嵌套虛擬化）。 此 VM 大小最適合用於關係資料庫、記憶體內部快取及分析。  此大小也支援嵌套虛擬化。|  
|虛擬機器映像| Windows 10 Pro|

> [!NOTE] 
> 我們需要使用 Medium （嵌套虛擬化），因為使用 Docker 部署 HDP 沙箱需要：
>   - 具有嵌套虛擬化的 Windows Hyper-v
>   - 至少 10 GB 的 RAM

## <a name="template-machine-configuration"></a>範本機器設定

為了設定範本機器，我們將：
- 安裝 Docker
- 部署 HDP 沙箱
- 使用 PowerShell 和 Windows 工作排程器自動啟動 Docker 容器

### <a name="install-docker"></a>安裝 Docker

本節中的步驟是以[使用 Docker 容器進行部署的 Cloudera 指示為](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)基礎。 

若要使用 Docker 容器，您必須先在範本 VM 上安裝 Docker Desktop：

1. 請遵循[必要條件一節](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中的步驟來安裝[適用於 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。 

    > [!IMPORTANT] 
    > 請確定未核取 [**使用 Windows 容器，而非 Linux 容器**] 設定選項。

1. 確定**Windows 容器和 hyper-v 功能**已開啟。
   ![開啟或關閉 Windows 功能](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 遵循 [ [Windows 的記憶體](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)] 區段中的步驟來設定 Docker 的記憶體配置。

    > [!WARNING]
    > 如果您在安裝 Docker 時不小心核取 [**使用 Windows 容器，而非 Linux 容器**] 選項，您將不會看到記憶體配置設定。  若要修正此問題，您可以[按一下 Windows 系統匣中的 Docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)，切換為使用 Linux 容器;當 [Docker 桌面] 功能表開啟時，選取 [**切換至 Linux 容器**]。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙箱

在本節中，您將部署 HDP 沙箱，並使用瀏覽器來存取 HDP 沙箱。

1. 請確定您已依照指南的[必要條件一節](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中所列的方式安裝[Git Bash](https://gitforwindows.org/) ，因為這是建議用來完成後續步驟的做法。

1. [針對 Docker 使用 Cloudera 的部署與安裝指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)，請完成下列各節中的步驟：
   
   -    部署 HDP 沙箱
   -    驗證 HDP 沙箱

    > [!WARNING] 
    > 當您下載 HDP 的最新 .zip 檔案時，請確定您*不*會將 .zip 檔案儲存在包含空白字元的目錄路徑中。

    > [!NOTE] 
    > 如果您在部署期間收到例外狀況，指出**磁片磁碟機尚未共用**，則您需要與 Docker 共用 C 磁片磁碟機，讓 HDP 的 Linux 容器可以存取本機 Windows 檔案。  若要修正此問題，請[按一下 Windows 系統匣中的 docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)，以開啟 docker Desktop 功能表，然後選取 [**設定**]。  當**Docker 的 [設定**] 對話方塊開啟時，請選取 [**資源] >** [檔案共用]，然後檢查**C**磁片磁碟機。  接著，您可以重複步驟來部署 HDP 沙箱。

1. 一旦部署並執行 HDP 沙箱的 Docker 容器，您就可以啟動瀏覽器，並遵循 Cloudera 的指示來開啟[沙箱歡迎頁面](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)並啟動 HDP 儀表板，以存取環境。

    > [!NOTE] 
    > 這些指示假設您已先將沙箱環境的本機 IP 位址對應至範本 VM 上主機檔案中的 sandbox-hdp.hortonworks.com。  如果您**未**執行此對應，可以流覽至來存取沙箱歡迎頁面 `http://localhost:8080` 。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>當學生登入時自動啟動 Docker 容器

為了為學生提供容易使用的體驗，我們將使用 PowerShell 腳本來自動執行下列動作：
  - 當學生啟動並聯機至其實驗室 VM 時，啟動 HDP 沙箱 Docker 容器。
  - 啟動瀏覽器，並導覽至沙箱歡迎使用頁面。
當學生登入其 VM 時，我們也會使用 Windows 工作排程器自動執行此腳本。
若要進行此設定，請遵循下列步驟： [Big Data Analytics 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本預估

如果您想要估計此實驗室的成本，可以使用下列範例。

對於25名學生的課程，其中有20小時的排程類別時間，以及10小時的家庭作業或指派配額，實驗室的價格如下：
  - 25名學生 * （20 + 10）小時 * 55 實驗室單位 * 每小時0.01 美元 = 412.50 美元

如需更多有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文逐步解說針對使用 Docker 部署之 Hortonworks 資料平臺的 big data 分析類別建立實驗室所需的步驟。  此類別類型的設定可用於類似的資料分析類別。  此設定可能也適用于使用 Docker 進行部署的其他類別類型。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
