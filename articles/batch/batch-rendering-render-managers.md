---
title: 轉譯管理員支援 - Azure Batch
description: 使用 Azure 批次處理呈現管理器集成。 瞭解常用渲染管理器的內置支援或載入項。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449687"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>使用 Azure Batch 搭配轉譯伺服陣列管理員

如果您使用現有內部部署轉譯伺服陣列，則轉譯管理員很有可能會控制轉譯伺服陣列容量和轉譯作業。

Azure 會為受歡迎的轉譯管理員提供內建支援或附加元件。 您可以接著新增及移除 Azure VM，包括具有按使用付費授權的 VM 以及低優先順序的 VM。

支援下列轉譯器管理員：

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure 渲染中心

Azure 呈現中心簡化了 Azure 呈現場的創建和管理。  渲染中心對管道Fx Qube 和截止時間 10 具有本機支援。  有關詳細資訊和詳細說明，請參閱[GitHub 存儲庫](https://github.com/Azure/azure-render-hub)。

## <a name="using-azure-with-pipelinefx-qube"></a>使用 Azure 搭配 PipelineFX Qube

Azure 渲染中心支援常用的渲染管理器，包括截止時間。  有關部署和使用呈現中心的說明，請參閱[GitHub 存儲庫](https://github.com/Azure/azure-render-hub)。

[GitHub 存儲庫](https://github.com/Azure/azure-qube)中還提供啟用 Azure Batch 池 VM 用作 Qube 輔助器的腳本和說明。

## <a name="using-azure-with-royal-render"></a>使用 Azure 搭配 Royal Render

Royal Render 已內建 Azure 和 Azure Batch 整合功能，可讓您使用以 Azure 為基礎的 VM 擴充轉譯伺服陣列。 如需摘要，請參閱[說明檔](https://www.royalrender.de/help8/index.html?Cloudrendering.html)。

如需使用 Azure 整合的 Royal Render 客戶範例，請參閱 [Jellyfish Pictures 客戶案例](https://customers.microsoft.com/story/jellyfishpictures)。

## <a name="using-azure-with-thinkbox-deadline"></a>使用 Azure 搭配 Thinkbox Deadline

Azure 渲染中心支援常用的渲染管理器，包括截止時間。  有關部署和使用呈現中心的說明，請參閱[GitHub 存儲庫](https://github.com/Azure/azure-render-hub)。

## <a name="next-steps"></a>後續步驟

使用 GitHub 上適當的外掛程式與指示，試用轉譯管理員的 Azure Batch 整合。
