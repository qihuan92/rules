# Mihomo 配置

一份给 Mihomo 用的配置。域名规则与 glados 一致，策略组的写法学 [YYDS](https://github.com/666OS/YYDS)：图标、锚点，以及按节点名过滤。

配置文件：[mihomo/config.yaml](mihomo/config.yaml)

远程地址：

```text
https://raw.githubusercontent.com/qihuan92/rules/main/mihomo/config.yaml
```

## 怎么用

1. 把节点写进配置的 `proxies`，或取消文件里 `proxy-providers` 那段注释并填上订阅。
2. 把 `mihomo/config.yaml` 交给 Mihomo。

`include-all` 只能匹配这份配置里的节点和订阅。节点名不用逐个写死，末尾数字增减都会进原来的组。名字对齐现在的 glados：`US-Balancer-01`、`Download-01`，不再使用 `US-X1-1` 这种旧名字。

| 节点名 | 进入的组 |
| --- | --- |
| `US-Fixed-数字` | `US Fixed IP`，以及 Auto、Default Proxy、Google、OpenAI、Claude、SpaceX、Microsoft、WebRTC |
| `TW-Fixed-数字` | `TW Fixed IP`，以及上面这些选择组 |
| `JP-Fixed-数字` | `JP Fixed IP`，以及上面这些选择组 |
| `US-Balancer-数字` | `US-Balancer`、`Streaming`、`Default Proxy` |
| `TW-Balancer-数字` | `TW-Balancer`、`Streaming`、`Default Proxy` |
| `JP-Balancer-数字` | `JP-Balancer`、`Default Proxy`。节点本身不进 `Streaming`，`Streaming` 里可以选 `JP-Balancer` 这个组 |
| `Download-数字` | 只进 `Download` |
| `Fast-B数字-数字` | 只进 `Auto` |
| `Balancer-B数字-数字` | 只进 `Streaming` |

OpenAI、Claude 只放固定 IP 和 `REJECT`。SpaceX 只放 `Default Proxy` 和固定 IP。Google 另外可以选三个地区负载均衡、`Auto` 和 `REJECT`。某一类节点暂时一个都没有时，自动组会落到 `REJECT`，避免整份配置加载失败。

## 规则顺序

Mihomo 先匹配先生效。文件后半里和前面完全重复、不可能再命中的行已经去掉，实际走向与 glados 相同。

- GitHub 走 `Microsoft`，因为 `github` 关键词更早出现。`objects.githubusercontent.com` 和 `release-assets.githubusercontent.com` 写在这个关键词前面，走 `Download`。
- `cursor.com`、`cursor.sh`、`cursorapi.com`、`cursor-cdn.com` 走 `SpaceX`。
- Apple、iCloud 走 `DIRECT`。
- `googlevideo.com` 走 `Streaming`，写在泛 Google 关键词之前。
- `replicate.com` 走 `Download`。`oaistatic.com` 也走 `Download`。
- `pstatp.com` 走 `Streaming`。
- UDP 的 STUN/TURN 端口走 `WebRTC`，这一组默认是 `REJECT`。
- 国内域名和 `GEOIP,CN` 走 `DIRECT`，其余走 `Default Proxy`。

个人直连写在所有规则前面：

- `zuoyebang.com`
- `zuoyebang.cc`
- `suanshubang.com`
- `suanshubang.cc`
- `yukework.com`
- `downloads.cursor.com`（只这一台主机。`cursor.com` 的其他主机走 `SpaceX`）

面板里 `Streaming` 排在 `Download` 下面。OpenAI、Steam、Download 用 [Koolson/Qure](https://github.com/Koolson/Qure/tree/master/IconSet/Color) 里对应的图标，Claude 用 Oasisic 的 Anthropic 图标，SpaceX 用官网那张黑底白 X 的 PNG，WebRTC 用 Qure 的 Reject，GLOBAL 用 Final。其余图标仍来自 Qure。

DNS 和 TUN 按这份 glados：IPv6 关闭，DNS 查询跟着规则走，TUN 接住不走系统代理的流量，局域网地址不进 TUN。Auto 180 秒，负载均衡 300 秒，固定 IP 回退 300 秒。`Streaming` 改为手动选择，不再自动测速。
