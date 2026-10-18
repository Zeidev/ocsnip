# Oracle ARM Sniper

Oracle Cloud (Oracle Bulut) Always Free ARM makinesi (`VM.Standard.A1.Flex`) otomatik yakalama iş akışı.

## Neden herkese açık repo?

GitHub Actions, **herkese açık** depolarda dakika saymaz, ücretsizdir ve 7×24 kesintisiz çalışabilir.
Özel depolarda aylık yalnızca 2000 dakika hakkın var, 3 güne bile dayanamaz.

## Nasıl çalışır?

- Her 5 saatte bir tur tetiklenir (`cron: 0 */5 * * *`), tek tur en fazla 290 dakika sürer
- Önceki tur bitmeden yeni tur kuyruğa alınır, birbirine karışmaz
- Tüm availability-domain'leri dener, spec `2C/12G → 1C/6G` sırasıyla küçülür
- Yakaladığında: Step Summary + WeChat push (Server酱) + `instance-ocid.txt` artefaktı

## Gerekli Secret'lar

Depoda `Settings → Secrets and variables → Actions` bölümüne ekle:

| Secret | Açıklama |
|---|---|
| `TENANCY_OCID` | Tenancy OCID |
| `USER_OCID` | Kullanıcı OCID |
| `FINGERPRINT` | API anahtarı parmak izi |
| `REGION` | Bölge kimliği, ör. `ap-tokyo-1` |
| `PRIVATE_KEY` | API özel anahtarının tamamı (BEGIN/END satırları dahil) |
| `SUBNET_ID` | Subnet OCID |
| `SSH_PUB_KEY` | Genel anahtarın tamamı (`ssh-ed25519 AAAA... user@host`) |
| `SERVERCHAN_SENDKEY` | Server酱 SendKey (opsiyonel, WeChat push için) |

## Elle tetikleme

Actions → Oracle ARM Sniper → Run workflow

- `test_notify=1`: sadece WeChat push'u test eder, makine yakalamaz
- `shape_config`: hedef spec JSON, varsayılan `{"ocpus":2,"memoryInGBs":12}`
- `boot_volume_gb`: boot volume boyutu, varsayılan 100
- `duration_min`: tek tur süre sınırı, varsayılan 290

## Sorumluluk reddi

Yüksek frekanslı denemek Oracle Cloud hizmet şartlarıyla çelişebilir, sorumluluk kullanıcıya aittir.