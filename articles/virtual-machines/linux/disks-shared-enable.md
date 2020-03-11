---
title: 啟用 Azure 受控磁片的共用磁片
description: 設定具有共用磁片的 Azure 受控磁片（預覽），讓您可以跨多個 Vm 共用
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970370"
---
# <a name="enable-shared-disk"></a>啟用共用磁片

本文涵蓋如何啟用 Azure 受控磁片的共用磁片（預覽）功能。 Azure 共用磁片（預覽）是 Azure 受控磁片的新功能，可讓您同時將受控磁片連結至多部虛擬機器（Vm）。 將受控磁片連結至多個 Vm，可讓您將新的叢集應用程式部署至 Azure 或將其遷移至 Azure。 

如果您要尋找已啟用共用磁片的受控磁片上的概念資訊，請參閱[Azure 共用磁片](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]