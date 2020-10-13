---
title: 安全程式碼的最佳作法
titleSuffix: Azure Machine Learning
description: 瞭解開發 Azure Machine Learning 時可能存在的潛在安全性威脅。 瞭解 Azure ML 提供的緩和措施，以及確保您的開發環境維持安全的最佳作法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 4bc9a982f6ce77b803a3ba91e050bcda9ec74fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728517"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>使用 Azure Machine Learning 保護程式碼的最佳作法

在 Azure Machine Learning 中，您可以將檔案和內容從任何來源上傳至 Azure。 您載入的 Jupyter 筆記本或腳本內的內容，可能會從您的會話讀取資料、在 Azure 中存取組織內的資料，或代表您執行惡意進程。

> [!IMPORTANT]
> 只執行來自信任來源的筆記本或腳本。 例如，您或您的安全性小組已審核筆記本或腳本。

## <a name="potential-threats"></a>潛在威脅

使用 Azure Machine Learning 的開發通常牽涉到 (筆記本 & Azure ML studio) 的 web 架構開發環境。 使用網頁型開發環境時，可能的威脅如下：

* [跨網站腳本 (XSS) ](https://owasp.org/www-community/attacks/xss/)

    * __DOM 插入__：這種類型的攻擊可修改瀏覽器中顯示的 UI。 例如，藉由變更 [執行] 按鈕在 Jupyter Notebook 中的運作方式。
    * __存取權杖/cookie__： XSS 攻擊也可以存取本機儲存體和瀏覽器 cookie。 Azure Active Directory (AAD) 驗證權杖儲存在本機儲存體中。 XSS 攻擊可以使用此權杖來代表您進行 API 呼叫，然後將資料傳送至外部系統或 API。

* [跨網站要求偽造 (CSRF) ](https://owasp.org/www-community/attacks/csrf)：這項攻擊可能會將影像或連結的 url 取代為惡意腳本或 API 的 url。 當載入影像或按一下連結時，就會對 URL 進行呼叫。

## <a name="azure-ml-studio-notebooks"></a>Azure ML studio 筆記本

Azure Machine Learning studio 會在瀏覽器中提供託管的筆記本體驗。 筆記本中的資料格可以輸出包含惡意程式碼的 HTML 檔案或片段。  轉譯輸出時，可以執行程式碼。

__可能的威脅__：
* 跨網站腳本 (XSS) 
* 跨網站要求偽造 (CSRF) 

__Azure Machine Learning 所提供的緩和措施__：
* Iframe 中的程式__代碼資料格輸出__會沙箱化。 Iframe 可防止腳本存取父 DOM、cookie 或會話儲存體。
* __Markdown 資料格__ 內容會使用 dompurify 程式庫來清理。 這會顯示與 markdown 資料格一起執行的惡意腳本。
* __影像 URL__ 和 __Markdown 連結__ 會傳送至 Microsoft 擁有的端點，其會檢查是否有惡意值。 如果偵測到惡意值，端點就會拒絕要求。

__建議的動作__：
* 在上傳至 studio 之前，請先確認您信任檔案的內容。 上傳時，您必須確認您正在上傳受信任的檔案。
* 當您選取連結來開啟外部應用程式時，系統會提示您信任應用程式。

## <a name="azure-ml-compute-instance"></a>Azure ML 計算實例

Azure Machine Learning 計算實例裝載 __Jupyter__ 和 __Jupyter Lab__。 使用時，筆記本或程式碼中的資料格可以輸出包含惡意程式碼的 HTML 檔案或片段。 轉譯輸出時，可以執行程式碼。 使用裝載在計算實例上的 __RStudio__ 時，也適用相同的威脅。

__可能的威脅__：
* 跨網站腳本 (XSS) 
* 跨網站要求偽造 (CSRF) 

__Azure Machine Learning 所提供的緩和措施__：
* 無。 Jupyter 和 Jupyter labs 是裝載在 Azure Machine Learning 計算實例上的開放原始碼應用程式。

__建議的動作__：
* 在上傳至 studio 之前，請先確認您信任檔案的內容。 上傳時，您必須確認您正在上傳受信任的檔案。

## <a name="report-security-issues-or-concerns"></a>報告安全性問題或考慮 

Azure Machine Learning 符合 Microsoft Azure 賞金計畫資格。 如需詳細資訊，請造訪  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) 。

## <a name="next-steps"></a>後續步驟

* [Azure Machine Learning 的企業安全性](concept-enterprise-security.md)