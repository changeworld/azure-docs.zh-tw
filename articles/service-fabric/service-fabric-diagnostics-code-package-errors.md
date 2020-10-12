---
title: 使用 Service Fabric 診斷常見的程式碼套件錯誤
description: 瞭解如何使用 Azure Service Fabric 針對常見的程式碼套件錯誤進行疑難排解
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 983d45a7a240701fa6441d2d9edeeda16f1ed18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256487"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>使用 Service Fabric 診斷常見的程式碼套件錯誤

本文說明程式碼套件非預期地終止的意義。 它可讓您深入瞭解常見錯誤碼的可能原因，以及疑難排解步驟。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>進程或容器何時意外終止？

當 Azure Service Fabric 收到啟動程式碼套件的要求時，它會根據應用程式和服務資訊清單中所設定的選項，開始在本機系統上準備環境。 這些準備工作可能包括保留網路端點或資源、設定防火牆規則，或設定資源治理條件約束。 

適當設定環境之後，Service Fabric 會嘗試顯示程式碼套件。 如果 OS 或容器執行時間報告已順利啟用進程或容器，則會將此步驟視為成功。 如果啟用失敗，您應該會在 SFX 中看到類似以下的健康情況訊息：

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

成功啟用程式碼封裝之後，Service Fabric 開始監視其存留期。 此時，進程或容器隨時可能會因為許多原因而終止。 例如，它可能無法初始化 DLL，或 OS 可能已用盡桌面堆積空間。 如果您的程式碼套件已終止，您應該會在 SFX 中看到下列健康情況訊息：

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

此健全狀況訊息中的結束代碼是進程或容器提供終止原因的唯一線索。 它可能是由堆疊的任何層級所產生。 例如，此結束碼可能與作業系統錯誤或 .NET 問題有關，或可能是您的程式碼所引發。 您可以使用這篇文章做為診斷終止程式碼來源和可能解決方案的起點。 但是請記住，這些是常見案例的一般解決方案，可能不適用於您所看到的錯誤。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何判斷 Service Fabric 是否終止我的程式碼套件？

基於各種原因，Service Fabric 可能會負責終止您的程式碼套件。 例如，它可能會決定將程式碼套件放在另一個節點上，以供負載平衡之用。 如果您在下表中看到任何結束代碼，您可以確認 Service Fabric 終止您的程式碼套件。

>[!NOTE]
> 如果您的進程或容器以下表中的程式碼以外的結束代碼終止，Service Fabric 不會負責終止它。

結束碼 | 說明
--------- | -----------
7147 | 指出 Service Fabric 藉由將 Ctrl + C 信號傳送給它，以正常方式關閉進程或容器。
7148 | 指出 Service Fabric 終止進程或容器。 有時候，此錯誤碼表示進程或容器在傳送 Ctrl + C 信號之後不會及時回應，而且必須終止。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常見的錯誤碼及其可能的修正

結束碼 | 十六進位值 | 簡短描述 | 根本原因 | 可能的修正
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此錯誤有時候表示電腦已用盡桌面堆積空間。 如果您的應用程式屬於節點上執行的應用程式，這種情況特別可能發生。 | 如果您的程式不是為了回應 Ctrl + C 信號而建立的，您可以啟用叢集資訊清單中的 **EnableActivateNoWindow** 設定。 啟用此設定表示您的程式碼套件將在沒有 GUI 視窗的情況下執行，且不會收到 Ctrl + C 信號。 此動作也可減少每個進程耗用的桌面堆積空間量。 如果您的程式碼套件需要接收 Ctrl + C 信號，您可以增加節點桌面堆積的大小。
3762504530 | 0xe0434352 | N/A | 此值代表 managed 程式碼中未處理之例外狀況的錯誤碼 (也就是 .NET) 。 | 此結束代碼表示您的應用程式引發例外狀況，此例外狀況會保持未處理狀態，並會終止進程。 在判斷觸發此錯誤的第一個步驟中，請將應用程式的記錄和傾印檔案進行偵錯工具。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解如何 [診斷其他常見案例](service-fabric-diagnostics-common-scenarios.md)。
* 閱讀 [Azure 監視器總覽](../azure-monitor/overview.md)，深入瞭解 Azure 監視器記錄以及它們所提供的資訊。
* 深入瞭解可協助偵測及診斷的 Azure 監視器記錄 [警示](../azure-monitor/platform/alerts-overview.md) 。
* 熟悉 [記錄搜尋和查詢](../azure-monitor/log-query/log-query-overview.md) Azure 監視器記錄檔中所提供的功能。
