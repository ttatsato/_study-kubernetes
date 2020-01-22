# はじめてみるKubernetes
# 前提
kubectlが使える

# ゴール
nginxのコンテナImageからコンテナを3つ起動し、
それらのコンテナとロードバランサを紐付けることで外部にサービスを公開する。

# しくみ
LoadBalancerを作成するとサービス公開に必要なIPアドレスが払い出され、自動的に３つのコンテナへ負荷分散された状態i
なる。

それ以外におmコンテナの死活監視、セルフヒーリング、ロードバランサのメンバ変更といった
コンテナ運用に必要なことは全てKubernetesが自動的に行なってくれる。

# 手順

```shell script
# nginxコンテナ３つからなるグループを作成し、myappというラベルつけておく
kubectl run myapp --image=nginx:1.12 --replicas 3 --labels="app=myapp"
```

```shell script
# nginx コンテナ3つできているかを確認
kubectl get pods
# myapp-7549955545-fqrbb            1/1     Running     0          13s
# myapp-7549955545-k9tmx            1/1     Running     0          13s
# myapp-7549955545-sxfwp            1/1     Running     0          13s
```

```shell script
# PORT 80 にリクエストを受けたら、myappと名前がついたコンテナに流すロードバランサを作成
kubectl create service loadbalancer --tcp 80:80 myapp
```

```shell script
# ロードバランサーサービスが作成されているかを確認
kubectl get service myapp

# NAME    TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
# myapp   LoadBalancer   10.51.254.159   35.221.113.159   80:31051/TCP   57s
```

# 結果の確認
## コンテナが３つ起動しているか？
```shell script
# nginx コンテナ3つできているかを確認
kubectl get pods
# myapp-7549955545-fqrbb            1/1     Running     0          13s
# myapp-7549955545-k9tmx            1/1     Running     0          13s
# myapp-7549955545-sxfwp            1/1     Running     0          13s
```
OK

## ロードバランサーが設定されて、外部にサービスが公開されているか？
http://localhost:80
にアクセス。
「It Works」とテキストが表示されれば、OK

