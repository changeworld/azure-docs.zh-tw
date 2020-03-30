---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174441"
---
根據用戶端的作業系統，遠端連線到設備的過程是不同的。

### <a name="remotely-connect-from-a-windows-client"></a>從 Windows 用戶端遠端連線

開始之前，請確保 Windows 用戶端正在運行 Windows PowerShell 5.0 或更高版本。

按照以下步驟從 Windows 用戶端遠端連線。

1. 以管理員身份運行 Windows PowerShell 會話。
2. 確保 Windows 遠端系統管理服務在用戶端上運行。 在命令提示字元中，輸入：

    `winrm quickconfig`

3. 將變數分配給設備 IP 位址。

    $ip = "<device_ip>"

    替換為`<device_ip>`設備的 IP 位址。

4. 要將設備的 IP 位址添加到用戶端的受信任主機清單中，請鍵入以下命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在設備上啟動 Windows PowerShell 會話：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 在出現提示時提供密碼。 使用用於登錄到本地 Web UI 的相同密碼。 預設的本地 Web UI 密碼為*密碼 1*。 使用遠端 PowerShell 成功連接到設備時，您將看到以下示例輸出：  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>從 Linux 用戶端遠端連線

在用於連接的 Linux 用戶端上：

- 從 GitHub[安裝最新的 Linux PowerShell 內核](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6)，以獲得 SSH 遠端處理功能。 
- [僅從`gss-ntlmssp`NTLM 模組安裝包](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 對於 Ubuntu 用戶端，請使用以下命令：
    - `sudo apt-get install gss-ntlmssp`

有關詳細資訊，請訪問[PowerShell 在 SSH 上進行遠端處理](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)。

按照以下步驟從 NFS 用戶端遠端連線。

1. 要打開 PowerShell 會話，鍵入：

    `sudo pwsh`
 
2. 對於使用遠端用戶端進行連接，鍵入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出現提示時，提供用於登錄到設備的密碼。
 
> [!NOTE]
> 此過程在 Mac 作業系統上不起作用。
