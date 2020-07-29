---
title: 建立並上傳 Flatcar 容器 Linux VHD 以在 Azure 中使用
description: 瞭解如何建立及上傳包含 Flatcar 容器 Linux 作業系統的 VHD。
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268234"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>針對 Azure 使用預先建立的 Flatcar 映射

您可以針對每個 Flatcar 支援的通道，下載 Flatcar 容器 Linux 的預建 Azure 虛擬硬碟映射：

- [穩定](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [搶鮮版 (Beta)](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [分量](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [下邊](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

此映射已完整設定，並已優化，可在 Azure 上執行。 您只需要將它解壓縮。

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>為 Azure 建立您自己的 Flatcar 型虛擬機器

或者，您可以選擇建立自己的 Flatcar 容器 Linux 映射。

在任何以 linux 為基礎的電腦上，遵循[Flatcar 容器 linux 開發人員 SDK 指南](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)中詳述的指示。 `image_to_vm.sh`執行腳本時，請確定您已通過， `--format=azure` 以建立 Azure 虛擬硬碟。

## <a name="next-steps"></a>後續步驟

一旦擁有 .vhd 檔案之後，您就可以使用產生的檔案在 Azure 中建立新的虛擬機器。 如果這是您第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁片建立 LINUX VM](upload-vhd.md#option-1-upload-a-vhd)。
