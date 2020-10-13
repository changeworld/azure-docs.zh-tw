---
title: 包含檔案
description: 包含檔案
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83797122"
---
在 Azure Batch 中執行的工作，可能會在執行時產生輸出資料。 工作輸出的資料通常需要加以儲存，才能供作業中的其他工作、執行作業的用戶端應用程式 (或兩者) 擷取。 工作會將輸出資料寫入 Batch 計算節點的檔案系統，但當它重新安裝映像，或當節點離開集區時，節點上的所有資料都會遺失。 工作可能也會有檔案保留期，在此期間之後，會將工作所建立的檔案刪除。 基於這些理由，請務必將您稍後需要的工作輸出保存至資料存放區，例如 [Azure 儲存體](https://docs.microsoft.com/azure/storage/)。

如需 Batch 中的儲存體帳戶選項，請參閱 [Batch 帳戶與 Azure 儲存體帳戶](../articles/batch/accounts.md#azure-storage-accounts)。
