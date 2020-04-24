---
title: Azure 資料資源管理員 Python SDK - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Azure 資料資源管理器 Python SDK。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502851"
---
# <a name="azure-data-explorer-python-sdk"></a>Azure 資料資源管理員 Python SDK

Azure 資料資源管理員*Kusto Python 用戶端*庫提供了使用 Python 查詢 Azure 資料資源管理員群集的功能。 它與 Python 2.x/3.x 相容,並且支援使用熟悉的 Python DB API 介面的所有資料類型。

例如,可以使用連接到Spark群集的[Jupyter筆記本](https://jupyter.org/)中的庫,包括Azure資料磚塊實例,但並非僅包含Azure[資料塊](https://azure.microsoft.com/services/databricks/)實例。

*Kusto Python Ingest 用戶端*是一個 python 庫,允許將資料發送到 Azure 資料資源管理員服務 - 即引入資料。 

* [如何安裝套件](https://github.com/Azure/azure-kusto-python#install)

* [函式庫斯托查詢範例](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [資料攝取範例](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [GitHub 儲存函式庫](https://github.com/Azure/azure-kusto-python)

    [![alt 文字](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* 皮皮包:

    * [azure-kusto-data](https://pypi.org/project/azure-kusto-data/)
    [![PyPI 版本](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [azure-kustoing0PyPI](https://pypi.org/project/azure-kusto-ingest/)
    [![版本](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [azure-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![PyPI 版本](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)