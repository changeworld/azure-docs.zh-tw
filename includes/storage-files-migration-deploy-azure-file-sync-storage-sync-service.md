---
title: 部署儲存體同步服務
description: 部署 Azure 檔案同步雲端資源（儲存體同步服務）。 跨遷移檔共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143541"
---
在此步驟中，您需要 Azure 訂用帳號憑證。

要設定 Azure 檔案同步的核心資源稱為「*儲存體同步服務*」。 我們建議您只針對目前或未來同步處理相同檔案集的所有伺服器部署一個。 只有當您有一組不一定要交換資料的不同伺服器（例如：同步處理相同的 Azure 檔案共用）時，才建立多個儲存體同步服務。 否則，單一儲存體同步服務是最佳做法。

為您的儲存體同步服務選擇接近您的位置的 Azure 區域。 所有其他雲端資源都必須部署在相同的區域中。
若要簡化管理，請在您的訂用帳戶中建立新的資源群組，以存放同步和儲存體資源。

如需詳細資訊，請參閱關於部署 Azure 檔案同步一文中的[部署儲存體同步服務一節](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)。請只遵循本文的這個部分。 稍後的步驟將會提供文章中其他區段的連結。