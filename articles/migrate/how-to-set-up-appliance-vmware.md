---
title: 設定 VMware 的 Azure Migrate 設備
description: 瞭解如何設定 Azure Migrate 設備，以評估和遷移 VMware Vm。
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538251"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>設定適用于 VMware Vm 的應用裝置

請遵循這篇文章來設定 Azure Migrate 應用裝置，以使用[Azure Migrate：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具進行評估，並使用[Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具進行無代理程式遷移。

[Azure Migrate 設備](migrate-appliance.md)是一種輕量設備，Azure Migrate：伺服器評估和伺服器遷移，用來探索內部部署 VMware vm、將 VM 中繼資料/效能資料傳送至 Azure，以及在無代理程式遷移期間複寫 VMware vm。

您可以使用幾種方法來部署應用裝置：

- 使用下載的 OVA 範本，在 VMware VM 上進行設定。 這是本文中所述的方法。
- 使用 PowerShell 安裝程式指令碼在 VMware VM 或實體機器上進行設定。 如果您無法使用 OVA 範本設定 VM，或如果您是在 Azure Government 中，則應該使用[此方法](deploy-appliance-script.md)。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。


## <a name="appliance-deployment-ova"></a>設備部署（OVA）

若要使用 OVA 範本來設定設備，您可以：
- 下載 OVA 範本檔案，並將其匯入 vCenter Server。
- 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
- 第一次設定設備，並向 Azure Migrate 專案進行註冊。

## <a name="download-the-ova-template"></a>下載 OVA 範本

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [是，使用 VMWare vSphere Hypervisor]  。
3. 按一下 [下載]**** 以下載 .OVA 範本檔案。

  ![用於下載 OVA 檔案的選項](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>確認安全性

請先確認 OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令，以產生 OVA 的雜湊：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 針對最新的設備版本，產生的雜湊應符合這些[設定](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)。



## <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 在 vSphere 用戶端主控台中，**按一下** > [檔案] [**部署 OVF 範本**]。
![用於部署 OVF 範本的功能表命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在 [部署 OVF 範本精靈] > [來源]  中，指定 OVA 檔案的位置。
3. 在 [名稱]   和 [位置] 中，指定 VM 的易記名稱。 選取將裝載 VM 的庫存物件。
5. 在 [主機/叢集]  中，指定 VM 的執行所在主機或叢集。
6. 在**儲存體**中，指定 VM 的儲存目的地。
7. 在 [磁碟格式]  中，指定磁碟類型和大小。
8. 在 [**網路對應**] 中，指定 VM 將連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure Migrate 伺服器評量。
9. 檢閱並確認設定，然後按一下 [完成]****。


## <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確定設備 VM 可以連線至[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端的 Azure URL。


## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。 如果您使用腳本（而不是 OVA 範本）來部署設備，程式中的前兩個步驟並不適用。

1. 在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]****。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址  :44368**。

   或者，您也可以按一下應用程式捷徑，從設備桌面開啟應用程式。
4. 在 [Web 應用程式] > [設定必要條件]  中，執行下列動作：
    - **授權**：接受授權條款，並閱讀第三方資訊。
    - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
        - 按一下 [Proxy 設定]  ，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，探索才能正常運作。
    - **安裝更新**： Azure Migrate 檢查是否已安裝最新的設備更新。
    - **安裝 VDDK**： Azure Migrate 檢查是否已安裝 VMWare VSphere 虛擬磁片開發工具組（VDDK）。
        - Azure 遷移會使用 VDDK 在遷移至 Azure 期間複寫機器。
        - 從 VMware 下載 VDDK 6.7，並將下載的 zip 內容解壓縮到設備上的指定位置。

## <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]  。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
2. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
3. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
4. 按一下 [註冊]  。


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>藉由提供 vCenter Server 和 VM 認證來開始連續探索

設備必須連線到 vCenter Server，才能探索 VM 的設定與效能資料。

### <a name="specify-vcenter-server-details"></a>指定 vCenter Server 詳細資料
1. 在 [指定 vCenter Server 詳細資料]**** 中，指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。 您可以保留預設的連接埠，或指定您 vCenter Server 接聽的自訂連接埠。
2. 在 [使用者名稱]**** 和 [密碼]**** 中，指定設備要用來探索 vCenter Server 上之 VM 的唯讀帳戶認證。 您可以藉由限制對 vCenter 帳戶的存取權，來界定探索範圍。 [深入了解](set-discovery-scope.md)。
3. 按一下 [驗證連線]**** 以確定設備可以連線到 vCenter Server。

### <a name="specify-vm-credentials"></a>指定 VM 認證
若要探索應用程式、角色和功能，以及視覺化 VM 的相依性，您可以提供可存取 VMware VM 的 VM 認證。 您可以新增一個適用於 Windows VM 的認證，以及一個適用於 Linux VM 的認證。 [深入了解](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)所需的存取權限。

> [!NOTE]
> 這是選擇性的輸入，而且是啟用應用程式探索和無代理程式相依性視覺效果的必要項。

1. 在 [探索 VM 上的應用程式和相依性]**** 中，按一下 [新增認證]****。
2. 選取 [作業系統]****。
3. 為認證提供易記名稱。
4. 在 [使用者名稱]**** 與 [密碼]**** 中，指定至少在 VM 上具有來賓存取權的帳戶。
5. 按一下 [加入]  。

一旦指定 vCenter Server 與 VM 認證 (選擇性)，請按一下 [儲存並開始探索]**** 以開始探索內部部署環境。

所探索到 VM 的中繼資料會在大約 15 分鐘後出現在入口網站中。 探索已安裝的應用程式、角色和功能需要一些時間，持續時間取決於所探索到的 VM 數目。 針對 500 部 VM，應用程式清查大約需要 1 小時才會出現在 Azure Migrate 入口網站中。

## <a name="next-steps"></a>後續步驟

回顧[VMware 評估](tutorial-assess-vmware.md)和[無代理程式遷移](tutorial-migrate-vmware.md)的教學課程。
