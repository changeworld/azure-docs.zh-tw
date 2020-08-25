---
title: 如何-使用 Fortanix 記憶體保護區管理員執行應用程式
description: 瞭解如何使用 Fortanix 記憶體保護區管理員來轉換您的容器化映射
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: d46354b18d5c3317cc1ed67d839feb26b7b6b5d0
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815839"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>How To：使用 Fortanix 記憶體保護區管理員執行應用程式 

使用[Fortanix 記憶體保護區 Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview)和[Fortanix](https://www.fortanix.com/)中的[Fortanix 節點代理](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent)程式，開始在 Azure 機密運算中執行您的應用程式。


Fortanix 是協力廠商軟體廠商，其產品和服務建置於 Azure 基礎結構之上。 其他協力廠商提供者在 Azure 上提供類似的機密計算服務，例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)。  

> [!Note] 
 > 本檔所提及的產品不在 MICROSOFT 的控制之下。 MICROSOFT 只是為了方便起見而提供這項資訊，而這些非 MICROSOFT 產品的參考並不表示 MICROSOFT 的背書。



本教學課程說明如何將您的應用程式映射轉換成受機密計算保護的映射。 此環境使用 [Fortanix](https://www.fortanix.com/) 軟體，由 Azure 的 DCsv2 系列 Intel 已啟用 SGX 的虛擬機器提供技術支援。 此解決方案會協調重要的安全性原則，例如身分識別驗證和資料存取控制。

 如需 Fortanix 特定的支援，請加入 Fortanix 的 [ [時差](https://fortanix.com/community/) ]，並使用 channel #enclavemanager。


## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Fortanix 記憶體保護區 Manager 帳戶，請在開始之前 [註冊](https://em.fortanix.com/auth/sign-up) 。
1. 用來推送已轉換應用程式映射的私人 [Docker](https://docs.docker.com/) 登錄。
1. 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)再開始。

> [!NOTE]
> 免費試用帳戶無法存取本教學課程中使用的虛擬機器。 請升級為隨用隨付訂用帳戶。

## <a name="add-an-application-to-fortanix-enclave-manager"></a>將應用程式新增至 Fortanix 記憶體保護區管理員
1. 登入 [FORTANIX EM](https://fortanix.com)
1. 流覽至 [ **帳戶** ] 頁面，然後選取 [ **新增帳戶** ] 以建立新的帳戶。 
    
![建立帳戶](media/how-to-fortanix-enclave-manager/create-account.png)

1. 建立帳戶之後，請按 [ **選取** ] 來選取新建立的帳戶。 現在我們可以開始註冊計算節點和建立應用程式。 
1. 選取 [ **+ 應用程式** ] 按鈕以新增應用程式。 在此範例中，我們將新增 Flask 伺服器記憶體保護區操作系統應用程式。 

1. 選取記憶體保護區操作系統應用程式的 [ **新增** ] 按鈕。 

    ![新增應用程式](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > 本教學課程僅說明如何新增記憶體保護區 OS 應用程式。 [深入](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) 瞭解如何將 EDP Rust 應用程式帶入 Fortanix 記憶體保護區 Manager。 

6. 在本教學課程中，我們將使用 Fortanix 的 docker registry 作為範例應用程式。 填寫下列資訊中的詳細資料。 使用您的私人 docker 登錄來保留輸出映射。 
 
    - **應用程式名稱**： Python 應用程式伺服器
    - **描述**： Python Flask Server
    - **輸入映射名稱**： fortanix/python-flask
    - **輸出映射名稱**： fortanx-私用/python-flask-sgx
    - **ISVPRODID**：1
    - **ISVSVM**：1
    - **記憶體大小**： 1 GB
    - **執行緒計數**：128

    *選用*：執行應用程式。
    - **Docker Hub**： [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**： fortanix/python-flask

        執行下列命令：
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. 新增憑證。 使用下列詳細資料填入資訊，然後選取 **[下一步]**：
    - **網域**： myapp. 定義域. dom
    - **類型**：記憶體保護區 Manager 發出的憑證 
    - 機**碼路徑**：/appkey.pem
    - **金鑰類型**： RSA
    - **憑證路徑**：/appcert.pem
    - **RSA 金鑰大小**：2048位
    

## <a name="create-an-image"></a>建立映像
Fortanix EM 映射是應用程式的軟體版本或版本。 每個映射都與一個記憶體保護區 hash 相關聯， (MRENCLAVE) 。 
1. 在 [**新增映射**] 頁面上，輸入**輸出映射名稱**的登錄**認證**。 這些認證會用來存取將推送映射的私人 docker 登錄。 

    ![建立映射](media/how-to-fortanix-enclave-manager/create-image.png)
1. 提供影像標記並選取 [ **建立**]。

    ![新增標記](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>網域和映射允許清單 
將網域新增至允許清單的應用程式，將會從 Fortanix 記憶體保護區 Manager 取得 TLS 憑證。 同樣地，當應用程式從已轉換的影像執行時，它會嘗試聯絡 Fortanix 記憶體保護區 Manager。 然後，應用程式會要求 TLS 憑證。 

切換至左側 **的 [工作** ] 索引標籤，並核准待決的要求以允許網域和映射。 

## <a name="enroll-compute-node-agent-in-azure"></a>在 Azure 中註冊計算節點代理程式

### <a name="generate-and-copy-join-token"></a>產生和複製聯結權杖
在 Fortanix 記憶體保護區 Manager 中，您將建立權杖。 此權杖可讓 Azure 中的計算節點自行驗證。 您必須將此權杖提供給您的 Azure 虛擬機器。
1. 在管理主控台中，選取 [ **+ 註冊節點** ] 按鈕。 
1. 選取 [ **產生權杖** ] 以產生聯結權杖。 複製權杖。

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>在 Azure Marketplace 中將節點註冊至 Fortanix 節點代理程式

建立 Fortanix 節點代理程式會將虛擬機器、網路介面、虛擬網路、網路安全性群組和公用 IP 位址部署到您的 Azure 資源群組中。 虛擬機器的 Azure 訂用帳戶將以每小時計費。 建立 Fortanix 節點代理程式之前，請先參閱 DCsv2 系列的 Azure [虛擬機器定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 。 刪除不在使用中的 Azure 資源。 

1. 移至 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 並使用您的 Azure 認證登入。
1. 在搜尋列中，輸入 **Fortanix 機密計算節點代理程式**。 選取 [搜尋] 方塊中顯示的應用程式，稱為 **Fortanix 機密運算節點代理** 程式，以流覽至供應專案的首頁。 
     ![搜尋 marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. 選取 [ **立即取得**]，視需要填寫您的資訊，然後選取 [ **繼續**]。 您會被重新導向至 Azure 入口網站。 
1. 選取 [ **建立** ]，輸入 Fortanix 的機密計算節點代理程式部署頁面。
1. 在此頁面上，您將會輸入資訊以部署虛擬機器。 具體而言，此 VM 是已安裝 Fortanix 節點代理程式軟體的 Azure 中，已啟用 DCsv2 系列 Intel SGX 的虛擬機器。 節點代理程式可讓您的已轉換影像安全地在 Azure 的 Intel SGX 節點上執行。  選取您要在其中部署虛擬機器和相關聯資源的 **訂** 用帳戶和 **資源群組** 。 
 
    > [!NOTE]
    > 在 Azure 中部署 DCsv2 系列虛擬機器時，會有一些限制。 您可能需要為其他核心要求配額。 如需詳細資訊，請參閱 [Azure vm 上的機密計算解決方案](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) 。 

1. 選取可用的區域。
1. 在 [ **節點名稱** ] 欄位中輸入虛擬機器的名稱。 
1. 輸入和使用者名稱和密碼 (或 SSH 金鑰) ，以便向虛擬機器進行驗證。
1. 將預設的 OS 磁片大小保留為200，然後選取 VM 大小 (Standard_DC4s_v2 將足夠用於本教學課程) 
1. 在 [ **聯結權杖** ] 欄位中貼上先前產生的權杖。

     ![部署資源](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. 選取 [檢閱 + 建立]  。 確定驗證通過，然後選取 [ **建立**]。 當所有資源都部署完成之後，計算節點現在會在記憶體保護區 Manager 中註冊。 

## <a name="run-the-application-image-on-the-compute-node"></a>在計算節點上執行應用程式映射
執行下列命令來執行應用程式。 請務必將節點 IP、埠和已轉換的映射名稱變更為特定應用程式的輸入。 
 
在本教學課程中，要執行的命令為：     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

其中 
- *52.152.206.164* 是節點代理程式主機 IP
- *9092* 是節點代理程式所接聽的埠
- *fortanix-private/python-flask-sgx*是已轉換的應用程式，可在 Fortanix 記憶體保護區管理入口網站中 [**影像**] 資料表的 **[映射]** 索引標籤下找到。 
    
## <a name="verify-and-monitor-the-running-application"></a>驗證及監視執行中的應用程式
1. 回到 [Fortanix 記憶體保護區 Manager](https://em.fortanix.com/console)
1. 確定您是在註冊節點的 **帳戶** 內工作
1. 選取左側流覽窗格中的上方圖示，以流覽至 **管理主控台** 。 
1. 選取 [ **應用程式** ] 索引標籤
1. 確認有一個正在執行的應用程式具有相關聯的計算節點


## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和相關聯的資源。 刪除資源群組將會取消註冊與您已轉換之影像相關聯的節點。 

請選取虛擬機器的資源群組，然後選取 [刪除]。 確認資源群組的名稱，以完成刪除資源。

若要刪除您所建立的 Fortanix 記憶體保護區管理員帳戶，請移至記憶體保護區管理員中的 [ [帳戶] 頁面](https://em.fortanix.com/accounts) 。 將滑鼠停留在您想要刪除的帳戶上方。 選取右上角的垂直黑色點，然後選取 [ **刪除帳戶**]。

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Fortanix 工具，將您的應用程式映射轉換成在機密計算虛擬機器上執行。 如需有關 Azure 上機密運算虛擬機器的詳細資訊，請參閱[虛擬機器的解決方案](virtual-machine-solutions.md)。 

若要深入瞭解 Azure 的機密計算供應專案，請參閱 [azure 機密計算總覽](overview.md)

