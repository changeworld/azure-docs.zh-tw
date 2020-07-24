---
title: 啟用 Azure 受控磁片的共用磁片
description: 設定具有共用磁片的 Azure 受控磁片，讓您可以跨多個 Vm 共用
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080213"
---
# <a name="enable-shared-disk"></a>啟用共用磁碟

本文涵蓋如何啟用 Azure 受控磁片的共用磁片功能。 Azure 共用磁片是 Azure 受控磁片的新功能，可讓您同時將受控磁片連結至多部虛擬機器（Vm）。 將受控磁片連結至多個 Vm，可讓您將新的叢集應用程式部署至 Azure 或將其遷移至 Azure。 

如果您要尋找已啟用共用磁片的受控磁片上的概念資訊，請參閱[Azure 共用磁片](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]