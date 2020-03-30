---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593701"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝連結連結用戶端二進位檔案

在 Windows 上基於 PowerShell 的`Invoke-WebRequest`shell 中，使用 下載 Linkerd 版本如下所示：

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

用戶端`linkerd`二進位檔案在用戶端電腦上運行，並允許您與 Linkerd 服務網格進行交互。 使用以下命令在 Windows 上基於`linkerd`PowerShell 的 shell 中安裝 Linkerd 用戶端二進位檔案。 這些命令將`linkerd`用戶端二進位檔案複製到 Linkerd 資料夾，然後使其立即（在當前 shell 中）通過 永久（跨 shell 重新開機）`PATH`可用。 運行這些命令不需要提升（管理員）許可權，也不需要重新開機 shell。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```