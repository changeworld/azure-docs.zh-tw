---
title: Application Proxy 應用程式載入時間過長 | Microsoft Docs
description: 為 Azure AD Application Proxy 的頁面載入效能問題疑難排解
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1dc1b14a917786e124775a4505c53f63238a383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203859"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Application Proxy 應用程式載入時間過長

本文協助您了解為何 Azure AD 應用程式 Proxy 應用程式的載入時間可能很長。 以及說明如何才能解決此問題。

## <a name="overview"></a>概觀
雖然您的應用程式可以運作，但可能會遭遇長時間延遲。 您可以對網路拓撲進行調整，以提升速度。 若要評估不同的拓樸，請參閱[網路考量文件](application-proxy-network-topology.md)。

除了網路拓撲，目前沒有進一步的效能微調建議。 隨著應用程式 Proxy 服務擴展，可能會來到實體上更接近的資料中心。 更接近的鄰近位置可能對延遲有所幫助。 如需 Azure 資料中心清單，請參閱[延遲測試頁面](http://www.azurespeed.com/Azure/Latency)。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>對 Application Proxy 資料中心位置的意見反應 
可能會有 Azure 資料中心尚未包含 Application Proxy，但能為您顯著改善延遲的問題。 請將資料中心位置傳送至 aadapfeedback@microsoft.com。 Microsoft 會將您的意見反應用於擴展計劃。

Microsoft 正努力處理其他功能，以改善延遲。 當這些改進功能可供使用時，文件將隨之更新。

## <a name="next-steps"></a>接下來的步驟
[使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
