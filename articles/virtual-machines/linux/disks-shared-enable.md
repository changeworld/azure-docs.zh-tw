---
title: 啟用 Azure 受控磁片的共用磁片
description: 設定具有共用磁片的 Azure 受控磁片（預覽），讓您可以跨多個 Vm 共用
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60070dc4c49f83866e2d789a6bc1f9fd6b253bae
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202277"
---
# <a name="enable-shared-disk"></a>啟用共用磁片

本文涵蓋如何啟用 Azure 受控磁片的共用磁片（預覽）功能。 Azure 共用磁片（預覽）是 Azure 受控磁片的新功能，可讓您同時將受控磁片連結至多部虛擬機器（Vm）。 將受控磁片連結至多個 Vm，可讓您將新的叢集應用程式部署至 Azure 或將其遷移至 Azure。 

如果您要尋找已啟用共用磁片的受控磁片上的概念資訊，請參閱[Azure 共用磁片](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]