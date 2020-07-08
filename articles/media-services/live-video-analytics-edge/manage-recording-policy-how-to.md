---
title: 記錄管理原則-Azure
description: 本主題說明如何記錄管理原則。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 321e68087bfe2a8b3e1354e49585a89f9d3af295
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260721"
---
# <a name="manage-recording-policy"></a>管理錄製原則

您可以在 IoT Edge 上使用即時影片分析來進行[連續的影片錄製](continuous-video-recording-concept.md)，讓您可以將影片錄製到雲端中數周或數個月。 您可以使用 Azure 儲存體內建的[生命週期管理工具](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)來管理該雲端封存的長度（以天為單位）。  

您的媒體服務帳戶會連結到 Azure 儲存體帳戶，而當您將影片錄製到雲端時，內容會寫入至媒體服務[資產](../latest/assets-concept.md)。 每個資產都會對應至儲存體帳戶中的容器。 生命週期管理可讓您定義儲存體帳戶的[原則](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#policy)，您可以在其中指定[規則](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#rules)，如下所示。

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
* 指定當 blob 存留期超過30天時，它們會從經常性[存取層移至非經常性存取層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)。
* 而當 blob 的存留期超過90天時，就會被刪除。

因為即時影片分析會在指定的時間單位內封存您的影片，所以您的資產會包含一連串的 blob，每個區段一個 blob。 當生命週期管理原則啟動並刪除較舊的 blob 時，您將可繼續透過媒體服務 Api 存取及播放其餘的 blob。 如需詳細資訊，請參閱[播放錄製](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

以下是一些有關生命週期管理的已知限制：

* 原則內最多可以有100個規則，而且每個規則最多可以指定10個容器。 因此，如果您需要有不同的錄音原則（例如，在相機上有3天的封存，並將相機放在外，則在載入 dock 的時間為30天，而在 [結帳] 計數器後方的相機則為180天），然後使用一個媒體服務帳戶，就可以自訂最多1000相機的規則。
* 不會立即更新生命週期管理原則。 如需詳細資訊，請參閱[此常見問題一節](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal#faq)。
* 如果您選擇套用將 blob 移至非經常性存取層的原則，則可能會影響該封存部分的播放。 您可能會看到額外的延遲或偶爾發生的錯誤。 媒體服務不支援在封存層中播放內容。

## <a name="next-steps"></a>後續步驟

[播放錄製](playback-recordings-how-to.md)
