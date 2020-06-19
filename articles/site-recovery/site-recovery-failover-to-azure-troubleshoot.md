---
title: 針對容錯移轉至 Azure 失敗進行疑難排解 | Microsoft Docs
description: 本文說明如何針對容錯移轉至 Azure 的常見錯誤進行疑難排解
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 9ef38829a18f9e43f38cbdb291a799110d710cd7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834725"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>針對將 VMware VM 或實體機器容錯移轉至 Azure 時的錯誤進行疑難排解

將虛擬機器容錯移轉至 Azure 時，您可能會收到下列錯誤。 若要進行疑難排解，請使用針對每種錯誤狀況所述的步驟。

## <a name="failover-failed-with-error-id-28031"></a>容錯移轉失敗，錯誤識別碼為 28031

Site Recovery 無法在 Azure 中建立已容錯移轉的虛擬機器。 它可能會因為下列其中一個原因而發生：

* 沒有足夠配額可用來建立虛擬機器：您可以移至 [訂用帳戶] -> [使用量 + 配額]，檢查可用的配額。 您可以開啟[新的支援要求](https://aka.ms/getazuresupport)來增加配額。

* 您嘗試在相同的可用性設定組中容錯移轉不同大小系列的虛擬機器。 確保您為相同可用性設定組中的所有虛擬機器，選擇相同的大小系列。 移至虛擬機器的 [計算] 和 [網路] 來變更大小，然後重試容錯移轉。

* 有訂用帳戶原則可避免建立虛擬機器。 將此原則變更為允許建立虛擬機器，然後重試容錯移轉。

## <a name="failover-failed-with-error-id-28092"></a>容錯移轉失敗，錯誤識別碼為 28092

Site Recovery 無法針對已容錯移轉的虛擬機器建立網路介面。 確定您有足夠的配額可用來在訂用帳戶中建立網路介面。 您可以移至 [訂用帳戶] -> [使用量 + 配額]，檢查可用的配額。 您可以開啟[新的支援要求](https://aka.ms/getazuresupport)來增加配額。 如果您有足夠的配額，這可能是間歇性問題，請重試一次此作業。 如果即使在重試之後，問題仍然存在，則在本文的結尾留言。  

## <a name="failover-failed-with-error-id-70038"></a>容錯移轉失敗，錯誤識別碼為 70038

Site Recovery 無法在 Azure 中建立已容錯移轉的傳統虛擬機器。 其發生原因可能是：

* 其中一個資源 (例如建立虛擬機器所需的虛擬網路) 不存在。 建立如虛擬機器的 [計算] 和 [網路] 設定之下提供的虛擬網路，或將此設定修改為已經存在的虛擬網路，然後重試容錯移轉。

## <a name="failover-failed-with-error-id-170010"></a>容錯移轉失敗，錯誤識別碼為 170010

Site Recovery 無法在 Azure 中建立已容錯移轉的虛擬機器。 這可能是因為內部部署虛擬機器的序列化內部活動失敗所造成。

若要在 Azure 中顯示任何機器，Azure 環境需要某些驅動程式處於開機啟動狀態，以及 DHCP 等服務處於自動啟動狀態。 因此，序列化活動會在容錯移轉時，將 **atapi、intelide、storflt、vmbus 和 storvsc 驅動程式**的啟動類型轉換為開機啟動。 它也會將 DHCP 等一些服務的啟動類型轉換為自動啟動。 此活動可能會因為環境特定問題而失敗。 

若要手動變更 [Windows 客體 OS] 驅動程式的啟動類型，請依照下列步驟進行：

1. [下載](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) no-hydration 指令碼，並如下所示執行。 此指令碼會檢查 VM 是否需要序列化。

    `.\Script-no-hydration.ps1`

    如果需要序列化，則會提供下列結果：

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    如果 VM 符合無序列化需求，指令碼會提供結果：「此系統符合無序列化需求」。 在此情況下，所有驅動程式和服務都會處於 Azure 所需的狀態，而且 VM 上不需要序列化。

2. 如果 VM 不符合無序列化需求，請如下所示執行 no-hydration-set 指令碼。

    `.\Script-no-hydration.ps1 -set`
    
    這會轉換驅動程式的啟動類型，並提供類似如下的結果：
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>因虛擬機器上的 [連線] 按鈕變成灰色，而無法對容錯移轉的虛擬機器進行連線/RDP/SSH

如果 Azure 中容錯移轉虛擬機器上的 [連線] 按鈕呈現灰色，而您未透過 Express Route 或網站間 VPN 連線來連線到 Azure，則請：

1. 移至 [虛擬機器] > [網路]，按一下所需網路介面的名稱。  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. 瀏覽至 [IP 組態]，然後按一下所需 IP 組態的名稱欄位。 ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. 若要啟用公用 IP 位址，請按一下 [啟用]。 ![啟用 IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. 按一下 [設定必要設定] > [建立新項目]。 ![建立新項目](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. 輸入公用位址的名稱，選擇 [SKU] 和 [指派] 的預設選項，然後按一下 [確定]。
6. 現在，為了儲存所做的變更，請按一下 [儲存]。
7. 關閉面板，然後瀏覽至虛擬機器的 [概觀] 區段來進行連線/RDP。

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>無法連線/RDP/SSH - [VM 連線] 按鈕可供使用

如果 Azure 中容錯移轉虛擬機器上的 [連線] 按鈕可供使用 (未呈現灰色)，請檢查虛擬機器上的 [開機診斷]，並檢查[本文](../virtual-machines/windows/boot-diagnostics.md)中列出的錯誤。

1. 如果虛擬機器尚未啟動，請嘗試容錯移轉至較舊的復原點。
2. 如果虛擬機器內的應用程式未啟動，請嘗試容錯移轉至與應用程式一致的復原點。
3. 如果虛擬機器已加入網域，請確定網域控制站正確運作。 做法是依照以下提供的步驟進行操作：

    a. 在相同的網路中建立新的虛擬機器。

    b.  確定它能夠加入預期啟動已容錯移轉之虛擬機器的相同網域。

    c. 如果網域控制站**未**正常運作，請使用本機系統管理員帳戶來嘗試登入已容錯移轉的虛擬機器。
4. 如果您使用自訂的 DNS 伺服器，則請確定其可供連線。 做法是依照以下提供的步驟進行操作：

    a. 在相同的網路中建立新的虛擬機器，以及

    b. 檢查虛擬機器是否能夠使用自訂 DNS 伺服器進行名稱解析

>[!Note]
>若要啟用「開機診斷」以外的任何設定，將必須於容錯移轉之前，在虛擬機器中安裝「Azure VM 代理程式」

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>將 UEFI 型機器容錯移轉到 Azure 之後，即無法開啟序列主控台

如果可使用 RDP 連線到機器，但無法開啟序列主控台，請遵循下列步驟：

* 如果機器 OS 是 Red Hat 或 Oracle Linux 7.*/8.0，請以根權限在容錯移轉 Azure VM 上執行下列命令。 在命令之後重新啟動 VM。

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* 如果機器 OS 是 CentOS 7.*，請以根權限在容錯移轉 Azure VM 上執行下列命令。 在命令之後重新啟動 VM。

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>非預期性關閉訊息 (事件識別碼 6008)

啟動 Windows VM 容錯移轉後，如果在復原的 VM 上收到非預期性關閉訊息，表示未在用於容錯移轉的復原點中擷取 VM 關閉狀態。 當您復原至 VM 未完全關閉的時間點時，就會發生這種情況。

一般而言，無須擔心此問題，對於非計劃性容錯移轉，通常可忽略此問題。 如果規劃容錯移轉，請確保在容錯移轉之前正確關閉 VM，並提供足夠時間來將擱置的內部部署複寫資料傳送至 Azure。 然後使用 [容錯移轉](site-recovery-failover.md#run-a-failover) 畫面控制項 上的 [最新] 選項，以便將 Azure 上的任何擱置資料處理到復原點，然後將其用於 VM 容錯移轉。

## <a name="unable-to-select-the-datastore"></a>無法選取資料存放區

此問題是指當嘗試重新保護發生容錯移轉的虛擬機器時，無法在 Azure 入口網站中看到資料存放區。 這是因為主要目標無法辨識為新增至 Azure Site Recovery 的 vCenters 下虛擬機器。

如需重新保護虛擬機器的詳細資訊，請參閱[在容錯移轉至 Azure 之後將機器重新保護和容錯回復至內部部署網站](vmware-azure-reprotect.md)。

若要解決此問題：

請在管理來源機器的 vCenter 中手動建立主要目標。 資料存放區將會在下一次 vCenter 探索和重新整理網狀架構作業之後提供使用。

> [!Note]
> 
> 探索和重新整理網狀架構作業最多可能需要 30 分鐘的時間才能完成。 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>將 Linux 主要目標註冊到 CS 失敗，發生 TLS 錯誤 35 

因為主要目標上將啟用驗證 Proxy，所以將 Azure Site Recovery 主要目標註冊到設定伺服器失敗。 
 
此錯誤會以安裝記錄中的下列字串表示： 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

若要解決此問題：
 
1. 在設定伺服器 VM 上，開啟命令提示字元，並使用下列命令來確認 Proxy 設定：

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. 如果先前命令的輸出顯示已定義 http_proxy 或 https_proxy 設定，請使用下列其中一種方法來解除封鎖與設定伺服器的主要目標通訊：
   
   - 下載 [PsExec 工具](https://aka.ms/PsExec)。
   - 使用此工具來存取系統使用者內容，並判斷是否已設定 Proxy 位址。 
   - 如果已設定 Proxy，請使用 PsExec 工具在系統使用者內容中開啟 IE。
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - 若要確保主要目標伺服器可與設定伺服器通訊：
  
     - 修改 Internet Explorer 中的 Proxy 設定，以略過透過 Proxy 的主要目標伺服器 IP 位址。   
     Or
     - 停用主要目標伺服器上的 Proxy。 


## <a name="next-steps"></a>後續步驟
- 對 [Windows VM 的 RDP 連線](../virtual-machines/windows/troubleshoot-rdp-connection.md)進行移難排解
- 對 [Linux VM 的 SSH 連線](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)進行疑難排解

如果需要更多說明，請將查詢張貼在 [Microsoft 問與答的 Site Recovery 問題頁面](https://docs.microsoft.com/answers/topics/azure-site-recovery.html)上或在本文件結尾留言。 我們有一個能夠協助您的使用中社群。
