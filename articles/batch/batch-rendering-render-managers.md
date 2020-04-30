---
title: 轉譯管理員支援
description: 使用 Azure Batch render manager 整合。 瞭解適用于熱門轉譯管理員的內建支援或附加元件。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 9921c68bf6ebe44d6d2a6b7b74afbe3fa98e0803
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115732"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>使用 Azure Batch 搭配轉譯伺服陣列管理員

如果您使用現有內部部署轉譯伺服陣列，則轉譯管理員很有可能會控制轉譯伺服陣列容量和轉譯作業。

Azure 會為受歡迎的轉譯管理員提供內建支援或附加元件。 您可以接著新增及移除 Azure VM，包括具有按使用付費授權的 VM 以及低優先順序的 VM。

支援下列轉譯器管理員：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render 中樞

Azure 轉譯中樞可簡化 Azure render 伺服器陣列的建立和管理。  呈現中樞具有 PipelineFx Qube 和期限10的原生支援。  如需詳細資訊和詳細指示，請參閱[GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

## <a name="using-azure-with-pipelinefx-qube"></a>使用 Azure 搭配 PipelineFX Qube

Azure Render Hub 支援熱門的轉譯管理員，包括期限。  如需部署和使用轉譯中樞的指示，請參閱[GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

[GitHub 存放庫](https://github.com/Azure/azure-qube)中也提供啟用 Azure Batch 集區 Vm 作為 Qube 背景工作的腳本和指示。

## <a name="using-azure-with-royal-render"></a>使用 Azure 搭配 Royal Render

Royal Render 已內建 Azure 和 Azure Batch 整合功能，可讓您使用以 Azure 為基礎的 VM 擴充轉譯伺服陣列。 如需摘要，請參閱[說明檔](https://www.royalrender.de/help8/index.html?Cloudrendering.html)。

如需使用 Azure 整合的 Royal Render 客戶範例，請參閱 [Jellyfish Pictures 客戶案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>使用 Azure 搭配 Thinkbox Deadline

Azure Render Hub 支援熱門的轉譯管理員，包括期限。  如需部署和使用轉譯中樞的指示，請參閱[GitHub 存放庫](https://github.com/Azure/azure-render-hub)。

## <a name="next-steps"></a>後續步驟

使用 GitHub 上適當的外掛程式與指示，試用轉譯管理員的 Azure Batch 整合。
