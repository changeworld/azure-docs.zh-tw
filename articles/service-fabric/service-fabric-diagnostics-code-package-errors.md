---
title: 使用 Service Fabric 診斷一般程式碼套件錯誤
description: 瞭解如何使用 Azure Service Fabric 針對常見的程式碼套件錯誤進行疑難排解
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 983d45a7a240701fa6441d2d9edeeda16f1ed18b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256487"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>使用 Service Fabric 診斷一般程式碼套件錯誤

本文說明程式碼套件意外終止的意義。 它可讓您深入瞭解常見錯誤代碼的可能原因，以及疑難排解步驟。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>進程或容器何時意外終止？

當 Azure Service Fabric 收到啟動程式碼套件的要求時，它會根據應用程式和服務資訊清單中設定的選項，開始在本機系統上準備環境。 這些準備工作可能包括保留網路端點或資源、設定防火牆規則，或設定資源治理條件約束。 

正確設定環境之後，Service Fabric 會嘗試顯示程式碼套件。 如果 OS 或容器執行時間報告已成功啟用進程或容器，則會將此步驟視為成功。 如果啟用失敗，您應該會在 SFX 中看到類似下列的健全狀況訊息：

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

成功啟用程式碼套件之後，Service Fabric 開始監視其存留期。 此時，進程或容器可能會因為許多原因而隨時終止。 例如，它可能無法初始化 DLL，或 OS 可能已用盡桌面堆積空間。 如果您的程式碼套件已終止，您應該會在 SFX 中看到下列健全狀況訊息：

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

此健全狀況訊息中的結束代碼是處理常式或容器提供終止原因的唯一線索。 它可以由堆疊的任何層級產生。 例如，此結束代碼可能與 OS 錯誤或 .NET 問題相關，或可能已由您的程式碼引發。 請使用本文作為診斷結束代碼來源和可能解決方案的起點。 但請記住，這些是常見案例的一般解決方案，可能不適用於您所看到的錯誤。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何判斷 Service Fabric 是否會終止我的程式碼套件？

基於各種原因，Service Fabric 可能會負責終止程式碼套件。 例如，它可能會決定將程式碼套件放在另一個節點上，以供負載平衡之用。 如果您看到下表中的任何結束代碼，您可以確認 Service Fabric 終止您的程式碼套件。

>[!NOTE]
> 如果您的進程或容器終止的結束代碼不是下表中的代碼，Service Fabric 不負責終止它。

結束碼 | 描述
--------- | -----------
7147 | 指出 Service Fabric 藉由傳送 Ctrl + C 信號的方式，正常關閉進程或容器。
7148 | 表示 Service Fabric 終止進程或容器。 有時，此錯誤碼表示進程或容器在傳送 Ctrl + C 信號之後，未及時回應，而且必須終止。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常見的錯誤碼及其可能的修正

結束碼 | 十六進位值 | 簡短描述 | 根本原因 | 可能的修正
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此錯誤有時表示電腦已用盡桌面堆積空間。 如果您有許多屬於在節點上執行之應用程式的進程，則此問題特別可能發生。 | 如果您的程式不是為了回應 Ctrl + C 信號而建立，您可以在叢集資訊清單中啟用**EnableActivateNoWindow**設定。 啟用這項設定表示您的程式碼封裝會在沒有 GUI 視窗的情況下執行，而且不會收到 Ctrl + C 信號。 此動作也會減少每個進程耗用的桌面堆積空間數量。 如果您的程式碼套件需要接收 Ctrl + C 信號，您可以增加節點的桌面堆積大小。
3762504530 | 0xe0434352 | N/A | 此值代表 managed 程式碼中未處理之例外狀況的錯誤碼 (也就是 .NET) 。 | 這個結束代碼會指出您的應用程式引發了例外狀況，而此例外狀況會保持未處理，並終止進程。 在判斷觸發此錯誤的第一個步驟中，請將應用程式的記錄檔和傾印檔案加以調試。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[診斷其他常見案例](service-fabric-diagnostics-common-scenarios.md)。
* 閱讀[Azure 監視器總覽](../azure-monitor/overview.md)，以深入瞭解 Azure 監視器記錄及其提供的功能。
* 深入瞭解 Azure 監視器記錄[警示](../azure-monitor/platform/alerts-overview.md)，以協助偵測和診斷。
* 熟悉[記錄搜尋和查詢](../azure-monitor/log-query/log-query-overview.md)Azure 監視器記錄中提供的功能。
