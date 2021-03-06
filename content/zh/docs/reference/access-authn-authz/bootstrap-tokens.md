---
reviewers:
- jbeda
title: 使用 Bootstrap Token 进行鉴权
content_template: templates/concept
weight: 20
---
<!-- 
---
reviewers:
- jbeda
title: Authenticating with Bootstrap Tokens
content_template: templates/concept
weight: 20
--- 
-->

{{% capture overview %}}
<!-- 
Bootstrap tokens are a simple bearer token that is meant to be used when
creating new clusters or joining new nodes to an existing cluster.  It was built
to support [kubeadm](/docs/reference/setup-tools/kubeadm/kubeadm/), but can be used in other contexts
for users that wish to start clusters without `kubeadm`. It is also built to
work, via RBAC policy, with the [Kubelet TLS
Bootstrapping](/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/) system. 
-->
Bootstrap token 是一种简单的 bearer token，用于创建新集群或将新节点连接到现有集群。它被用来支持 [kubeadm](/docs/reference/setup-tools/kubeadm/kubeadm/)，但是对于那些希望在没有 `kubeadm` 的情况下创建集群的用户，也可以在其他环境中使用。它也可以通过 RBAC 策略用于 [Kubelet TLS
Bootstrapping](/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/) 系统。
{{% /capture %}}

{{% capture body %}}
<!-- 
## Bootstrap Tokens Overview 
-->
## Bootstrap Token 概览

<!-- 
Bootstrap Tokens are defined with a specific type
(`bootstrap.kubernetes.io/token`) of secrets that lives in the `kube-system`
namespace.  These Secrets are then read by the Bootstrap Authenticator in the
API Server.  Expired tokens are removed with the TokenCleaner controller in the
Controller Manager.  The tokens are also used to create a signature for a
specific ConfigMap used in a "discovery" process through a BootstrapSigner
controller. 
-->
Bootstrap Token 在 `kube-system` 命名空间中使用特定类型 (`bootstrap.kubernetes.io/token`) 的 secret 来定义。
然后，这些 Secret 被 API Server 中的 Bootstrap Authenticator 读取。过期的 token 被 Controller Manager 中的 TokenCleaner 控制器移除。
这些 Token 也被 BootstrapSigner 控制器在一个"发现"的过程中用来为特定的 ConfigMap 创建签名。 

{{< feature-state state="beta" >}}

<!-- 
## Token Format 
-->
## Token 格式

<!-- 
Bootstrap Tokens take the form of `abcdef.0123456789abcdef`.  More formally,
they must match the regular expression `[a-z0-9]{6}\.[a-z0-9]{16}`. 
-->
Bootstrap Token 采用 `abcdef.0123456789abcdef` 的形式。为了更加正式，它们必须匹配正则表达式 `[a-z0-9]{6}\.[a-z0-9]{16}`。

<!--
The first part of the token is the "Token ID" and is considered public
information.  It is used when referring to a token without leaking the secret
part used for authentication. The second part is the "Token Secret" and should
only be shared with trusted parties.
-->
Token 的第一部分是 "Token ID" 并且被认为是公共信息。这部分会在引用一个 token 但是不想泄露那些用于鉴权的私密部分时被用到。第二部分则是 "Token Secret"，应该仅与受信任方共享。

<!-- 
## Enabling Bootstrap Token Authentication
-->
## 启用 Bootstrap Token 鉴权

<!-- 
The Bootstrap Token authenticator can be enabled using the following flag on the
API server: 
-->
Bootstrap Token authenticator 可以通过在 API server 上添加下列标志来启用。

```
--enable-bootstrap-token-auth
```

<!-- 
When enabled, bootstrapping tokens can be used as bearer token credentials to
authenticate requests against the API server. 
-->
被启用时，Bootstrap Token 可以被用来作为针对 API server 鉴权请求的 bearer token 凭据。

```http
Authorization: Bearer 07401b.f395accd246ae52d
```

<!-- 
Tokens authenticate as the username `system:bootstrap:<token id>` and are members
of the group `system:bootstrappers`.  Additional groups may be specified in the
token's Secret. 
-->
Token 鉴权信息为：用户名 `system:bootstrap:<token id>` 且在 `system:bootstrappers` 组内。我们也可以在 token 的 Secret 中指定其它组信息。

<!-- Expired tokens can be deleted automatically by enabling the `tokencleaner`
controller on the controller manager. -->
过期的 token 可以通过启用 controller manager 中的 `tokencleaner` 控制器来自动删除。

```
--controllers=*,tokencleaner
```

<!-- 
## Bootstrap Token Secret Format 
-->
## Bootstrap Token Secret 格式

<!-- 
Each valid token is backed by a secret in the `kube-system` namespace.  You can
find the full design doc
[here](https://github.com/kubernetes/community/blob/{{< param "githubbranch" >}}/contributors/design-proposals/cluster-lifecycle/bootstrap-discovery.md). 
-->
每个有效的 token 都对应着 `kube-system` 命名空间中的一个 secret。您可以从 [这里](https://github.com/kubernetes/community/blob/{{< param "githubbranch" >}}/contributors/design-proposals/cluster-lifecycle/bootstrap-discovery.md) 获得整个的设计文档。

<!-- 
Here is what the secret looks like. 
-->
Secret 样例如下所示。

```yaml
apiVersion: v1
kind: Secret
metadata:
  # Name MUST be of form "bootstrap-token-<token id>"
  name: bootstrap-token-07401b
  namespace: kube-system

# Type MUST be 'bootstrap.kubernetes.io/token'
type: bootstrap.kubernetes.io/token
stringData:
  # Human readable description. Optional.
  description: "The default bootstrap token generated by 'kubeadm init'."

  # Token ID and secret. Required.
  token-id: 07401b
  token-secret: f395accd246ae52d

  # Expiration. Optional.
  expiration: 2017-03-10T03:22:11Z

  # Allowed usages.
  usage-bootstrap-authentication: "true"
  usage-bootstrap-signing: "true"

  # Extra groups to authenticate the token as. Must start with "system:bootstrappers:"
  auth-extra-groups: system:bootstrappers:worker,system:bootstrappers:ingress
```

<!-- 
The type of the secret must be `bootstrap.kubernetes.io/token` and the name must
be `bootstrap-token-<token id>`.  It must also exist in the `kube-system`
namespace. 
-->
Secret 的类型必须是 `bootstrap.kubernetes.io/token` 并且名字的格式必须是 `bootstrap-token-<token id>`。它必须被放在 `kube-system` 命名空间中。

<!-- 
The `usage-bootstrap-*` members indicate what this secret is intended to be used
for.  A value must be set to `true` to be enabled. 
-->
`usage-bootstrap-*` 的成员指出了这个 secret 的作用。值被设定为 `true` 时才会启用。

<!-- * `usage-bootstrap-authentication` indicates that the token can be used to
authenticate to the API server as a bearer token. -->
* `usage-bootstrap-authentication` 表示这个 token 可以被用来作为针对 API server 鉴权请求的 bearer token 凭据。
<!-- * `usage-bootstrap-signing` indicates that the token may be used to sign the
`cluster-info` ConfigMap as described below. -->
* `usage-bootstrap-signing` 表示这个 token 可以被用来对 `cluster-info` ConfigMap 通过下述方式来签名。

<!-- 
The `expiration` field controls the expiry of the token.  Expired tokens are
rejected when used for authentication and ignored during ConfigMap signing. 
The expiry value is encoded as an absolute UTC time using RFC3339.  Enable the
`tokencleaner` controller to automatically delete expired tokens. 
-->
`expiration` 字段控制 token 的到期。过期的 token 会在用于身份验证时被拒绝，在 ConfigMap 签名时被忽略。
使用 RFC3339 将过期时间编码为绝对 UTC 时间。启用 `tokencleaner` 控制器来自动删除过期的 token。

<!-- 
## Token Management with kubeadm 
-->
## 使用 kubeadm 管理 token

<!-- 
You can use the `kubeadm` tool to manage tokens on a running cluster. See the
[kubeadm token docs](/docs/reference/setup-tools/kubeadm/kubeadm-token/) for details. 
-->
您可以在集群中使用 `kubeadm` 工具来管理 token。查阅 [kubeadm token 文档](/docs/reference/setup-tools/kubeadm/kubeadm-token/) 获取更多信息。

<!-- 
## ConfigMap Signing 
-->
## ConfigMap 签名

<!-- 
In addition to authentication, the tokens can be used to sign a ConfigMap.  This
is used early in a cluster bootstrap process before the client trusts the API
server.  The signed ConfigMap can be authenticated by the shared token. 
-->
除了身份验证之外，token 还可用于对 ConfigMap 进行签名。
这个用于在客户端和 API Server 互信之前，在集群引导过程的前期使用。
签名的 ConfigMap 可以通过共享 token 进行身份鉴权。

<!-- 
Enable ConfigMap signing by enabling the `bootstrapsigner` controller on the
Controller Manager. 
-->
通过启用 Controller Manager 中的 `bootstrapsigner` 控制器来启用 ConfigMap 签名选项。

```
--controllers=*,bootstrapsigner
```

<!-- 
The ConfigMap that is signed is `cluster-info` in the `kube-public` namespace.
The typical flow is that a client reads this ConfigMap while unauthenticated and
ignoring TLS errors.  It then validates the payload of the ConfigMap by looking
at a signature embedded in the ConfigMap. 
-->
被签名的 ConfigMap 作为 `kube-public` 命名空间中的 `cluster-info` 存在。
典型的流程是客户端在未经身份验证和忽略 TLS 错误时读取此 ConfigMap。
然后通过嵌入 ConfigMap 中的签名来校验 ConfigMap。

<!-- 
The ConfigMap may look like this: 
-->
ConfigMap 样例如下所示:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-info
  namespace: kube-public
data:
  jws-kubeconfig-07401b: eyJhbGciOiJIUzI1NiIsImtpZCI6IjA3NDAxYiJ9..tYEfbo6zDNo40MQE07aZcQX2m3EB2rO3NuXtxVMYm9U
  kubeconfig: |
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: <really long certificate data>
        server: https://10.138.0.2:6443
      name: ""
    contexts: []
    current-context: ""
    kind: Config
    preferences: {}
    users: []
```

<!-- 
The `kubeconfig` member of the ConfigMap is a config file with just the cluster
information filled out.  The key thing being communicated here is the
`certificate-authority-data`.  This may be expanded in the future. 
-->
ConfigMap 的 `kubeconfig` 成员是一个配置文件，里面只填写了集群信息。
在通信过程中的关键是 `certificate-authority-data`。这部分可能在将来会扩展。

<!-- 
The signature is a JWS signature using the "detached" mode.  To validate the
signature, the user should encode the `kubeconfig` payload according to JWS
rules (base64 encoded while discarding any trailing `=`).  That encoded payload
is then used to form a whole JWS by inserting it between the 2 dots.  You can
verify the JWS using the `HS256` scheme (HMAC-SHA256) with the full token (e.g.
`07401b.f395accd246ae52d`) as the shared secret.  Users _must_ verify that HS256
is used. 
-->
签名是使用“分离”模式的 JWS 签名。
为了验证签名，用户应该根据 JWS 规则加密 `kubeconfig`的信息（base64 编码，同时丢弃任何的 `=`）。
然后将使用该编码的信息插入 2 个点之间来形成整个 JWS。
您可以使用 `HS256` 方案（HMAC-SHA256）验证 JWS，并使用完整令牌（例如 `07401b.f395accd246ae52d`）作为共享密钥。 用户 _必须_ 验证 HS256 是否被使用。

{{< warning >}}
<!-- 
**Warning:** Any party with a bootstrapping token can create a valid signature for that
token. When using ConfigMap signing it's discouraged to share the same token with
many clients, since a compromised client can potentially man-in-the middle another
client relying on the signature to bootstrap TLS trust. 
-->
**注意:** 拥有 bootstrapping token 任何一方都可以为该 token 创建有效签名。
当使用 ConfigMap 签名时，不建议许多客户端共享相同的 token，因为受入侵的客户端可能会作为中间人为其它客户端来引导 TLS 信任。
{{< /warning >}}

<!-- 
Consult the [kubeadm security model](/docs/reference/generated/kubeadm/#security-model)
section for more information. 
-->
参阅 [kubeadm 安全模型](/docs/reference/generated/kubeadm/#security-model) 获取更多信息。
{{% /capture %}}
