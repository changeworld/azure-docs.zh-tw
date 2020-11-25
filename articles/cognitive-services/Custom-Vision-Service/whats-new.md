---
title: 自訂視覺的新功能
titleSuffix: Azure Cognitive Services
description: 本文包含有關自訂視覺的消息。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 57f3cf0cb15243d054da0111366f3a1dc0fb5349
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95739752"
---
# <a name="whats-new-in-custom-vision"></a>自訂視覺的新功能

了解該服務的新功能。 這些項目可能是版本資訊、影片、部落格文章與其他類型的資訊。 將此頁面加入書簽，以掌握服務的最新狀態。


## <a name="october-2020"></a>2020 年 10 月 

### <a name="custom-base-model"></a>自訂基底模型

- 有些應用程式有大量的聯合定型資料，但需要分別微調其模型；對於來自不同來源但有些許差異的影像，這會導致較佳的效能。 在此情況下，您可以像平常一樣使用大量的定型資料來定型第一個模型。 然後在要求本文中使用 _CustomBaseModelInfo_ 呼叫 3.4 公開預覽 API 中的 **TrainProject**，以使用第一個階段定型的模型作為下游專案的基底模型。 如果來源專案和下游目標專案有類似的影像特性，則應該會有更好的效能。 

### <a name="new-domain-information"></a>新的網域資訊

- 從自訂視覺 3.4 公開預覽 API 中的 **GetDomains** 傳回的網域資訊，現在會包含支援的可匯出平台、模型架構的簡短描述，以及精簡網域的模型大小。

### <a name="training-divergence-feedback"></a>定型分歧意見反應

- 自訂視覺 3.4 公開預覽 API 現在會從 **GetIteration** 呼叫傳回 **TrainingErrorDetails**。 在失敗的反復項目上，這會顯示失敗是否因定型分歧而造成，從而可以利用更多更高品質的定型資料來補救。

## <a name="july-2020"></a>2020 年 7 月

### <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

* 自訂視覺支援 Azure 角色型存取控制 (Azure RBAC) 授權系統，可用於管理 Azure 資源的個別存取權。 若要了解如何管理自訂視覺專案的存取權，請參閱 [Azure 角色型存取控制](./role-based-access-control.md)。

### <a name="subset-training"></a>子集訓練

* 訓練物件偵測專案時，您可以選擇只在已套用的標記子集上進行訓練。 您可以在尚未套用足夠的特定標記，但其他套用的標記已足夠時執行此操作。 請遵循適用於 C# 或 Python 的[用戶端程式庫快速入門](./quickstarts/object-detection.md)來深入了解。

### <a name="azure-storage-notifications"></a>Azure 儲存體通知

* 您可以將自訂視覺專案與 Azure Blob 儲存體佇列整合，以取得專案訓練/匯出活動的推播通知，以及已發行模型的備份副本。 這項功能有助於避免長時間執行作業時，系統持續輪詢服務來取得結果。 相反地，您可以將儲存體佇列通知整合到您的工作流程中。 若要深入了解，請參閱[儲存體整合](./storage-integration.md)指南。

### <a name="copy-and-move-projects"></a>複製並移動專案

* 您現在可以將專案從一個自訂視覺帳戶複製到其他帳戶。 您可以將專案從開發移至實際執行環境，或將專案備份到不同 Azure 區域中的帳戶，以提高資料安全性。 若要深入了解，請參閱[複製和移動專案](./copy-move-projects.md)指南。

## <a name="september-2019"></a>2019 年 9 月

### <a name="suggested-tags"></a>建議的標記

* [自訂視覺網站](https://www.customvision.ai/)上的智慧型 Labeler 工具會為您的定型影像產生建議的標記。 這可讓您在定型自訂視覺模型時，更快速地標記大量影像。 如需如何使用這項功能的指示，請參閱[建議的標記](./suggested-tags.md)。

## <a name="cognitive-service-updates"></a>認知服務更新

[認知服務的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)