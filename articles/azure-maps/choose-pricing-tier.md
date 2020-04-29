---
title: 選擇正確的定價層 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure Maps 所提供的定價層。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335668"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖服務提供兩個定價層： S0 和 S1。 本文旨在協助您根據需求來選擇正確的定價層。 若要選擇正確的定價層，請詢問您自己的下列兩個問題。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？ 
S0 和 S1 定價層會處理不同的資料輸送量。 S0 定價層最多可處理**每秒 50 筆查詢**。 而 S1 層處理的**每秒查詢數不超過50個**。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？
如果核心地理空間 Api 符合您的服務需求，則 S0 定價層適合您。 如果您想要應用程式有更進階的功能，請考慮選擇 S1 定價層。 先進的功能包括：空中加上混合影像、取得路線範圍和批次地理編碼。 請參閱**定價層功能**資料表，以選取最適合您應用程式的定價層。

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地圖呈現                              | ✓                   | ✓       |
| 衛星圖像                       |                     | ✓        |
| 搜尋                                  | ✓                    | ✓        |
| Batch 搜尋                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批次路由                            |                    | ✓        |
| 矩陣路由                          |                     | ✓        |
| 路由範圍（等時線）                |                     | ✓        |
| 交通流量                                |✓                    |✓        |
| 時區                               |✓                    |✓        |
| 地理位置（預覽）                    |✓                   |✓        |
| 空間作業                        |                    |✓        |
| 地理柵欄                                |                    |✓        |
| Azure 地圖服務資料（預覽）                |                     | ✓        |
| 行動性（預覽）                       |                     | ✓        |
| 氣象（預覽）                        |✓                    |✓        |

請考慮下列其他重點：
* 您有何種類型的企業？
* 您的應用程式有多重要？

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

請參閱**定價層的目標客戶**表，以深入了解 S0 和 S1 定價層。 如需詳細資訊，請參閱 [Azure 地圖服務定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定價層  |     目標客戶                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定價層適用于所有生產階段中的應用程式：從概念證明開發和早期階段測試到應用程式生產和部署。 不過，這一層是專為小規模開發或具有低並行使用者或兩者的客戶所設計。 <p>|
| S1            |    <p>S1 定價層適用于具有大規模企業應用程式、任務關鍵性應用程式或大量並行使用者的客戶。 也適用於需要進階地理空間服務的客戶。</p>|

## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"] 
> [管理定價層](how-to-manage-pricing-tier.md)
