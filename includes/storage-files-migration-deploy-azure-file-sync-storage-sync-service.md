---
title: 部署儲存體同步服務
description: 部署 Azure 檔案同步雲端資源。 儲存體同步服務。 共同的文字區塊，在遷移檔之間共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209450"
---
在此步驟中，您需要 Azure 訂用帳號憑證。 您所使用的 Azure 訂用帳戶可以不同于您用於 StorSimple 的訂用帳戶。

要設定 Azure 檔案同步的核心資源稱為「儲存體同步服務」。
我們建議您只針對公司中的所有伺服器部署一個，以便立即或未來同步處理同一組檔案。 如果您有一個以上的 StorSimple 應用裝置，您可以考慮為每個設備建立儲存體同步服務資源。 不過，如果您有一組不一定要交換資料的不同伺服器，您應該只建立多個儲存體同步服務。 否則，單一儲存體同步服務是最佳做法。

為接近您辦公室位置的儲存體同步服務選擇 Azure 區域。 所有其他雲端資源都必須部署在相同的區域中。
最佳做法是在您的訂用帳戶中建立新的資源群組，以存放同步處理和儲存體資源，以方便管理。

下列文章說明如何部署儲存體同步服務。 請只遵循檔的這個部分。在稍後的步驟中，將會有此檔其他小節的連結。

[瞭解如何部署儲存體同步服務。](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
