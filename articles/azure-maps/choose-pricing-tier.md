---
title: 為 Microsoft Azure 對應選擇正確的定價層
description: 瞭解 Azure 地圖服務定價層。 查看哪些層級提供哪些功能，以及查看選擇定價層的重要考慮。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3603a4f5d103987b25bd5f976b89f943f98565a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163980"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖服務提供兩個定價層： S0 和 S1。 本文旨在協助您根據需求來選擇正確的定價層。 若要選擇正確的定價層，請詢問自己下列兩個問題。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？

S0 和 S1 定價層會處理不同的資料輸送量。 S0 定價層最多可處理**每秒 50 筆查詢**。 而 S1 層會處理 **每秒超過50個查詢**。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？

如果核心地理空間 Api 符合您的服務需求，請選擇 S0 定價層。 如果您想要讓應用程式擁有更先進的功能，請考慮選擇 S1 定價層。 先進的功能包括：空出加上混合影像、取得路線範圍和批次地理編碼。 若要選取最適合您應用程式的定價層，請參閱下方的 **定價層功能** 表格：

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地圖轉譯                              | ✓                   | ✓       |
| 衛星影像                       |                     | ✓        |
| 搜尋                                  | ✓                    | ✓        |
| 批次搜尋                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批次路由                            |                    | ✓        |
| 矩陣路由                          |                     | ✓        |
| 路由範圍 (等時線)                 |                     | ✓        |
| 交通流量                                |✓                    |✓        |
| 時區                               |✓                    |✓        |
|  (預覽) 的地理位置                    |✓                   |✓        |
| 空間作業                        |                    |✓        |
| 地理柵欄                                |                    |✓        |
| Azure 地圖服務資料 (預覽)                 |                     | ✓        |
| 行動 (預覽)                        |                     | ✓        |
| 天氣 (預覽)                         |✓                    |✓        |
|  Creator (預覽)                          |                   |✓        |

請考慮下列其他幾點：

* 您有何種企業？
* 您的應用程式有多重要？

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

請參閱**定價層的目標客戶**表，以深入了解 S0 和 S1 定價層。 如需詳細資訊，請參閱 [Azure 地圖服務定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定價層  |     目標客戶                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    S0 定價層適用于所有生產階段中的應用程式：從概念證明開發和早期階段測試到應用程式生產和部署。 不過，此層級是針對小規模開發，或是具有低並行使用者或兩者的客戶而設計。 
| S1            |    S1 定價層適用于具有大型企業應用程式、任務關鍵性應用程式或大量並行使用者的客戶。 也適用於需要進階地理空間服務的客戶。

## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"]
> [管理定價層](how-to-manage-pricing-tier.md)
