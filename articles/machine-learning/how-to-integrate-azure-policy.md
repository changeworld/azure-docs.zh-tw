---
title: 審核及管理原則合規性
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 原則針對 Azure Machine Learning 使用內建原則，以確保您的工作區符合您的需求。
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 22901c4e8409fc4846c1566a57b2679f4fa92396
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444554"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>使用 Azure 原則來審核和管理 Azure Machine Learning

[Azure 原則](../governance/policy/index.yml) 是一種管理工具，可讓您確保 Azure 資源符合您的原則。 您可以使用 Azure Machine Learning 指派下列原則：

* **客戶管理的金鑰** ：無論工作區是否必須使用客戶管理的金鑰，請進行審核或強制執行。
* **Private link** ： Audit 工作區是否使用私人端點來與虛擬網路通訊。

您可以在不同範圍（例如訂用帳戶或資源群組層級）設定原則。 如需詳細資訊，請參閱 [Azure 原則檔](../governance/policy/overview.md)。

## <a name="built-in-policies"></a>內建原則

Azure Machine Learning 提供一組原則，可讓您用於 Azure Machine Learning 的一般案例。 您可以將這些原則定義指派給現有的訂用帳戶，或使用它們作為基礎來建立您自己的自訂定義。 如需 Azure Machine Learning 內建原則的完整清單，請參閱 Azure Machine Learning 的 [內建原則](../governance/policy/samples/built-in-policies.md#machine-learning)。

若要查看與 Azure Machine Learning 相關的內建原則定義，請使用下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)中的 __Azure 原則__ 。
1. 選取 [ __定義__ ]。
1. 在 [ __類型__ ] 中，選取 [ _內建_ ]，然後在 [ __類別__ ] 中選取 [ __Machine Learning__ ]。

您可以從這裡選取原則定義來加以查看。 在查看定義時，您可以使用 [ __指派__ ] 連結將原則指派給特定範圍，並設定原則的參數。 如需詳細資訊，請參閱 [指派原則-入口網站](../governance/policy/assign-policy-portal.md)。

您也可以使用 [Azure PowerShell](../governance/policy/assign-policy-powershell.md)、 [Azure CLI](../governance/policy/assign-policy-azurecli.md)和 [範本](../governance/policy/assign-policy-template.md)來指派原則。

## <a name="workspaces-encryption-with-customer-managed-key"></a>使用客戶管理的金鑰進行工作區加密

控制是否應使用客戶管理的金鑰來加密工作區，或使用 Microsoft 管理的金鑰來加密計量和中繼資料。 如需使用客戶管理金鑰的詳細資訊，請參閱資料加密文章的 [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) 一節。

若要設定此原則，請將效果參數設定為 __audit__ 或 __deny__ 。 如果設定為 __audit__ ，您可以建立不含客戶管理金鑰的工作區，並在活動記錄中建立警告事件。

如果原則設定為 [ __拒絕__ ]，除非指定客戶管理的金鑰，否則您無法建立工作區。 嘗試建立不含客戶管理金鑰的工作區時，會產生類似的錯誤 `Resource 'clustername' was disallowed by policy` ，並在活動記錄中建立錯誤。 原則識別碼也會在這個錯誤中傳回。

## <a name="workspaces-should-use-private-link"></a>工作區應使用 private link

控制工作區是否應使用 Azure Private Link 與 Azure 虛擬網路進行通訊。 如需使用 private link 的詳細資訊，請參閱 [設定工作區的私人連結](how-to-configure-private-link.md)。

若要設定此原則，請將效果參數設定為 __audit__ 。 如果您在不使用 private link 的情況下建立工作區，則會在活動記錄中建立警告事件。

## <a name="next-steps"></a>後續步驟

* [Azure 原則文件](../governance/policy/overview.md)
* [Azure Machine Learning 的內建原則](policy-reference.md)
* [使用 Azure 資訊安全中心的安全性原則](../security-center/tutorial-security-policy.md)