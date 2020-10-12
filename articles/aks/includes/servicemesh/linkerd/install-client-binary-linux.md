---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86277821"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 Linux 或 [Windows 子系統 Linux 版][install-wsl]上以 bash 為基礎的 shell 中，請使用 `curl` 下載 Linkerd 版本，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd`用戶端二進位檔會在用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令， `linkerd` 在 Linux 或 [Windows 子系統 Linux 版][install-wsl]上以 bash 為基礎的 shell 安裝 Linkerd 用戶端二進位檔。 這些命令會將 `linkerd` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果您想要 Linkerd 用戶端二進位檔的命令列完成 `linkerd` ，請設定它，如下所示：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
