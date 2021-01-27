---
title: 建立風險評量報告
description: 深入瞭解個別感應器偵測到的網路風險，或是由所有感應器偵測到的風險匯總查看。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 367974e214892d4dfefeb138ae5bfa516f49882a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811618"
---
# <a name="risk-assessment-reporting"></a>風險評量報告

## <a name="about-risk-assessment-reports"></a>關於風險評量報告

風險評估報告提供：

- 組織感應器偵測到之裝置的整體安全性分數。

- 個別感應器偵測到的每個網路裝置的安全性分數。

- 有弱點的裝置、需要改進和安全裝置的裝置數目的細目。

-  深入瞭解安全性和操作問題：

    - 設定問題

    - 依安全性等級排列優先順序的裝置弱點

    - 網路安全性問題

    - 網路操作問題

    - 連接至 ICS 網路

    - 網際網路連線

    - 產業惡意程式碼指標

    - 通訊協定問題

    - 攻擊媒介

### <a name="risk-mitigation"></a>風險降低

報表會提供建議來協助您改善安全性分數。 例如，安裝最新的安全性更新、將固件升級到最新版本，或追蹤警示。

## <a name="about-security-scores"></a>關於安全性分數

每份報告中都會產生整體網路安全性分數。 分數代表100% 安全性的百分比。 例如，分數為30% 時，表示您的網路有30% 的安全。

風險評量分數是以從封包檢查、行為模型引擎和 SCADA 特定狀態機器設計所學到的資訊為基礎。

**安全裝置** 是安全性分數高於90% 的裝置。

**需要改進的裝置**：安全性分數介於70% 到89% 之間的裝置。

**易受攻擊的裝置** 是安全性分數低於70% 的裝置。

## <a name="create-risk-assessment-reports"></a>建立風險評量報告

建立 PDF 風險評量報告。 報表名稱會自動產生為 risk-assessment-report-1.pdf。 您所建立的每個新報表都會更新這個數位。  隨即顯示建立的時間和日期。

### <a name="create-a-sensor-risk-assessment-report"></a>建立感應器風險評量報告

根據您登入的感應器所做的偵測，建立風險評定報告。

若要建立報表：

1. 登入感應器主控台。
1. 選取側邊功能表上的 [ **風險評** 量]。
1. 選取 [ **產生報告**]。 報表就會出現在 [封存的報表] 區段中。
1. 從 [封存的報表] 區段中選取報表以下載報表。

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="風險評估的觀點。":::

若要匯入公司標誌：

- 選取 [匯 **入標誌**]。

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>建立內部部署管理主控台風險評量報告

根據您的內部部署管理主控台所管理的任何感應器所做的偵測，建立風險評定報告。 

若要建立報表：

1. 選取側邊功能表上的 [ **風險評** 量]。

2. 從 [ **選取感應器** ] 下拉式清單中選取一個感應器。

3. 選取 [ **產生報告**]。

4. 從 [封存的 **報告**] 區段中選取 [**下載**]。

若要匯入公司標誌：

- 選取 [匯 **入標誌**]。

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="透過風險評估視圖匯入您的標誌。":::

## <a name="see-also"></a>另請參閱

[攻擊向量報告](how-to-create-attack-vector-reports.md)

