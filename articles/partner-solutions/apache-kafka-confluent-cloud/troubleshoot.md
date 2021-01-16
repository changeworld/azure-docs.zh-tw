---
title: 針對適用于 Confluent Cloud 的 Apache Kafka 進行疑難排解-Azure 合作夥伴解決方案
description: 本文提供有關 Azure 上的 Confluent Cloud (常見問題) 的疑難排解和常見問題的資訊。
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253409"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>針對 Confluent 雲端解決方案的 Apache Kafka 進行疑難排解

本檔包含針對使用 Apache Kafka for Confluent Cloud 的解決方案進行疑難排解的相關資訊。

如果您找不到答案或無法解決問題，請 [透過 Azure 入口網站](manage.md#get-support) 或 contact [Confluent 支援](https://support.confluent.io)建立要求。

## <a name="cant-find-offer-in-the-marketplace"></a>在 Marketplace 中找不到供應專案

若要在 Azure Marketplace 中尋找供應專案，請使用下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [ **建立資源**]。
1. 搜尋 _Confluent Cloud 上的 Apache Kafka_。
1. 選取應用程式磚。

如果未顯示供應專案，請洽詢 [Confluent 支援](https://support.confluent.io)人員。 您的 Azure Active Directory 租使用者識別碼必須位於允許的租使用者清單上。 若要瞭解如何尋找您的租使用者識別碼，請參閱 [如何尋找您的 Azure Active Directory 租使用者識別碼](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)。

## <a name="conflict-error"></a>衝突錯誤

如果您先前已註冊 Confluent Cloud，您必須使用新的電子郵件地址來建立另一個 Confluent 雲端組織資源。 使用先前註冊的電子郵件地址時，您會收到 **衝突** 錯誤。 重新註冊，但這次使用新的電子郵件地址。

## <a name="deploymentfailed-error"></a>DeploymentFailed 錯誤

如果您收到 **DeploymentFailed** 錯誤，請檢查您的 Azure 訂用帳戶狀態。 請確定它未暫停，而且沒有任何帳單問題。

## <a name="resource-isnt-displayed"></a>資源未顯示

如果 Confluent Cloud 的 **總覽** 或 **刪除** blade 未顯示在入口網站中，請嘗試重新整理頁面。 此錯誤可能是入口網站的間歇性問題。 如果沒有解決問題，請洽詢 [Confluent 支援](https://support.confluent.io)人員。

如果在 Azure [ **所有資源** ] 清單中找不到 Confluent 雲端資源，請聯絡 [Confluent 支援](https://support.confluent.io)人員。

## <a name="resource-creation-takes-long-time"></a>建立資源需要很長的時間

如果部署程式需要三個小時以上才能完成，請聯絡支援人員。

如果部署失敗，且 Confluent 雲端資源的狀態為 `Failed` ，則刪除資源。 刪除之後，請再試一次以建立資源。

## <a name="offer-plan-doesnt-load"></a>未載入供應專案方案

此錯誤可能是 Azure 入口網站的間歇性問題。 請嘗試再次部署供應專案。

## <a name="unable-to-delete"></a>無法刪除 

如果您無法刪除 Confluent 資源，請確認您有刪除資源的許可權。 您必須允許 Confluent/*/Delete 動作。 如需有關查看許可權的詳細資訊，請參閱 [使用 Azure 入口網站列出 Azure 角色指派](../../role-based-access-control/role-assignments-list-portal.md)。

如果您有正確的許可權，但仍無法刪除資源，請洽詢 [Confluent 支援](https://support.confluent.io)人員。 這種狀況可能與 Confluent 的保留原則相關。 Confluent 支援人員可以為您刪除組織和電子郵件地址。

## <a name="unable-to-use-single-sign-on"></a>無法使用單一登入

如果 SSO 無法針對 Confluent Cloud SaaS 入口網站運作，請確認您使用的是正確的 Azure Active Directory 電子郵件。 您也必須同意允許 Confluent 雲端軟體即服務 (SaaS) 入口網站的存取權。 如需詳細資訊，請參閱 [單一登入指引](manage.md#single-sign-on)。

如果問題持續發生，請洽詢 [Confluent 支援](https://support.confluent.io)人員。

## <a name="next-steps"></a>後續步驟

瞭解如何 [管理您](manage.md) 的 Apache Kafka For Confluent Cloud 實例。
