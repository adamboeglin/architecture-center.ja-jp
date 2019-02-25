---
title: 'CAF: ソフトウェア定義ネットワーク - クラウド ネイティブ'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: クラウド ネイティブの仮想ネットワーク サービスの説明
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901241"
---
# <a name="software-defined-networks-cloud-native"></a>ソフトウェア定義ネットワーク: クラウド ネイティブ

クラウド ネイティブ仮想ネットワークは、仮想マシンなどの IaaS リソースをクラウド プラットフォームにデプロイするときに必要です。 Web のような外部ソースから仮想ネットワークへのアクセスは、明示的にプロビジョニングする必要があります。 これらの種類の仮想ネットワークでは、サブネット、ルーティング規則、および仮想ファイアウォール、およびトラフィック管理デバイスの作成がサポートされます。

クラウド ネイティブ仮想ネットワークには、クラウドでホストされるワークロードをサポートするために、組織のオンプレミスまたはクラウド以外のリソースへの依存関係はありません。 必要なリソースはすべて、仮想ネットワーク自体またはマネージド PaaS オファリングを使用してプロビジョニングされます。

## <a name="cloud-native-assumptions"></a>クラウド ネイティブの前提条件

クラウド ネイティブ仮想ネットワークのデプロイでは、以下のことが前提とされます。

- 仮想ネットワークにデプロイするワークロードに、オンプレミス ネットワーク内からのみアクセスできるアプリケーションまたはサービスへの依存関係はありません。 パブリック インターネット経由でアクセスできるエンドポイントを提供する場合を除き、オンプレミスで内部的にホストされるアプリケーションとサービスは、クラウド プラットフォームでホストされるリソースから使用することができません。
- ワークロードの ID 管理とアクセス制御は、クラウド プラットフォームの ID サービスまたはクラウド環境でホストされている IaaS サーバーに依存します。 オンプレミスまたはその他の外部の場所でホストされている ID サービスに直接接続する必要はありません。
- ID サービスは、オンプレミス ディレクトリによってシングル サインオン (SSO) をサポートする必要はありません。

クラウド ネイティブ仮想ネットワークに外部の依存関係はありません。 このため、そのデプロイと構成は簡単になっていて、その結果として、このアーキテクチャは多くの場合、実験やその他の小規模な自己完結型または反復処理型のデプロイにとって最適な選択肢となっています。

クラウド導入チームが、クラウド ネイティブ仮想ネットワーク アーキテクチャについて検討するときに考慮する必要のあるその他の問題には、以下が含まれます。

- オンプレミスのデータ センターで実行するように設計された既存のワークロードでは、ストレージや認証サービスなどのクラウド ベースの機能を活用するために、大がかりな変更が必要になることがあります。
- クラウド ネイティブのネットワークは、クラウド プラットフォーム管理ツールを通じてのみ管理されるので、時間の経過とともに、管理とポリシーが既存の IT 標準とは異なる状況になる可能性があります。

## <a name="learn-more"></a>詳細情報

Azure プラットフォームでのクラウド ネイティブ仮想ネットワークの詳細については、以下を参照してください。

- [Azure Virtual Network: 攻略ガイド](/azure/virtual-network/virtual-network-vnet-plan-design-arm)。 新しく作成される Azure の仮想ネットワークは、既定でクラウド ネイティブとなります。 これらのガイドを使用すると、仮想ネットワークの設計とデプロイの計画に役立ちます。
- [サブスクリプションの制限: ネットワーク](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) を選択します。 1 つのサブスクリプション内には、1 つの仮想ネットワークと、接続されたリソースのみが存在できます。これらはサブスクリプションの制限によってバインドされています。