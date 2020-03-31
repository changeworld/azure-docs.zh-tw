---
title: 使用 Service Fabric 診斷常見代碼包錯誤
description: 瞭解如何使用 Azure 服務交換矩陣解決常見代碼包錯誤
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463106"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>使用 Service Fabric 診斷常見代碼包錯誤

本文介紹了代碼包意外終止的含義。 它提供了對常見錯誤代碼的可能原因以及故障排除步驟的見解。

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>進程或容器何時意外終止？

當 Azure Service Fabric 收到啟動代碼包的請求時，它會開始根據應用和服務清單中設置的選項在本地系統上準備環境。 這些準備工作可能包括保留網路終結點或資源、配置防火牆規則或設置資源治理約束。 

正確配置環境後，Service Fabric 會嘗試啟動代碼包。 如果作業系統或容器運行時報告進程或容器已成功啟動，則此步驟被視為成功。 如果啟動不成功，您應該在 SFX 中看到類似于以下內容的運行狀況消息：

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

成功啟用代碼包後，Service Fabric 開始監視其存留期。 此時，進程或容器可以出於多種原因隨時終止。 例如，它可能未能初始化 DLL，或者作業系統可能耗盡了桌面堆空間。 如果代碼包終止，應在 SFX 中看到以下運行狀況消息：

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

此運行狀況消息中的結束代碼是進程或容器提供有關其終止原因的唯一線索。 它可以由堆疊的任何級別生成。 例如，此結束代碼可能與作業系統錯誤或 .NET 問題相關，或者代碼可能已引發。 使用本文作為診斷終止結束代碼和可能解決方案來源的起點。 但請記住，這些是常見方案的一般解決方案，可能不適用於您看到的錯誤。

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>如何判斷 Service Fabric 是否終止了代碼包？

由於各種原因，Service Fabric 可能負責終止代碼包。 例如，它可能會決定將代碼包放在另一個節點上以進行負載平衡。 如果看到下表中的任何結束代碼，則可以驗證 Service Fabric 是否終止了代碼包。

>[!NOTE]
> 如果進程或容器終止了下表中代碼以外的結束代碼，則 Service Fabric 不負責終止它。

結束代碼 | 描述
--------- | -----------
7147 | 指示 Service Fabric 通過向進程或容器發送 Ctrl_C 信號，從而正常關閉進程或容器。
7148 | 指示 Service 結構終止了進程或容器。 有時，此錯誤代碼指示進程或容器在發送 Ctrl_C 信號後沒有及時回應，並且必須終止。


## <a name="other-common-error-codes-and-their-potential-fixes"></a>其他常見錯誤代碼及其潛在修復

結束代碼 | 十六進位值 | 簡短描述 | 根本原因 | 潛在修復
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 此錯誤有時表示電腦已耗盡桌面堆空間。 如果有許多進程屬於在節點上運行的應用程式，則此原因尤其可能。 | 如果程式不是為回應 Ctrl_C 信號而構建的，則可以在群集清單中**啟用啟用ActivateNoWindow**設置。 啟用此設置意味著代碼包將運行而不帶 GUI 視窗，並且不會接收 Ctrl_C 信號。 此操作還減少了每個進程消耗的桌面堆空間量。 如果代碼包需要接收 Ctrl_C 信號，則可以增加節點桌面堆的大小。
3762504530 | 0xe0434352 | N/A | 此值表示託管代碼（即 .NET）中未處理異常的錯誤代碼。 | 此結束代碼指示應用程式引發一個未處理的異常，該異常終止了進程。 作為確定觸發此錯誤的第一步，請調試應用程式的日誌和轉儲檔。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[診斷其他常見方案的更多詳細資訊](service-fabric-diagnostics-common-scenarios.md)。
* 通過閱讀[Azure 監視器概述](../operations-management-suite/operations-management-suite-overview.md)，詳細瞭解 Azure 監視器日誌及其提供的內容。
* 詳細瞭解 Azure 監視器日誌[，用於提醒](../log-analytics/log-analytics-alerts.md)用於檢測和診斷。
* 熟悉 Azure 監視器日誌中提供的[日誌搜索和查詢](../log-analytics/log-analytics-log-searches.md)功能。
