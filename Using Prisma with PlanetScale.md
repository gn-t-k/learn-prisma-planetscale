# PlanetScaleでPrismaを使う

## PlanetScaleとは？

PlanetScaleの特長

- 高いスケーラビリティ
- ブランチを利用することでデータベーススキーマをテストできる
- データベースをロックさせたりダウンタイムを発生させたりすることなくデータベーススキーマを更新できる

## PlanetScaleと他のDBプロバイダーとの共通点

- Prisma Schema Languageを使用してデータベースをモデル化することができる
- PlanetScaleが提供する接続文字列・Prismaのデータベースコネクターを使って、mysqlデータベースに接続できる
- Introspectionを使用できる
- `db push`を使用して、スキーマの変更をデータベースにプッシュすることができる
= Prisma CLientを使用してPlanetScaleのデータベース・サーバーと通信することができる

## PlanetScaleと他のDBプロバイダーとの相違点

### branchとdeploy requestsの運用

PlanetScaleでは2種類のデータベースブランチを提供している

- development branches … スキーマの変更をテストする
- production branches … スキーマの直接変更から保護されている

スキーマの変更はまず開発ブランチで作成され、その後deploy requests機能を使用して本番環境にデプロイされる必要がある

### 参照アクションと整合性

PlanetScaleでは高いスケーラビリティのために複数のデータベースサーバーにまたがるスケーリングをサポートしている

そのためPlanetScaleでは外部キー制約が使えず、アプリケーションで成約を実装するようにユーザーに求めている

PrismaではPrisma Clientでprisma relation modeを使用してリレーションをエミュレートする機能を使用することで、データ内でこれらの関係を維持し、参照アクションを使用できる

### 外部キーにインデックスを作成する

Prismaでリレーションをエミュレートしたい場合、外部キーにインデックスを作成する必要がある

標準的なMySQLデータベースではテーブルに外部キー制約を持つ列がある場合、その列は自動的にインデックスが作成されるが、PlanetScaleでは外部キー制約は使えない

そのため、Prisma Clientがリレーションをエミュレートする際にこれらのインデックスが作成されず、クエリは最適化されない可能性がある

これを回避するために、Prismaでインデックスを作成することができる

### `db push`によるスキーマ変更

develop branchをproduction branchにマージすると、PlanetScaleは自動的に2つのスキーマを比較し、独自のスキーマdiffを生成する

Prismaもマイグレーションワークフローにおいて独自のマイグレーション履歴ファイルを作成する

Prismaのマイグレーション履歴ファイルは、PlanetScaleによって実行された実際のスキーマ変更を反映していない可能性がある

Prismaは、PlanetScaleでスキーマ変更をする場合は`prisma migrate`を使用せず`prisma db push`を使用することを推奨している

### introspection

既存のデータベースをintrospectすると、PlanetScaleは外部キーをサポートしていないため、リレーションのないスキーマが作成されてしまう

そのため、introspectionを利用してスキーマを作ったあとには、手動でリレーションを追加することでPrismaによるリレーションのエミュレートができる状態にする必要がある

## branchesとdeploy requestsの使い方

## Prisma Clientでリレーションをエミュレートする

## 外部キー制約の作り方、インデックスの貼り方

## `db push`によるスキーマ変更のやり方

## イントロスペクションの後、足りないリレーションを追加する方法

## PlanetScaleでPrismaをもっと使う
