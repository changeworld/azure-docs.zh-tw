---
title: 部署儲存體同步服務
description: 部署 Azure 檔案同步雲端資源（儲存體同步服務）。 共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043180"
---
在此步驟中，您需要 Azure 訂用帳號憑證。

要為 Azure 檔案同步設定的核心資源稱為「 *儲存體同步服務* 」。 我們建議您只針對目前或未來同步相同檔案集的所有伺服器部署一個。 只有當您有一組必須永遠不能交換資料的不同伺服器時，才建立多個儲存體同步服務。 例如，您可能有永遠不需要同步相同 Azure 檔案共用的伺服器。 否則，單一儲存體同步服務是最佳作法。

為您的儲存體同步服務選擇接近您的位置的 Azure 區域。 所有其他雲端資源都必須部署在相同的區域中。 若要簡化管理，請在訂用帳戶中建立新的資源群組，以存放同步和儲存體資源。

如需詳細資訊，請參閱有關部署 Azure 檔案同步的文章中的 [部署儲存體同步服務一節](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) 。請只遵循本文的這個部分。 稍後的步驟中將會有文章的其他章節連結。