---
title: 轉譯管理員支援
description: 使用 Azure Batch 轉譯管理員整合。 了解熱門轉譯管理員的內建支援或附加元件。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726446"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>使用 Azure Batch 搭配轉譯伺服陣列管理員

如果您使用現有內部部署轉譯伺服陣列，則轉譯管理員很有可能會控制轉譯伺服陣列容量和轉譯作業。

Azure 會為受歡迎的轉譯管理員提供內建支援或附加元件。 您可以接著新增及移除 Azure VM，包括具有按使用付費授權的 VM 以及低優先順序的 VM。

支援下列轉譯器管理員：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure 轉譯中樞

Azure 轉譯中樞可簡化 Azure render 轉譯伺服器陣列的建立和管理。  轉譯中樞具有 PipelineFx Qube 和 Deadline 10 的原生支援。  如需相關資訊及詳細指示，請參閱 [GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

## <a name="using-azure-with-pipelinefx-qube"></a>使用 Azure 搭配 PipelineFX Qube

Azure 轉譯中樞支援熱門轉譯管理員，包括 Deadline。  如需部署和使用轉譯中樞的相關指示，請參閱 [GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

在 [GitHub 存放庫](https://github.com/Azure/azure-qube)中，也會提供可讓 Azure Batch 集區 VM 作為 Qube 背景工作角色的指令碼和指示。

## <a name="using-azure-with-royal-render"></a>使用 Azure 搭配 Royal Render

Royal Render 已內建 Azure 和 Azure Batch 整合功能，可讓您使用以 Azure 為基礎的 VM 擴充轉譯伺服陣列。 如需摘要，請參閱[說明檔](https://www.royalrender.de/help8/index.html?Cloudrendering.html)。

如需使用 Azure 整合的 Royal Render 客戶範例，請參閱 [Jellyfish Pictures 客戶案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>使用 Azure 搭配 Thinkbox Deadline

Azure 轉譯中樞支援熱門轉譯管理員，包括 Deadline。  如需部署和使用轉譯中樞的相關指示，請參閱 [GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

## <a name="next-steps"></a>後續步驟

使用 GitHub 上適當的外掛程式與指示，試用轉譯管理員的 Azure Batch 整合。
