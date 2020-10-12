---
title: 部署儲存體同步服務
description: 部署 Azure 檔案同步雲端資源（儲存體同步服務）。 共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143541"
---
在此步驟中，您需要 Azure 訂用帳號憑證。

要為 Azure 檔案同步設定的核心資源稱為「 *儲存體同步服務*」。 我們建議您只針對目前或未來同步相同檔案集的所有伺服器部署一個。 只有當您有一組絕不能交換資料的不同伺服器時，才建立多個儲存體同步服務 (例如：同步處理相同的 Azure 檔案共用) 。 否則，單一儲存體同步服務是最佳作法。

為您的儲存體同步服務選擇接近您的位置的 Azure 區域。 所有其他雲端資源都必須部署在相同的區域中。
若要簡化管理，請在訂用帳戶中建立新的資源群組，以存放同步和儲存體資源。

如需詳細資訊，請參閱有關部署 Azure 檔案同步的文章中的 [部署儲存體同步服務一節](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) 。請只遵循本文的這個部分。 稍後的步驟中將會有文章的其他章節連結。