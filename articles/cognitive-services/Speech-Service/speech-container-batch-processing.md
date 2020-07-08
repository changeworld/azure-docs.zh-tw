---
title: 語音容器的批次處理套件
titleSuffix: Azure Cognitive Services
description: 使用批次處理套件來調整語音容器要求。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4d0800ff8a35c5c91b067a85dfcc089f2e343d1f
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090914"
---
# <a name="batch-processing-kit-for-speech-containers"></a>語音容器的批次處理套件

使用批次處理套件來補充和相應放大語音容器上的工作負載。 這個開放原始碼公用程式是以容器的形式提供，可協助您在任意數目的內部部署和雲端式語音容器端點上，對大量的音訊檔案進行批次轉譯。 

:::image type="content" source="media/containers/general-diagram.png" alt-text="顯示範例 batch 套件容器工作流程的圖表。":::

Batch 套件容器可在[GitHub](https://github.com/microsoft/batch-processing-kit)和[Docker hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)上免費使用。 您只需[支付](speech-container-howto.md#billing)所使用的語音容器費用。

| 功能  | 描述  |
|---------|---------|
| Batch 音訊檔案散發     | 會自動將大量檔案分派至內部部署或雲端式語音容器端點。 檔案可以位於任何 POSIX 相容的磁片區上，包括網路檔案系統。       |
| 語音 SDK 整合 | 將通用旗標傳遞給語音 SDK，包括： n-最佳假設、diarization、語言、不雅內容遮罩。  |
|執行模式     | 在背景中持續執行 batch 用戶端一次，或建立音訊檔案的 HTTP 端點。         |
| 容錯 | 自動重試並繼續轉譯，而不會遺失進度，並區別哪些錯誤可以，而且無法在上重試。 |
| 端點可用性偵測 | 如果端點變得無法使用，則 batch 用戶端會繼續使用其他容器端點轉譯。 再次可用之後，用戶端將會自動開始使用端點。   |
| 端點熱交換 | 在執行時間期間新增、移除或修改語音容器端點，而不會中斷批次進度。 更新會立即進行。 |
| 即時記錄 | 針對每個音訊檔案的語音 SDK 記錄檔，即時記錄嘗試的要求、時間戳記和失敗原因。 |

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令來下載最新的 batch 套件容器。

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>端點組態

Batch 用戶端會使用 yaml 設定檔來指定內部內部部署容器端點。 下列範例可寫入至 `/mnt/my_nfs/config.yaml` ，這會在下列範例中使用。 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

此 yaml 範例會在三部主機上指定三個語音容器。 第一個主機是由 IPv4 位址指定，第二個是在與批次用戶端相同的 VM 上執行，而第三個容器是由另一個 VM 的 DNS 主機名稱所指定。 `concurrency`值會指定可以在同一個容器上執行的最大並行檔案轉譯。 `rtf`（即時因數）值是選擇性的，可用來微調效能。
 
Batch 用戶端可以動態偵測端點是否變成無法使用（例如，因為容器重新開機或網路問題），以及何時可再次使用。 轉譯要求不會傳送到無法使用的容器，而且用戶端會繼續使用其他可用的容器。 您可以隨時新增、移除或編輯端點，而不會中斷批次的進度。


## <a name="run-the-batch-processing-container"></a>執行批次處理容器
  
> [!NOTE] 
> * 這個範例會 `/my_nfs` 針對設定檔和輸入、輸出和記錄目錄使用相同的目錄（）。 您可以使用裝載或 NFS 掛接的目錄來存放這些資料夾。
> * 執行用戶端時 `–h` ，將會列出可用的命令列參數及其預設值。 

使用 Docker `run` 命令來啟動容器。 這會在容器內啟動互動式 shell。

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

若要執行 batch 用戶端：  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

若要在單一命令中執行 batch 用戶端和容器：

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

用戶端將開始執行。 如果在上一次執行中已經轉譯音訊檔案，用戶端會自動略過該檔案。 如果發生暫時性錯誤，則會以自動重試傳送檔案，而且您可以區分想要讓用戶端重試的錯誤。 在轉譯錯誤時，用戶端會繼續轉譯，而且可以重試而不會遺失進度。  

## <a name="run-modes"></a>執行模式 

批次處理套件提供三種模式，使用 `--run-mode` 參數。

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT`[模式] 會將一批音訊檔案（從輸入目錄和選擇性檔案清單）可將至輸出檔案夾。

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="圖表，顯示 oneshot 模式的批次套件容器處理檔案。":::

1. 定義 batch 用戶端將在檔案中使用的語音容器端點 `config.yaml` 。 
2. 將轉譯的音訊檔案放在輸入目錄中。  
3. 叫用目錄上的容器，這將會開始處理檔案。 如果已經使用相同的輸出目錄（相同的檔案名和總和檢查碼）在上一次執行中轉譯音訊檔案，用戶端將會略過該檔案。 
4. 這些檔案會分派至步驟1中的容器端點。
5. 記錄和語音容器輸出會傳回到指定的輸出目錄。 

#### <a name="daemon"></a>[便](#tab/daemon)

> [!TIP]
> 如果同時將多個檔案新增至輸入目錄，您可以改為定期加入它們來改善效能。

`DAEMON`模式可將指定資料夾中的現有檔案，並在新增檔案時持續可將新的音訊檔案。          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="圖表，顯示以 daemon 模式表示的批次套件容器處理檔案。":::

1. 定義 batch 用戶端將在檔案中使用的語音容器端點 `config.yaml` 。 
2. 叫用輸入目錄上的容器。 Batch 用戶端會開始監視傳入檔案的目錄。 
3. 設定將連續音訊檔案傳遞至輸入目錄。 如果已經使用相同的輸出目錄（相同的檔案名和總和檢查碼）在上一次執行中轉譯音訊檔案，用戶端將會略過該檔案。 
4. 一旦偵測到 file write 或 POSIX 信號，就會觸發容器以回應。
5. 這些檔案會分派至步驟1中的容器端點。
6. 記錄和語音容器輸出會傳回到指定的輸出目錄。 

#### <a name="rest"></a>[REST](#tab/rest)

`REST`模式是一種 API 伺服器模式，可提供一組基本的 HTTP 端點來進行音訊檔案批次提交、狀態檢查，以及長時間輪詢。 也可讓您使用 python 模組擴充功能以程式設計方式取用，或匯入為子模組。

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="圖表，顯示以 daemon 模式表示的批次套件容器處理檔案。":::

1. 定義 batch 用戶端將在檔案中使用的語音容器端點 `config.yaml` 。 
2. 將 HTTP 要求要求傳送至其中一個 API 伺服器的端點。 
        
    |端點  |描述  |
    |---------|---------|
    |`/submit`     | 用於建立新批次要求的端點。        |
    |`/status`     | 用於檢查批次要求狀態的端點。 連接會保持開啟狀態，直到批次完成為止。       |
    |`/watch`     | 使用 HTTP 長輪詢直到批次完成為止的端點。        |

3. 系統會從輸入目錄上傳音訊檔案。 如果已經使用相同的輸出目錄（相同的檔案名和總和檢查碼）在上一次執行中轉譯音訊檔案，用戶端將會略過該檔案。 
4. 如果將要求傳送至 `/submit` 端點，檔案就會分派至步驟1中的容器端點。
5. 記錄和語音容器輸出會傳回到指定的輸出目錄。 

---

## <a name="logging"></a>記錄

> [!NOTE]
> 如果批次用戶端太大，可能會定期覆寫*執行 .log*檔案。

用戶端會在 docker 命令的引數所指定的目錄中建立*執行 .log*檔案 `-log_folder` `run` 。 預設會在 DEBUG 層級上捕捉記錄。 相同的記錄會傳送至 `stdout/stderr` ，並根據引數進行篩選 `-log_level` 。 只有在進行偵錯工具或需要傳送追蹤以支援時，才需要此記錄檔。 記錄資料夾也包含每個音訊檔案的語音 SDK 記錄檔。

所指定的輸出目錄 `-output_folder` 將包含檔案*上的run_summary.js*   ，每隔30秒或每次新轉譯完成時，就會定期重寫該檔案。 當批次繼續進行時，您可以使用此檔案來檢查進度。 當批次完成時，它也會包含每個檔案的最終執行統計資料和最終狀態。 當進程有乾淨結束時，就會完成批次。 

## <a name="next-steps"></a>後續步驟

* [如何安裝及執行容器](speech-container-howto.md)