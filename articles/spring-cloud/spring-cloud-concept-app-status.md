---
title: 了解 Azure Spring Cloud 中的應用程式狀態
description: 瞭解 Azure 春季雲端中的應用程式狀態類別
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82569000"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>了解 Azure Spring Cloud 中的應用程式狀態

Azure 春季雲端 UI 會提供有關執行中應用程式狀態的資訊。  訂用帳戶中的每個資源群組都有**一個應用程式選項，** 可顯示應用程式類型的一般狀態。  針對每個應用程式類型，會顯示**應用程式實例**。

## <a name="apps-status"></a>應用程式狀態
若要查看應用程式類型的一般狀態，請在資源群組的左側流覽窗格中選取 [**應用程式**]。 結果會顯示已部署應用程式的狀態：

* [布建**狀態**] 會顯示部署的布建狀態
* 執行中的**實例**會顯示有多少應用程式實例正在執行中，以及所需的應用程式實例數目。 如果應用程式應該停止，此欄會顯示 [*已停止*]。
* **已註冊的實例**會顯示有多少應用程式實例註冊至 eureka/需要多少應用程式實例。 如果應用程式應該停止，此欄會顯示 [*已停止*]。


 ![應用程式狀態](media/spring-cloud-concept-app-status/apps-ui-status.png)

**部署狀態會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 執行中 | 部署應該正在執行。 |
| 已停止 | 部署應該是 [已停止]。 |

**布建狀態只能從 CLI 存取。 系統會將它報告為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 建立中 | 正在建立資源。 |
| 更新 | 正在更新資源。 |
| 成功 | 已成功提供資源並部署二進位檔。 |
| 失敗 | 無法達到*成功*的目標。 |
| 刪除中 | 正在刪除資源。 這會防止作業，且此狀態無法使用資源。 |

## <a name="app-instances-status"></a>應用程式實例狀態

若要查看已部署之應用程式的特定實例狀態，請在**應用**程式 UI 中按一下應用程式的**名稱**。 結果會顯示：
* **狀態**：實例是否正在執行或其狀態
* **DiscoveryStatus**： Eureka 伺服器中應用程式實例的已註冊狀態

 ![應用程式實例狀態](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**實例狀態會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 啟動中 | 二進位檔已成功部署至指定的實例。 啟動 jar 檔案的實例可能會失敗，因為 jar 無法正常執行。 |
| 執行中 | 實例可以運作。 |
| 失敗 | 應用程式實例在數次重試後無法啟動使用者的二進位檔。 |
| 終止 | 正在關閉應用程式實例。 |

**實例的探索狀態會報告為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| UP | 應用程式實例已註冊到 eureka，並準備好接收流量 |
| OUT_OF_SERVICE | 應用程式實例會註冊為 Eureka，並能夠接收流量。 但是會刻意關閉流量。 |
| DOWN | 應用程式實例未註冊至 Eureka，或已登錄但無法接收流量。 |


## <a name="see-also"></a>另請參閱
* [準備 Java Spring 應用程式以部署到 Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md)