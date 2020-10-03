---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666691"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下載並安裝 Istio istioctl 用戶端二進位檔

在 macOS 上以 bash 為基礎的 shell 中，使用 `curl` 下載 Istio 版本，然後以如下方式解壓縮 `tar` ：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl`用戶端二進位檔會在用戶端電腦上執行，並可讓您在 AKS 叢集中安裝及管理 Istio。 使用下列命令， `istioctl` 在 macOS 上以 bash 為基礎的 shell 中安裝 Istio 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

如果您想要讓 Istio `istioctl` 用戶端二進位檔完成命令列，請進行如下所示的設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```