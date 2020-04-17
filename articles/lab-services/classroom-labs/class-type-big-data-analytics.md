---
title: 設置實驗室,使用 Azure 實驗室服務教授大數據分析 |微軟文件
description: 瞭解如何使用霍頓工程數據平臺 (HDP) 的 Docker 部署設置實驗室來教授大數據分析。
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
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538774"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 Docker 部署霍頓工程數據平臺,為大數據分析設置實驗室

本文介紹如何設置實驗室來教授大數據分析類。  通過此類課程,學生學習如何處理大量數據,並應用機器和統計學習演演演算法來獲取數據見解。  學生的關鍵目標是學習使用數據分析工具,例如[Apache Hadoop 的開源套件](https://hadoop.apache.org/),它提供了用於存儲、管理和處理大數據的工具。

在這個實驗室中,學生將使用[由Cloudera](https://www.cloudera.com/)提供的廣受歡迎的商業版本Hadoop,稱為[霍頓工程數據平臺(HDP)。](https://www.cloudera.com/products/hdp.html)  具體來說,學生將使用[HDP 沙箱 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html)這是一個簡化、易於使用的平臺版本,是免費的,用於學習和實驗。  儘管此類在部署 HDP 沙箱時可以使用 Windows 或 Linux 虛擬機 (VM),但本文將介紹如何使用 Windows。

本實驗的另一個有趣的方面是,我們將使用[Docker](https://www.docker.com/)容器在實驗室 VM 上部署 HDP 沙箱。  每個 Docker 容器都為軟體應用程式提供了自己的隔離環境,以便內部運行。  從概念上講,Docker 容器類似於嵌套 VM,可用於根據[Docker Hub](https://www.docker.com/products/docker-hub)上提供的容器映射輕鬆部署和運行各種軟體應用程式。  Cloudera 用於 HDP 沙箱的部署文稿會自動從 Docker 集線器中拉出[HDP 沙箱 3.0.1 Docker 映像](https://hub.docker.com/r/hortonworks/sandbox-hdp)並運行兩個 Docker 容器:
  - 沙箱-hdp
  - 沙箱代理

## <a name="lab-configuration"></a>實驗室組態

要設置此實驗,需要 Azure 訂閱和實驗室帳戶才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲取 Azure 訂閱後,可以在 Azure 實驗室服務中創建新的實驗室帳戶。 有關創建新實驗室帳戶的詳細資訊,請參閱[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。  您還可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

為實驗室帳戶啟用下表中描述的設置。 有關如何啟用市場映像的詳細資訊,請參閱[指定可供實驗室建立者使用的市場映像](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
|市場映射| 啟用 Windows 10 專業版映射,以便在實驗室帳戶中使用。|

### <a name="lab-settings"></a>實驗室設定

設置教室實驗室時,請使用下表中的設置。  有關如何創建教室實驗室的詳細資訊,請參閱[設置教室實驗室教程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/說明 |
| ------------ | ------------------ |
|虛擬機器大小| 中等(嵌套虛擬化)。 此 VM 大小最適合關係資料庫、記憶體緩存和分析。  此大小還支援嵌套虛擬化。|  
|虛擬機器映像| Windows 10 Pro|

> [!NOTE] 
> 我們需要使用媒體(嵌套虛擬化),因為使用 Docker 部署 HDP 沙箱需要:
>   - 具有嵌入虛擬化 Windows Hyper-V
>   - 至少 10 GB 的 RAM

## <a name="template-machine-configuration"></a>樣本機設定

要設置範本計算機,我們將:
- 安裝 Docker
- 部署 HDP 沙箱
- 使用 PowerShell 與 Windows 工作計劃程式自動啟動 Docker 容器

### <a name="install-docker"></a>安裝 Docker

在您區塊的步驟基於[Cloudera 使用 Docker 容器進行部署的說明](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)。 

要使用 Docker 容器,必須在樣本 VM 安裝 Docker 桌面:

1. 按照[「先決條件」部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中的步驟安裝 Windows 的[Docker。](https://docs.docker.com/docker-for-windows/install/) 

    > [!IMPORTANT] 
    > 確保未選取**的 「使用 Windows 容器」而不是 Linux 容器**配置選項。

1. 確保**Windows 容器與超 V 功能**。
   ![開啟或關閉 Windows 功能](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 按照[「記憶體為 Windows」](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)部分中的步驟配置 Docker 的記憶體配置。

    > [!WARNING]
    > 如果在安裝 Docker 時無意中選取**的 「使用 Windows 容器」而不是 Linux 容器**選項,則看不到記憶體設定設定。  要解決此問題,您可以通過[按一下 Windows 系統托盤中的 Docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)切換到使用 Linux 容器;開啟 Docker 桌面選單時,選擇 **「切換到 Linux 容器**」。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙箱

在本節中,您將部署 HDP 沙箱,然後使用瀏覽器訪問 HDP 沙箱。

1. 請確保您已安裝本指南[「先決條件」部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中列出的[Git Bash,](https://gitforwindows.org/)因為建議完成後續步驟。

1. 使用[Cloudera 的 Docker 部署和安裝指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html),完成以下各節中的步驟:
   
   -    部署 HDP 沙箱
   -    驗證 HDP 沙箱

    > [!WARNING] 
    > 下載 HDP 的最新 .zip 檔時,請確保*不要*將 .zip 檔儲存在包含空白的目錄路徑中。

    > [!NOTE] 
    > 如果您在部署期間收到異常,指出**驅動器尚未共用**,則需要與 Docker 共用 C 驅動器,以便 HDP 的 Linux 容器可以存取本地 Windows 檔。  要解決此問題,[請按下 Windows 系統匣中的 Docker 圖示](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)以打開 Docker 桌面選單並選擇 **"設定**" 。  開啟**Docker 的「設定」** 對話框時,選擇 **「資源>檔共用**」並檢查**C**驅動器。  然後,您可以重複部署 HDP 沙箱的步驟。

1. 部署並運行 HDP 沙箱的 Docker 容器後,您可以透過啟動瀏覽器並按照 Cloudera 的說明存取環境,以打開[沙箱歡迎頁面](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)並啟動 HDP 儀表板。

    > [!NOTE] 
    > 這些說明假定您首先將沙箱環境的本地 IP 位址映射到範本 VM 上的主機檔中的 sandbox-hdp.hortonworks.com。  **如果不**執行此操作映射,可以通過[http://localhost:8080](http://localhost:8080)導航到 訪問"沙盒歡迎"頁。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>學生登入時自動啟動 Docker 容器

為了為學生提供易於使用的體驗,我們將使用自動使用 PowerShell 腳本:
  - 當學生啟動並連接到其實驗室 VM 時,啟動 HDP 沙箱 Docker 容器。
  - 啟動瀏覽器並導航到沙盒歡迎頁面。
我們還將使用 Windows 任務計劃程式在學生登錄到其 VM 時自動運行此文本。
要設定此設定,請按照以下步驟操作:[大數據分析腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本估算

如果要估計本實驗的成本,可以使用以下示例。

對於有 25 名學生的班級,有 20 小時的上課時間和 10 小時的家庭作業或作業配額,實驗室的價格是:
  - 25 名學生 = (20 + 10) 小時 = 55 個實驗室單位 = 每小時 0.01 USD = 412.50 USD

有關定價的更多詳細資訊,請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文介紹了為使用 Docker 部署的 Hortonworks 數據平臺為大數據分析類創建實驗室所需的步驟。  此類類型的設置可用於類似的數據分析類。  此設定還可能適用於使用 Docker 進行部署的其他類型的類。

## <a name="next-steps"></a>後續步驟

後續步驟是設置任何實驗室的常見步驟。

- [建立及管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定計劃](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)
