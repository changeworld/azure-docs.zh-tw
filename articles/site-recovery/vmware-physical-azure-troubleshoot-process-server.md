---
title: 排除 Azure 網站恢復過程伺服器故障
description: 本文介紹如何解決 Azure 網站恢復過程伺服器的問題
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256857"
---
# <a name="troubleshoot-the-process-server"></a>對進程伺服器進行故障排除

將災害復原設置為 Azure 用於本地 VMware VM 和物理伺服器時，將使用[網站恢復](site-recovery-overview.md)過程伺服器。 本文介紹如何解決進程伺服器的問題，包括複製和連接問題。

[瞭解有關](vmware-physical-azure-config-process-server-overview.md)進程伺服器的更多詳細資訊。

## <a name="before-you-start"></a>開始之前

在開始故障排除之前：

1. 請確保您瞭解如何[監視進程伺服器](vmware-physical-azure-monitor-process-server.md)。
2. 查看以下最佳實踐。
3. 請確保遵循[容量注意事項](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並使用[佈建服務器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或[獨立進程伺服器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)的尺寸調整指南。

## <a name="best-practices-for-process-server-deployment"></a>流程伺服器部署的最佳做法

為了獲得最佳流程伺服器的性能，我們總結了一些一般最佳實踐。

**最佳實踐** | **詳細資料**
--- |---
**使用** | 確保佈建服務器/獨立進程伺服器僅用於預期目的。 不要在機器上運行任何其他操作。
**IP 位址** | 確保進程伺服器具有靜態 IPv4 位址，並且未配置 NAT。
**控制記憶體/CPU 使用方式** |將 CPU 和記憶體使用量保持在 70% 以下
**確保可用空間** | 可用空間是指進程伺服器上的緩存磁碟空間。 複製資料在上載到 Azure 之前存儲在緩存中。<br/><br/> 保持 25% 以上的可用空間。 如果低於 20%，則限制與進程伺服器關聯的複製電腦的複製。

## <a name="check-process-server-health"></a>檢查進程伺服器運行狀況

故障排除的第一步是檢查進程伺服器的運行狀況和狀態。 為此，請查看所有警報，檢查所需服務是否正在運行，並驗證進程伺服器是否有活動訊號。 這些步驟在下圖中總結，後跟過程一起説明您執行這些步驟。

![排除進程伺服器運行狀況的故障](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>第 1 步：對進程伺服器運行狀況警報進行故障排除

進程伺服器生成許多運行狀況警報。 下表匯總了這些警報和建議的操作。

**警報類型** | **錯誤** | **疑難排解**
--- | --- | --- 
![Healthy][green] | None  | 進程伺服器已連接且正常。
![警告][yellow] | 指定的服務未運行。 | 1. 檢查服務正在運行。<br/> 2. 如果服務按預期運行，請按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。
![警告][yellow]  | 在過去 15 分鐘內，CPU 利用率> 80%。 | 1. 不要添加新電腦。<br/>2. 檢查使用進程伺服器的 VM 數量是否符合[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設置[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。
![重大][red] |  在過去 15 分鐘內，CPU 利用率> 95%。 | 1. 不要添加新電腦。<br/>2. 檢查使用進程伺服器的 VM 數量是否符合[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設置[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。<br/> 4. 如果問題仍然存在，運行 VMware/物理伺服器複製[的部署計畫](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 記憶體使用量> 80% 的最後 15 分鐘。 |  1. 不要添加新電腦。<br/>2. 檢查使用進程伺服器的 VM 數量是否符合[定義的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，並考慮設置[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 按照與警告相關的任何說明進行操作。<br/> 4. 如果問題仍然存在，請按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。
![重大][red] | 記憶體使用量 15 分鐘> 95%。 | 1. 不要添加新電腦，並考慮設置[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/> 2. 按照與警告相關的任何說明進行操作。<br/> 3. 4. 如果問題仍然存在，請按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。<br/> 4. 如果問題仍然存在，運行 VMware/物理伺服器複製問題的[部署規劃器](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 快取檔案夾可用空間< 30% 的最後 15 分鐘。 | 1. 不要添加新電腦，並考慮設置[額外的進程伺服器](vmware-azure-set-up-process-server-scale.md)。<br/>2. 檢查使用進程伺服器的 VM 數量是否符合[準則](site-recovery-plan-capacity-vmware.md#capacity-considerations)。<br/> 3. 按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。
![重大][red] |  可用空間< 25% 持續 15 分鐘 | 1. 按照與此問題的警告相關的說明進行操作。<br/> 2. 3. 按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。<br/> 3. 如果問題仍然存在，運行 VMware/物理伺服器複製[的部署計畫](https://aka.ms/asr-v2a-deployment-planner)。
![重大][red] | 15 分鐘或更長時間沒有來自進程伺服器的活動訊號。 tmansvs 服務未與佈建服務器通信。 | 1） 檢查進程伺服器是否啟動並運行。<br/> 2. 檢查進程伺服器上是否運行了 tmassvc。<br/> 3. 按照以下說明[解決連接和複製問題](#check-connectivity-and-replication)。


![表鍵](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>第 2 步：檢查進程伺服器服務

應在進程伺服器上運行的服務將在下表中匯總。 服務存在細微差異，具體取決於進程伺服器的部署方式。 

對於除 Microsoft Azure 恢復服務代理（obengine）之外的所有服務，檢查 StartType 是否設置為 **"自動**"或 **"自動（延遲啟動"）。**
 
**部署** | **正在運行服務**
--- | ---
**佈建服務器上的進程伺服器** | 進程伺服器;進程伺服器監視器;cx進程伺服器;在 Mage 推送安裝;日誌上傳服務（日誌上傳）;內奇童子軍申請服務;微軟 Azure 恢復服務代理（obengine）;Inage Scout VX 代理-Sentinel/前哨（svagent）;特曼斯夫夫;萬維網出版服務（W3SVC）;MySQL;微軟 Azure 網站恢復服務 （德拉）
**進程伺服器作為獨立伺服器運行** | 進程伺服器;進程伺服器監視器;cx進程伺服器;在 Mage 推送安裝;日誌上傳服務（日誌上傳）;內奇童子軍申請服務;微軟 Azure 恢復服務代理（obengine）;Inage Scout VX 代理-Sentinel/前哨（svagent）;特曼斯夫夫
**在 Azure 中部署的進程伺服器以進行故障倒退** | 進程伺服器;進程伺服器監視器;cx進程伺服器;在 Mage 推送安裝;日誌上傳服務（日誌上傳）


## <a name="step-3-check-the-process-server-heartbeat"></a>第 3 步：檢查進程伺服器活動訊號

如果進程伺服器沒有活動訊號（錯誤代碼 806），請執行以下操作：

1. 驗證進程伺服器 VM 是否啟動並運行。
2. 檢查這些日誌是否存在錯誤。

    C：_程式資料\ASR_home_svsystems_事件管理器 *.log C_程式資料\ASR_svsystems_monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>檢查連接和複製

 初始和持續的複製失敗通常是由源電腦和進程伺服器之間的連接問題或進程伺服器和 Azure 之間的連接問題引起的。 這些步驟在下圖中總結，後跟過程一起説明您執行這些步驟。

![故障排除連接和複製](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>第 4 步：驗證源電腦上的時間同步

確保複製電腦的系統日期/時間同步。[瞭解更多](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>第 5 步：檢查源電腦上的防毒軟體

檢查複製電腦上的防毒軟體是否阻止網站恢復。 如果需要從防毒程式中排除網站恢復，請查看[本文](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>第 6 步：檢查源電腦的連接


1. 如果需要，請在源電腦上安裝[Telnet 用戶端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)。 不要使用 Ping。
2. 從源電腦，使用 Telnet ping HTTPS 埠上的進程伺服器。 預設情況下，9443 是複製流量的 HTTPS 埠。

    `telnet <process server IP address> <port>`

3. 驗證連接是否成功。


**連接** | **詳細資料** | **動作**
--- | --- | ---
**成功** | Telnet 顯示一個空白螢幕，進程伺服器是可訪問的。 | 無需採取進一步行動。
**失敗** | 無法連接 | 確保進程伺服器上允許入站埠 9443。 例如，如果您有周邊網路或遮罩子網。 再次檢查連接。
**部分成功** | 您可以連接，但源電腦報告無法到達進程伺服器。 | 繼續下一個故障排除過程。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>第 7 步：對無法訪問的進程伺服器進行故障排除

如果無法從源電腦到達進程伺服器，將顯示錯誤 78186。 如果不解決，此問題將導致應用一致性和崩潰一致性復原點無法按預期生成。

通過檢查源電腦是否可以到達進程伺服器的 IP 位址並在源電腦上運行 cxpsclient 工具來檢查端到端連接，從而進行故障排除。


### <a name="check-the-ip-connection-on-the-process-server"></a>檢查進程伺服器上的 IP 連接

如果 telnet 成功，但源電腦報告無法訪問進程伺服器，請檢查是否可以到達進程伺服器的 IP 位址。

1. 在 Web 瀏覽器中，嘗試訪問 IP 位址 HTTPs：//<PS_IP>：<PS_Data_Port>/。
2. 如果此檢查顯示 HTTPS 證書錯誤，則這是正常的。 如果忽略該錯誤，應會看到 400 錯誤請求。 這意味著伺服器無法為瀏覽器請求提供服務，並且標準 HTTPS 連接正常。
3. 如果此檢查不起作用，則記下瀏覽器錯誤訊息。 例如，407 錯誤將指示代理身份驗證出現問題。

### <a name="check-the-connection-with-cxpsclient"></a>檢查與 cxps 用戶端的連接

此外，您可以運行 cxpsclient 工具來檢查端到端連接。

1. 按照下列步驟執行此工具：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在進程伺服器上，檢查這些資料夾中生成的日誌：

    C：_程式資料\ASR_home_svsystems_傳輸\log_cxps.err C：程式資料_ASR_svsystems_傳輸\log_cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>檢查源 VM 日誌是否存在上載失敗（錯誤 78028）

資料從源電腦上載到進程服務時出現問題，可能會導致不生成崩潰一致性和應用一致的復原點。 

1. 要排除網路上載故障，可以在此日誌中查找錯誤：

    C：\程式檔 （x86）\微軟 Azure 網站恢復\代理\svagent_.log 

2. 使用本文中的其他過程可以説明您解決資料上載問題。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>第 8 步：檢查進程伺服器是否推送資料

檢查處理伺服器是否主動將資料推送至 Azure。

  1. 在處理伺服器上，開啟 [工作管理員] \(按 Ctrl+Shift+Esc\)。
  2. 選擇"打開**資源監視器****>"性能**"選項卡。
  3. 在 **"資源監視器"** 頁中，選擇"**網路**"選項卡。在 **"具有網路活動的進程**"下，檢查 cbengine.exe 是否主動發送大量資料。

       ![具有網路活動的流程下的卷](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未傳送大量資料，請完成下列各節中的步驟。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步驟 9：檢查進程伺服器與 Azure Blob 存儲的連接

1. 在資源監視器中，選擇**cbengine.exe**。
2. 在**TCP 連接**下，檢查進程伺服器與 Azure 存儲是否存在連接。

  ![cbengine.exe 和 Azure Blob 存儲 URL 之間的連接](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>檢查服務

如果進程伺服器與 Azure Blob 存儲 URL 之間沒有連接，請檢查服務是否正在運行。

1. 在控制台中，選擇 **"服務**"。
2. 驗證以下服務是否正在運行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 復原服務代理程式
    - Microsoft Azure Site Recovery 服務
    - tmansvc

3. 啟動或重新啟動任何未執行的服務。
4. 驗證進程伺服器是否已連接且可到達。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步驟 10：檢查進程伺服器連接到 Azure 公共 IP 位址

1. 在進程伺服器上，在 **%程式檔 %_微軟 Azure 恢復服務代理\Temp**中，打開最新的 CBEngineCurr.errlog 檔。
2. 在檔中，搜索**443**或字串**連接嘗試失敗**。

  !["臨時"資料夾中的錯誤日誌](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果看到問題，請使用埠 443 在 CBEngineCurr.currLog 檔中找到 Azure 公共 IP 位址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在進程伺服器上的命令列中，使用 Telnet ping Azure 公共 IP 位址。
6. 如果無法連接，請按照下一步過程操作。

## <a name="step-11-check-process-server-firewall-settings"></a>步驟 11：檢查進程伺服器防火牆設置。 

檢查進程伺服器上基於 IP 位址的防火牆是否阻止訪問。

1. 對於基於 IP 位址的防火牆規則：

    a） 下載[微軟 Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)的完整清單。

    b） 將 IP 位址範圍添加到防火牆配置中，以確保防火牆允許與 Azure 通信（以及預設 HTTPS 埠 443）。

    c） 允許訂閱的 Azure 區域和 Azure 西美國區域（用於存取控制和標識管理）的 IP 位址範圍。

2. 對於基於 URL 的防火牆，將下表中列出的 URL 添加到防火牆配置中。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>第 12 步：驗證進程伺服器代理設置 

1. 如果您使用 Proxy 伺服器，請確定 DNS 伺服器可解析 Proxy 伺服器名稱。 檢查在註冊表金鑰**HKEY_LOCAL_MACHINE_軟體_Microsoft_Azure 網站恢復\代理設置**中設置佈建服務器時提供的值。
2. 確保 Azure 網站修復代理使用相同的設置來發送資料。

    a） 搜索**微軟 Azure 備份**。

    b）**打開微軟 Azure 備份**，並選擇**操作** > **更改屬性**。

    c） 在 **"代理配置"** 選項卡上，代理位址應與註冊表設置中顯示的代理位址相同。 如果不同，請將它變更為相同的位址。

## <a name="step-13-check-bandwidth"></a>第 13 步：檢查頻寬

增加進程伺服器和 Azure 之間的頻寬，然後檢查問題是否仍然存在。


## <a name="next-steps"></a>後續步驟

如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
