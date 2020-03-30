---
title: 選擇正確的定價層 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 地圖提供的定價層。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335668"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure 地圖服務中選擇正確的定價層

Azure 地圖提供兩個定價層，S0 和 S1。 本文旨在協助您根據需求來選擇正確的定價層。 要選擇正確的定價層，請問自己以下兩個問題。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>我打算支援多少並行使用者人數？ 
S0 和 S1 定價層會處理不同的資料輸送量。 S0 定價層最多可處理**每秒 50 筆查詢**。 而 S1 層**每秒處理 50 多個查詢**。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>我打算使用哪些地理空間功能？
如果核心地理空間 API 滿足您的服務要求，S0 定價層適合您的選擇。 如果您想要應用程式有更進階的功能，請考慮選擇 S1 定價層。 高級功能包括：航空和混合影像、獲取路由範圍和批次處理地理編碼。 查看**定價層功能**表以選擇最適合您的應用程式的定價層。

### <a name="pricing-tier-capabilities"></a>定價層功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地圖渲染                              | ✓                   | ✓       |
| 衛星圖像                       |                     | ✓        |
| 搜尋                                  | ✓                    | ✓        |
| 批次處理搜索                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批次處理路由                            |                    | ✓        |
| 矩陣路由                          |                     | ✓        |
| 路線範圍（伊索奇羅內斯）                |                     | ✓        |
| 交通流量                                |✓                    |✓        |
| 時區                               |✓                    |✓        |
| 地理位置（預覽）                    |✓                   |✓        |
| 空間作業                        |                    |✓        |
| 地理柵欄                                |                    |✓        |
| Azure 地圖資料（預覽）                |                     | ✓        |
| 移動性（預覽版）                       |                     | ✓        |
| 天氣（預覽）                        |✓                    |✓        |

請考慮以下其他要點：
* 您擁有哪種類型的企業？
* 您的應用程式有多重要？

### <a name="pricing-tier-targeted-customers"></a>定價層的目標客戶

請參閱**定價層的目標客戶**表，以深入了解 S0 和 S1 定價層。 如需詳細資訊，請參閱 [Azure 地圖服務定價](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定價層  |     目標客戶                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定價層適用于生產所有階段的應用程式：從概念驗證開發和早期測試到應用程式生產和部署。 但是，此層專為小規模開發或併發使用者數低的客戶或兩者兼而有之而設計。 <p>|
| S1            |    <p>S1 定價層適用于具有大型企業應用程式、任務關鍵型應用程式或大量併發使用者的客戶。 也適用於需要進階地理空間服務的客戶。</p>|

## <a name="next-steps"></a>後續步驟

深入了解如何檢視及變更定價層：

> [!div class="nextstepaction"] 
> [管理定價層](how-to-manage-pricing-tier.md)
