---
title: 使用 Azure Active Directory 建立具恢復功能的身分識別與存取管理
description: 適用于架構設計師、IT 系統管理員和開發人員的指南，可建立其身分識別系統中斷的復原能力。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919416"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>使用 Azure Active Directory 建立身分識別與存取管理的恢復功能

身分識別和存取管理 (IAM) 是程式、原則和技術的架構，可協助您管理身分識別和其存取權。 它包含許多元件，支援使用者的驗證和授權，以及您系統中的其他帳戶。

IAM 復原能力是忍受系統元件中斷的能力，並以最少的影響對您的業務、使用者、客戶和營運進行復原。 減少相依性、複雜度和單點失敗，同時確保完整的錯誤處理會提高您的復原能力。

中斷可能來自 IAM 系統的任何元件。 若要建立具恢復功能的 IAM 系統，將會發生中斷並進行規劃。 

在規劃 IAM 解決方案的復原能力時，請考慮下列元素： 

* 依賴 IAM 系統的應用程式。

* 驗證呼叫所使用的公用基礎結構，包括電信公司、網際網路服務提供者和公開金鑰提供者。

* 您的雲端和內部部署身分識別提供者。

* 依賴您 IAM 的其他服務，以及連接這些服務的 Api。

* 您系統中的任何其他內部部署元件。

無論是什麼來源、辨識和規劃應變措施都很重要。 但是，新增其他身分識別系統及其產生的相依性和複雜性，可能會降低您的復原能力，而不是增加。

若要在系統中建立更多的復原能力，請參閱下列文章：

* [在您的 IAM 基礎結構中建立恢復功能](resilience-in-infrastructure.md)

* [在您的應用程式中建立 IAM 復原](resilience-app-development-overview.md)

* [在您的 CIAM 系統中打造復原能力](resilience-b2c.md)
