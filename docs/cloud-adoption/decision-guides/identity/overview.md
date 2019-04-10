---
title: CAF:ID 決定ガイド
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 移行のコア サービスとしての ID について説明します。
author: rotycenh
ms.openlocfilehash: 511e27c7d63968417a7eb47764d2e7768496d34f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242773"
---
# <a name="identity-decision-guide"></a>ID 決定ガイド

オンプレミス環境、ハイブリッド環境、クラウドのみの環境など、どのような環境であっても、IT 部門はリソースへのアクセス権を持つ管理者、ユーザー、およびグループを制御する必要があります。 ID 管理とアクセス管理 (IAM) サービスを使用すると、クラウドでアクセス制御を管理できます。

![ID オプションを単純なものから複雑なものへと順番に配置し、その下にジャンプ先へのリンクを示します](../../_images/discovery-guides/discovery-guide-identity.png)

ジャンプ先:[ID 統合の要件の決定](#determine-identity-integration-requirements) | [クラウド ネイティブ](#cloud-baseline) | [ディレクトリ同期](#directory-synchronization) | [クラウドでホストされたドメイン サービス](#cloud-hosted-domain-services) | [Active Directory フェデレーション サービス](#active-directory-federation-services) | [ID 統合の発展](#evolving-identity-integration) | [詳細情報](#learn-more)

クラウド環境で ID を管理する方法は複数あり、コストや複雑さは多岐に渡ります。 クラウド ベースの ID サービスを構築する際の重要な要素は、既存のオンプレミス ID インフラストラクチャで必要とされる統合のレベルです。

Azure では、Azure Active Directory (Azure AD) はクラウド リソースを対象にした基本レベルのアクセス制御と ID 管理を提供します。 ただし、組織の Active Directory (AD) インフラストラクチャに複雑なフォレスト構造やカスタマイズされた組織単位 (OU) があると、クラウドベース ワークロードでは、オンプレミス環境とクラウド環境の間で一貫性のある ID、グループ、およびロールを保持するために、Azure AD とのディレクトリ同期が必要となる場合があります。 さらに、レガシ認証メカニズムに依存するアプリケーションをサポートするために、クラウドでの Active Directory Domain Services (AD DS) のデプロイが必要になる場合もあります。

## <a name="determine-identity-integration-requirements"></a>ID 統合の要件の決定

| 質問 | クラウド ベースライン | ディレクトリ同期 | クラウドでホストされたドメイン サービス | AD フェデレーション サービス |
|------|------|------|------|------|
| 現在、オンプレミス ディレクトリ サービスがない | はい | いいえ  | いいえ  | いいえ  |
| ワークロードがクラウド環境とオンプレミス環境の間で共通のユーザーおよびグループのセットを使用する必要がある | いいえ  | はい | いいえ  | いいえ  |
| ワークロードがレガシ認証メカニズム (Kerberos や NTLM など) に依存している | いいえ  | いいえ  | 可能  | はい |
| 複数の ID プロバイダーにわたってシングル サインオンが必要である | いいえ  | いいえ  | いいえ  | はい |

Azure への移行を計画する際には、既存の ID 管理とクラウド ID サービスを最適に統合する方法を決定する必要があります。 一般的な統合シナリオを以下に示します。

### <a name="cloud-baseline"></a>クラウド ベースライン

Azure AD は、ユーザーとグループに Azure プラットフォーム上の管理機能へのアクセス許可を付与するための、ネイティブの ID とアクセス管理 (IAM) システムです。 有効なオンプレミス ID ソリューションを持たず、クラウド ベースの認証メカニズムとの互換性を持たせるためにワークロードの移行を計画する組織は、Azure AD を基盤として使用しながら自身の ID インフラストラクチャの構築を開始する必要があります。

**クラウド ベースラインの前提条件**。 完全にクラウドネイティブな ID インフラストラクチャを使用するには、次の事項が前提となります。

- クラウドベースのリソースは、オンプレミス ディレクトリ サービスや Active Directory サーバーに従属しないこと。または、そのような従属性を取り除くためにワークロードを変更できること。
- 移行するアプリケーションまたはサービスのワークロードが Azure AD と互換性のある認証メカニズムをサポートしていること。または、そのようなメカニズムをサポートするために容易に変更可能であること。 Azure AD は、SAML、OAuth、OpenID Connect などのインターネット対応の認証メカニズムを使用します。 Kerberos や NTLM などのプロトコルを使用するレガシ認証方法に依存する既存のワークロードは、クラウドに移行する前に、クラウドのベースライン パターンを使用してリファクタリングしなければならない場合があります。

> [!TIP]
> ID サービスを Azure AD に完全に移行することで、独自の ID インフラストラクチャを維持する必要がなくなり、IT 管理が大幅に簡素化されます。
>
> ただし、Azure AD は従来のオンプレミスの Active Directory インフラストラクチャに完全に置き換わるものではありません。 レガシ認証方法、コンピューター管理、グループ ポリシーなどのディレクトリ機能は、クラウドに追加のツールやサービスをデプロイしないと利用できません。
>
> オンプレミスの ID またはドメイン サービスをクラウドのデプロイと統合する必要があるシナリオの場合、以下で説明するディレクトリ同期およびクラウドでホストされるドメイン サービスのパターンを参照してください。

### <a name="directory-synchronization"></a>ディレクトリ同期

多くの場合、既存のオンプレミス Active Directory インフラストラクチャを持つ組織においては、既存のユーザーとアクセス管理を保持すると同時に、クラウド リソースの管理に必要な IAM 機能を提供するためには、ディレクトリ同期が最適なソリューションです。 このプロセスでは、Azure AD とオンプレミスのディレクトリ サービスの間でディレクトリ情報を継続的にレプリケートし、ユーザーの共通資格情報と、一貫性のある ID、ロール、アクセス許可のシステムを組織全体で可能にします。

注:Office 365 を導入済みの組織では、オンプレミス Active Directory インフラストラクチャと Azure Active Directory の間に[ディレクトリ同期](/office365/enterprise/set-up-directory-synchronization)を既に実装している場合があります。

**ディレクトリ同期の前提条件**。 同期済み ID ソリューションを使用するには、次の事項が前提となります。

- クラウドとオンプレミスの IT インフラストラクチャ全体で、ユーザー アカウントとグループの共通セットを保持する必要がある。
- オンプレミス ID サービスが Azure AD とのレプリケーションをサポートしている。

> [!TIP]
> オンプレミスの Active Directory サーバーによって提供され、Azure AD によってサポートされないレガシ認証メカニズムに依存するすべてのクラウドベース ワークロードでは、これらのサービスを提供するクラウド環境のオンプレミス ドメイン サービスまたは仮想サーバーのいずれかへの接続が引き続き必要となります。 オンプレミス ID サービスを使用すると、クラウド ネットワークとオンプレミス ネットワーク間の接続性にも依存します。

### <a name="cloud-hosted-domain-services"></a>クラウドでホストされたドメイン サービス

Kerberos や NTLM などの従来のプロトコルを使用する要求ベースの認証に依存するワークロードがあり、SAML や OAuth、OpenID Connect などの最新の認証プロトコルを許可するようにそれらのワークロードをリファクタリングできない場合、クラウドをデプロイする際に、一部のドメイン サービスをクラウドに移行しなければならない場合があります。

このパターンでは、クラウド上のリソース用の Active Directory Domain Services (AD DS) を提供するために、Active Directory を実行する仮想マシンをクラウドベース仮想ネットワークにデプロイすることが必要になります。 クラウド ネットワークに移行する既存のすべてのアプリケーションとサービスは、マイナーな変更によって、これらのクラウドでホストされたディレクトリ サーバーを使用できるようにする必要があります。

既存のディレクトリとドメイン サービスは、オンプレミス環境で引き続き使用される場合が多いと思われます。 そのようなシナリオでは、クラウド環境とオンプレミス環境の両方で共通のユーザーとロールを提供するために、ディレクトリ同期も使用することをお勧めします。

**クラウドでホストされたドメイン サービスの前提条件**。 ディレクトリの移行を実行するには、次の事項が前提となります。

- ワークロードが Kerberos や NTLM などのプロトコルを使用する要求ベース認証に依存している。
- Active Directory グループ ポリシーの管理または利用のために、ワークロード仮想マシンをドメインに参加させる必要がある。

> [!TIP]
> クラウドでホストされたドメイン サービスを伴うディレクトリ移行では、既存のワークロードの移行において優れた柔軟性がありますが、これらのサービスを提供するためにクラウド仮想ネットワーク内に仮想マシンをホストすることにより、IT 管理タスクが複雑化します。 クラウド移行に慣れてきたら、これらのサーバーをホスティングする長期的なメンテナンスの要件を確認します。 Azure Active Directory などのクラウド ID プロバイダとの互換性のために既存のワークロードをリファクタリングすることで、クラウドでホストされたこれらのサーバーの必要性を削減することが可能かどうかを検討します。

### <a name="active-directory-federation-services"></a>Active Directory フェデレーション サービス (AD FS)

ID フェデレーションは、複数の ID 管理システムにわたって信頼関係を確立し、共通の認証機能と許可機能を可能にします。 これにより、組織内の複数のドメインや、お客様またはビジネス パートナーによって管理される ID システム全体でシングル サインオン機能をサポートできるようになります。

Azure AD は、[Active Directory フェデレーション サービス](/azure/active-directory/hybrid/how-to-connect-fed-whatis) (AD FS) を使用するオンプレミス.Active Directory ドメインのフェデレーションをサポートします。 参照アーキテクチャ「[AD FS の Azure への拡張](../../../reference-architectures/identity/adfs.md)」を参照し、Azure での実装方法をご確認ください。

## <a name="evolving-identity-integration"></a>ID 統合の発展

ID 統合は反復的なプロセスです。 最初のデプロイでは、少数のユーザーと対応するロールを持つクラウド ネイティブ ソリューションで開始してください。 移行に慣れてきたら、フェデレーション モデルの採用や、オンプレミス ID サービスのクラウドへの完全なディレクトリ移行を検討します。 移行プロセスを繰り返すたびに、ID 戦略を見直してください。

## <a name="learn-more"></a>詳細情報

Azure プラットフォーム上の ID サービスの詳細については、以下をご覧ください。

- [Azure AD](https://azure.microsoft.com/services/active-directory)。 Azure AD は、クラウド ベースの ID サービスを提供します。 Azure リソースへのアクセスの管理と、ID 管理、デバイス登録、ユーザー プロビジョニング、アプリケーション アクセス制御、およびデータ保護の制御を可能にします。
- [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity)。 Azure AD Connect ツールを使用すると、既存の ID 管理ソリューションを使用して Azure AD インスタンスに接続でき、クラウド上の既存ディレクトリの同期が可能になります。
- [ロールベースのアクセス制御](/azure/role-based-access-control/overview) (RBAC)。 Azure AD は、管理プレーン内のリソースへのアクセスを効果的かつ安全に管理するための RBAC を提供します。 ジョブと責任はロールに編成され、ユーザーにはこれらのロールが割り当てられます。 RBAC により、リソースへアクセス可能なユーザーと、そのリソースに実行可能なアクションを制御できます。
- [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) (PIM)。 PIM は、リソース アクセス特権の露出時間を短縮し、レポートとアラートを介して特権の使用への可視性を高めます。 「ジャスト イン タイム」 (JIT) での特権の取得や、短期間での特権の割り当て (割り当て後に自動的に特権が取り消される) により、ユーザーを制限します。
- [オンプレミス Active Directory ドメインと Azure Active Directory の統合](../../../reference-architectures/identity/azure-ad.md)。 この参照アーキテクチャでは、オンプレミス Active Directory ドメインと Azure AD の間のディレクトリ同期の例を示します。
- [Active Directory Domain Services (AD DS) の Azure への拡張](../../../reference-architectures/identity/adds-extend-domain.md)。 この参照アーキテクチャでは、ドメイン サービスをクラウドベース リソースに拡張するために AD DS サーバーをデプロイする例を示します。
- [Active Directory Federation Services (AD FS) の Azure への拡張](../../../reference-architectures/identity/adfs.md)。 この参照アーキテクチャでは、Azure AD ディレクトリでのフェデレーション認証と許可を実行するために Active Directory フェデレーション サービス (AD FS) を構成する方法を示します。

## <a name="next-steps"></a>次の手順

クラウドでポリシーの適用を実装する方法について説明します。

> [!div class="nextstepaction"]
> [ポリシーの適用](../policy-enforcement/overview.md)
