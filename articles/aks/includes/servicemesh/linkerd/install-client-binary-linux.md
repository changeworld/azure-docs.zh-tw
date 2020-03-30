---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593700"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝連結連結用戶端二進位檔案

在 Linux 或適用于 Linux[的 Windows 子系統][install-wsl]上的基於`curl`bash 的 shell 中，用於下載 Linkerd 版本，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

用戶端`linkerd`二進位檔案在用戶端電腦上運行，並允許您與 Linkerd 服務網格進行交互。 使用以下命令在 Linux 或 Linux`linkerd`的[基於][install-wsl]bash 的 shell 中安裝 Linkerd 用戶端二進位檔案。 這些命令會將 `linkerd` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果希望完成 Linkerd`linkerd`用戶端二進位檔案的命令列，則按照如下方式設置：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10