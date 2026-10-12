# Oracle ARM Sniper

甲骨文云 (Oracle Cloud) Always Free ARM 实例 (`VM.Standard.A1.Flex`) 自动抢机工作流。

## 为什么放公开仓库

GitHub Actions 对**公开仓库**完全免费、不计分钟数，可以 7×24 不间断跑。
私有仓库每月只有 2000 分钟，跑不到 3 天就烧完了。

## 工作方式

- 每 5 小时触发一轮（`cron: 0 */5 * * *`），单轮最长跑 290 分钟
- 上一轮没结束时新的一轮会排队，不会互相踩
- 覆盖所有可用区 (AD)，配置按 `2C/12G → 1C/6G` 依次降级尝试
- 抢到后：写 Step Summary + 微信推送 (Server酱) + 上传 `instance-ocid.txt` 产物

## 需要的 Secrets

在仓库 `Settings → Secrets and variables → Actions` 里添加：

| Secret | 说明 |
|---|---|
| `TENANCY_OCID` | 租户 OCID |
| `USER_OCID` | 用户 OCID |
| `FINGERPRINT` | API 密钥指纹 |
| `REGION` | 区域标识，如 `ap-tokyo-1` |
| `PRIVATE_KEY` | API 私钥全文（含 BEGIN/END 行） |
| `SUBNET_ID` | 子网 OCID |
| `SSH_PUB_KEY` | 公钥全文（`ssh-ed25519 AAAA... user@host`） |
| `SERVERCHAN_SENDKEY` | Server酱 SendKey（可选，用于微信推送） |

## 手动触发

Actions → Oracle ARM Sniper → Run workflow

- `test_notify=1`：只测微信推送，不抢机
- `shape_config`：目标规格 JSON，默认 `{"ocpus":2,"memoryInGBs":12}`
- `boot_volume_gb`：引导卷大小，默认 100
- `duration_min`：单轮运行上限分钟数，默认 290

## 免责

高频轮询抢机可能与甲骨文云的服务条款存在冲突，风险自负。
