---
title: 透過 Azure Migrate 伺服器評量使用匯入的伺服器資料來評估伺服器
description: 說明如何透過 Azure Migrate 伺服器評量使用匯入的資料來評估內部部署伺服器是否可移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 23fa1a2a0b035d04334c51c02411de6de70f2cad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79453641"
---
# <a name="assess-servers-by-using-imported-data"></a>使用匯入的資料來評估伺服器

本文說明如何使用 [Azure Migrate：伺服器評量](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具，藉由以逗號分隔值 (CSV) 格式匯入伺服器中繼資料，來評估內部部署伺服器。 使用此評量方法時，您無須設定 Azure Migrate 設備來建立評量。 適用時機：

- 您想要在部署設備之前建立快速初始評量。
- 您無法在組織中部署 Azure Migrate 設備。
- 您無法共用允許存取內部部署伺服器的認證。
- 有安全性條件約束使您無法收集設備所收集到的資料，並將其傳送至 Azure。 您可以控制在匯入的檔案中共用的資料。 此外，大部分的資料 (例如，提供 IP 位址) 都是選擇性的。

## <a name="before-you-start"></a>開始之前

請注意下列幾點：

- 您最多可以在單一 CSV 檔案中新增 20,000 個伺服器。
- 您可以使用 CSV 在 Azure Migrate 專案中新增最多 20,000 個伺服器。
- 您可以使用 CSV 將伺服器資訊上傳至「伺服器評量」多次。
- 當您評估內部部署環境是否可進行移轉時，收集應用程式資訊會很有用處。 不過，「伺服器評量」目前不會執行應用層級評量，或是在建立評量時將應用程式納入考量。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 在 CSV 檔案中填入伺服器資訊。
> * 匯入檔案，以將伺服器資訊新增至伺服器評量。
> * 建立並檢閱評量。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作指南。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="set-azure-permissions-for-azure-migrate"></a>設定 Azure Migrate 的 Azure 權限

您的 Azure 帳戶必須有建立 Azure Migrate 專案的權限。

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，找出相關的帳戶並加以選取，以檢視權限。
3. 確定您具有「參與者」  或「擁有者」  權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。

## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

若要設定新的 Azure Migrate 專案：

1. 在 Azure 入口網站的 [所有服務]  中，搜尋 **Azure Migrate**。
2. 在 [服務]  下，選取 [Azure Migrate]  。
3. 在 [概觀]  的 [探索、評估和遷移伺服器]  底下，選取 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-assess-import/assess-migrate.png)

4. 在 [開始使用]  中，選取 [新增工具]  。
5. 在 [Migrate 專案]  中選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有的話)。
6. 在 [專案詳細資料]  中，指定專案名稱以及要在其中建立專案的地理位置。 其他資訊：

    - 檢閱[支援的地理位置](migrate-support-matrix.md#supported-geographies)。 專案地理區域只會用來儲存從內部部署 VM 收集到的中繼資料。
    - 當您執行移轉時，可以選取任何目的地區域。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-import/migrate-project.png)

7. 選取 [下一步]  。
8. 在 [選取評量工具]  中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]  。

    ![建立 Azure Migrate 評量](./media/tutorial-assess-import/assessment-tool.png)

9. 在 [選取移轉工具]  中，選取 [暫時跳過新增移轉工具]   > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後選取 [新增工具]  。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您隨後會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器]  來存取。

## <a name="prepare-the-csv"></a>準備 CSV

下載 CSV 範本，並在其中新增伺服器資訊。

### <a name="download-the-template"></a>下載範本

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評估]** 中，選取 [探索]  。
2. 在 [探索機器]  中，選取 [使用 CSV 匯入]  。
3. 選取 [下載]  以下載 CSV 範本。 或者，您也可以[直接下載](https://go.microsoft.com/fwlink/?linkid=2109031)。

    ![下載 CSV 範本](./media/tutorial-assess-import/download-template.png)

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
**伺服器類型** | 否 | 值可以是「實體」或「虛擬」。
**Hypervisor** | 否 | 機器執行所在的 Hypervisor。 <br/> 值可以是 "VMware"、"Hyper-V"、"Xen"、"AWS"、"GCP" 或「其他」。
**Hypervisor 版本號碼** | 否 | Hypervisor 版本。
**虛擬機器識別碼** | 否 | VM 識別碼。 這是 VMware vCenter VM 的 **InstanceUUid** 值，或 Hyper-V 的 **Hyper-V VM 識別碼**。
**Virtual Machine Manager 識別碼** | 否 | 這是 VMware vCenter 的 **InstanceUUid** 值。 Hyper-V 不需要此項目。
**MAC 位址**| 否 | 伺服器 MAC 位址。
**BIOS 識別碼** | 否 | 伺服器 BIOS 識別碼。
**自訂伺服器識別碼** | 否 | 本機唯一的內部部署伺服器識別碼。 <br/> 可用來依本機識別碼追蹤匯入的伺服器。
**應用程式 1 名稱** | 否 | 在伺服器上執行的工作負載名稱。<br/>您可以藉由在範本中[新增資料行](#add-multiple-applications)，來新增更多應用程式的詳細資料。 您最多可以新增五個應用程式。
**應用程式 1 類型** | 否 | 在伺服器上執行的工作負載類型
**應用程式 1 版本** | 否 | 在伺服器上執行的工作負載版本。
**應用程式 1 授權到期日** | 否 | 工作負載的授權到期日 (如果適用)。
**業務單位** | 否 | 伺服器所屬的業務單位。
**公司擁有者** | 否 | 業務單位擁有者。
**商務應用程式名稱** | 否 | 應用程式所屬的應用程式名稱。
**位置** | 否 | 伺服器所在的資料中心。

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

### <a name="add-multiple-applications"></a>新增多個應用程式

範本會提供單一應用程式的欄位。 您最多可為五個應用程式新增類似的資料行。  

例如，若要指定第二個應用程式的所有欄位，請新增下列資料行：

- 應用程式 2 名稱
- 應用程式 2 類型
- 應用程式 2 版本
- 應用程式 2 授權到期日

> [!NOTE]
> 當您評估內部部署環境是否可進行移轉時，應用程式資訊會很有用處。 不過，Azure Migrate 伺服器評量目前不會執行應用層級評量，或是在建立評量時將應用程式納入考量。

## <a name="import-the-server-information"></a>匯入伺服器資訊

將資訊新增至 CSV 範本之後，請將伺服器匯入伺服器評量中。

1. 在 Azure Migrate 的 [探索機器]  中，移至已完成的範本。
2. 選取 [匯入]  。
3. 匯入狀態隨即顯示。
    - 如果狀態中出現警告，您可以修正相關狀況，或繼續作業而不加以處理。
    - 若要提升評量的正確性，請依照警告中的建議改善伺服器資訊。
    - 若要檢視並修正警告，請選取 [下載警告詳細資料 .CSV]  。 這項作業會下載包含警告的 CSV。 請檢閱警告，並視需要修正問題。
    - 如果狀態中出現錯誤 (匯入狀態為「失敗」  )，您必須先修正這些錯誤，才能繼續匯入：
        1. 下載現在包含錯誤詳細資料的 CSV。
        1. 檢閱錯誤並視需要加以解決。 
        1. 重新上傳修改過的檔案。
4. 當匯入狀態「完成」  時，表示伺服器資訊已匯入。

## <a name="update-server-information"></a>更新伺服器資訊

您可以使用相同的**伺服器名稱**再次匯入伺服器的資料，以更新伺服器資訊。 您無法修改 [伺服器名稱]  欄位。 目前不支援刪除伺服器。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成後，若要驗證伺服器是否出現在 Azure 入口網站中：

1. 開啟 Azure Migrate 儀表板。
2. 在 [Azure Migrate - 伺服器]   >  **[Azure Migrate：伺服器評量]** 頁面上，選取顯示 [探索到的伺服器]  計數的圖示。
3. 選取 [以匯入為基礎]  索引標籤。

## <a name="set-up-and-run-an-assessment"></a>設定並執行評量

您可以使用伺服器評量建立兩種類型的評量。

**評量類型** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以指定的效能資料值為基礎的評量。 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁片的每秒輸入/輸出 (IOPS) 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以指定的伺服器大小為基礎。<br/><br> **建議的磁碟類型**：以您為評量選取的儲存體類型設定為基礎。

若要執行評量：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器]  索引標籤的 **[Azure Migrate：伺服器評估]** 圖格中，選取 [評估]  。

    ![評定](./media/tutorial-assess-physical/assess.png)

3. 在 [評估伺服器]  中，指定評估的名稱。
4. 在 [探索來源]  中，選取 [透過匯入至 Azure Migrate 新增的機器]  。
5. 選取 [檢視全部]  以檢閱評量屬性。

    ![評量屬性](./media/tutorial-assess-physical/view-all.png)

6. 在 [選取或建立群組]  中，選取 [新建]  ，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
7. 在 [將電腦新增至群組]  中，選取要新增至群組的伺服器。
8. 選取 [建立評量]  以建立群組，然後執行評量。

    ![建立評估](./media/tutorial-assess-physical/assessment-create.png)

9. 評量建立好之後，可在 [伺服器]   >  **[Azure Migrate：伺服器評量]**  > [評量]  中加以檢視。
10. 選取 [匯出評量]  ，將其下載為 Microsoft Excel 檔案。

## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：伺服器是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行伺服器的預估每月計算與儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標]   > [伺服器]  中，選取 [Azure Migrate：伺服器評估]  中的 [評估]  。
2. 在 [評估]  中，選取評估來加以開啟。

    ![評量摘要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]  中，確認伺服器是否已準備好移轉至 Azure。
2. 檢閱狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：Azure Migrate 因資料可用性問題而無法評估整備程度。

3. 選取 [Azure 移轉整備程度]  狀態。 您可以檢視伺服器的整備程度詳細資料，並向下切入以查看伺服器詳細資料，包括計算、儲存體與網路設定。

### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。 您可以：

- 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有伺服器彙總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 成本預估可用來執行內部部署伺服器，以作為基礎結構即服務 (IaaS) VM。 伺服器評量不會考量平台即服務 (PaaS) 或軟體即服務 (SaaS) 的成本。

- 檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
- 向下切入以查看特定 VM 的詳細資料。

> [!NOTE]
> 信賴評等不會指派給使用 CSV 匯入至伺服器評量的伺服器評量。

## <a name="supported-operating-system-names"></a>支援的作業系統名稱

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 已使用 CSV 將伺服器匯入至 Azure Migrate：伺服器評量。
> * 已建立和檢閱評量。

現在，請[部署設備](./migrate-appliance.md)以進行更精確的評量，並將伺服器一起收集到群組中，以使用[相依性分析](./concepts-dependency-visualization.md)進行更深入的評量。
