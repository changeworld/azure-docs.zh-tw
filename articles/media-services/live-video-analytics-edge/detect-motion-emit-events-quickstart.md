---
title: 偵測動作並發出事件 - Azure
description: 本快速入門說明如何透過程式設計方式呼叫直接方法，在 IoT Edge 上使用 Live Video Analytics 來偵測動作並發出事件。
ms.topic: quickstart
ms.date: 08/10/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 4d8b91529eaf5a9ee93cff28153b28b26fb7e685
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89566891"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>快速入門：偵測動作並發出事件

本快速入門會逐步引導您完成在 IoT Edge 上開始使用 Live Video Analytics 的步驟。 這裡使用 Azure VM 作為 IoT Edge 裝置，以及模擬的即時影片串流。 完成設定步驟之後，您將能夠透過媒體圖表執行模擬的即時影片串流，以偵測並報告該串流中的任何動作。 下圖顯示該媒體圖表的圖表表示。

![以動作偵測為基礎的 Live Video Analytics](./media/analyze-live-video/motion-detection.svg) 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>必要條件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="set-up-azure-resources"></a>設定 Azure 資源

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/csharp/setup-azure-resources.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/python/setup-azure-resources.md)]
::: zone-end

## <a name="set-up-your-development-environment"></a>設定開發環境

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/csharp/setup-development-environment.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/python/setup-development-environment.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>檢查範例檔案

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>產生和部署部署資訊清單

::: zone pivot="programming-language-csharp"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="prepare-to-monitor-events"></a>準備監視事件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/csharp/prepare-monitor-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/python/prepare-monitor-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>執行範例程式

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>解譯結果

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要嘗試其他快速入門，則請保留所建立的資源。 否則，請在 Azure 入口網站中移至您的資源群組、選取您執行本快速入門時所用的資源群組，然後刪除所有資源。

## <a name="next-steps"></a>後續步驟

執行其他快速入門，例如，偵測即時影片摘要中的物件。        
