---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666690"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下載並安裝 Istio istioctl 用戶端二進位檔

在 Windows 上以 PowerShell 為基礎的 shell 中，使用 `Invoke-WebRequest` 下載 Istio 版本，然後使用解壓縮，如下 `Expand-Archive` 所示：

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl`用戶端二進位檔會在用戶端電腦上執行，並可讓您在 AKS 叢集中安裝及管理 Istio。 使用下列命令， `istioctl` 在 Windows 上以 PowerShell 為基礎的 shell 中安裝 Istio 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 Istio 資料夾，然後立即在目前的 shell 中使用 () ，並透過您的) 在 shell 重新開機之間永久 (`PATH` 。 您不需要提高許可權 (系統管理員) 許可權來執行這些命令，而且您不需要重新開機 shell。

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```