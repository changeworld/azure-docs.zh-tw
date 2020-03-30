---
title: 部署存儲同步服務
description: 部署 Azure 檔同步雲資源。 存儲同步服務。 在遷移文檔之間共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124732"
---
在此步驟中，您需要 Azure 訂閱憑據。

配置 Azure 檔同步的核心資源稱為"存儲同步服務"。
我們建議您只為公司中的所有伺服器部署一個，這些伺服器現在或將來同步同一組檔。 僅當具有必須交換資料的不同伺服器集時，才創建多個存儲同步服務。 （例如：同步相同的 Azure 檔共用）。 否則，最佳做法是單個存儲同步服務。

為靠近辦公室位置的存儲同步服務選擇 Azure 區域。 所有其他雲資源必須部署在同一區域。
為了簡化管理，請在訂閱中創建一個包含同步和存儲資源的新資源組。

以下文章介紹如何部署存儲同步服務。 只遵循文檔的這一部分。在後面的步驟中，將有指向此文檔其他子部分的連結。

[瞭解如何部署存儲同步服務。](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
