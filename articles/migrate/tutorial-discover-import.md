---
title: 透過 Azure Migrate 伺服器評量使用匯入的 CSV 檔案來評估內部部署伺服器
description: 說明如何在 Azure Migrate 伺服器評量中使用匯入的 CSV 檔案來探索內部部署伺服器是否可移轉至 Azure
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753104"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>教學課程：使用匯入的 CSV 檔案評估伺服器

在遷移至 Azure 的過程中，您會探索內部部署清查和工作負載。 

本教學課程說明如何透過「Azure Migrate：伺服器評量工具」評估內部部署機器：伺服器評估工具，使用匯入的逗號分隔值 (CSV) 檔案。 

如果您使用 CSV 檔案，就不需要設定 Azure Migrate 設備來探索及評估伺服器。 您可以控制您在檔案中共用的資料，而大部分的資料都是選擇性的。 在下列情況下，這個方法很有用：

- 您想要在部署設備之前建立快速初始評量。
- 您無法在組織中部署 Azure Migrate 設備。
- 您無法共用允許存取內部部署伺服器的認證。
- 有安全性條件約束使您無法收集設備所收集到的資料，並將其傳送至 Azure。

> [!NOTE]
> 您無法使用 CSV 檔案來遷移匯入的伺服器。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定 Azure 帳戶
> * 設定 Azure Migrate 專案
> * 準備 CSV 檔案
> * 匯入檔案
> * 評定伺服器

> [!NOTE]
> 教學課程顯示嘗試案例的最快路徑，並且在可行時使用預設選項。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>必要條件

- 您可以在單一 CSV 檔案中及在 Azure Migrate 專案中新增最多 20,000 個伺服器。 
- CSV 檔案中指定的作業系統名稱必須包含並符合[支援的名稱](#supported-operating-system-names)。


## <a name="prepare-an-azure-user-account"></a>準備 Azure 使用者帳戶

若要建立 Azure Migrate 專案，您需要一個帳戶，其具有：
- Azure 訂用帳戶的參與者或擁有者權限。
- 註冊 Azure Active Directory 應用程式的權限。

如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。 如果您不是訂用帳戶擁有者，請與擁有者合作以指派權限，如下所示：

1. 在 Azure 入口網站中搜尋「訂用帳戶」，然後在 [服務] 底下選取 [訂用帳戶]。

    ![搜尋 Azure 訂用帳戶的搜尋方塊](./media/tutorial-discover-import/search-subscription.png)

2. 在 [訂用帳戶] 頁面中，選取您要在其中建立 Azure Migrate 專案的訂用帳戶。 
3. 在訂用帳戶中，選取 [存取控制 (IAM)] > [檢查存取權]。
4. 在 [檢查存取權] 中，搜尋相關的使用者帳戶。
5. 在 [新增角色指派] 中，按一下 [新增]。

    ![搜尋使用者帳戶以檢查存取權並指派角色](./media/tutorial-discover-import/azure-account-access.png)

6. 在 [新增角色指派] 中，選取 [參與者] 或 [擁有者] 角色，然後選取帳戶 (在我們的範例中為 azmigrateuser)。 然後按一下 [儲存]  。

    ![開啟 [新增角色指派] 頁面，將角色指派給帳戶](./media/tutorial-discover-import/assign-role.png)

7. 在入口網站中搜尋使用者，然後在 [服務] 底下選取 [使用者]。
8. 在 [使用者設定] 中，確認 Azure AD 使用者可以註冊應用程式 (預設為 [是])。

    ![在 [使用者設定] 中確認使用者可以註冊 Active Directory 應用程式](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>設定專案

如果您還沒有新的 Azure Migrate 專案，請加以設定。

1. 在 Azure 入口網站 > [所有服務] 中，搜尋 **Azure Migrate**。
2. 在 [服務] 下，選取 [Azure Migrate]。
3. 在 [概觀] 中，選取 [建立專案]。
5. 在 [建立專案] 中，選取您的 Azure 訂用帳戶和資源群組。 如果您還沒有資源群組，請加以建立。
6. 在 [專案詳細資料] 中，指定專案名稱以及要在其中建立專案的地理位置。 請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

   ![專案名稱和區域的方塊](./media/tutorial-discover-import/new-project.png)

7. 選取 [建立]。
8. 等候幾分鐘讓 Azure Migrate 專案完成部署。

**Azure Migrate：伺服器評量** 工具依預設會新增至新專案中。

![顯示依預設新增伺服器評量工具的頁面](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>準備 CSV

下載 CSV 範本，並在其中新增伺服器資訊。

### <a name="download-the-template"></a>下載範本

1. 在 [移轉目標] > [伺服器] >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]。
2. 在 [探索機器] 中，選取 [使用 CSV 匯入]。
3. 選取 [下載] 以下載 CSV 範本。 或者，您也可以[直接下載](https://go.microsoft.com/fwlink/?linkid=2109031)。

    ![下載 CSV 範本](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>新增伺服器資訊

收集伺服器資料，並將其新增至 CSV 檔案。

- 若要收集資料，您可以從用於內部部署伺服器管理的工具 (例如 VMware vSphere 或您的設定管理資料庫 (CMDB)) 將資料匯出。
- 若要檢閱範例資料，請下載我們的[範例檔案](https://go.microsoft.com/fwlink/?linkid=2108405)。

下表摘要說明要填入的檔案欄位：

**欄位名稱** | **必要** | **詳細資料**
--- | --- | ---
**伺服器名稱** | 是 | 建議您指定完整網域名稱 (FQDN)。
**IP 位址** | 否 | 伺服器位址。
**核心** | 是 | 配置給伺服器的處理器核心數目。
**記憶體** | 是 | 配置給伺服器的 RAM 總計 (以 MB 為單位)。
**作業系統名稱** | 是 | 伺服器作業系統。 <br/> 評估會識別符合或包含[此](#supported-operating-system-names)清單中之名稱的作業系統名稱。
**作業系統版本** | 否 | 伺服器作業系統版本。
**OS 架構** | 否 | 伺服器 OS 架構 <br/> 有效值為：x64、x86、amd64、32 位元或 64 位元
**磁碟數量** | 否 | 如果提供了個別磁碟詳細資料，則不需要。
**磁碟 1 大小**  | 否 | 磁碟的大小上限 (以 GB 為單位)。<br/>您可以藉由在範本中[新增資料行](#add-multiple-disks)，來新增更多磁碟的詳細資料。 您最多可以新增八個磁碟。
**磁碟 1 讀取作業數** | 否 | 每秒的磁碟讀取作業數。
**磁碟 1 寫入作業數** | 否 | 每秒的磁碟寫入作業數。
**磁碟 1 讀取輸送量** | 否 | 每秒從磁碟讀取的資料量 (以每秒 MB 數為單位)。
**磁碟 1 寫入輸送量** | 否 | 每秒寫入至磁碟的資料量 (以每秒 MB 數為單位)。
**CPU 使用率百分比** | 否 | 已使用的 CPU 百分比。
**記憶體使用率百分比** | 否 | 已使用的 RAM 百分比。
**磁碟讀取作業數總計** | 否 | 每秒的磁碟讀取作業數。
**磁碟寫入作業數總計** | 否 | 每秒的磁碟寫入作業數。
**磁碟讀取輸送量總計** | 否 | 從磁碟讀取的資料量 (以每秒 MB 數為單位)。
**磁碟寫入輸送量總計** | 否 | 寫入至磁碟的資料量 (以每秒 MB 數為單位)。
**網路輸入輸送量** | 否 | 伺服器接收到的資料量 (以每秒 MB 數為單位)。
**網路輸出輸送量** | 否 | 伺服器傳輸的資料量 (以每秒 MB 數為單位)。
**韌體類型** | 否 | 伺服器韌體。 值可以是 "BIOS" 或 "UEFI"。
**MAC 位址**| 否 | 伺服器 MAC 位址。


### <a name="add-operating-systems"></a>新增作業系統

評量可識別特定作業系統名稱。 您指定的任何名稱都必須完全符合[支援的名稱清單](#supported-operating-system-names)中的字串之一。

### <a name="add-multiple-disks"></a>新增多個磁碟

範本會提供第一個磁碟的預設欄位。 您最多可為八個磁碟新增類似的資料行。

例如，若要指定第二個磁碟的所有欄位，請新增下列資料行：

- 磁碟 2 大小
- 磁碟 2 讀取作業數
- 磁碟 2 寫入作業數
- 磁碟 2 讀取輸送量
- 磁碟 2 寫入輸送量


## <a name="import-the-server-information"></a>匯入伺服器資訊

將資訊新增至 CSV 範本之後，請將 CSV 檔案匯入伺服器評量中。

1. 在 Azure Migrate 的 [探索機器] 中，移至已完成的範本。
2. 選取 [匯入]。
3. 匯入狀態隨即顯示。
    - 如果狀態中出現警告，您可以修正相關狀況，或繼續作業而不加以處理。
    - 若要提升評量的正確性，請依照警告中的建議改善伺服器資訊。
    - 若要檢視並修正警告，請選取 [下載警告詳細資料 .CSV]。 這項作業會下載包含警告的 CSV。 請檢閱警告，並視需要修正問題。
    - 如果狀態中出現錯誤 (匯入狀態為「失敗」)，您必須先修正這些錯誤，才能繼續匯入：
        1. 下載現在包含錯誤詳細資料的 CSV。
        1. 檢閱錯誤並視需要加以解決。 
        1. 重新上傳修改過的檔案。
4. 當匯入狀態「完成」時，表示伺服器資訊已匯入。 如果匯入程序似乎不完整，請重新整理。

## <a name="update-server-information"></a>更新伺服器資訊

您可以使用相同的 **伺服器名稱** 再次匯入伺服器的資料，以更新伺服器資訊。 您無法修改 [伺服器名稱] 欄位。 目前不支援刪除伺服器。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成後，若要驗證伺服器是否出現在 Azure 入口網站中：

1. 開啟 Azure Migrate 儀表板。
2. 在 [Azure Migrate - 伺服器] >  **[Azure Migrate：伺服器評量]** 頁面上，選取顯示 [探索到的伺服器] 計數的圖示。
3. 選取 [以匯入為基礎] 索引標籤。



## <a name="supported-operating-system-names"></a>支援的作業系統名稱

CSV 中提供的作業系統名稱必須包含並符合。 如果沒有，您將無法進行評估。 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Azure Migrate 專案 
> * 使用匯入的 CSV 檔案探索伺服器。 現在，執行 [VMware VM 遷移至 Azure VM](./tutorial-assess-vmware-azure-vm.md) 的評量。