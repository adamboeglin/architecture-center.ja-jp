---
title: 'エンタープライズ クラウドの導入: クラウド リソース ガバナンスとは'
description: Azure でのリソース アクセス ガバナンスの概念についての説明
author: petertaylor9999
ms.date: 09/10/2018
ms.openlocfilehash: 14c26cbccdbea524a7c7220b3bb98ed2a118c30d
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389215"
---
# <a name="enterprise-cloud-adoption-what-is-cloud-resource-governance"></a>エンタープライズ クラウドの導入: クラウド リソース ガバナンスとは

[Azure のしくみ](what-is-azure.md)に関するページでは、Azure は、仮想化されたハードウェアとソフトウェアをユーザーの代わりに実行する、サーバーとネットワーク ハードウェアのコレクションであることを説明しました。 Azure を使用すると、必要に応じてリソースを容易に作成、読み取り、更新、および削除できるようにすることで、組織の開発および IT 部門の俊敏性を高めることが可能です。

ただし、開発者が無制限にリソースにアクセスできるようにすると、俊敏性は高まりますが、意図しないコストが発生する場合があります。 たとえば、テスト用に一連のリソースのデプロイを承認された開発チームが、テスト完了後に、そのリソースを削除し忘れることがあります。 これらのリソースのコストは、承認が無効になったり不要になったりしても発生し続けます。 

この問題を解決するのが、リソース アクセス **ガバナンス**です。 ガバナンスとは、組織の目標と要件を満たすために、Azure リソースの使用を継続的に管理、監視、および監査するプロセスを意味します。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94] 

これらの目標や要件は組織によって異なるため、1 つのアプローチですべてのガバナンス対応するのは不可能です。 正確には、Azure では、主要ガバナンス ツールとして**リソース ベースのアクセス制御 (RBAC)** と**リソース ポリシー**の 2 つが実装され、これらのツールを使用してガバナンス モデルを設計することは、それぞれの組織の責任です。

RBAC ではロールが定義され、そのロールによって、ロールに割り当てられているユーザーの機能が定義されます。 たとえば、**所有者**ロールでは、リソースのすべての機能 (作成、読み取り、更新、および削除) が有効になりますが、**閲覧者**ロールでは、読み取り機能しか有効になりません。 広範なスコープでロールを定義して、多くの種類のリソースに適用することも、スコープを狭くして適用対象を少なくすることもできます。 

リソース ポリシーでは、リソース作成のルールが定義されます。 たとえば、リソース ポリシーによって、VM の SKU を、事前に承認された特定のサイズに制限できます。 または、リソースの作成が要求されたときに、リソース ポリシーによって、タグとコスト センターの追加を強制できます。 

これらのツールを構成するときは、ガバナンスと組織の機敏性のバランスを考慮することが重要です。 つまり、ガバナンス ポリシーの制限が厳しいほど、開発者や IT 作業者の俊敏性が低下します。 これは、ガバナンス ポリシーの制限が厳しいと、開発者がフォームを入力するか、ガバナンス チームの担当者にメールして、リソースを手動で作成しなければならない、といった手動による操作が必要になる場合があるためです。 ガバナンス チームの能力には限りがあり、バックログが発生する可能性があります。これにより、リソースが作成されるのを待っている開発チームの生産性が低下します。また、リソースが削除されるのを待っている場合は、その間に不要なリソース コストが発生します。

## <a name="next-steps"></a>次の手順

クラウド リソース ガバナンスの概念を理解したので、Azure での[リソース アクセスの管理方法](azure-resource-access.md)の詳細の説明に進み、[単一チーム](../governance/governance-single-team.md)または[複数チーム](../governance/governance-multiple-teams.md)向けガバナンス モデルの設計方法の学習に備えます。

> [!div class="nextstepaction"]
> [Azure でのリソース アクセスについて確認する](azure-resource-access.md)