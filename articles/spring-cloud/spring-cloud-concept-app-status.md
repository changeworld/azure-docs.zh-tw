---
title: 了解 Azure Spring Cloud 中的應用程式狀態
description: 瞭解 Azure 春季雲端中的應用程式狀態類別
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ec87c5523d6f608363ca615037f20365705609df
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892851"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>了解 Azure Spring Cloud 中的應用程式狀態

本文**適用于：** ✔️ JAVA ✔️ C#

Azure 春季雲端 UI 可提供有關執行中應用程式狀態的資訊。  訂用帳戶中的每個資源群組都有 **一個應用程式選項，** 可顯示應用程式類型的一般狀態。  針對每個應用程式類型，會顯示 **應用程式實例**。

## <a name="apps-status"></a>應用程式狀態
若要查看應用程式類型的一般狀態，請在資源群組的左側導覽窗格中選取 [ **應用程式** ]。 結果會顯示已部署應用程式的狀態：

* 布建**狀態**會顯示部署的布建狀態
* 執行中的**實例**會顯示有多少個應用程式實例正在執行，以及所需的應用程式實例數目。 如果應用程式應該停止，此資料行會顯示 [ *已停止*]。
* **已註冊的實例** 會顯示有多少應用程式實例已註冊至 eureka/需要多少應用程式實例。 如果應用程式應該停止，此資料行會顯示 [ *已停止*]。


 ![應用程式狀態](media/spring-cloud-concept-app-status/apps-ui-status.png)

**部署狀態會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 執行中 | 部署應該正在執行。 |
| 已停止 | 應停止部署。 |

**布建狀態只能從 CLI 存取。 它會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 建立中 | 資源正在建立。 |
| 更新 | 資源正在更新。 |
| 成功 | 已成功提供資源並部署二進位檔。 |
| Failed | 無法達成 *成功* 的目標。 |
| 刪除 | 正在刪除資源。 這會導致作業無法運作，且此狀態無法使用資源。 |

## <a name="app-instances-status"></a>應用程式實例狀態

若要查看已部署應用程式之特定實例的狀態，請按一下**應用**程式 UI 中的應用程式**名稱**。 結果將會顯示：
* **狀態**：實例是否正在執行或其狀態
* **DiscoveryStatus**： Eureka server 中應用程式實例的註冊狀態

 ![應用程式實例狀態](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**實例狀態會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| 啟動中 | 二進位檔已成功部署到指定的實例。 啟動 jar 檔案的實例可能會失敗，因為 jar 無法正常執行。 |
| 執行中 | 實例可運作。 |
| Failed | 應用程式實例在數次重試之後無法啟動使用者的二進位檔。 |
| 終止 | 應用程式實例正在關閉。 |

**實例的探索狀態會回報為下列其中一個值：**

| 列舉 | 定義 |
|:--:|:----------------:|
| UP | 應用程式實例已註冊至 eureka，並已準備好接收流量 |
| OUT_OF_SERVICE | 應用程式實例會註冊至 Eureka，且能夠接收流量。 但會刻意關閉流量。 |
| DOWN | 應用程式實例未註冊至 Eureka 或已註冊但無法接收流量。 |


## <a name="see-also"></a>另請參閱
* [準備春季或 Steeltoe 應用程式以在 Azure 春季雲端中進行部署](spring-cloud-tutorial-prepare-app-deployment.md)