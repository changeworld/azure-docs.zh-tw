---
title: 建立並上傳要在 Azure 中使用的 Flatcar 容器 Linux VHD
description: 瞭解如何建立及上傳包含 Flatcar 容器 Linux 作業系統的 VHD。
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 555e53899ed78a5200009d04659e974f8157057e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87268234"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>使用預建的 Azure Flatcar 映射

您可以針對每個 Flatcar 支援的通道，下載 Flatcar 容器 Linux 預建的 Azure 虛擬硬碟映射：

- [穩定](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [搶鮮版 (Beta)](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [邊緣](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

此映射已完整設定並優化，可在 Azure 上執行。 您只需要將它解壓縮。

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>為 Azure 建立您自己的 Flatcar 型虛擬機器

或者，您也可以選擇建立自己的 Flatcar 容器 Linux 映射。

在任何以 linux 為基礎的電腦上，依照 [Flatcar 容器 linux DEVELOPER SDK 指南](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)中所述的指示進行。 `image_to_vm.sh`執行腳本時，請務必傳遞 `--format=azure` 來建立 Azure 虛擬硬碟。

## <a name="next-steps"></a>接下來的步驟

擁有 .vhd 檔案之後，您就可以使用產生的檔案，在 Azure 中建立新的虛擬機器。 如果這是您第一次將 .vhd 檔案上傳至 Azure，請參閱 [從自訂磁片建立 LINUX VM](upload-vhd.md#option-1-upload-a-vhd)。
