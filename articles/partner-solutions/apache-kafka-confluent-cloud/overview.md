---
title: Apache Kafka on Confluent Cloud 總覽-Azure 合作夥伴解決方案
description: 瞭解如何在 Azure Marketplace 中使用 Confluent Cloud 上的 Apache Kafka。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: b4aa00b7a2cc7ca6bc968be70957c127a8e00738
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253410"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>什麼是 Apache Kafka for Confluent Cloud？

Apache Kafka for Confluent Cloud 是一種 Azure Marketplace 供應專案，提供 Apache Kafka 即服務。 它是完全受控的，因此您可以專注于建立應用程式，而不是管理叢集。

為了降低跨平臺管理的負擔，Microsoft 與 Confluent Cloud 合作，以建立從 Azure 到 Confluent Cloud 的整合布建層。 它提供在 Azure 上使用 Confluent Cloud 的整合體驗。 您可以輕鬆地將 Confluent Cloud 與 Azure 應用程式整合並加以管理。

先前，您必須在 Marketplace 中購買 Confluent 雲端供應專案，並在 Confluent Cloud 中另外設定帳戶。 若要管理設定和資源，您必須在 Azure 與 Confluent Cloud 的入口網站之間流覽。

現在，您可以透過名為 **Confluent** 的資源提供者來布建 Confluent 雲端資源。 您可以透過 [Azure 入口網站](https://portal.azure.com/)、 [Azure CLI](/cli/azure/)或 [Azure Sdk](/azure/#languages-and-tools)來建立及管理 Confluent 雲端組織資源。 Confluent Cloud 擁有及執行軟體即服務 (SaaS) 應用程式，包括環境、叢集、主題、API 金鑰和受控連接器。

## <a name="capabilities"></a>功能

Confluent Cloud 與 Azure 之間的深層整合可提供下列功能：

- 使用完全受控的基礎結構，從 Azure 入口網站布建新的 Confluent Cloud 組織資源。
- 使用 Azure Active Directory (Azure AD) ，簡化單一登入 (從 Azure 到 Confluent Cloud 的 SSO) 。 Confluent Cloud 入口網站不需要個別的驗證。
- 透過 Azure 訂用帳戶發票來取得 Confluent 雲端耗用量的統一帳單。
- 從 Azure 入口網站管理 Confluent 雲端資源，並在 [ **所有資源** ] 頁面中使用您的其他 Azure 資源進行追蹤。

## <a name="confluent-organization"></a>Confluent 組織

Confluent 組織是一種資源，可提供 Azure 與 Confluent 雲端資源之間的對應。 它是其他 Confluent 雲端資源的父資源。

每個 Azure 訂用帳戶可以包含多個 Confluent 方案。 每個 Confluent 方案都會對應到 Confluent 入口網站中的使用者帳戶和組織。 在每個 Confluent 組織內，您可以建立多個環境、叢集、主題和連接器。

當您在 Azure 中布建 Confluent 雲端資源時，您會取得 Confluent 的組織識別碼、預設環境及使用者帳戶。 如需詳細資訊，請參閱 [快速入門：開始使用 Azure 上的 Confluent Cloud](create.md)。

針對計費，在 Marketplace 中購買的每個 Confluent 雲端供應專案都會對應到唯一的 Confluent 組織。

## <a name="single-sign-on"></a>單一登入

當您登入 Azure 入口網站時，也會使用您的認證來登入 Confluent Cloud SaaS 入口網站。 此體驗使用 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 和 [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) 來提供安全且方便的方式讓您登入。

如需詳細資訊，請參閱 [單一登入](manage.md#single-sign-on)。

## <a name="billing"></a>計費

有兩個可用的計費選項：隨用隨付的每月方案和承諾用量方案。

- 使用隨用 **隨付的每月方案** 時，您會收到 Azure 每月帳單的 Confluent 雲端耗用量費用。
- 在 **承諾用量方案** 中，您可以註冊最少的支出，並取得 Confluent Cloud 的承諾用量折扣。

您可以決定在建立服務時要使用的計費選項。

## <a name="confluent-links"></a>Confluent 連結

如需使用 Apache Kafka for Confluent Cloud 的其他協助，請參閱下列 [Confluent 網站](https://docs.confluent.io/home/overview.html)連結。

若要瞭解帳單選項，請參閱：

* [隨用隨付 Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [具有承諾的 Azure Marketplace](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

若要瞭解如何管理解決方案，請參閱：

* [在 Confluent Cloud 中建立叢集](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluent 雲端環境](https://docs.confluent.io/current/cloud/using/environments.html)
* [Confluent 雲端基本概念](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

如需支援和條款，請參閱：

* [Confluent 支援](https://support.confluent.io)
* [服務條款](https://www.confluent.io/confluent-cloud-tos)。

## <a name="next-steps"></a>後續步驟

若要建立 Apache Kafka for Confluent Cloud 的實例，請參閱 [快速入門：開始使用 Azure 上的 Confluent Cloud](create.md)。
