---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593702"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 MacOS 上以 bash 為基礎的 shell 中`curl` ，使用下載 Linkerd 版本，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd`用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令，將 Linkerd `linkerd`用戶端二進位檔安裝在 MacOS 上以 bash 為基礎的 shell 中。 這些命令會將 `linkerd` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果您想要讓 Linkerd `linkerd`用戶端二進位檔完成命令列，請依照下列方式進行設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
