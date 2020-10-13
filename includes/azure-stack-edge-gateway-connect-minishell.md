---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/12/2019
ms.author: alkohli
ms.openlocfilehash: f69f3ed4a439deec58d4e5d30cefe48c4834cd66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083605"
---
視用戶端的作業系統而定，從遠端連線到裝置的程式會有所不同。

### <a name="remotely-connect-from-a-windows-client"></a>從 Windows 用戶端遠端連線

開始之前，請確定您的 Windows 用戶端正在執行 Windows PowerShell 5.0 或更新版本。

遵循下列步驟從遠端連線到 Windows 用戶端。

1. 以系統管理員身分執行 Windows PowerShell 會話。
2. 確定您的用戶端上正在執行 Windows 遠端管理服務。 在命令提示字元中，輸入：

    `winrm quickconfig`

3. 將變數指派給裝置 IP 位址。

    $ip = "<device_ip>"

    `<device_ip>`以您裝置的 IP 位址取代。

4. 若要將裝置的 IP 位址新增至用戶端的信任主機清單，請輸入下列命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在裝置上啟動 Windows PowerShell 會話：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 出現提示時，請提供密碼。 使用用來登入本機 web UI 的相同密碼。 預設的本機 web UI 密碼為 *Password1*。 當您使用遠端 PowerShell 成功連接到裝置時，您會看到下列範例輸出：  

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

### <a name="remotely-connect-from-a-linux-client"></a>從遠端連線至 Linux 用戶端

在您要用來連接的 Linux 用戶端上：

- 從 GitHub[安裝最新的 Linux PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) ，以取得 SSH 遠端功能。 
- [只 `gss-ntlmssp` 從 NTLM 模組安裝套件](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 針對 Ubuntu 用戶端，請使用下列命令：
    - `sudo apt-get install gss-ntlmssp`

如需詳細資訊，請移至透過 [SSH 的 PowerShell 遠端](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)功能。

請遵循下列步驟從遠端連線到 NFS 用戶端。

1. 若要開啟 PowerShell 會話，請輸入：

    `sudo pwsh`
 
2. 若要使用遠端用戶端進行連接，請輸入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出現提示時，請提供用來登入您裝置的密碼。
 
> [!NOTE]
> 此程式無法在 Mac OS 上運作。
