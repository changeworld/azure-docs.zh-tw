---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81734100"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 Windows 上以 PowerShell 為基礎的 shell 中，使用 `Invoke-WebRequest` 下載 Linkerd 版本，如下所示：

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd`用戶端二進位檔會在用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令， `linkerd` 在 Windows 上以 PowerShell 為基礎的 shell 中安裝 Linkerd 用戶端二進位檔。 這些命令會將 `linkerd` 用戶端二進位檔複製到 Linkerd 資料夾，然後立即在目前的 shell 中 () ，並透過從 shell 重新開機) 永久 (`PATH` 。 您不需要提高許可權 (系統管理員) 許可權來執行這些命令，而且您不需要重新開機 shell。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```