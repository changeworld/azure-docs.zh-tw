---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174441"
---
視用戶端的作業系統而定，遠端連線至裝置的程式會不同。

### <a name="remotely-connect-from-a-windows-client"></a>從 Windows 用戶端遠端連線

開始之前，請確定您的 Windows 用戶端正在執行 Windows PowerShell 5.0 或更新版本。

請遵循下列步驟，從 Windows 用戶端進行遠端連線。

1. 以系統管理員身分執行 Windows PowerShell 會話。
2. 請確定 Windows 遠端管理服務正在您的用戶端上執行。 在命令提示字元中，輸入：

    `winrm quickconfig`

3. 將變數指派給裝置 IP 位址。

    $ip = "<device_ip>"

    將取代 `<device_ip>` 為您裝置的 IP 位址。

4. 若要將裝置的 IP 位址新增至用戶端的信任主機清單，請輸入下列命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在裝置上啟動 Windows PowerShell 會話：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 出現提示時提供密碼。 使用與用來登入本機 web UI 相同的密碼。 預設的本機 web UI 密碼是*Password1*。 當您使用遠端 PowerShell 成功連接到裝置時，您會看到下列範例輸出：  

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

在您要用來連接的 Linux 用戶端上：

- 從 GitHub[安裝最新的 PowerShell Core For Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) ，以取得 SSH 遠端功能。 
- [只安裝 `gss-ntlmssp` 來自 NTLM 模組的套件](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 若是 Ubuntu 用戶端，請使用下列命令：
    - `sudo apt-get install gss-ntlmssp`

如需詳細資訊，請移至透過[SSH 的 PowerShell 遠端](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)。

請遵循下列步驟，從 NFS 用戶端進行遠端連線。

1. 若要開啟 PowerShell 會話，請輸入：

    `sudo pwsh`
 
2. 若要使用遠端用戶端進行連線，請輸入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出現提示時，請提供用來登入裝置的密碼。
 
> [!NOTE]
> 此程式不適用於 Mac OS。
