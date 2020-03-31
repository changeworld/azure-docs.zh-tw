---
title: 使用 Habitat 將應用程式部署至 Azure
description: 了解如何以一致的方式將應用程式部署至 Azure 虛擬機器和容器
keywords: azure, chef, devops, 虛擬機器, 概觀, 自動化, habitat
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158052"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>使用 Habitat 將應用程式部署至 Azure
[Habitat](https://www.habitat.sh/) 是應用程式封裝與執行階段系統，可將應用程式和其自動化結合為部署單位。 這會為應用程式創造強大的便利性，可讓應用程式部署到容器、虛擬機器、裸機或 PaaS，而不需要重寫或重新封裝。

本文將說明使用 Habitat 的主要優點。

## <a name="modernize-and-move-legacy-applications"></a>更新及搬移舊版應用程式
藉由使用 Habitat 重新封裝舊版應用程式，即可從較舊的作業系統和中介軟體中釋放這些舊版應用程式。 產生的成品具可攜性，並可輕鬆地將平台更換到較新的基礎結構上，例如雲端中執行的虛擬機器或容器。

## <a name="accelerate-container-adoption"></a>加速容器採用
Habitat 能夠精確地表示執行階段相依性，藉此解決複雜微服務導向應用程式的持續部署。 超越個別元件的簡單藍/綠部署，且無須產生複雜的協調流程即可建構複雜的部署行為。

## <a name="run-any-application-anywhere"></a>在任何位置執行任何應用程式
有了 Habitat，應用程式就可以不經修改，在任何執行階段環境下執行。 這包括從裸機和虛擬機器到容器 (例如 Docker)、叢集管理系統 (例如 Mesosphere 或 Kubernetes) 及 PaaS 系統 (例如 Pivotal Cloud Foundry) 的所有項目。

## <a name="integrate-into-the-chef-devops-workflow"></a>整合至 Chef DevOps 工作流程
Habitat 專案 Chef 軟體的其中一開放原始碼專案，具有強大的社群支援。 Habitat 會利用 Chef 對基礎結構自動化的深厚經驗，為應用程式帶來所未有的自動化功能。 Chef 可提供對於 Habitat 的商務支援，並且建置 Habitat 與 Chef Automate 之間的無縫整合，讓應用程式發行週期 (從開發到部署) 完全自動化。

## <a name="next-steps"></a>後續步驟

* [試用 Habitat](https://www.habitat.sh/learn/)
