---
title: 管理錄製原則-Azure
description: 本主題說明如何管理錄製原則。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011476"
---
# <a name="manage-recording-policy"></a>管理錄製原則

您可以在 IoT Edge 上使用即時影片分析進行 [持續錄製影片](continuous-video-recording-concept.md)，讓您可以將影片記錄到雲端，以獲得數周或數個月的時間。 您可以使用 Azure 儲存體內建的 [生命週期管理工具](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) ，來管理該雲端封存 (的時間長度) 。  

您的媒體服務帳戶會連結至 Azure 儲存體帳戶，當您將影片記錄到雲端時，內容會寫入媒體服務 [資產](../latest/assets-concept.md)。 每個資產都會對應到儲存體帳戶中的容器。 生命週期管理可讓您為儲存體帳戶定義 [原則](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) ，您可以在其中指定 [規則](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) ，如下所示。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上述規則：

* 適用于儲存體帳戶中的所有區塊 blob。
* 指定當 blob 的存留期超過30天時，它們會從經常性 [存取層移至非經常性存取層](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)。
* 當 blob 的存留期超過90天時，就會被刪除。

由於即時影片分析會以指定的時間單位封存您的影片，因此您的資產會包含一連串的 blob，每個區段一個 blob。 當生命週期管理原則開始使用並刪除較舊的 blob 時，您將會繼續透過媒體服務 Api 存取和播放其餘的 blob。 如需詳細資訊，請參閱 [播放錄製](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

以下是一些與生命週期管理有關的已知限制：

* 原則中最多可以有100個規則，而且每個規則最多可以指定10個容器。 因此，如果您需要不同的錄製原則 (例如，相機的3天封存（大約是停車場）、在載入 dock 中為30天，而在結帳) 計數器後方有180天，則您可以使用一個媒體服務帳戶自訂最多1000攝影機的規則。
* 生命週期管理原則更新不會立即更新。 如需詳細資訊，請參閱 [此常見問題一節](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) 。
* 如果您選擇套用將 blob 移至非經常性存取層的原則，則可能會影響該部分封存的播放。 您可能會看到額外的延遲，或偶爾發生錯誤。 媒體服務不支援在封存層中播放內容。

## <a name="next-steps"></a>接下來的步驟

[播放錄製](playback-recordings-how-to.md)
