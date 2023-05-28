## 起動手順

※aws configureは設定済みとして進めて行きます。
1. 以下GitHubリポジトリをクローンしてきて、初回インストールを行う。
```
git clone git@github.com:RysSuzuki/simple-api-for-quota-validation.git
npm ci
```

2. API GatewayとLambdaのデプロイ
```
cd packages/simple-api
npx serverless deploy
```

3. 構築したAPI Gatewayに5MBのBASE64データを含んでリクエストを送信する。

※送信するデータは5MBのダミーファイルをBASE64形式に変換した値を送信します。
```
cat ../../base64_5mb | \
curl -i -X POST <API Gatewayのendpoint> -H "Content-Type:application/json" -d @-
```

### 送信結果

413のエラーが想定される挙動になりますが、まれに500エラーが返却されます。

- 正常な返却値
```
HTTP/2 413
content-type: application/json
content-length: 31
----略----

{"message": "Request Too Long"}
```

- 想定外の返却値
```
HTTP/2 500
content-type: application/json
content-length: 36
----略----

{"message": "Internal server error"}

```