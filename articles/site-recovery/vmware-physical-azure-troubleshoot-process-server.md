---
title: 針對 Azure Site Recovery 進程伺服器進行疑難排解
description: 本文說明如何針對 Azure Site Recovery 進程伺服器的問題進行疑難排解
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395033"
---
# <a name="troubleshoot-the-process-server"></a>針對進程伺服器進行疑難排解

當您針對內部部署 VMware Vm 和實體伺服器設定 Azure 的嚴重損壞修復時，會使用[Site Recovery](site-recovery-overview.md)進程伺服器。 本文說明如何對進程伺服器的問題進行疑難排解，包括複寫和連線能力問題。

[深入瞭解](vmware-physical-azure-config-process-server-overview.md)進程伺服器。

## <a name="before-you-start"></a>開始之前

開始進行疑難排解之前：

1. 請確定您瞭解如何[監視進程伺服器](vmware-physical-azure-monitor-process-server.md)。
2. 請參閱下列最佳作法。
3. 請確定您遵循[容量考慮](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並使用設定[伺服器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或[獨立進程伺服器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)的調整大小指導方針。

## <a name="best-practices-for-process-server-deployment"></a>進程伺服器部署的最佳作法

為了取得進程伺服器的最佳效能，我們摘要了一些一般的最佳作法。

**最佳做法** | **詳細資料**
--- |---
**使用量** | 請確定設定伺服器/獨立進程伺服器僅用於預期的用途。 不要在電腦上執行其他任何動作。
**IP 位址** | 請確定進程伺服器具有靜態 IPv4 位址，而且未設定 NAT。
**控制記憶體/CPU 使用量** |將 CPU 和記憶體使用量保持在70% 之下。
**確保可用空間** | 可用空間指的是進程伺服器上的快取磁碟空間。 複寫資料會先儲存在快取中，然後再上傳至 Azure。<br/><br/> 將可用空間保持在25% 以上。 如果低於20%，則複寫會針對與進程伺服器相關聯的複寫機器進行節流。

## <a name="check-process-server-health"></a>檢查進程伺服器健全狀況

進行疑難排解的第一個步驟是檢查進程伺服器的健全狀況和狀態。 若要這樣做，請檢查所有警示，檢查所需的服務是否正在執行，並確認是否有來自進程伺服器的心跳。 下圖摘要說明這些步驟，後面接著可協助您執行這些步驟的程式。

![針對進程伺服器健全狀況進行疑難排解](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>步驟1：針對進程伺服器健全狀況警示進行疑難排解

進程伺服器會產生一些健康情況警示。 下表摘要說明這些警示和建議的動作。

**警示類型** | **錯誤** | **疑難排解**
--- | --- | --- 
![Healthy][green] | None  | 進程伺服器已連線且狀況良好。
![警告][yellow] | 指定的服務不在執行中。 | 1. 檢查服務是否正在執行。<br/> 2. 如果服務如預期般執行，請依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。
![警告][yellow]  | 過去15分鐘內的 CPU 使用率 > 80%。 | 1. 不要加入新電腦。<br/>2. 檢查使用進程伺服器的 Vm 數目與[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)相符，並考慮設定[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。
![重大][red] |  過去15分鐘內的 CPU 使用率 > 95%。 | 1. 不要加入新電腦。<br/>2. 檢查使用進程伺服器的 Vm 數目與[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)相符，並考慮設定[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。<br/> 4. 如果問題持續發生，請執行 VMware/實體伺服器複寫的[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 過去15分鐘內記憶體使用量 > 80%。 |  1. 不要加入新電腦。<br/>2. 檢查使用進程伺服器的 Vm 數目與[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)相符，並考慮設定[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 遵循與警告相關聯的任何指示。<br/> 4. 如果問題持續發生，請依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。
![重大][red] | 過去15分鐘內記憶體使用量 > 95%。 | 1. 不要加入新的電腦，並考慮設定[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/> 2. 遵循與警告相關聯的任何指示。<br/> 3. 4。 如果問題持續發生，請依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。<br/> 4. 如果問題持續發生，請執行 VMware/實體伺服器複寫問題的[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 過去15分鐘內快取資料夾可用空間 < 30%。 | 1. 不要加入新的電腦，並考慮設定[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>2. 檢查使用進程伺服器的 Vm 數目符合[方針](site-recovery-plan-capacity-vmware.md#capacity-considerations)。<br/> 3. 依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。
![重大][red] |  過去15分鐘內 < 25% 的可用空間 | 1. 遵循與此問題相關之警告的指示。<br/> 2. 3。 請依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。<br/> 3. 如果問題持續發生，請執行 VMware/實體伺服器複寫的[部署規劃工具](https://aka.ms/asr-v2a-deployment-planner)。
![重大][red] | 進程伺服器不會有15分鐘或更久的任何心跳。 Tmansvs 服務未與設定伺服器通訊。 | 1）檢查進程伺服器是否已啟動且正在執行。<br/> 2. 檢查 tmassvc 是否正在進程伺服器上執行。<br/> 3. 依照下列指示進行連線[和複寫問題的疑難排解](#check-connectivity-and-replication)。


![資料表索引鍵](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>步驟2：檢查進程伺服器服務

下表摘要說明應該在進程伺服器上執行的服務。 服務有些許差異，視進程伺服器的部署方式而定。 

針對 [Microsoft Azure 復原服務代理程式（obengine）] 以外的所有服務，檢查 StartType 是否設定為 [**自動**] 或 [**自動（延遲啟動）** ]。
 
**部署** | **正在執行服務**
--- | ---
**設定伺服器上的進程伺服器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;記錄上傳服務（LogUpload）;InMage Scout 應用程式服務;Microsoft Azure 復原服務代理程式（obengine）;InMage Scout VX Agent-Sentinel/Outpost （svagents）;tmansvcWorld Wide Web 發行服務（W3SVC）;MySQLMicrosoft Azure Site Recovery 服務（dra）
**當做獨立伺服器執行的進程伺服器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;記錄上傳服務（LogUpload）;InMage Scout 應用程式服務;Microsoft Azure 復原服務代理程式（obengine）;InMage Scout VX Agent-Sentinel/Outpost （svagents）;tmansvc.
**部署在 Azure 中以進行容錯回復的進程伺服器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;記錄上傳服務（LogUpload）


## <a name="step-3-check-the-process-server-heartbeat"></a>步驟3：檢查進程伺服器的心跳

如果進程伺服器沒有任何心跳（錯誤碼806），請執行下列動作：

1. 確認進程伺服器 VM 已啟動且正在執行。
2. 請檢查這些記錄檔中的錯誤。

    C:\ProgramData\ASR\home\svsystems\eventmanager *.Log c\programdata\asr\home\svsystems\ monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>檢查連線能力和複寫

 初始和進行中的複寫失敗通常是來源電腦與進程伺服器之間，或是進程伺服器與 Azure 之間的連線問題所造成。 下圖摘要說明這些步驟，後面接著可協助您執行這些步驟的程式。

![針對連線能力和複寫進行疑難排解](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>步驟4：確認來源電腦上的時間同步

請確定複寫電腦的系統日期/時間已同步。[深入瞭解](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>步驟5：檢查來源電腦上的防毒軟體

檢查複寫電腦上的防毒軟體是否 Site Recovery 封鎖。 如果您需要從防毒程式中排除 Site Recovery，請參閱[這篇文章](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>步驟6：檢查來源電腦的連線能力


1. 如有需要，請在來源電腦上安裝[Telnet 用戶端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)。 請勿使用 Ping。
2. 從來源電腦，使用 Telnet ping HTTPS 埠上的進程伺服器。 根據預設，9443是用於複寫流量的 HTTPS 埠。

    `telnet <process server IP address> <port>`

3. 確認連接是否成功。


**連線能力** | **詳細資料** | **動作**
--- | --- | ---
**成功** | Telnet 會顯示空白畫面，而且可以連線到進程伺服器。 | 不需要進一步的動作。
**從不** | 您無法連接 | 請確定進程伺服器上允許輸入埠9443。 例如，如果您有周邊網路或遮蔽式子網路。 再次檢查連線能力。
**部分成功** | 您可以連接，但來源電腦會報告無法連線到進程伺服器。 | 繼續進行下一個疑難排解程式。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>步驟7：針對無法連線的進程伺服器進行疑難排解

如果無法從來源電腦連線到進程伺服器，將會顯示錯誤78186。 如果未解決，此問題會導致應用程式一致和當機時保持一致復原點未如預期般產生。

藉由檢查來源機器是否可連線到進程伺服器的 IP 位址，並在來源電腦上執行 cxpsclient 工具來檢查端對端連線，以進行疑難排解。


### <a name="check-the-ip-connection-on-the-process-server"></a>檢查進程伺服器上的 IP 連線

如果 telnet 成功，但來源電腦報告無法連線到進程伺服器，請檢查您是否可以連線到進程伺服器的 IP 位址。

1. 在網頁瀏覽器中，嘗試連接到 IP 位址 HTTPs：//< PS_IP >： < PS_Data_Port >/。
2. 如果這種檢查顯示 HTTPS 憑證錯誤，這是正常的。 如果您忽略此錯誤，您應該會看到 400-不正確的要求。 這表示伺服器無法服務瀏覽器要求，而且標準 HTTPS 連接正常。
3. 如果此檢查無法正常執行，請記下瀏覽器錯誤訊息。 例如，407錯誤會指出 proxy 驗證的問題。

### <a name="check-the-connection-with-cxpsclient"></a>檢查與 cxpsclient 的連接

此外，您也可以執行 cxpsclient 工具來檢查端對端連接。

1. 按照下列步驟執行此工具：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在進程伺服器上，檢查下列資料夾中產生的記錄檔：

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>檢查來源 VM 記錄是否有上傳失敗（錯誤78028）

從來源電腦封鎖的資料上傳到進程服務的問題，可能會導致損毀一致和應用程式一致復原點不會產生。 

1. 若要針對網路上傳失敗進行疑難排解，您可以在此記錄檔中尋找錯誤：

    C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\svagents * .log 

2. 請使用本文中的其餘程式來協助您解決資料上傳問題。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>步驟8：檢查進程伺服器是否正在推送資料

檢查進程伺服器是否主動將資料推送至 Azure。

  1. 在處理伺服器上，開啟 [工作管理員] \(按 Ctrl+Shift+Esc\)。
  2. 選取 [**效能**] 索引標籤 >**開啟資源監視器**。
  3. 在**資源監視器** 頁面中，選取 **網路** 索引標籤。在 **具有網路活動的進程** 底下，檢查 cbengine.exe 是否正在主動傳送大量資料。

       ![具有網路活動的進程底下的磁片區](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未傳送大量資料，請完成下列各節中的步驟。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步驟9：檢查與 Azure blob 儲存體的進程伺服器連接

1. 在 資源監視器中，選取  **cbengine.exe**。
2. 在 [ **TCP**連線] 底下，檢查是否有從進程伺服器到 Azure 儲存體的連線能力。

  ![Cbengine.exe 與 Azure Blob 儲存體 URL 之間的連線](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>檢查服務

如果沒有從進程伺服器到 Azure blob 儲存體 URL 的連線能力，請檢查服務是否正在執行。

1. 在 [控制台] 中，選取 [**服務**]。
2. 確認下列服務正在執行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 復原服務代理程式
    - Microsoft Azure Site Recovery 服務
    - tmansvc

3. 啟動或重新啟動任何未執行的服務。
4. 確認進程伺服器已連接且可連線。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步驟10：檢查與 Azure 公用 IP 位址的進程伺服器連接

1. 在進程伺服器上的 [ **%Programfiles%\Microsoft Azure 復原服務] Agent\Temp**中，開啟最新的 cbenginecurr.errlog cbenginecurr.errlog 檔案。
2. 在檔案中，搜尋**443**，或針對字串**連接嘗試失敗**。

  ![暫存資料夾中的錯誤記錄檔](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果您發現問題，請使用埠443，將您的 Azure 公用 IP 位址放在 Cbenginecurr.errlog Cbenginecurr.currlog 檔案中：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在進程伺服器的命令列中，使用 Telnet 來 ping 您的 Azure 公用 IP 位址。
6. 如果您無法連接，請遵循下一個程式。

## <a name="step-11-check-process-server-firewall-settings"></a>步驟11：檢查處理伺服器防火牆設定。 

檢查進程伺服器上以 IP 位址為基礎的防火牆是否封鎖存取。

1. 以 IP 位址為基礎的防火牆規則：

    a）下載[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)的完整清單。

    b）將 IP 位址範圍新增至您的防火牆設定，以確保防火牆允許對 Azure （和預設的 HTTPS 埠443）進行通訊。

    c）允許您訂用帳戶的 Azure 區域和 Azure 美國西部區域的 IP 位址範圍（用於存取控制和身分識別管理）。

2. 針對以 URL 為基礎的防火牆，請將下表所列的 Url 新增至防火牆設定。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>步驟12：驗證進程伺服器 proxy 設定 

1. 如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。 檢查您在登錄機碼中設定伺服器時所提供的值**HKEY_LOCAL_MACHINE \Software\microsoft\azure Site Recovery\ProxySettings**。
2. 請確定 Azure Site Recovery 代理程式會使用相同的設定來傳送資料。

    a）搜尋**Microsoft Azure 備份**。

    b）開啟**Microsoft Azure 備份**，然後選取 **動作** > **變更屬性**。

    c）在 [ **proxy**設定] 索引標籤上，proxy 位址應該與登錄設定中顯示的 proxy 位址相同。 如果不同，請將它變更為相同的位址。

## <a name="step-13-check-bandwidth"></a>步驟13：檢查頻寬

增加進程伺服器與 Azure 之間的頻寬，然後檢查是否仍然發生問題。


## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
