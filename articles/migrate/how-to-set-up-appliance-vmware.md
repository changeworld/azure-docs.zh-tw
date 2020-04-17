---
title: 為 VMware 設定 Azure 移轉裝置
description: 瞭解如何設置 Azure 遷移設備以評估和遷移 VMware VM。
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538251"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>為 VMware VM 設定裝置

請按照本文設置 Azure 遷移設備以使用 Azure[遷移:伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具進行評估,並使用[Azure 遷移:伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具進行無代理遷移。

[Azure 移植裝置](migrate-appliance.md)是 Azure 移轉:伺服器評估和伺服器遷移使用的輕量級設備,用於發現本地 VMware VM、向 Azure 發送 VM 中繼資料/效能數據,以及用於在無代理遷移期間複製 VMware VM。

可以使用以下幾種方法部署裝置:

- 使用下載的 OVA 範本在 VMware VM 上設置。 這是本文中描述的方法。
- 使用 PowerShell 安裝程式文本在 VMware VM 或物理電腦上設置。 如果無法使用 OVA 樣本設定 VM,或者您位於 Azure 政府中,則應使用[此方法](deploy-appliance-script.md)。

建立設備之後，您會檢查其是否可以連線到 Azure Migrate：伺服器評量、進行第一次設，以及向 Azure Migrate 專案註冊設備。


## <a name="appliance-deployment-ova"></a>裝置部署 (OVA)

要使用 OVA 樣本設定裝置,請:
- 下載 OVA 範本檔案，並將其匯入 vCenter Server。
- 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
- 第一次設定設備，並向 Azure Migrate 專案進行註冊。

## <a name="download-the-ova-template"></a>下載 OVA 範本

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在**顯示電腦** > **中,您的電腦是否虛擬化嗎?** 點擊「**是」,使用 VMWare vSphere 虛擬機器管理程式**。
3. 按一下 [下載]**** 以下載 .OVA 範本檔案。

  ![用於下載 OVA 檔案的選項](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>確認安全性

請先確認 OVA 檔案是安全的，再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行以下指令,以產生 OVA 的哈希:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 對於最新的設備版本,生成的哈希應與這些[設置](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)匹配。



## <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 在 vSphere 用戶端控制台中,按下 **「檔** > **部署 OVF 範本**」。。
![用於部署 OVF 範本的功能表命令](./media/tutorial-assess-vmware/deploy-ovf.png)

2. 在 [部署 OVF 範本精靈] > [來源]  中，指定 OVA 檔案的位置。
3. 在 [名稱]   和 [位置] 中，指定 VM 的易記名稱。 選取將裝載 VM 的庫存物件。
5. 在 [主機/叢集]  中，指定 VM 的執行所在主機或叢集。
6. 在**儲存體**中，指定 VM 的儲存目的地。
7. 在 [磁碟格式]  中，指定磁碟類型和大小。
8. 在**網路映射**中 ,指定 VM 將連接到的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure Migrate 伺服器評量。
9. 檢閱並確認設定，然後按一下 [完成]****。


## <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

確保設備 VM 可以連接到[公共](migrate-appliance.md#public-cloud-urls)[和政府雲](migrate-appliance.md#government-cloud-urls)的 Azure URL。


## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。 如果使用腳本而不是 OVA 範本部署設備,則該過程的前兩個步驟不適用。

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
    - **安裝更新**:Azure 遷移檢查是否安裝了最新的設備更新。
    - **安裝 VDDK**:Azure 遷移檢查是否安裝了 VMWare vSphere 虛擬磁碟開發工具套件 (VDDK)。
        - Azure 遷移使用 VDDK 在遷移到 Azure 期間複製電腦。
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


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>透過提供 vCenter 伺服器和 VM 認證開始連續發現

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
5. 按一下 [新增]  。

一旦指定 vCenter Server 與 VM 認證 (選擇性)，請按一下 [儲存並開始探索]**** 以開始探索內部部署環境。

所探索到 VM 的中繼資料會在大約 15 分鐘後出現在入口網站中。 探索已安裝的應用程式、角色和功能需要一些時間，持續時間取決於所探索到的 VM 數目。 針對 500 部 VM，應用程式清查大約需要 1 小時才會出現在 Azure Migrate 入口網站中。

## <a name="next-steps"></a>後續步驟

查看[VMware 評估和](tutorial-assess-vmware.md)[無代理遷移](tutorial-migrate-vmware.md)的教程。
