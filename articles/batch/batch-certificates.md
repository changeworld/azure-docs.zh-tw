---
title: 使用憑證 - Azure Batch
description: 使用憑證啟用應用程式的驗證
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146380"
---
# <a name="using-certificates-with-batch"></a>搭配 Batch 使用憑證

目前搭配 Batch 使用憑證的主要原因是，如果您的應用程式是在需要使用端點進行驗證的集區中執行。 

如果您還沒有憑證，可以使用 `makecert` 命令列工具建立一個自我簽署憑證。

## <a name="upload-certificates-manually-through-the-azure-portal"></a>透過 Azure 入口網站手動上傳憑證

1. 從您想要在其中上傳憑證的 Batch 帳戶中，選取 [憑證]，然後選取 [新增]。 

2. 上傳副檔名為 .pfx 或 .cer 的憑證。 

上傳之後，憑證就會新增至憑證清單中，而且您可以驗證指紋。

現在，當您建立 Batch 集區時，可以瀏覽至集區中的憑證，並將您上傳的憑證指派給該集區。

## <a name="next-steps"></a>後續步驟

Batch 具有憑證 API，也就是 [AZ batch certificate create](/cli/azure/batch/certificate)

如需有關使用 Key Vault 的資訊，請參閱[使用 Batch 安全地存取 Key Vault](credential-access-key-vault.md)。
