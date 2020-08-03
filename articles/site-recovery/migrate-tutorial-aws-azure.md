---
title: 使用 Azure Site Recovery 服務將 AWS VM 移轉至 Azure | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 將 Amazon Web Services (AWS) 中執行的 Windows VM 移轉至 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281288"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>將 Amazon Web Services (AWS) VM 移轉至 Azure

本文說明將 Amazon Web Services (AWS) 執行個體遷移至 Azure 的選項。

## <a name="migrate-with-azure-migrate"></a>使用 Azure Migrate 遷移

建議您使用 [Azure Migrate](../migrate/migrate-services-overview.md) 服務，將 AWS 執行個體遷移至 Azure。 Azure Migrate 提供使用 Azure Migrate、其他 Azure 服務及協力廠商工具，來評估內部部署機器並將其遷移至 Azure 的集中式中樞。

[了解如何](../migrate/tutorial-migrate-aws-virtual-machines.md)使用 Azure Migrate 遷移 AWS 執行個體。 


## <a name="migrate-with-site-recovery"></a>使用 Site Recovery 遷移

Site Recovery 應該僅用於災害復原，而不應用於移轉。

如果您已經在使用 Azure Site Recovery，而想繼續用其進行 AWS 移轉，請遵循您用來設定[實體機器的災害復原](physical-azure-disaster-recovery.md)的相同步驟。


> [!NOTE]
> 執行容錯移轉以進行災害復原時，您會在最後一個步驟中認可容錯移轉。 遷移 AWS 執行個體時，[認可] 選項並不相關。 相反地，您可以選取 [完成移轉] 選項。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [檢閱關於 Azure Migrate 的常見問題](../migrate/resources-faq.md)。
