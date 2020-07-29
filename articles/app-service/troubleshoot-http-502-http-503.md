---
title: 修正 HTTP 502 和 HTTP 503 錯誤
description: 針對 Azure App Service 所裝載應用程式發生的「502 不正確的閘道」和「503 服務無法使用」錯誤，進行疑難排解。
tags: top-support-issue
keywords: 502 不正確的閘道、503 服務無法使用、錯誤 503、錯誤 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9cb672f2ada88b1fc67bcd8f022c5faeeac6dddf
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170795"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>針對 Azure App Service 中「502 不正確的閘道」和「503 服務無法使用」的 HTTP 錯誤，進行疑難排解
在裝載於 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 的應用程式中，「502 不正確的閘道」和「503 服務無法使用」是常見的錯誤。 本文可協助您對這些錯誤進行疑難排解。

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

## <a name="symptom"></a>徵狀
當您瀏覽至應用程式時，傳回 HTTP「502 不正確的閘道」錯誤或 HTTP「503 服務無法使用」錯誤。

## <a name="cause"></a>原因
此問題通常是因為應用程式層級問題所造成，例如：

* 要求耗費過長的時間
* 應用程式的記憶體/CPU 使用率過高
* 應用程式因例外狀況導致損毀

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>解決「502 錯誤閘道」和「503 服務無法使用」錯誤的疑難排解步驟
疑難排解可以分成三種不同的工作，依序為：

1. [觀察和監視應用程式行為](#observe)
2. [收集資料](#collect)
3. [減輕問題](#mitigate)

[App Service](overview.md) 在每個步驟均提供您各種選項。

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1.觀察和監視應用程式行為
#### <a name="track-service-health"></a>追蹤服務健全狀況
每次發生服務中斷或效能降低時，Microsoft Azure 就會發出公告。 您可以在 [Azure 入口網站](https://portal.azure.com/)上追蹤服務健全狀況。 如需詳細資訊，請參閱[追蹤服務健全狀況](../monitoring-and-diagnostics/insights-service-health.md)。

#### <a name="monitor-your-app"></a>監視應用程式
此選項可讓您了解應用程式是否有任何問題。 在應用程式刀鋒視窗中，按一下 [要求和錯誤] 圖格。 [ **度量** ] 刀鋒視窗將顯示所有可以加入的計量。

某些您可能想用以監視應用程式的計量為

* 平均記憶體工作集
* 平均回應時間
* CPU 時間
* 記憶體工作集
* Requests

![監視應用程式，以解決 502 不正確的閘道和 503 服務無法使用的 HTTP 錯誤](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

如需詳細資訊，請參閱

* [監視 Azure App Service 中的應用程式](web-sites-monitor.md)
* [接收警示通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2.收集資料
#### <a name="use-the-diagnostics-tool"></a>使用診斷工具
App Service 提供智慧型和互動式的體驗，可協助您對應用程式進行疑難排解，而且不需設定。 您的應用程式發生問題時，診斷工具將找出問題，並以正確的資訊引導您更輕鬆且快速地對問題進行疑難排解並解決。

若要存取 App Service 診斷，請在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 App Service 應用程式或 App Service Environment。 在左方導覽列中，按一下 [診斷並解決問題]。

#### <a name="use-the-kudu-debug-console"></a>使用 Kudu 偵錯主控台
App Service 隨附可用於偵錯、探索、上傳檔案的偵錯主控台，以及可取得您環境相關資訊的 JSON 端點。 這稱為應用程式的 *Kudu 主控台*或 *SCM 儀表板*。

您可以前往連結 **https://&lt;Your app name>.scm.azurewebsites.net/** 存取此儀表板。

Kudu 提供的部分項目為：

* 您的應用程式的環境設定
* 記錄檔串流
* 診斷傾印
* 您可以執行 Powershell Cmdlet 和基本 DOS 命令的偵錯主控台。

Kudu 的另一項實用功能是，如果應用程式擲回第一次例外狀況，您可以使用 Kudu 和 SysInternals 工具 Procdump 建立記憶體傾印。 這些記憶體傾印是處理序的快照集，通常可以協助您疑難排解應用程式較複雜的問題。

如需有關 Kudu 可用功能的詳細資訊，請參閱 [您應該知道的 Azure 網站線上工具](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)。

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3.減輕問題
#### <a name="scale-the-app"></a>調整應用程式
在 Azure App Service 中，為提高效能和輸送量，您可以調整所執行之應用程式的大小。 相應增加應用程式規模牽涉到兩個相關動作：將 App Service 方案變更為較高的定價層，以及在改為較高的定價層後進行某些設定。

如需有關調整的詳細資訊，請參閱[在 Azure App Service 中調整應用程式規模](manage-scale-up.md)。

此外，您可以選擇在多個執行個體上執行應用程式。 這不僅提供您更強大的處理能力，同時也提供您一定程度的容錯量。 若處理序在某個執行個體上中斷，其他執行個體仍將繼續處理要求。

您可以將調整設定為手動或自動。

#### <a name="use-autoheal"></a>使用 AutoHeal
AutoHeal 會根據您選擇的設定 (例如組態變更、要求、以記憶體為基礎的限制或執行要求所需的時間)，回收應用程式的背景工作角色處理序。 在大部分情況下，回收處理序是從問題中復原的最快方式。 您可隨時從 Azure 入口網站中直接重新啟動應用程式，但 AutoHeal 會自動為您完成此作業。 您只需要在應用程式的根目錄 web.config 中加入某些觸發程序。 請注意，即使您的應用程式並非 .NET，這些設定的運作方式仍相同。

如需詳細資訊，請參閱 [自動修復 Azure 網站](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)。

#### <a name="restart-the-app"></a>重新啟動應用程式
若要從一次性問題中復原，這通常是最簡單的方式。 在 [Azure 入口網站](https://portal.azure.com/)上的應用程式刀鋒視窗中，有提供停止或重新啟動應用程式的選項。

 ![重新啟動應用程式，以解決 502 不正確的閘道和 503 服務無法使用的 HTTP 錯誤](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

您也可以使用 Azure Powershell 管理應用程式。 如需詳細資訊，請參閱 [將 Azure PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager.md)。

