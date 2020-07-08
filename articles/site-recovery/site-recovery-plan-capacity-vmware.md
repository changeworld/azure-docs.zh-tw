---
title: 使用 Azure Site Recovery 規劃 VMware 損毀修復的容量
description: 本文可協助您在使用 Azure Site Recovery 來設定 VMware VM 到 Azure 的災害復原時，進行容量和規模調整規劃。
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710706"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>針對復原到 Azure 的 VMware 災害復原進行容量和規模調整規劃

您可以使用本文，在使用 [Azure Site Recovery](site-recovery-overview.md)將內部部署 VMware VM 和實體伺服器複寫至 Azure 時，進行容量和規模調整規劃。

## <a name="how-do-i-start-capacity-planning"></a>如何開始容量規劃？

若要了解 Azure Site Recovery 基礎結構需求，請針對 VM 複寫執行 [Azure Site Recovery 部署規劃工具](https://aka.ms/asr-deployment-planner-doc)，以收集複寫環境的相關資訊。 如需詳細資訊，請參閱[關於 VMware 至 Azure 的 Azure Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)。 

「Site Recovery 部署規劃工具」會提供一個報告，內含有關相容與不相容 VM、每一 VM 之磁碟及每一磁碟之資料變換的完整資訊。 此工具也會摘要列出要達到目標 RPO 所需的網路頻寬，以及要成功複寫和測試容錯移轉所需的 Azure 基礎結構。

## <a name="capacity-considerations"></a>容量考量

元件 | 詳細資料
--- | ---
**複寫** | **每日變更率上限**︰受保護的機器只能使用一個處理序伺服器。 單一處理序伺服器可以處理多達 2 TB 的每日變動率。 因此，針對受保護機器支援的每日資料變更率上限為 2 TB。<br /><br /> **最大輸送量**：複寫的機器可以屬於 Azure 中的一個儲存體帳戶。 一個標準「Azure 儲存體」帳戶每秒最多可以處理 20,000 個要求。 建議您將整個來源機器的每秒輸入/輸出作業數 (IOPS) 保持為 20,000。 例如，如果您有一部具備 5 個磁碟的來源機器，而每個磁碟會在來源機器上產生 120 個 IOPS (8 K 大小)，則來源機器會在 Azure 的每一磁碟 IOPS 限制 (500) 範圍內。 (所需的儲存體帳戶數目等於來源機器 IOPS 總數除以 20,000)。
**設定伺服器** | 設定伺服器必須能夠處理在受保護機器上執行之所有工作負載的每日變更率容量。 設定機器必須有足夠的頻寬以持續地將資料複寫到「Azure 儲存體」。<br /><br /> 最佳做法是將設定伺服器放在與您想要保護之機器相同的網路和 LAN 區段上。 您可以將設定伺服器放在不同的網路上，但是您想要保護的機器應該具備第 3 層網路可見性。<br /><br /> 下一節的資料表會摘要說明組態伺服器的大小建議。
**進程伺服器** | 組態伺服器上會安裝第一部處理序伺服器。 您可以部署額外的處理序伺服器來調整您的環境。 <br /><br /> 處理伺服器會從受保護的機器接收複寫資料。 處理伺服器會藉由使用快取、壓縮及加密，將資料最佳化。 接著，處理伺服器會將資料傳送給 Azure。 處理伺服器機器必須具備足夠的資源來執行這些工作。<br /><br /> 處理序伺服器使用磁碟快取。 請另外使用一個 600 GB 以上的快取磁碟，來處理發生網路瓶頸或中斷時所儲存的資料變更。

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>設定伺服器和內建處理伺服器的大小建議

設定伺服器如果使用內建處理伺服器來保護工作負載，根據下列設定，最多可處理 200 部虛擬機器：

CPU | 記憶體 | 快取磁碟大小 | 資料變更率 | 受保護的機器
--- | --- | --- | --- | ---
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | 用來複寫少於 100 部機器。
12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5GHz) | 18 GB | 600 GB | 501 GB 至 1 TB | 用來複寫 100 至 150 部機器。
16 個 vCPU (2 個插槽 * 8 核心 \@ 2.5GHz) | 32 GB | 1 TB | >1 TB 至 2 TB | 用來複寫 151 至 200 部機器。
使用 [OVF 範本](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)來部署另一個設定伺服器。 | | | | 如果您要複寫超過 200 部機器，請部署新的設定伺服器。
部署另一個[處理伺服器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。 | | | >2 TB| 如果整體的每日資料變更率超過 2 TB，請部署新的向外延展處理伺服器。

在這些設定中：

* 每部來源機器都有三個各 100 GB 的磁碟。
* 我們使用了由 8 個 RAID 10、RPM 達 10 K 之共用存取簽章磁碟機組成的效能評定儲存體，來進行快取磁碟度量。

## <a name="size-recommendations-for-the-process-server"></a>處理序伺服器的大小建議

處理伺服器是 Azure Site Recovery 中處理資料複寫的元件。 如果每日變更率大於 2 TB，您就必須新增向外延展處理伺服器來處理複寫負載。 若要擴充，您可以：

* 藉由使用 [OVF 範本](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)來進行部署，增加設定伺服器的數目。 例如，您可以使用兩個設定伺服器來保護最多 400 部機器。
* 新增[向外延展處理伺服器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。 使用向外延展處理伺服器來處理複寫流量，而不使用 (或搭配使用) 設定伺服器來處理。

下表描述此案例：

* 您已設定向外延展處理伺服器。
* 您已設定讓受保護的虛擬機器使用向外延展處理伺服器。
* 每個受保護的來源機器都有三個各 100 GB 的磁碟。

額外處理序伺服器 | 快取磁碟大小 | 資料變更率 | 受保護的機器
--- | --- | --- | ---
4 個 vCPU (2 個通訊端 * 2 核心 \@ 2.5 GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 用來複寫 85 部以內的機器。
8 個 vCPU (2 個通訊端 * 4 核心 \@ 2.5 GHz)，12 GB 記憶體 | 600 GB | 251 GB 至 1 TB | 用來複寫 86 至 150 部機器。
12 個 vCPU (2 個通訊端 * 6 核心 \@ 2.5 GHz)，24 GB 記憶體 | 1 TB | >1 TB 至 2 TB | 用來複寫 151 至 225 部機器。

如何調整伺服器取決於相應增加或相應放大模型的喜好設定。 若要相應增加規模，請部署一些高階設定伺服器和處理伺服器。 若要相應放大規模，請部署更多個具有較少資源的伺服器。 例如，如果想要保護 200 部整體每日資料變更率為 1.5 TB 的機器，您可以執行下列其中一個動作：

* 設定單一處理伺服器 (16 個 vCPU、24 GB RAM)。
* 設定兩個處理伺服器 (2 x 8 vCPU、2* 12 GB RAM)。

## <a name="control-network-bandwidth"></a>控制網路頻寬

在使用 [Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)來計算複寫 (初始複寫，然後是差異複寫) 所需的頻寬之後，有幾個選項可供您用來控制用於複寫的頻寬大小：

* **節流頻寬**︰複寫至 Azure 的 VMware 流量會經過特定的處理序伺服器。 您可在以處理伺服器身分執行的機器上進行頻寬節流。
* 會**影響頻寬**.. 您可以使用幾個登錄機碼來影響用於複寫的頻寬：
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** 登錄值可指定用於磁碟資料傳輸 (初始或差異複寫) 的執行緒數目。 較高的值可增加用於複寫的網路頻寬。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** 登錄值會指定在容錯回復期間用於資料傳輸的執行緒數目。

### <a name="throttle-bandwidth"></a>頻寬節流處理

1. 在作為處理伺服器的機器上，開啟「Azure 備份」MMC 嵌入式管理單元。 根據預設，備份的快捷方式位於桌面上或在下列資料夾中： C:\Program Files\Microsoft Azure Recovery Services Agent\bin。
2. 在嵌入式管理單元中，選取 [變更內容]****。

    ![用以變更內容的「Azure 備份」MMC 嵌入式管理單元選項螢幕擷取畫面](./media/site-recovery-vmware-to-azure/throttle1.png)
3. 在 [節流]**** 索引標籤上，選取 [啟用備份作業的網際網路頻寬使用節流功能]****。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。

    ![[Azure 備份內容] 對話方塊的螢幕擷取畫面](./media/site-recovery-vmware-to-azure/throttle2.png)

您也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) Cmdlet 來設定節流。 以下是範例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要節流。

### <a name="alter-the-network-bandwidth-for-a-vm"></a>更改 VM 的網路頻寬

1. 在 VM 的登錄中，移至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要更改複寫磁碟上的頻寬流量，請修改 **UploadThreadsPerVM** 的值。 如果該機碼不存在，請建立該機碼。
   * 若要更改來自 Azure 之容錯回復流量的頻寬，請修改 **DownloadThreadsPerVM** 的值。
2. 每個機碼的預設值為 **4**。 在 “overprovisioned” 網路中，這些登錄機碼必須變更自其預設值。 您可以使用的最大值為 **32**。 監視流量，將此值最佳化。

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>設定 Site Recovery 基礎結構以保護 500 個以上的虛擬機器

在設定 Site Recovery 基礎結構之前，請存取環境以衡量下列因素：相容的虛擬機器、每日資料變更率、您想要達成之 RPO 所需的網路頻寬、所需的 Site Recovery 元件數目，以及完成初始複寫所需的時間。 請完成下列步驟以收集所需的資訊：

1. 若要衡量這些參數，請在您的環境執行「Site Recovery 部署規劃工具」。 如需有幫助的指導方針，請參閱[關於 VMware 至 Azure 的 Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)。
2. 部署符合[設定伺服器的大小建議](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)的設定伺服器。 如果您的生產工作負載超過 650 個虛擬機器，請部署另一個組態伺服器。
3. 根據所測得的每日資料變更率，參考[大小指導方針](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)來部署[向外延展處理伺服器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。
4. 如果您預期磁片虛擬機器的資料變更率超過 2 MBps，請確定您使用的是 premium 受控磁片。 「Site Recovery 部署規劃工具」會在一段特定的期間內執行。 報告中可能不會擷取其他時間發生的資料變更率尖峰。
5. 根據您想要達成的 RPO 來[設定網路頻寬](site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。
6. 設定妥基礎結構時，為您的工作負載啟用災害復原。 若要了解做法，請參閱[設定 VMware 至 Azure 複寫的來源環境](vmware-azure-set-up-source.md)。

## <a name="deploy-additional-process-servers"></a>部署額外處理序伺服器

如果您將部署規模相應放大到超過 200 部來源機器，或是您的每日變換率總計超過 2 TB，就必須新增處理伺服器來處理流量。 我們已增強9.24 版中的產品，以便在設定相應放大進程伺服器的時機提供[進程伺服器警示](vmware-physical-azure-monitor-process-server.md#process-server-alerts)。 [設定進程伺服器](vmware-azure-set-up-process-server-scale.md)來保護新的來源電腦或[平衡負載](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)。

### <a name="migrate-machines-to-use-the-new-process-server"></a>移轉機器以使用新的處理序伺服器

1. 選取 [**設定**] [  >  **Site Recovery 伺服器**]。 選取設定伺服器，然後展開 [處理伺服器]****。

    ![[處理伺服器] 對話方塊的螢幕擷取畫面](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 在目前使用中的處理伺服器上按一下滑鼠右鍵，然後選取 [切換]****。

    ![[設定伺服器] 對話方塊的螢幕擷取畫面](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. 在 [選取目標處理伺服器]**** 中，選取您想要使用的新處理伺服器。 然後，選取伺服器將處理的虛擬機器。 若要取得伺服器的相關資訊，請選取資訊圖示。 為了協助您進行負載決策，會顯示將每個所選虛擬機器複寫到新處理伺服器所需的平均空間。 請選取核取記號以開始複寫到新的處理序伺服器。

## <a name="deploy-additional-master-target-servers"></a>部署額外主要目標伺服器

在下列案例中，需要多個主要目標伺服器：

*   您想要保護 Linux 型虛擬機器。
*   設定伺服器上可用的主要目標伺服器無法存取 VM 的資料存放區。
*   主要目標伺服器上的磁碟總數 (伺服器上的本機磁碟數加上要保護的磁碟數) 大於 60 個磁碟。

若要了解如何為 Linux 型虛擬機器新增主要目標伺服器，請參閱[安裝 Linux 主要目標伺服器以便進行容錯回復](vmware-azure-install-linux-master-target.md)。

為 Windows 型虛擬機器新增主要目標伺服器：

1. 前往 [復原**服務保存庫**] Site Recovery [  >  **基礎結構**設定  >  **伺服器**]。
2. 選取所需的設定伺服器，然後選取 [主要目標伺服器]****。

    ![顯示 [新增主要目標伺服器] 按鈕的螢幕擷取畫面](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. 下載統一的安裝程式檔案，然後在 VM 上執行該檔案，以安裝主要目標伺服器。
4. 選取**Install master target**  >  **[下一步**安裝主要目標]。

    ![顯示選取 [Install master target] \(安裝主要目標\) 選項的螢幕擷取畫面](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. 選取預設安裝位置，然後選取 [Install] \(安裝\)****。

     ![顯示預設安裝位置的螢幕擷取畫面](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. 若要向設定伺服器註冊主要目標，請選取 [Proceed to Configuration] \(繼續設定\)****。

    ![顯示 [Proceed to Configuration] \(繼續設定\) 按鈕的螢幕擷取畫面](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. 輸入設定伺服器的 IP 位址，然後輸入複雜密碼。 若要了解如何產生複雜密碼，請參閱[產生設定伺服器複雜密碼](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。 

    ![顯示設定伺服器 IP 位址和複雜密碼輸入位置的螢幕擷取畫面](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. 選取 [註冊]。 註冊完成時，選取 [Finish] \(完成)****。

當註冊成功完成時，伺服器會列在**Recovery Services Vault**  >  **Site Recovery Infrastructure**  >  **Configuration servers**設定伺服器的主要目標伺服器的復原服務保存庫 Site Recovery 的 Azure 入口網站中。

 > [!NOTE]
 > 請下載 [Windows 的主要目標伺服器統一安裝程式檔案](https://aka.ms/latestmobsvc)最新版本。

## <a name="next-steps"></a>後續步驟

下載並執行 [Site Recovery 部署規劃工具](https://aka.ms/asr-deployment-planner)。
