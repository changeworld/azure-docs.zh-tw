---
title: Azure 虛擬機器客體作業系統防火牆設定不正確 | Microsoft Docs
description: 瞭解如何使用序列主控台或離線方法，在遠端 Azure VM 上診斷並修正設定錯誤的客體作業系統防火牆。
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422541"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure 虛擬機器客體作業系統防火牆設定不正確

本文介紹如何修正 Azure 虛擬機器上設定錯誤的客體作業系統防火牆。

## <a name="symptoms"></a>徵兆

1.  虛擬機器 (VM) 的 [歡迎使用] 畫面顯示已完全載入虛擬機器。

2.  根據客體作業系統的設定方式不同，可能會有部分網路流量到達 VM，或完全沒有流量抵達。

## <a name="cause"></a>原因

若客體系統防火牆設計不正確，可能會封鎖部分前往 VM 的網路流量，或是將所有類型的流量全數擋下。

## <a name="solution"></a>解決方法

在按照下列步驟進行之前，請先擷取受影響虛擬機器系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。

若要針對此問題進行疑難排解，請使用「序列主控台」，或是將 VM 系統磁碟連結至復原 VM，藉以[修復離線的 VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)。

## <a name="online-mitigations"></a>線上風險降低措施

連線至[序列主控台，然後開啟 PowerShell 執行個體](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 如果 VM 上未啟用序列主控台，請移至以下Azure文章的〈修復離線的 VM〉一節：

 [嘗試透過遠端桌面連線至 Azure VM 時發生內部錯誤](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

可以編輯下列規則，以啟用對 VM 的存取權 (透過 RDP)，或提供更簡單的疑難排解體驗：

*   遠端桌面 (TCP-In)：此為標準規則，可允許 Azure 中的 RDP，藉以提供對 VM 的主要存取權。

*   Windows 遠端管理 (HTTP-In)：此規則可使用 PowerShell 連線到 VM。在 Azure 中，這種類型的存取可讓您使用遠端指令碼和疑難排解的指令碼編寫層面。

*   檔案及印表機共用 (SMB-in)：此規則可將網路共用存取做為疑難排解的選項之一。

*   檔案及印表機共用 (回應要求 - ICMPv4-In)：此規則可讓您偵測虛擬機器。

在序列主控台存取執行個體中，可查詢防火牆規則的目前狀態。

*   使用顯示名稱做為參數進行查詢：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   使用應用程式所用的本機連接埠進行查詢：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   使用應用程式所用的本機 IP 位址進行查詢：

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   如果看到此規則已停用，可執行下列命令來啟用規則：

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   如需疑難排解，可關閉防火牆設定檔：

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    如果您是為了正確設定防火牆而進行此操作，請在完成疑難排解後，重新啟用防火牆。

    > [!Note]
    > 您不需要重新啟動 VM 即可套用變更。

*   再試一次，以透過 RDP 連線到 VM。

### <a name="offline-mitigations"></a>離線風險降低措施

1.  若要啟用或停用防火牆規則，請參閱 [啟用或停用 AZURE VM 來賓 OS 上的防火牆規則](enable-disable-firewall-rule-guest-os.md)。

2.  請檢查您是否處於[客體作業系統防火牆封鎖傳入流量的情況](guest-os-firewall-blocking-inbound-traffic.md) (英文)。

3.  如果對於防火牆是否封鎖存取仍有疑問，請參閱[停用 Azure 虛擬機器中的客體作業系統防火牆](disable-guest-os-firewall-windows.md)，然後使用正確的規則來重新啟用客體系統防火牆。

