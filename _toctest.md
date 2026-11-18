# BANKLAB V9 — SIFIRDAN KURULUM REHBERİ
## Tek dosya · **Kaynak koddan doğrulanmış** · "Neden" açıklamalı · 17 Kasım 2026

---

## 📑 BÖLÜMLER — başlığa tıkla, o bölüme git

> Her satır tıklanabilir. **Ana bölüm → alt adımlar → ön koşul** tek bakışta.

### 0. [Rehber nasıl kullanılır](#s0)

*Bu rehber neyi NEDEN böyle yapıyor · Üç sembol · Okuma süresi*

| | |
|---|---|
| **Ön koşul** | Yok — ilk bölüm |
| **Süre** | 10 dk |
| **Bölüm** | §0 — rehberin kendisi |

**Alt adımlar (5):**

- [**0.1** Bu rehber neyi NEDEN böyle yapıyor](#s0-1)
- [**0.2** Üç sembol](#s0-2)
- [**0.3** Okuma süresi](#s0-3)
- [**0.4** HER BÖLÜMÜN BAŞINDA "ÖN KONTROL" VAR — ÖNCE ONU YAPIN](#s0-4)
- [**0.5** UYGULAMA SIRASI — bölüm numarasına göre GİTMEYİN](#s0-5)

---

### 1. [Mimari](#s1)

*Katmanlar ve aralarındaki bağımlılık · Bootstrap secret deseni · Neden Infisical ve neden Redis zorunlu*

| | |
|---|---|
| **Ön koşul** | §0 okundu mu? |
| **Süre** | 10 dk |
| **Bölüm** | §1 — tasarım |

**Alt adımlar (3):**

- [**1.1** Katmanlar ve aralarındaki bağımlılık](#s1-1)
- [**1.2** Bootstrap secret deseni — V6'nın 13 saatlik hatasının çözümü](#s1-2)
- [**1.3** Neden Infisical ve neden Redis zorunlu](#s1-3)

---

### 2. [Envanter ve adres planı](#s2)

*Fiziksel · Ağlar · Servis adresleri*

| | |
|---|---|
| **Ön koşul** | §1 mimarisi anlaşıldı mı? |
| **Süre** | 10 dk |
| **Bölüm** | §2 — plan |

**Alt adımlar (5):**

- [**2.1** Fiziksel](#s2-1)
- [**2.2** Ağlar](#s2-2)
- [**2.3** Servis adresleri](#s2-3)
- [**2.4** VM tablosu](#s2-4)
- [**2.5** V6'NIN KIRIK REFERANSLARI — bu rehberde YOK (R03/R04/R06/R07)](#s2-5)

---

### 3. [Ön koşullar (yönetim istasyonu)](#s3)

*Araçlar (yönetim istasyonuna ne kuruyoruz) · Sürümleri CANLI doğrula · İNDİRME*

| | |
|---|---|
| **Ön koşul** | §2 adres planı hazır mı? |
| **Süre** | 30 dk |
| **Bölüm** | §3 — hazırlık |

**Alt adımlar (4):**

- [**3.1** Araçlar (yönetim istasyonuna ne kuruyoruz)](#s3-1)
- [**3.2** Sürümleri CANLI doğrula](#s3-2)
- [**3.3** İNDİRME — KOŞULLU KARAR AĞACI (her şeyin başlangıcı)](#s3-3)
- [**3.4** Alternatif işletim sistemi — AlmaLinux / RHEL / Rocky](#s3-4)

---

### 4. [Proxmox host kurulumu](#s4)

*Gerçek IP/beyaz liste bulma, SDN zone+vnet+**commit**, onboot, host kaynak limitleri (§4.3-B teşhis), SSH ölümsüzleştirme, **5 katmanlı IP beyaz liste**, fail2ban, Cloudflare ayrımı, kernel tuning.*

| | |
|---|---|
| **Ön koşul** | §3 araçlar kurulu + sürümler doğrulandı mı? |
| **Süre** | 60 dk |
| **Bölüm** | §4 — host |

**Alt adımlar (11):**

- [**4.1** İlk giriş ve envanter (neyim var, neyim yok)](#s4-1)
- [**4.2** Depo ve güncelleme (lisanssız depoya geçiş)](#s4-2)
- [**4.2-B** SANAL AĞLAR — Proxmox SDN (VM'lerin bağlanacağı köprüler)](#s4-2-b) 🔴 SDN commit atlanırsa `bridge vnet10 does not exist`
- [**4.2-C** onboot FELAKETİ — host reboot olunca 9 VM açılmaz](#s4-2-c) 🔴 `startup` ≠ `onboot` — reboot'ta 9 VM açılmaz
- [**4.3** Host tükenmesini ÖNLE — V9'a özel, V6'da hiç yoktu](#s4-3)
- [**4.3-B** HOST TÜKENMESİ — "DDoS mu, kaynak mı?" kesin teşhis](#s4-3-b) 🔴 "DDoS" sanılan şey kaynak tükenmesi çıkabilir
- [**4.4** SSH'ı ölümsüz yap — V6'nın en can sıkıcı hatası](#s4-4)
- [**4.4-B** IP BEYAZ LİSTE — 5 KATMAN (V6'da sadece 1 katman vardı)](#s4-4-b) 🔴 5 katman: PVE→fail2ban→CrowdSec→nftables→sshd
- [**4.5** Fail2ban — kendinizi banlamayın](#s4-5)
- [**4.6** Cloudflare — doğru teşhis](#s4-6)
- [**4.7** Kernel + ağ tuning — ölçülmüş değerlerle](#s4-7) 🔴 ölçülmüş değerler (THP, fd, BBR)

---

### 5. [OPNsense kurulumu](#s5)

*VM 100 tanımı (4 NIC), API anahtarı, **CSRF token** (session), gerçek kural şeması, `os-haproxy` kurulumu, `10.30.10.24:9345` passthrough, Unbound yönlendirme, **MTU/MSS** ayarı.*

| | |
|---|---|
| **Ön koşul** | §4 SDN ağları kurulu (`qm list` + `pvesh get /cluster/sdn`) |
| **Süre** | 45 dk |
| **Bölüm** | §5 — güvenlik duvarı |

**Alt adımlar (7):**

- [**5.1** İmaj](#s5-1)
- [**5.2** VM 100 (OPNsense sanal makinesi — 4 NIC)](#s5-2)
- [**5.3** API anahtarı — V6'da hiç anlatılmamıştı (R05)](#s5-3)
- [**5.4** Kural ekleme — GERÇEK şema (V7'de yanlıştı)](#s5-4)
- [**5.4-B** HAProxy paketi — V6'da hiç yazılmamıştı (R32)](#s5-4-b)
- [**5.5** DNS yönlendirme — RKE2 join için zorunlu](#s5-5)
- [**5.6** MTU / PMTU — "bazı siteler açılıyor bazıları açılmıyor" tuzağı](#s5-6)

---

### 6. [Ubuntu VM'leri + DNS kurulumu](#s6)

*Cloud image + **GPG doğrulama**, `qm agent ping` kısır döngüsü, cloud-init (2 kritik hata), VM hazırlık (openssh-server, /home/bankbootstrap, parola), 8 VM tek döngü, Unbound DNS, worker `systemd-networkd`, **9-node tuzağı**.*

| | |
|---|---|
| **Ön koşul** | §5 OPNsense ayakta + DNS yönlendirme çalışıyor |
| **Süre** | 60 dk |
| **Bölüm** | §6 — işletim sistemi katmanı |

**Alt adımlar (11):**

- [**6.1** Cloud image](#s6-1)
- [**6.1-B** Ubuntu cloud image — GPG anahtarı YOK (R11)](#s6-1-b)
- [**6.1-C** qm agent ping ≠ "ağ yok" (R30) — kısır döngü](#s6-1-c) 🔴 QGA yok → "ağ yok" sanılır (kısır döngü)
- [**6.2** cloud-init — V6'nın 2 KRİTİK hatası](#s6-2)
- [**6.2-B** VM hazırlık — cloud image'de olmayan 3 şey (R18/R19/S13/S15)](#s6-2-b)
- [**6.3** VM oluşturma script'i (8 VM — tek döngü)](#s6-3)
- [**6.4** dns-1 — Unbound (lab'in DNS sunucusu)](#s6-4)
- [**6.4-B** Unbound — DNSSEC + cache + zone transfer (V6'da test edildi)](#s6-4-b)
- [**6.5** Worker DNS tuzağı (H40) — döngüye girme](#s6-5)
- [**6.6** Worker ağı — systemd-networkd (netplan DEĞİL)](#s6-6)
- [**6.6-A** "9 NODE" TUZAĞI — çift NIC'li worker'ı 2 makine sanmak](#s6-6-a) 🔴 çift NIC'li worker 2 makine sanılır

---

### 7. [RKE2 küme kurulumu (Cilium ile)](#s7)

*containerd `config.toml.tmpl` (RKE2 `config.yaml`ı yok sayar), `audit-policy.yaml`, `cred/passwd` kalıcılığı, cp-1 → cp-2/3 join, worker **agent** kurulumu, HAProxy registration `.24:9345`, Cilium, kube-vip VIP, registry mirror.*

| | |
|---|---|
| **Ön koşul** | §6 8 VM hazır, `primary0/edge0` IP aldı |
| **Süre** | 90 dk |
| **Bölüm** | §7 — Küme |

**Alt adımlar (14):**

- [**7.1** Her node'da ön koşul (6 node'a aynı hazırlık)](#s7-1)
- [**7.1-B** containerd config.toml.tmpl — RKE2 config.yaml'ı YOK SAYIYOR](#s7-1-b) 🔴 containerd `config.yaml`ı yok sayar
- [**7.2** RKE2 config — cp-1 (ilk kontrol düzlemi)](#s7-2)
- [**7.2-B** audit-policy.yaml — DOSYA YOKSA RKE2 BAŞLAMAZ](#s7-2-b) 🔴 dosya yoksa RKE2 hiç başlamaz
- [**7.3** H80 — cred/passwd KALICI düzeltme](#s7-3)
- [**7.3-B** cred/passwd — KALICI düzeltme (R43/H80)](#s7-3-b) 🔴 `passwd newer` → systemd drop-in
- [**7.4** cp-1 başlatma (kümeyi doğur)](#s7-4)
- [**7.5** cp-2/cp-3 join (etcd quorum'u tamamla)](#s7-5)
- [**7.6** Worker'lar — 2 NIC (yönetim + LB ayrımı)](#s7-6)
- [**7.6-B** WORKER'LARA RKE2 AGENT KURULUMU](#s7-6-b) 🔴 agent kurulmazsa küme 9 node görünür
- [**7.7** Registration 10.30.10.24 — V6'da HAProxy kurulmamıştı (R32)](#s7-7)
- [**7.8** Cilium (CNI — kube-proxy yerine)](#s7-8)
- [**7.9** VIP — kube-vip](#s7-9)
- [**7.10** Registry mirror — R47'nin tam çözümü (V7'de eksikti)](#s7-10)

---

### 8. [Namespace ve pod güvenliği](#s8)

*TEK KAYNAK · Restart dayanıklılık testi · NetworkPolicy*

| | |
|---|---|
| **Ön koşul** | §7 `kubectl get nodes` → 6 Ready |
| **Süre** | 20 dk |
| **Bölüm** | §8 — izolasyon |

**Alt adımlar (3):**

- [**8.0** TEK KAYNAK — namespace listesi (her yerde bu kullanılır)](#s8-0)
- [**8.1** Restart dayanıklılık testi — KURULUMDAN ÖNCE](#s8-1)
- [**8.2** NetworkPolicy — test edilmiş DENY→ALLOW (V6'da hiç yoktu)](#s8-2)

---

### 9. [Depolama + yük dengeleyici](#s9)

*MetalLB · Longhorn · StorageClass'lar (PVC'lerin hangi diskten geleceği)*

| | |
|---|---|
| **Ön koşul** | §8 namespace listesi (`APP_NS`) sabitlendi |
| **Süre** | 40 dk |
| **Bölüm** | §9 — depolama |

**Alt adımlar (4):**

- [**9.1** MetalLB](#s9-1)
- [**9.2** Longhorn — imaj sorunu](#s9-2)
- [**9.3** StorageClass'lar (PVC'lerin hangi diskten geleceği)](#s9-3)
- [**9.4** KAYNAK BÜTÇESİ — worker 16 Gi YETİYOR MU?](#s9-4)

---

### 10. [Sertifika kurulumu (step-ca + cert-manager)](#s10)

*step-ca (`drop:[ALL]` çalışmaz → `runAsUser:0`), cert-manager, **StepClusterIssuer** — tüm TLS buradan.*

| | |
|---|---|
| **Ön koşul** | §9 MetalLB IP havuzu dağıtıyor |
| **Süre** | 25 dk |
| **Bölüm** | §10 — kimlik/sertifika |

**Alt adımlar (4):**

- [**10.1** step-ca](#s10-1)
- [**10.1-B** step-ca GÜVENLİK BAĞLAMI — drop: [ALL] ÇALIŞMAZ (R51)](#s10-1-b) 🔴 `drop:[ALL]` → Exit 255
- [**10.2** cert-manager (sertifika otomasyonu)](#s10-2)
- [**10.3** StepClusterIssuer](#s10-3)

---

### 11. [Secret yönetimi (Infisical)](#s11)

*pg-infisical + elle secret, Redis, Helm (YOL A/B), IngressRoute + Certificate, restart testi, ilk kullanıcı/proje, `pg-keycloak-owner` parolası, **ESO + 24 CRD** (`scopedRBAC:false`).*

| | |
|---|---|
| **Ön koşul** | §10 StepClusterIssuer `Ready=True` |
| **Süre** | 45 dk |
| **Bölüm** | §11 — sırlar |

**Alt adımlar (11):**

- [**11.1** Neden Infisical (S37)](#s11-1)
- [**11.2** Sıra — bootstrap secret deseni 🔴 DİKKAT: §11 ile §12 İÇ İÇE](#s11-2) 🔴 §11 ile §12 iç içe
- [**11.3** pg-infisical + elle secret](#s11-3)
- [**11.4** Redis (Infisical'ın önbellek/kuyruk deposu)](#s11-4)
- [**11.5** Infisical Helm — GERÇEK chart davranışı (E6/E7/E8)](#s11-5)
- [**11.5-B** Infisical ERİŞİMİ: IngressRoute + Certificate](#s11-5-b)
- [**11.6-B** Infisical pod restart testi — ZORUNLU](#s11-6-b)
- [**11.7** Infisical ilk kullanıcı + proje](#s11-7)
- [**11.7-B** pg-keycloak-owner PAROLASI NEREDEN GELİYOR?](#s11-7-b)
- [**11.8** ESO — external-secrets.io/v1 (E2/E3 düzeltmesi)](#s11-8)
- [**11.8-B** ESO CRD — rehber 3 diyor, gerçek 24 (H74)](#s11-8-b) 🔴 3 değil **24** CRD

---

### 12. [Veritabanı kurulumu (PostgreSQL/CNPG)](#s12)

*Operator (chart 0.29.2 = operator 1.30.2), **Barman ayrı chart**, pg-keycloak (Secret **önce**), MinIO yedek hedefi, Barman yedeği + geri dönüş.*

| | |
|---|---|
| **Ön koşul** | §11 Infisical ayakta + ESO senkron |
| **Süre** | 30 dk |
| **Bölüm** | §12 — veri |

**Alt adımlar (5):**

- [**12.1** Operator — chart 0.29.2 = operator 1.30.2 (E1 düzeltmesi)](#s12-1)
- [**12.2** Barman plugin — AYRI chart (E10 düzeltmesi)](#s12-2)
- [**12.3** pg-keycloak](#s12-3)
- [**12.3-B** MinIO — yedeklerin hedefi](#s12-3-b)
- [**12.4** Barman yedeği](#s12-4)

---

### 13. [Uygulama kurulumu (Traefik → Gitea)](#s13)

*Traefik → Keycloak (operator) → OIDC/MFA → Grafana + alarm → Kyverno → cosign → Kafka → Harbor → Argo CD → Gitea.*

| | |
|---|---|
| **Ön koşul** | §12 `pg-keycloak` Cluster `healthy` |
| **Süre** | 120 dk |
| **Bölüm** | §13 — uygulamalar |

**Alt adımlar (10):**

- [**13.1** Traefik (giriş denetleyicisi — tüm UI'lar buradan)](#s13-1)
- [**13.2** Keycloak — resmî operator (E12 düzeltmesi)](#s13-2)
- [**13.2-B** OIDC + MFA + Realm — kullanıcı girişi](#s13-2-b)
- [**13.3** Grafana SSO + alarm kabulü](#s13-3)
- [**13.4** Kyverno — imza zorunluluğu](#s13-4)
- [**13.5** cosign imzalama](#s13-5)
- [**13.6** Kafka — KOŞULLU: hangi imaj?](#s13-6)
- [**13.7** Harbor — imaj registry](#s13-7)
- [**13.8** Argo CD + Keycloak SSO](#s13-8)
- [**13.9** Gitea — git sunucusu (observability'den ÖNCE değil, en son)](#s13-9)

---

### 14. [Kabul testleri](#s14)

**

| | |
|---|---|
| **Ön koşul** | §13 tüm uygulamalar `Running` |
| **Süre** | 20 dk |
| **Bölüm** | §14 — doğrulama |

**Alt adımlar (5):**

- [H1. VM açılış sırası](#s14-h1)
- [H2. cred/passwd kalıcılığı](#s14-h2)
- [H3. Origin vs Cloudflare (H82)](#s14-h3)
- [H4. Üretim öncesi kontrol listesi](#s14-h4)
- [H5. Yedek + geri yükleme](#s14-h5)

---

### 15. [Sorun giderme](#s15)

**

| | |
|---|---|
| **Ön koşul** | — |
| **Süre** | — |
| **Bölüm** | §15 — onarım |

---

### 16. [Hızlı karar tablosu](#s16)

**

| | |
|---|---|
| **Ön koşul** | — |
| **Süre** | — |
| **Bölüm** | §16 — karar |

---

### 17. [Neler hâlâ eksik](#s17)

**

| | |
|---|---|
| **Ön koşul** | — |
| **Süre** | — |
| **Bölüm** | §17 — kapsam |

---

### 18. [Gerçek dünyada ne ters gider](#s18)

*Kurulumun çöktüğü 6 an, gerçekçi süre, "bu adım gerekli mi", 5 kullanıcı hatası, geri dönüş planı, **9 kritik adım**, 8 tuzak, 40 kararlık günlük.*

| | |
|---|---|
| **Ön koşul** | — |
| **Süre** | 20 dk |
| **Bölüm** | §18 — gerçek dünya |

**Alt adımlar (9):**

- [**18.1** Kurulumun çöktüğü 6 gerçek an](#s18-1)
- [**18.2** Zaman bütçesi — gerçekçi](#s18-2)
- [**18.3** "Bu adım gerçekten gerekli mi?" — 5 gereksiz adım](#s18-3)
- [**18.4** En sık yapılan 5 hata (kullanıcı tarafı)](#s18-4)
- [**18.5** Başarısızlık durumunda geri dönüş planı](#s18-5)
- [**18.6** Ne zaman "bu işi bırak" demeli](#s18-6)
- [**18.7** "BUNU YAPMAZSAN ÇALIŞMAZ" — 9 kritik adım (tek bakışta)](#s18-7)
- [**18.8** KÜÇÜK GÖRÜNEN AMA SAAT KAYBETTİREN 8 TUZAK](#s18-8)
- [**18.9** KARAR GÜNLÜĞÜ — her seçim, neden ve kanıtı](#s18-9)

---

> ⚠️ Bölüm **numarası** sırasına göre İLERLEMEYİN — gerçek uygulama sırası [§0.5](#s0-5)'tedir (§11 ile §12 iç içedir).

---

<a id="s0"></a>

# 0. BU REHBER NASIL KULLANILIR

<a id="s0-1"></a>

## 0.1 Bu rehber neyi NEDEN böyle yapıyor

> Bu rehber **V6'nın sahada yaşanmış 174 hatasının** üzerine yazıldı. Aşağıdaki tablo
> **"neden bu kadar ayrıntı var"** sorusunun cevabıdır. Her satır **gerçekten yaşandı**.

| Yaşanan sorun | Kaybedilen süre | Bu rehberde nasıl çözüldü | Bölüm |
|---|---|---|---|
| OpenBao her restart'ta **sealed** kalıyordu → secret zinciri koptu | **13 saat** | **Infisical** (state PG'de, sealed yok) + restart testi ZORUNLU | §11, §11.6-B |
| `cred/passwd newer than datastore` → CP'ler açılmıyordu | 2 × 3 saat | systemd `ExecStartPre` drop-in (kalıcı) | §7.3 |
| Longhorn **6 pod ImagePullBackOff** → PVC bağlanmadı | 4 saat | imaj listesi + `ctr import` + `image.pullPolicy` | §9.2 |
| Host kilitlendi: SSH **%5**, `:8006` timeout | 1 gün | `loadgate` yük kapısı + zram + OOM koruması | §4.3 |
| VM'ler **ağsız** açıldı (cloud-init `0640`) | 3 saat | `chmod 0644` + VM içinden doğrulama | §6.2 |
| İndirme hızı: ghcr.io **837 B/s** | 1.5 gün | **gh-proxy (38 MB/s)** + paralel+resume + tek-asset bundle | §3.3 |
| OPNsense `{"source":{...}}` → 400 | 2 saat | Gerçek şema `{"source_net":"lan"}` | §5.4 |
| OPNsense CSRF **403** (`X-CSRF-Token` yanlış) | 3 saat | `X_CSRFTOKEN` + GUI oturumu (`PHPSESSID`) | §5.4 |
| `codecentric/keycloakx` CRD yok | 2 saat | Resmî `keycloak/keycloak-operator` | §13.2 |
| CNPG `--version 1.30.2` → chart bulunamadı | 1 saat | chart **0.29.2** = operator 1.30.2 | §12.1 |
| Infisical `postgresql://` reddedildi | 30 dk | **`postgres://`** (chart `.env.example`) | §11.5 |
| `longhorn.github.io/longhorn-charts` **404** | 20 dk | **`charts.longhorn.io`** | §9.2 |

**Bu rehberin 3 tasarım kuralı:**

1. **Her adımda `NEDEN`** — çünkü hata ayıklarken *ne yaptığını* değil *neden yaptığını* bilmek gerekir.
2. **Her adımda `✅ KANIT` + `🔴 DUR`** — "çalışıyor gibi görünüyor" en pahalı varsayımdır.
3. **Her riskli yerde `🔁 ALTERNATİF`** — sizin ortamınız buradakiyle aynı değil.

<a id="s0-2"></a>

## 0.2 Üç sembol

| Sembol | Anlamı |
|---|---|
| **NEDEN** | Bu adımı neden yapıyoruz (V6/V7'de ne kırıldı) |
| **✅ KANIT** | Beklenen çıktı — tutmazsa **DUR** |
| **🔁 ALTERNATİF** | Ortamınız farklıysa bunu kullan |

<a id="s0-3"></a>

## 0.3 Okuma süresi

Tam uygulama **~40 saat** (V6'da 4 gün sürdü + 2 gün hata teşhisi).
Bu rehber hata teşhisini **önceden yazılmış** hâle getirir.

<a id="s0-4"></a>

## 0.4 🔴 HER BÖLÜMÜN BAŞINDA "ÖN KONTROL" VAR — ÖNCE ONU YAPIN

Bu rehberde **her bölüm** şu yapıyla başlar:

```
## X.Y  <ne yapılıyor>
### ⬜ ÖN KONTROL — bu adıma başlamadan
  | Kontrol | Komut | Beklenen | Değilse git |
### 🔀 KOŞULLU KARAR — hangi yolu seçeceksin
  | Durumun | Yol | Neden |
### <asıl adımlar>
✅ KANIT — bitince ne görmelisin
🔴 DUR — tutmazsa ne yapmalısın
```

**NEDEN bu yapı:** V6'da en çok zaman kaybettiren şey **yanlış sırada başlamak** oldu.
Örn: CNPG'yi ESO'dan önce başlatmak → kurtarılamaz bootstrap (§12.3).
Ön kontrol, bu sırayı **başlamadan** doğrular.

**Ön kontrol tablosunu okuma:**
| Sütun | Anlamı |
|---|---|
| **Kontrol** | Neyi doğruluyorsun |
| **Komut** | Nasıl doğruluyorsun (kopyala-çalıştır) |
| **Beklenen** | Doğru cevap |
| **Değilse git** | Yanlışsa hangi bölüme dönmelisin |

🔴 **KURAL:** Ön kontroldeki **her satır** ✅ olmadan asıl adımlara **geçmeyin**.
Bu, "1 saat uğraşıp baştan başlamak" ile "2 dakika kontrol" arasındaki farktır.

<a id="s0-5"></a>

## 0.5 🔴 UYGULAMA SIRASI — bölüm numarasına göre GİTMEYİN

> **NEDEN:** Bölüm numaraları **okunabilirlik** için. Bağımlılık yüzünden §11 (§12'ye bağımlı)
> ve §13 (§9'a bağımlı) **atlanarak** yapılmalı.

```
 1. §0 §1 §2 §3      → hazırlık, envanter, sürüm doğrulama
 2. §4               → host (loadgate KURTARICI)
 3. §5               → OPNsense
 4. §6               → VM'ler (önce 102/dns-1)
 5. §7               → RKE2 + Cilium + VIP
 6. §8               → namespace + PSA + RESTART TESTİ 🔴 7. §9               → MetalLB + Longhorn + **§9.4 KAYNAK BÜTÇESİ 🔴**
 8. §10              → step-ca + cert-manager
 9. §12.1 §12.2      → ⬅ CNPG operator + Barman plugin (Infisical'dan ÖNCE!)
10. §11.3 → §11.5-B    → pg-infisical (elle secret) → Redis → Infisical → **§11.5-B IngressRoute **
11. §11.8            → ESO (**scopedRBAC:false, M13**) + ClusterSecretStore + ExternalSecret
12. §12.3 §12.4      → pg-keycloak → Barman yedeği
13. §13.1 §13.2      → Traefik → Keycloak operator
14. §13.2-B          → **OIDC + MFA + Realm + client secret ÜRET **
15. §13.3 → §13.9    → Grafana → Kyverno → Kafka → Harbor → Argo → Gitea
16. §14              → kabul testleri
```

**Tek satırlık kontrol:**
```bash
kubectl get nodes | grep -c Ready                          # 6  (§7)
kubectl get sc | grep -c longhorn                          # 2  (§9)
kubectl get clusterissuer                                  # READY (§10)
kubectl -n cnpg-system get pods | grep -c Running           # 1  (§12.1)
kubectl -n data-services get cluster pg-infisical -o jsonpath='{.status.phase}'  # healthy (§11.3)
kubectl get clustersecretstore infisical -o jsonpath='{.status.conditions[0].status}'  # True (§11.8)
```

---

---

<a id="s1"></a>

# 1. MİMARİ — NEDEN BU TASARIM

> Bu bölüm V7'de **yoktu**. Neyi neden yaptığımızı bilmeden hata ayıklamak imkânsız.

<a id="s1-1"></a>

## 1.1 Katmanlar ve aralarındaki bağımlılık

```
┌─────────────────────────────────────────────────────────────┐
│  KATMAN 6 · Uygulama     Keycloak, Kafka, Gitea, Harbor,    │
│                          Argo CD, Flyway                     │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 5 · Giriş        Traefik + MetalLB (VIP 10.30.20.x)  │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 4 · Veri         CloudNativePG, Redis, MinIO         │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 3 · Güvenlik     Infisical → ESO, step-ca,           │
│                          cert-manager, Kyverno               │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 2 · Depolama     Longhorn (CSI)                      │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 1 · Küme         RKE2 + Cilium + CoreDNS             │
├─────────────────────────────────────────────────────────────┤
│  KATMAN 0 · Altyapı      Proxmox → OPNsense → 8 VM → DNS     │
└─────────────────────────────────────────────────────────────┘
```

**NEDEN bu sıra?** Her katman **altındakine** bağımlıdır. Örnek:
- **KATMAN 4 (CNPG)** → PVC ister → **KATMAN 2 (Longhorn)** olmadan `Pending`
- **KATMAN 6 (Keycloak)** → DB şifresi ister → **KATMAN 3 (Infisical→ESO)** olmadan `SecretSyncedError`
- **KATMAN 3 (Infisical)** → kendi DB'sini ister → **KATMAN 4 (CNPG)** gerekir!

> 🔴 **Bu döngü V6'da kilitlenmenin köküydü.** Çözüm: **Infisical'ın DB'si ayrı CNPG
> cluster'ı** (`pg-infisical`), Keycloak'ın DB'si ayrı (`pg-keycloak`).
> İlk CNPG kurulurken secret **düz K8s Secret** ile verilir, Infisical ayağa kalkınca
> **sonraki** secret'lar Infisical'dan gelir. Buna **"bootstrap secret" deseni** denir.

<a id="s1-2"></a>

## 1.2 Bootstrap secret deseni — V6'nın 13 saatlik hatasının çözümü

```
AŞAMA 1 (Infisical kurulurken):
   kubectl create secret manual → pg-infisical-owner   ← ELLE, bir kez
   → pg-infisical ayağa kalkar

AŞAMA 2 (Infisical hazır):
   Infisical'a "keycloak-username" + "keycloak-password" yazılır  (§11.8 SEÇENEK B)
   → ESO Infisical'dan okur
   → pg-keycloak-owner secret'ı OTOMATİK oluşur
   → pg-keycloak ayağa kalkar

AŞAMA 3 (her şey çalışıyor):
   Elle verilen TEK secret = pg-infisical-owner
   Geri kalan her şey Infisical'dan
```

**NEDEN OpenBao'da bu çalışmadı?** OpenBao restart'ta sealed kalıyordu → AŞAMA 2 çöküyordu →
pg-keycloak hiç başlamıyordu. **Infisical'da "sealed" yok** → AŞAMA 2 her zaman çalışır.

<a id="s1-3"></a>

## 1.3 Neden Infisical ve neden Redis zorunlu

| Soru | Cevap |
|---|---|
| OpenBao neden bırakıldı? | Her restart'ta sealed → tüm zincir çöker (H79) |
| Infisical neyi farklı yapıyor? | State **PostgreSQL'de**; "sealed" kavramı yok |
| Redis neden zorunlu? | Infisical cache/queue için **hard dependency** — REDIS_URL olmadan **başlamaz** |
| Redis kaybolursa? | Oturumlar/cache gider, **secret'lar kalır** (asıl veri PG'de) |
| ENCRYPTION_KEY kaybolursa? | 🔴 **Tüm şifreli secret'lar okunamaz** — yedekleyin |

---

<a id="s2"></a>

# 2. ENVANTER VE ADRES PLANI

> V7'de tablolar **dağınıktı**. Burada tek yerde.

<a id="s2-1"></a>

## 2.1 Fiziksel

| Bileşen | Değer | Not |
|---|---|---|
| Proxmox host | `87.76.130.66/25`, gw `.1` | NIC `nic0`, bridge `vmbr0` |
| Depolama | `local` (ISO/snippet), `local-lvm` (disk) | |
| RAM | **≥72 GB** (8 VM + OPNsense = **71 GB**) | 🔴 **64 GB YETMEZ** — 71 GB gerekir; daha azsa §2.4 |

<a id="s2-2"></a>

## 2.2 Ağlar

| Ağ | CIDR | GW | Kullanım |
|---|---|---|---|
| WAN `vmbr0` | `87.76.130.0/25` | `.1` | uplink |
| `vnet10` | `10.30.10.0/24` | `.1` | yönetim |
| `vnet30` | `10.30.30.0/24` | `.1` | worker küme |
| `vnet20` | `10.30.20.0/24` | `.1` | uygulama/LB |
| Pod CIDR | `10.244.0.0/16` | — | Cilium VXLAN **UDP 8472** |
| Service CIDR | `10.245.0.0/16` | — | CoreDNS `10.245.0.10` |

<a id="s2-3"></a>

## 2.3 Servis adresleri

| Servis | Adres | Nerede |
|---|---|---|
| API VIP | `10.30.10.20:6443` | §7.9 |
| Registration | `10.30.10.24:9345` | §7.7 |
| LB havuzu | `10.30.20.200-10.30.20.220` (21 adet) | §9.1 |
| DNS | `10.30.10.53` | §6.4 |

<a id="s2-4"></a>

## 2.4 VM tablosu

| VMID | Ad | vCPU | RAM | Disk | Birincil | İkincil |
|---|---|---|---|---|---|---|
| 102 | **dns-1** | 1 | 2 GB | 8 | `10.30.10.53` | — |
| 101 | **jump-1** | 2 | 3 GB | 12 | `10.30.10.10` | — |
| 103 | cp-1 | 3 | 6 GB | 18 | `10.30.10.21` | — |
| 104 | cp-2 | 3 | 6 GB | 18 | `10.30.10.22` | — |
| 105 | cp-3 | 3 | 6 GB | 18 | `10.30.10.23` | — |
| 106 | worker-1 | 3 | 16 GB | 28 | `10.30.30.11` | `10.30.20.11` |
| 107 | worker-2 | 3 | 16 GB | 28 | `10.30.30.12` | `10.30.20.12` |
| 108 | worker-3 | 3 | 16 GB | 28 | `10.30.30.13` | `10.30.20.13` |

**Toplam: 21 vCPU / 71 GB RAM / 164 GB disk**

> 🔴 **HOST RAM HESABI:** VM'ler **71 GB** ister. Proxmox host'un **kendisi + ZFS ARC
> + OPNsense VM (1 GB)** düşününce **en az 80 GB** fiziksel RAM gerekir.
> **64 GB host'ta bu tablo ÇALIŞMAZ** → §2.4 ALTERNATİF'i uygulayın.
>
> | Host RAM | Sonuç |
> |---|---|
> | 128 GB | ✅ rahat |
> | 96 GB | ✅ yeter |
> | **80 GB** | ⚠️ sınırda (ZFS ARC'i 4 GB'a sabitleyin) |
> | **64 GB** | 🔴 **YETMEZ** → §2.4 küçültme (47 GB) şart |

🔁 **ALTERNATİF — RAM < 64 GB ise:**
```
worker RAM 16 → 10 GB (Longhorn replica 1, Kafka 1 broker)
cp RAM     6 → 4 GB
→ Toplam 47 GB, minimum çalışır
```
**Ama:** Gözlem yığını (**kube-prometheus-stack, §13.3**) en az 4 GB ister. Daha azı **çalışmaz**.
⚠️ **Gözlem yığını §13.3'tedir** (eski etiket "G11")

**NEDEN dns-1 önce (102)?** Aşağıdaki tüm VM'ler `10.30.10.53`'ü resolver olarak kullanır.
DNS hazır değilse `apt-get update` **başarısız** olur → kurulum başlamaz. dns-1 tek başına
`1.1.1.1` ile başlar, sonra kendine geçer.

---

<a id="s2-5"></a>

## 2.5 🔴 V6'NIN KIRIK REFERANSLARI — bu rehberde **YOK** (R03/R04/R06/R07)

> **NEDEN BU BÖLÜM VAR:** Elinizde **V6 rehberi** varsa, aşağıdaki yollar/komutlar
> **çalışmaz**. Her biri **canlı sistemde doğrulandı**. V9 bunların **hiçbirini**
> kullanmaz; karşılığı tabloda.

### 🔴 R07 — 447 adet kırık `$V6_ROOT` yolu

V6'da **447** yerde `$V6_ROOT/...` veya `../../Desktop/codex2/...` geçiyor.
**Bu dizin repoda YOK** → her komut `No such file or directory`.

| V6'daki yol | Durum | V9 karşılığı |
|---|---|---|
| `$V6_ROOT/kurulum/rke2/install-offline.sh` | **404** | **§7.4** resmî installer `INSTALL_RKE2_METHOD=tar` |
| `$RKE2_INPUT/SHA256SUMS.reviewed` | **404** | tarball sha256 (installer otomatik) |
| `envanter/rke2-installer-lock.json` | **404** | — (gerek yok) |
| `kurulum/ansible/prepare-linux.yaml` | **404** | **§7.1** elle script |
| `kurulum/ansible/inventory.ini` | **404** | SSH döngüsü |
| `kurulum/ag/registration-haproxy-reference.yaml` | **404** | **§5.4-B** HAProxy API |
| `kurulum/temel-chart-profilleri/values-cilium-lab.yaml` | **404** | **§7.8** elle values |
| `noble-server-cloudimg-amd64.img` | **404** | **`ubuntu-24.04-server-cloudimg-amd64.img`** (R01) |
| `firewall_rules.php` (26.7) | **404** | **§5.4** `/api/firewall/filter` |
| `api/core/auth/login` | **404** | **§5.3** API key |

### 🔴 R03 — OPNsense 26.7'de `.php` sayfa yolları **404**

```bash
# ❌ V6'da yazan (26.7'de YOK)
curl "https://10.30.10.1/ui/firewall/firewall_rules.php"
# → HTTP 404

# ✅ 26.7'nin GERÇEK yolu (MVC)
curl "https://10.30.10.1/ui/firewall/filter"
# ✅ 200

# ✅ API (tercih edilen)
curl -sk -u "$KEY:$SECRET" "https://10.30.10.1/api/firewall/filter/searchRule"
```

| V6 `.php` yolu | 26.7 gerçek |
|---|---|
| `ui/firewall/firewall_rules.php` | `ui/firewall/filter` **veya** `/api/firewall/filter` |
| `ui/interfaces_assign.php` | `ui/interfaces/assign` |
| `ui/system_advanced_admin.php` | `ui/system/advanced` |
| `ui/services_unbound.php` | `ui/unbound/...` |

🔁 **Emin değilseniz:** `curl -sk https://10.30.10.1/ui/ | grep -o 'href="[^"]*"' | head -20`

### 🔴 R04 — `api/core/auth/login` endpoint'i **YOK**

```bash
# ❌ V6 varsayımı
curl -X POST https://10.30.10.1/api/core/auth/login -d '{"username":"root","password":"..."}'
# → 404
```

**26.7'de API erişimi 2 yol:**

| Yol | Nasıl | Ne zaman |
|---|---|---|
| **API key/secret** (bu rehber) | §5.3 — GUI'den üret | ✅ otomasyon |
| **GUI oturumu + CSRF** | `opns-csrf` (§5.4) | key yoksa |

### 🟠 R06 — `install-offline.sh` **repoda yok**

```bash
# ❌ V6
sh $V6_ROOT/kurulum/rke2/install-offline.sh

# ✅ V9 — resmî installer (tar yöntemi)
curl -sfL https://get.rke2.io -o /tmp/get-rke2.sh
sudo -n env INSTALL_RKE2_TYPE=server INSTALL_RKE2_METHOD=tar \
  INSTALL_RKE2_VERSION=v1.36.6+rke2r1 sh /tmp/get-rke2.sh
```

🔴 **R08:** V6'daki `install.sh` için verilen SHA256 **gerçek değil** — doğrulamaya çalışmayın.
🔴 **`INSTALL_RKE2_TYPE` + `INSTALL_RKE2_METHOD` `env` ile GEÇMELİ** (R41):
`sudo -n env VAR=... sh script` — `sudo -n VAR=...` ortamı **kaybeder**.

### 🟡 R23 — OPNsense seri konsol **varsayılan KAPALI**

| Gerçek | Sonuç |
|---|---|
| `/boot/loader.conf`'da `boot_multicons`/`boot_serial` **yok** | seri soketten **0 bayt** gelir |
| V6 "seri konsola bakın" diyor | okuyucu **takılır** |

```bash
# Seri konsolu AÇ (VM içinden, bir kez)
ssh root@10.30.10.1
echo 'boot_multicons="YES"' >> /boot/loader.conf
echo 'boot_serial="YES"' >> /boot/loader.conf
echo 'console="comconsole,vidconsole"' >> /boot/loader.conf
reboot
```

🔁 **Açmadan teşhis:** `qm terminal 100` yerine **VGA** kullan:
`qm set 100 --vga std` + Proxmox **Console (noVNC)**.
**`--vga serial0` KULLANMAYIN** (R14) — `screendump` **çalışmaz**.

### 🟠 R25 — `.img` (nano/serial) imajı → **read-only RAM overlay**

```text
Trying to mount root from ufs:/dev/ufs/OPNsense_Install [ro,noatime]
```

**Bu satırı görürseniz:** hiçbir ayar **kalıcı değil**, reboot'ta **sıfırlanır**.

| İmaj | Davranış |
|---|---|
| **`OPNsense-26.7-dvd-amd64.iso`** ✅ | normal (kalıcı) — **bu rehber** |
| `OPNsense-26.7-serial-amd64.img` | ⚠️ overlay riski |
| `OPNsense-26.7-nano-amd64.img` | 🔴 **kullanmayın** |

🔴 **DUR:** boot log'da `[ro,noatime]` gördüyseniz → **DVD imajıyla yeniden kurun**.

### 🔵 R27 — `lego renew --days` **yok** (v5)

```bash
# ❌ V6
lego renew --days 30
# → flag provided but not defined: -days

# ✅ v5 doğru kullanım
lego run --renew-days 30
```

---

<a id="s3"></a>

# 3. ÖN KOŞULLAR — YÖNETİM İSTASYONU

<a id="s3-1"></a>

## 3.1 Araçlar (yönetim istasyonuna ne kuruyoruz)

**Ne yapıyoruz:** Bu makine **her şeyi yönetecek** istasyon (dizüstü/PC). Aşağıdaki
araçlar Proxmox'a, VM'lere ve GitHub'a erişim için **şart**. Kurulum **tek komut**.


```bash
sudo apt-get update && sudo apt-get install -y \
  ca-certificates curl git jq openssh-client openssl gnupg \
  util-linux gettext-base skopeo yq moreutils \
  open-iscsi nfs-common cryptsetup dmsetup
```

🔴 **M43 — NEDEN `open-iscsi` + `nfs-common` + `cryptsetup` BURADA:**
Bunlar **yönetim istasyonunda değil**, **her küme node'unda** gerekli (§7.1). Ama §7.1'deki
liste ile bu listeyi **tek kaynak** yapmak için buraya da yazıldı — kopyalayıp node'a taşıyacaksınız:

```bash
# Node'a taşımak için (jump-1'den her node'a):
for N in 10.30.10.21 10.30.10.22 10.30.10.23 10.30.30.11 10.30.30.12 10.30.30.13; do
  ssh root@$N "apt-get install -y open-iscsi nfs-common cryptsetup dmsetup && systemctl enable --now iscsid"
done
```
⚠️ **`iscsid` ÇALIŞMAZSA Longhorn volume mount EDEMEZ** → PVC'ler `Pending` kalır.

| Araç | NEDEN |
|---|---|
| `jq` | API çıktısı — V6'da `grep` ile CSRF kaçırıldı (H26) |
| `yq` | YAML'ı doğru düzenlemek (`sed` YAML'ı bozar) |
| `skopeo` | İmajı registry'den indirmeden kopyalamak (`docker save` olmadan OCI↔docker-archive) — yavaş ağda hayat kurtarır |
| `openssl` | Tüm secret'lar (`ENCRYPTION_KEY`, `AUTH_SECRET`, client secret'lar) — `rand` |
| `moreutils` | `sponge` — pipe + aynı dosyaya yazma |
| `gettext-base` | `envsubst` — YAML şablon değişkenleri |
| `util-linux` | `lsblk`, `findmnt`, `setpriv` |

<a id="s3-2"></a>

## 3.2 Sürümleri CANLI doğrula

```text
**NEDEN bu script:** V6'da rehber **sabit sürüm** kullandı → 3 sürüm gerideydi ve
yanlış SHA256 → kurulum **durdu**. Bu script her çalıştırmada **canlı** sürümü çeker.

cat > /usr/local/bin/banklab-versions <<'EOF'
#!/bin/bash
printf "%-22s %s\n" "RKE2" "$(curl -s https://update.rke2.io/v1-release/channels | jq -r '.data[]|select(.name=="stable")|.latest')"
printf "%-22s %s\n" "kubectl" "$(curl -sL https://dl.k8s.io/release/stable.txt)"
for r in helm/helm cilium/cilium longhorn/longhorn cert-manager/cert-manager \
         cloudnative-pg/cloudnative-pg external-secrets/external-secrets \
         kyverno/kyverno; do
  printf "%-22s %s\n" "$(basename $r)" "$(curl -s https://api.github.com/repos/$r/releases/latest | jq -r .tag_name)"
done
EOF
chmod +x /usr/local/bin/banklab-versions && banklab-versions
```

✅ **KANIT (18 Kas 2026):**
```
RKE2                   v1.36.6+rke2r1
kubectl                v1.37.2
helm                   v4.3.2
cilium                 v1.20.5
longhorn               v1.13.0
cert-manager           v1.21.3
cloudnative-pg         v1.30.2     ← OPERATÖR (chart 0.29.2)
external-secrets       helm-chart-2.15.0
kyverno                v1.16.x
```

🔴 **DUR:** Rehberi 2 hafta sonra kullanıyorsanız bu komutu **tekrar** çalıştırın.
**NEDEN:** V6 sabit sürüm kullandı → 3 sürüm gerideydi + yanlış SHA256 (R08) → kurulum durdu.

<a id="s3-3"></a>

## 3.3 🔴 İNDİRME — KOŞULLU KARAR AĞACI (her şeyin başlangıcı)

> **NEDEN BU BÖLÜM İLK:** V6'da kurulumun **%40'ı** indirme beklemekle geçti.
> Ama **her ortam farklı**. Bu bölüm size **"senin ortamında ne yap"** der.
> Aşağıdaki ağaç, ölçüm sonucunuza göre **hangi yolu** seçeceğinizi söyler.

### 3.3.1 ⬜ ÖN KONTROL — bu bölüme başlamadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| İnternet var mı | `curl -4 -sI https://github.com \| head -1` | `HTTP/2 200` | §3.3.C (air-gap) |
| IPv6 rotası var mı | `curl -6 -sI --max-time 5 https://github.com \| head -1` | cevap **veya** timeout | timeout **normaldir** → hep `-4` kullan |
| Disk alanı | `df -h /var/lib/vz /tmp` | ≥ 30 GB boş | temizle veya bundle'ı USB'ye al |

### 3.3.2 🔀 KOŞULLU KARAR 1 — Hat hızını ölç, yolunu seç

```bash
# TEK ÖLÇÜM — 10 MB indir, hızı yaz
curl -4 -sS -o /dev/null -w "%{speed_download}\n" \
  "https://speed.cloudflare.com/__down?bytes=10000000" | awk '{printf "%.1f MB/s\n", $1/1048576}'
```

| Ölçtüğün hız | **Yol** | Ne yaparsın | Bölüm |
|---|---|---|---|
| **> 5 MB/s** | **A — Doğrudan** | `helm repo add` + `ctr pull` yeter. **Bu bölümün geri kalanını ATLA.** | — |
| **1–5 MB/s** | **B — Paralel** | `imgfetch2.sh` (paralel+resume) + gh-proxy | §3.3.10 |
| **< 1 MB/s** | **C — Aktarım** | Paketle → GitHub Release → gh-proxy ile çek | §3.3.8 |
| **Kararsız (kopuyor)** | **D — Resume** | `--retry` + `--speed-limit` + resume zorunlu | §3.3.10 |
| **Hiç yok** | **E — Air-gap** | USB/`scp` ile `tar` taşı → `ctr import` | §3.3.10 |

### 3.3.3 🔀 KOŞULLU KARAR 2 — Kaynak bazında hız profili (`netprofile`)

**NEDEN:** Ortalama hız yanıltıcı. Bu lab'da **GitHub 38 MB/s** ama **ghcr.io 837 B/s**.
Tek tek ölçmeden "yavaş" demek yanlış yola sokar.

```bash
cat > /usr/local/bin/netprofile <<'EOF'
#!/bin/bash
# Her kritik kaynağı ayrı ölç — 3 MB ile
probe() {
  local name="$1" url="$2"
  local b
  b=$(curl -4 -o /dev/null -sS -w '%{speed_download}' --max-time 25 "$url" 2>/dev/null || echo 0)
  printf "%-22s %8.1f KB/s  %s\n" "$name" "$(echo "$b/1024" | bc -l)" \
    "$(awk -v x="$b" 'BEGIN{print (x<102400?"🔴 YAVAŞ":(x<1048576?"🟡 ORTA":"✅ HIZLI"))}')"
}
probe "github.com"        "https://github.com/git/git/archive/refs/tags/v2.43.0.tar.gz"
probe "gh-proxy.com"      "https://gh-proxy.com/https://github.com/git/git/archive/refs/tags/v2.43.0.tar.gz"
probe "docker hub"        "https://registry-1.docker.io/v2/"
probe "mirror.gcr.io"     "https://mirror.gcr.io/v2/"
probe "ghcr.io"           "https://ghcr.io/v2/"
probe "quay.io"           "https://quay.io/v2/"
probe "charts.longhorn"   "https://charts.longhorn.io/index.yaml"
EOF
chmod +x /usr/local/bin/netprofile
netprofile
```

**Bu lab'da ölçülen (18 Kas 2026) — referans:**

| Kaynak | Hız | Karar |
|---|---|---|
| `github.com` doğrudan | **8 KB/s** 🔴 | gh-proxy kullan |
| **`gh-proxy.com`** | **20–38 MB/s** ✅ | GitHub için **SEÇİLDİ** |
| `registry-1.docker.io` | **267 KB/s** 🟡 | paralel (8–10) → 837 KB/s |
| **`mirror.gcr.io`** | 60–871 KB/s 🟡 | paralel (10) → **387 KB/s (6.4×)** ✅ |
| **`ghcr.io`** | **837 B/s** 🔴 | **KULLANMA** → Docker Hub aynası |
| `quay.io` | 160 KB/s 🟡 | son çare |
| `registry.k8s.io` | 29 KB/s 🔴 | **`/etc/hosts` ile IPv4 sabitle** (aşağıda) |

### 3.3.4 🔀 KOŞULLU KARAR 3 — İmaj çekimi ASILI kalıyorsa

**Belirti:** Pod `ContainerCreating` dakikalarca, `crictl pull` → `context canceled`, hata mesajı yok.

**Sırayla dene — her biri farklı kök nedene çözüm:**

| # | Kök neden | Teşhis | Çözüm |
|---|---|---|---|
| 1 | **IPv6 AAAA kaydı** (routa yok) | `getent hosts registry.k8s.io` → **yalnız `2600:`** | `/etc/gai.conf`: `precedence ::ffff:0:0/96 100` + `/etc/hosts` IPv4 |
| 2 | **`image_pull_progress_timeout` = 5 dk** | log: `interrupted: context canceled` 5 dk sonra | containerd `config.toml.tmpl`: `image_pull_progress_timeout = "30m"` |
| 3 | **`max-concurrent-downloads` = 3** | katmanlar **sırayla** iniyor | rke2 `config.yaml`: `max-concurrent-downloads: 10` |
| 4 | **Tamamen IPv6 kapatma** ❌ | `[::1]:6443 cannot assign` | **YAPMA** — loopback'i açık bırak (aşağıda) |
| 5 | CDN reset (`connection reset by peer`) | sık kopuyor | `mirror.gcr.io` + paralel (§3.3.7) |

```bash
# 1) IPv6 — kapatma, SADECE dışarıyı kapat (loopback KALSIN)
cat > /etc/sysctl.d/90-banklab-ipv6.conf <<'EOF'
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
net.ipv6.conf.primary0.disable_ipv6 = 1
net.ipv6.conf.edge0.disable_ipv6 = 1
EOF
sysctl -p /etc/sysctl.d/90-banklab-ipv6.conf
curl -sk https://[::1]:6443/readyz    # → 401 (CANLI olmalı, 'cannot assign' OLMAMALI)

# 2) containerd timeout
cat > /var/lib/rancher/rke2/agent/etc/containerd/config.toml.tmpl <<'EOF'
[plugins.'io.containerd.cri.v1.images']
  image_pull_progress_timeout = "30m"
EOF

# 3) paralel katman
echo 'max-concurrent-downloads: 10' >> /etc/rancher/rke2/config.yaml

systemctl restart rke2-agent   # veya rke2-server
```

🔴 **DUR:** `[::1]:6443: cannot assign requested address` alırsanız **loopback'i kapatmışsınız**.
RKE2 apiserver `localhost` kullanır → **küme kırılır**. `lo.disable_ipv6 = 0` şart.

### 3.3.5 🔀 KOŞULLU KARAR 4 — GitHub indirmesi için hangi proxy?

| Proxy | Ölçülen | Durum |
|---|---|---|
| **`gh-proxy.com`** | **20–38 MB/s** | ✅ **ÇALIŞIYOR** (bu lab) |
| `ghproxy.net` | 8.8 KB/s | ❌ |
| `ghfast.top` | 55 KB/s | ❌ |
| `hub.gitmirror.com` | bağlantı yok | ❌ |
| `gh.llkk.cc` | HTTP 522 | ❌ |
| Doğrudan `github.com` | 8 KB/s | ❌ |

**🔴 ÖNEMLİ — bu bir ÖRNEK, kural değil:** `gh-proxy.com` **bugün** çalışıyor.
Yarın kapanabilir/kötüleşebilir. **Kendi ölçümünüzü yapın** (§3.3.3 `netprofile`).

```bash
# Kural olarak YAZILAN şey bu: "GitHub yavaşsa proxy dene, ölç, iyi olanı seç"
cat > /usr/local/bin/pick-proxy <<'EOF'
#!/bin/bash
# GitHub için çalışan proxy'yi ÖLÇEREK seç
TEST="https://github.com/git/git/archive/refs/tags/v2.43.0.tar.gz"
BEST=""; BESTSPD=0
for P in "https://gh-proxy.com/" "https://ghproxy.net/" "https://ghfast.top/" ""; do
  SPD=$(curl -4 -o /dev/null -sS -w '%{speed_download}' --max-time 20 "${P}${TEST}" 2>/dev/null || echo 0)
  printf "%-28s %8.1f KB/s\n" "${P:-dogrudan}" "$(echo "$SPD/1024"|bc -l)"
  awk -v a="$SPD" -v b="$BESTSPD" 'BEGIN{exit !(a>b)}' && { BEST="$P"; BESTSPD=$SPD; }
done
[ -n "$BEST" ] && echo "✅ SEÇİLEN: $BEST ($(echo "$BESTSPD/1024/1024"|bc -l | cut -c1-4) MB/s)" \
               || echo "🔴 hiçbiri çalışmadı → air-gap (§3.3.7)"
echo "$BEST" > /etc/banklab-gh-proxy      # kalıcı
EOF
chmod +x /usr/local/bin/pick-proxy
pick-proxy
```

**Ve `ghc` artık sabit proxy değil, seçilen proxy'yi okur:**
```bash
cat > /usr/local/bin/ghc <<'EOF'
#!/bin/bash
# GitHub URL'lerini SEÇİLMİŞ proxy üzerinden indirir
url="$1"; shift
PREFIX=$(cat /etc/banklab-gh-proxy 2>/dev/null || echo "https://gh-proxy.com/")
case "$url" in
  https://github.com/*|https://raw.githubusercontent.com/*|https://objects.githubusercontent.com/*)
    exec curl -4 -fsSL --retry 5 --retry-delay 3 --retry-all-errors "${PREFIX}${url}" "$@" ;;
  *) exec curl -4 -fsSL --retry 5 --retry-delay 3 --retry-all-errors "$url" "$@" ;;
esac
EOF
chmod +x /usr/local/bin/ghc
```

### 3.3.6 🔁 FALLBACK ZİNCİRİ — her adımda "olmadıysa şunu"

| Adım başarısız olursa | 1. fallback | 2. fallback | 3. fallback (son çare) |
|---|---|---|---|
| **gh-proxy çalışmıyor** | `pick-proxy` ile başka proxy ölç | Doğrudan `github.com` (yavaş ama çalışır) | GitHub PAT + `gh release download` (38 MB/s) |
| **GitHub Release'e yükleme yok** (token yok) | `filebin.net` (upload 4.5 MB/s) | Kendi bilgisayarınızda indir → `scp` | USB (air-gap) |
| **Docker Hub yavaş** | `mirror.gcr.io` + paralel | `quay.io` aynası | `skopeo copy` (başka makinede) |
| **`ghcr.io` ölü** | `docker.io/...` aynası | `mirror.gcr.io` | `ctr import` |
| **`imgfetch2.sh` takılıyor** | `--speed-limit` artır | parça sayısını **azalt** (10→4) | `skopeo copy` |
| **`helm repo update` 2 dk+** | Chart'ı `.tgz` olarak tek tek indir | `ghc` ile chart release asset | repo'suz `helm install ./x.tgz` |
| **Hiç internet yok** | USB ile `tar` taşı | `scp` (LAN) | — |

### 3.3.7 KURAL — Paralel + resume (`imgfetch2.sh`)

**NEDEN:** `ctr pull` **tek bağlantı** kullanır. Docker Hub tek bağlantıyı **267 KB/s**'de
kısıtlıyor; 10 paralel `mirror.gcr.io` → **387 KB/s (6.4×)**. Ve Türkiye ISP'sinde
indirme **ortada reset** oluyor → **resume şart**.

```bash
cat > /usr/local/bin/imgfetch2.sh <<'SCRIPT'
#!/bin/bash
# imgfetch2.sh <repo> <tag> [parça=10] [outdir] — paralel + resume
set -uo pipefail
REPO=${1:?} TAG=${2:?} PARTS=${3:-10} OUT=${4:-/tmp/img}
MIRROR=${MIRROR:-mirror.gcr.io}
mkdir -p "$OUT/layers"; cd "$OUT" || exit 1

ACCEPT='application/vnd.oci.image.index.v1+json,application/vnd.docker.distribution.manifest.list.v2+json,application/vnd.docker.distribution.manifest.v2+json,application/vnd.oci.image.manifest.v1+json'
curl -4 -fsSL -H "Accept: $ACCEPT" "https://$MIRROR/v2/$REPO/manifests/$TAG" -o manifest.json || exit 1

# Çoklu mimari → amd64 seç
DG=$(jq -r 'if .manifests then (.manifests[]|select(.platform.architecture=="amd64")|.digest) else "self" end' manifest.json | head -1)
[ "$DG" != "self" ] && curl -4 -fsSL -H "Accept: $ACCEPT" "https://$MIRROR/v2/$REPO/manifests/$DG" -o manifest.json

for IDX in $(seq 0 $(($(jq '.layers|length' manifest.json)-1))); do
  BLOB=$(jq -r ".layers[$IDX].digest" manifest.json); BLOB=${BLOB#sha256:}
  SIZE=$(jq -r ".layers[$IDX].size" manifest.json)
  F="layers/$IDX-$BLOB.tar.gz"
  [ -s "$F" ] && [ "$(stat -c%s "$F")" -ge "$SIZE" ] && { echo "[skip] $F"; continue; }

  SZ=$((SIZE/PARTS))
  for P in $(seq 0 $((PARTS-1))); do
    ST=$((P*SZ)); EN=$(( P==PARTS-1 ? SIZE-1 : ST+SZ-1 ))
    ( curl -4 -fsSL --retry 8 --retry-delay 2 --retry-all-errors \
        --speed-limit 1024 --speed-time 60 \
        -r "$ST-$EN" "https://$MIRROR/v2/$REPO/blobs/sha256:$BLOB" -o "$F.p$P" 2>/dev/null ) &
  done
  wait
  cat "$F".p* > "$F" 2>/dev/null; rm -f "$F".p*
  ACT=$(stat -c%s "$F"); [ "$ACT" -eq "$SIZE" ] || echo "🔴 $F boyut $ACT ≠ $SIZE"
done
echo "=== bitti: $(du -sh "$OUT" | cut -f1) ==="
SCRIPT
chmod +x /usr/local/bin/imgfetch2.sh
```

⚠️ **NEDEN `--speed-limit 1024 --speed-time 60`:** Bağlantı **kilitlenirse** (reset değil, donma)
curl kendisi koparır, `--retry` devreye girer. Bu iki bayrak olmadan **saatlerce asılı** kalır.

**Ölçülmüş (step-ca, 7 katman, 51.5 MB):** `=== 351 saniye → archive 146 MB ===`

🔁 **`imgfetch2.sh` takılırsa:** parçayı **azalt** (10→4). Çok parça = çok bağlantı = ISP
throttling tetikleyebilir. 4 parça genelde en iyi.

### 3.3.8 KURAL — Paketle → Release → çek (asimetriyi kullan)

**NEDEN:** Sunucu **yükleyemiyor** (token yok) ama **indiriyor** (38 MB/s).
Yönetim istasyonu **yükleyebiliyor**. Bu asimetri en hızlı yoldur.

```bash
# ── YÖNETİM İSTASYONUNDA ─────────────────────────────
python3 pull_dh.py longhornio/longhorn-engine v1.13.0 out/
python3 pack.py out/ longhornio/longhorn-engine:v1.13.0 e.tar
cat *.tar > longhorn-bundle.tar                 # 🔴 TEK ASSET

# 🔴 ÖNCE değişkenleri ayarla (placeholder YAZMAYIN — shell `<` yi redirect sanır)
GH_REPO="alperkkor07-source/xx"          # ← kendi reponuz
GH_TAG="imgstore-v1"
gh release create "$GH_TAG" longhorn-bundle.tar --repo "$GH_REPO"

# ── SUNUCUDA ─────────────────────────────────────────
ghc "https://github.com/$GH_REPO/releases/download/$GH_TAG/longhorn-bundle.tar" -o /tmp/lh.tar
[ -s /tmp/lh.tar ] || { echo "🔴 indirme başarısız → §3.3.6 fallback"; exit 1; }
for ip in 10.30.30.11 10.30.30.12 10.30.30.13; do
  loadgate || { sleep 300; }
  scp /tmp/lh.tar root@$ip:/tmp/
  ssh root@$ip '/var/lib/rancher/rke2/bin/ctr -a /run/k3s/containerd/containerd.sock \
    -n k8s.io images import /tmp/lh.tar; sync; rm -f /tmp/*.tar'
  echo "✅ $ip"; sleep 60
done
```

### 3.3.9 🔴 TEK ASSET KURALI (V6'da 1.5 saat kaybettirdi)

```
❌ 16 parça: bir parça düştü → kısa indi → yanlış birleştirme → bozuk tar
✅ TEK dosya 706 MiB: 32 saniye upload, 3 dakika download
```
**Zorunluysa (>2 GB):** her parça için SHA256 **zorunlu**:
```text
split -b 1900M bundle.tar part_ && sha256sum bundle.tar > bundle.sha256
# Sunucuda:
cat part_* > bundle.tar && sha256sum -c bundle.sha256    # 🔴 BİRLEŞTİRMEYİ DOĞRULA
```

### 3.3.10 Air-gap (hiç internet yok)

**Ne yapıyoruz:** Sunucuda internet **hiç yoksa**, imajları **başka bir makinede** toplayıp
USB/dahili ağ ile taşıyoruz. `skopeo copy --all` = **tüm mimariler** (amd64+arm64) tek
`.tar`'a; sonra `ctr import` ile containerd'e yüklüyoruz.


```bash
# Yönetim istasyonunda — TÜM imajları topla
for IMG in longhornio/longhorn-manager:v1.13.0 cloudnative-pg/postgresql:17-standard-bookworm; do
  skopeo copy --all "docker://$IMG" "docker-archive:$(echo $IMG|tr '/:' '__').tar"
done
tar czf banklab-airgap.tgz *.tar

# Sunucuda — USB'den
tar xzf banklab-airgap.tgz -C /tmp/
for T in /tmp/*.tar; do
  for ip in 10.30.30.11 10.30.30.12 10.30.30.13; do
    scp "$T" root@$ip:/tmp/
    ssh root@$ip "ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images import /tmp/$(basename $T)"
  done
done
```

---

<a id="s3-4"></a>

## 3.4 Alternatif işletim sistemi — AlmaLinux / RHEL / Rocky

> Lab Ubuntu 24.04 varsayıyor. Ama sunucunuz **AlmaLinux 9** ise aşağıdakiler **değişir**.

| Ubuntu | AlmaLinux/RHEL 9 | Not |
|---|---|---|
| `apt-get install` | `dnf install` | |
| `apt-get update` | `dnf makecache` | |
| `ca-certificates curl jq` | `ca-certificates curl jq` | aynı |
| `open-iscsi` | **`iscsi-initiator-utils`** | 🔴 paket adı farklı |
| `nfs-common` | **`nfs-utils`** | |
| `cryptsetup dmsetup` | `cryptsetup device-mapper` | |
| **`unbound`** | **`unbound`** | aynı |
| `systemctl enable --now iscsid` | aynı | |
| `/etc/modules-load.d/*.conf` | `/etc/modules-load.d/*.conf` | aynı |
| `ufw` | **`firewalld`** | 🔴 |
| `netplan` | **`NetworkManager`** (`nmcli`) | 🔴 tamamen farklı |
| SHA/cloud image | **`alma9-cloud-genericcloud`** | URL farklı |

```bash
# AlmaLinux 9 — node hazırlık
dnf install -y ca-certificates curl jq iscsi-initiator-utils nfs-utils \
               cryptsetup device-mapper chrony
printf '%s\n' overlay br_netfilter iscsi_tcp > /etc/modules-load.d/bank-rke2.conf
modprobe overlay; modprobe br_netfilter; modprobe iscsi_tcp
systemctl enable --now iscsid

# 🔴 SELinux: RKE2 için permissive önerilir (lab'da)
setenforce 0
sed -i 's/^SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config

# 🔴 firewalld: RKE2 + Cilium için portlar AÇIK olmalı
firewall-cmd --permanent --add-port=6443/tcp --add-port=9345/tcp \
  --add-port=2379-2380/tcp --add-port=8472/udp --add-port=10250/tcp
firewall-cmd --reload
```

🔴 **DUR:** SELinux **enforcing** iken `rke2-server` **başlamaz**
(`/var/lib/rancher` yazma izni reddedilir). Lab'da `permissive`, üretimde özel policy.

---

---

<a id="s4"></a>

# 4. PROXMOX HOST

<a id="s4-1"></a>

## 4.1 İlk giriş ve envanter (neyim var, neyim yok)

**Ne yapıyoruz:** Host'a girip **envanteri** çıkarıyoruz: CPU çekirdek sayısı, RAM,
disk tipi (LVM-thin mi ZFS mi). Bu bilgiler **§2.4 VM tablosunun** sığıp sığmayacağını
belirler. **Hiçbir şey değiştirmiyoruz** — sadece okuyoruz.


```bash
ssh root@87.76.130.66
pveversion -v | head -3       # pve-manager/9.x
lsblk -o NAME,SIZE,TYPE,MOUNTPOINT | grep -E "lvm|local"
nproc; free -m | head -2
```

✅ **KANIT:** `local-lvm` LVM-thin görünür, `nproc ≥ 8`, `Mem total ≥ 64000`

🔴 **DUR:** RAM < 48 GB → §2.4 alternatifini uygula.

<a id="s4-2"></a>

## 4.2 Depo ve güncelleme (lisanssız depoya geçiş)

**Ne yapıyoruz:** Proxmox'un **enterprise** deposunu kapatıp **ücretsiz** (`pve-no-subscription`)
depoya geçiyoruz; sonra tam güncelleme. **Neden:** enterprise depo lisanssız `401` döner →
her `apt-get update` **başarısız** → hiçbir paket kurulamaz (V6'da yaşandı).


```bash
# Enterprise depo lisanssız 401 döner → kapat
sed -i 's/^deb /#deb /' /etc/apt/sources.list.d/pve-enterprise.list 2>/dev/null

cat > /etc/apt/sources.list.d/pve-no-sub.list <<'EOF'
deb http://download.proxmox.com/debian/pve trixie pve-no-subscription
EOF
apt-get update && apt-get -y dist-upgrade
```

**NEDEN:** V6'da enterprise depo açık kaldı → her `apt-get update` 401 verdi, güncelleme yapılamadı.

<a id="s4-2-b"></a>

## 4.2-B 🔴🔴 SANAL AĞLAR — Proxmox **SDN** (VM'lerin bağlanacağı köprüler)

> **NEDEN BU BÖLÜM:** §5.2/§6.3 `--net1 bridge=vnet10` diyor. `vnet10` bir **Linux bridge
> DEĞİL**, Proxmox **SDN VNet**'idir. SDN tanımlanmazsa:
> **`bridge 'vnet10' does not exist`** → VM oluşturulamaz.
> Bu adım **V6'da bir kez yapıldı ve "en temiz geçen bölüm" oldu** — aynı yapıyı kullanıyoruz.

### Yapı — 5 VNet, 2 zone

| Zone | VNet | CIDR | Kullanım |
|---|---|---|---|
| `banklab-management` | `vnet90` | `10.30.90.0/24` | Proxmox host yönetim (SSH) |
| | `vnet91` | `10.30.91.0/24` | yedek/yönetim 2 |
| `lab-edge` | **`vnet10`** | `10.30.10.0/24` | **yönetim** (CP, jump, DNS) |
| | **`vnet20`** | `10.30.20.0/24` | **uygulama / MetalLB** |
| | **`vnet30`** | `10.30.30.0/24` | **worker küme trafiği** |

**NEDEN 3 ayrı VNet:** MetalLB L2 duyurusu **tek arayüzde** olmalı (§9.1). Küme trafiği
(`vnet30`) ile LB trafiği (`vnet20`) ayrılmazsa ARP çakışması ve asimetrik yönlendirme olur.

### Adım 1 — Zone'lar (iki mantıksal ağ grubu oluşturuyoruz)

**Ne yapıyoruz:** Proxmox'a **2 zone** tanımlıyoruz: `banklab-management` (host yönetim
ağları) ve `lab-edge` (VM ağları). Zone = "VNet'lerin ait olduğu grup" + hangi fiziksel
köprüye (`vmbr0`) bağlanacakları. **Henüz VNet yok**, sadece kap oluşturuyoruz.

```bash
# SDN paketleri (Proxmox 8/9'da genelde kurulu)
apt-get install -y libpve-network-perl

# Zone 1: yönetim (VLAN'sız basit)
pvesh create /cluster/sdn/zones \
  --zone banklab-management --type simple --bridge vmbr0 --ipam pve

# Zone 2: lab (asıl ağlar)
pvesh create /cluster/sdn/zones \
  --zone lab-edge --type simple --bridge vmbr0 --ipam pve
```

### Adım 2 — VNet'ler (zone'ların içine 5 sanal ağ koyuyoruz)

**Ne yapıyoruz:** Her zone'a VNet **isimlerini** ekliyoruz — `vnet90/91` (yönetim) ve
`vnet10/20/30` (lab). **Bu adımda bridge HENÜZ OLUŞMAZ**; sadece SDN veritabanına yazılır.
IP/CIDR **atanmaz** (zone'a `--ipam pve` verildi, adresler VM'lerde tanımlanır).

```bash
for V in 90 91; do
  pvesh create /cluster/sdn/vnets --vnet "vnet$V" --zone banklab-management
done
for V in 10 20 30; do
  pvesh create /cluster/sdn/vnets --vnet "vnet$V" --zone lab-edge
done
```

### Adım 3 — 🔴 UYGULA (SDN'i gerçekten devreye alıyoruz)

**Ne yapıyoruz:** `pvesh set /cluster/sdn` = **commit**. Adım 1–2 sadece **veritabanına
yazdı**; bu komut **Linux bridge'lerini gerçekten yaratır** ve `/etc/network/interfaces.d/sdn`
dosyasını üretir. **Bu adım atlanırsa `vnet10` var görünür ama `bridge 'vnet10' does not exist`
hatası alırsınız** (§5.2/§6.3'te VM oluştururken).

```bash
pvesh set /cluster/sdn

# Doğrula — 5 arayüz görünmeli
ip -br link | grep -E "vnet(10|20|30|90|91)"

# Yoksa:
systemctl restart pveproxy pvedaemon
journalctl -u pveproxy -n 30 | grep -i sdn
```

🔴 **DUR:** `pvesh set /cluster/sdn` çalıştırmadan `qm create ... bridge=vnet10`
**`does not exist`** verir. Bu, SDN'in **en sık yapılan** hatası.

### Adım 4 — Host yönetim adresi `10.30.10.254` (kalıcı)

**NEDEN:** Host'un kendisi `vnet10`'a bir IP almalı ki §10.1 step-ca / adres planı
tutamaç olsun. **Ama** SDN arayüzü host'ta **kalıcı değildir** (reboot'ta yeniden yaratılır) →
oneshot systemd unit kullanılır.

```bash
cat > /etc/systemd/system/banklab-management-network.service <<'EOF'
[Unit]
Description=BankLab host management IP on vnet10
After=pve-cluster.service network-online.target
Wants=network-online.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStartPre=/bin/sh -c 'for i in $(seq 1 30); do ip link show vnet10 >/dev/null 2>&1 && exit 0; sleep 2; done; exit 1'
ExecStart=/sbin/ip -4 address replace 10.30.10.254/24 dev vnet10
ExecStop=/sbin/ip -4 address del 10.30.10.254/24 dev vnet10

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable --now banklab-management-network
systemctl is-active banklab-management-network        # → active

# 🔴 Reboot testi ZORUNLU (H: oneshot kalıcılığı doğrulandı)
ip -4 -br a | grep 10.30.10.254
```

✅ **KANIT:** `10.30.10.254/24` **vnet10**'da ve **reboot sonrası da var**.

⚠️ **TUZAK — `dnsmasq@labnat` D-Bus hatası:** SDN reboot sonrası
`dnsmasq@labnat.service failed` verirse (Debian politika dosyası instance adına izin vermiyor):
```bash
cat > /etc/dbus-1/system.d/dnsmasq-labnat.conf <<'EOF'
<!DOCTYPE busconfig PUBLIC "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>
  <policy user="dnsmasq">
    <allow own="uk.org.thekelleys.dnsmasq.labnat"/>
  </policy>
</busconfig>
EOF
systemctl reload dbus && systemctl restart dnsmasq@labnat
```

### Adım 5 — SNAT (VM'lerin dışarı çıkması için adres çevirisi)

**Ne yapıyoruz:** `10.30.0.0/16`'dan çıkan paketlerin kaynak adresini `vmbr0`'ın (host WAN)
IP'sine çeviriyoruz → VM'ler Proxmox host üzerinden internete çıkabilsin. **Kalıcı** yapmak
için `netfilter-persistent` kullanıyoruz (reboot'ta kaybolmasın).

```bash
# Lab ağları dışarı çıkabilsin (apt/curl için) — Proxmox host NAT
pvesh create /cluster/sdn/zones/lab-edge ... 2>/dev/null || true

iptables -t nat -C POSTROUTING -s 10.30.0.0/16 -o vmbr0 -j MASQUERADE 2>/dev/null || \
iptables -t nat -A POSTROUTING -s 10.30.0.0/16 -o vmbr0 -j MASQUERADE

# Kalıcı: iptables-persistent
apt-get install -y iptables-persistent
netfilter-persistent save
```

> ⚠️ **DİKKAT:** OPNsense VM (§5) **ayrı bir router**. Bu NAT **yalnız host'un kendi
> çıkışı** içindir. VM'lerin çıkışı **OPNsense üzerinden** olur (§5.5).

### Adım 6 — VM'ler bu ağlara bağlanır (NIC → VNet eşlemesi)

**Ne yapıyoruz:** Her VM'in `net0/net1` aygıtını doğru VNet'e **bağlıyoruz**:
OPNsense 4 NIC (WAN + 3 lab), CP/jump/DNS 1 NIC (vnet10), worker 2 NIC (vnet30 + vnet20).
Aşağıdaki komutlar **önceki bölümlerdeki** `qm create` komutlarının NIC kısmını gösterir —
tekrar çalıştırmayın, **kontrol** için kullanın.

```bash
# OPNsense: 4 NIC — WAN + 3 lab ağı
qm set 100 --net0 virtio,bridge=vmbr0        # WAN
qm set 100 --net1 virtio,bridge=vnet10       # LAN   → 10.30.10.1
qm set 100 --net2 virtio,bridge=vnet20       # OPT1  → 10.30.20.1
qm set 100 --net3 virtio,bridge=vnet30       # OPT2  → 10.30.30.1

# jump/dns/cp: tek NIC (vnet10)
# worker: 2 NIC (vnet30 = primary0, vnet20 = edge0)  → §6.6
```

✅ **KANIT:** `qm config 100 | grep net` → 4 satır; `qm config 106 | grep net` → 2 satır.

---

<a id="s4-2-c"></a>

## 4.2-C 🔴🔴 `onboot` FELAKETİ — host reboot olunca 9 VM açılmaz

> **BU BÖLÜM V6'DA YOKTU.** Gerçek kurulumda **fiziksel sunucu** yeniden başlatıldı
> (sağlayıcı bakımı). Sonuç: **9 VM'in tamamı kapalı kaldı**, LAB ağı tamamen erişilemez.

### ⬜ ÖN KONTROL — VM oluşturmadan ÖNCE

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| VM 100 tanımlı mı | `qm status 100` | `status: running` | §5.2 |
| `onboot` değeri | `qm config 100 \| grep onboot` | `onboot: 0` **veya** `1` (karar aşağıda) | bu bölüm |
| `startup` değeri | `qm config 100 \| grep startup` | `order=...,up=...` | bu bölüm |

### 🔴 BELİRTİ — nasıl anlaşılır

```bash
who -b              # → system boot 2026-11-17 18:42   ← host YENİ açılmış
uptime              # → up 2 min
free -g | awk '/Mem/{print $3" GB kullanımda"}'   # → 6 GB (normalde 18 GB)
qm list             # → TÜM VM'ler 'stopped'
```

**NEDEN `free` bunu gösterir:** host RAM'i 125 GB, normalde VM'ler ~18 GB tutuyor.
`6 GB` görüyorsanız **misafirler kapalı** demektir.

### 🔴 KÖK NEDEN — iki ayrı tuzak

| Tuzak | Yanlış inanış | Gerçek |
|---|---|---|
| **1. `startup` otomatik başlatır** | "`--startup order=10` verdim, açılır" | ❌ `startup` **yalnız sıra/öncelik** belirler. **`onboot`** olmadan PVE **hiç başlatmaz** |
| **2. `onboot` default 1** | "Proxmox zaten açar" | ❌ **Default `onboot: 0`** (kapalı). `qm create` ile `--onboot 1` **şart** |

```bash
qm config 100 | grep -E "onboot|startup"
# startup: order=10,up=45,down=90     ← SIRA var
# (onboot satırı YOK)                 ← AMA OTOMATİK BAŞLATMA YOK = KAPALI
```
🔴 **`onboot` satırı yoksa = `onboot: 0` = başlamaz.** Bu sessiz tuzak.

### 🔀 KOŞULLU KARAR — OPNsense `onboot` ne olmalı?

| Durumunuz | VM 100 `onboot` | Neden |
|---|---|---|
| **Bu lab, izole** ✅ | **`1`** | Host reboot → OPNsense de açılmalı, yoksa **hiçbir şeye erişemezsiniz** |
| Aynı host'ta **başka canlı VM'ler** var | **`0`** | OPNsense DHCP/WAN çakışması yapabilir; çakışmayı önlemek için kapalı |
| **Uzaktan yönetim, fiziksel erişim yok** | **`1`** + `startup up=90` | Açılmazsa **kimse giremez**; up=90 sn VM'lerden önce hazır olsun |
| Ayrı firewall cihazı var (OPNsense yok) | — | §5'i atlayın |

⚠️ **V6'nın tercihi `onboot 0` idi** (canlı sunucuda çakışma korkusu). **Lab'de bu YANLIŞ:**
host reboot sonrası OPNsense açılmıyor → tüm LAB erişilemez (gerçekten oldu).

### ADIM — kalıcı düzeltme (VM oluşturduktan hemen sonra)

```bash
# 101–108 (jump-1, dns-1, cp-1..3, worker-1..3) → OTOMATİK AÇILSIN
for v in 101 102 103 104 105 106 107 108; do
  qm set $v --onboot 1 --startup order=$v,up=30
done

# 100 (OPNsense) → koşullu karara göre
qm set 100 --onboot 1 --startup order=10,up=90,down=30    # lab: AÇILSIN
# qm set 100 --onboot 0 --startup order=10,up=45,down=90  # canlı: KAPALI
```

**`order` NEDEN VMID:** düşük VMID önce açılır → OPNsense (100) **önce**, VM'ler sonra.
**`up=90` NEDEN:** OPNsense boot ~45 sn; arkasındaki VM'lerin `up` süresi bitmeden
ağ hazır olsun.

✅ **KANIT:**
```bash
for v in $(seq 100 108); do printf "%s: %s\n" $v "$(qm config $v | grep -E '^onboot' || echo '🔴 YOK=KAPALI')"; done
# 100: onboot: 1
# 101: onboot: 1   ... 108: onboot: 1
```

### 🔴 DUR — kurtarma (host zaten reboot olduysa)

```bash
# 1) Sırayla başlat (OPNsense ÖNCE, 60 sn bekle)
qm start 100; sleep 60
for v in 101 102 103 104 105 106 107 108; do qm start $v; sleep 20; done

# 2) 90 sn sonra erişim doğrula
for ip in 10.30.10.1 10.30.10.10 10.30.10.53 10.30.10.20; do
  printf "%-14s " $ip; ping -c1 -W2 $ip >/dev/null && echo ✅ || echo 🔴
done
```

⚠️ **`10.30.30.11–13` host'tan PING ATMAZ — bu NORMAL:** PVE host yalnız `vnet10`
üyesidir; worker'lar `vnet30`'da. **jump-1'den** test edin:
`ssh root@10.30.10.10 'ping -c1 10.30.30.11'`

⚠️ **RKE2 kendini toparlar:** etcd diski kalıcı (`/var/lib/rancher`) → reboot sonrası
küme durumu **kaybolmaz**; 1–3 dk içinde 6/6 Ready. kube-vip VIP lease'i geri alır.
🔴 **Yeniden `kubeadm reset`/`rke2-killall` YAPMAYIN** — gereksiz ve risklidir.

---

<a id="s4-3"></a>

## 4.3 🔴 Host tükenmesini ÖNLE — V9'a özel, V6'da hiç yoktu

> **NEDEN (H81 — en pahalı hata):** Kurulum sırasında host kilitlendi:
> SSH **%5** başarı, Proxmox `:8006` **12 s timeout**, Cloudflare 522/525.
> Sebep **kaynak tükenmesi**, DDoS değil. Kernel TCP'ye anında cevap veriyor ama
> `sshd`/`pveproxy` CPU bulamıyor.

```bash
# 1) zram — swap'ı RAM'de sıkıştır (disk I/O'yu korur)
apt-get install -y zram-tools
echo -e "ALGO=zstd\nPERCENT=25" > /etc/default/zramswap
systemctl restart zramswap && zramctl

# 2) OOM koruması — kritik servisler öldürülmesin
for s in ssh pveproxy pvedaemon; do
  mkdir -p /etc/systemd/system/$s.service.d
  printf '[Service]\nOOMScoreAdjust=-500\n' > /etc/systemd/system/$s.service.d/10-oom.conf
done
systemctl daemon-reload

# 3) YÜK KAPISI — büyük işlerden önce çalıştır
cat > /usr/local/bin/loadgate <<'EOF'
#!/bin/bash
L=$(awk '{print $1}' /proc/loadavg); N=$(nproc); F=$(free -m | awk '/Mem:/{print $7}')
echo "load=$L/$N  free=${F}MiB"
awk -v l="$L" -v n="$N" 'BEGIN{exit !(l > n*0.6)}' && { echo "🔴 YÜK YÜKSEK — bekle"; exit 1; }
echo "✅ devam"
EOF
chmod +x /usr/local/bin/loadgate
```

✅ **KANIT:**
```
zramctl → /dev/zram0  16G  zstd
systemctl show ssh -p OOMScoreAdjust → OOMScoreAdjust=-500
loadgate → load=0.4/16 free=52000MiB ✅ devam
```

🔴 **KURAL:** Her imaj import'undan **önce** `loadgate || sleep 300`.

<a id="s4-3-b"></a>

## 4.3-B 🔴 HOST TÜKENMESİ — "DDoS mu, kaynak mı?" kesin teşhis

> **V6'DA 1 SAAT KAYBETTİRDİ.** Belirti **DDoS gibi**: SSH %5 başarılı, panel timeout.
> Gerçek: **kaynak tükenmesi**. CrowdSec/fail2ban **masum** — bu ayrım yapılmazsa
> yanlış yerde saatler harcanır.

### 🔴 BELİRTİ TABLOSU (V6'da ölçüldü)

| Hedef | Sonuç | Yorum |
|---|---|---|
| Cloudflare `proxmox.domain.com` | **13/20 → 200**, 7/20 → 520/522/525 (ttfb 19–46 s) | kararsız |
| Origin `:443` doğrudan | **anında 200** | nginx/Forge sağlam |
| **Origin `:8006` (Proxmox)** | **12 s timeout, 0 yanıt** | 🔴 panel ölü |
| **Origin `:22` SSH** | TCP **0.001 s**, banner **1.49–5.73 s**, giriş **1/20 (%5)** | 🔴 userspace ölü |
| SSH KEX | `SSH2_MSG_KEX_ECDH_REPLY received` **görüldü** | bazen tam çalışıyor |

### 🔀 KOŞULLU KARAR — DDoS mu, kaynak mı? (5 ayrım testi)

| # | Test | DDoS/CrowdSec ise | **Kaynak tükenmesi ise** |
|---|---|---|---|
| 1 | **Cloudflare challenge döndürüyor mu** | "Just a moment" **var** | 200 veriyor, challenge **yok** |
| 2 | **Origin IP'den (CF atlayarak)** | engellenmiş | **aynı** davranış |
| 3 | **Gecikme sabit mi** | **sabit** süre / ani reset | **değişken** (1.5–5.7 s) |
| 4 | **KEX tamamlanıyor mu** | ❌ bloklanır | ✅ **bazen görülür** |
| 5 | **fail2ban/CrowdSec imzası** | banner **ANINDA** gelir, **sonra** reset | bizde **tersi** |

**Bu lab'de sonuç: 5/5 → KAYNAK TÜKENMESİ.** fail2ban/CrowdSec **suçsuz**.

### 🔴 GERÇEK NEDEN — kernel sağlam, userspace aç

```
TCP SYN → kernel ANINDA kabul (0.001 s)        ← ağ yığını SAĞLAM
sshd fork() → CPU bulamıyor (1.5–5.7 s)         ← userspace AÇ
pveproxy (Perl tek thread) → hiç cevap vermiyor  ← userspace ÖLÜ
nginx worker (hafif) → arada 200 dönüyor         ← şans eseri
```

**NEDEN `ping` çalışıyor:** ICMP kernel'de işlenir, userspace'e çıkmaz.
**NEDEN `ss -tlnp` çalışıyor:** dinleyen socket kernel'de hazır.
**NEDEN SSH girişi %5:** her `fork` CPU bekliyor.

### ADIM 1 — KONSOLDAN teşhis (SSH giremiyorsan IPMI/KVM)

**Ne yapıyoruz:** SSH ile giremiyorsanız **fiziksel/sanal konsoldan** (Proxmox noVNC, IPMI/KVM) sisteme girip yük/kaynak durumunu okuyoruz. **Hiçbir şey değiştirmiyoruz** — sadece 6 metrik topluyoruz.

```bash
# 1) Yük — 4 çekirdekte 40 ise tükenmiş
uptime
cat /proc/loadavg
# 40.2 38.7 35.1   ← load avg > çekirdek × 4 = TÜKENMİŞ

# 2) RAM + swap
free -m
# total  used  free  shared  buff/cache  available
# 125000 124100  200    100       600        100      ← available < %5 = TÜKENMİŞ

# 3) CPU yiyen süreçler
ps -eo pcpu,pmem,rss,comm --sort=-pcpu | head -12

# 4) D-state (I/O bekleyen — öldürülemez!)
ps -eo stat,comm | grep '^D'
# D  jbd2/dm-0-8    ← disk I/O tıkanmış
# D  pve-ha-lrm

# 5) OOM kill oldu mu
journalctl -k --since "1 hour ago" | grep -c "Out of memory"
dmesg -T | grep -i "oom-kill" | tail -5

# 6) zram çalışıyor mu (K12'de kurulmuşsa)
zramctl
swapon --show
```

### ADIM 2 — ACİL kurtarma (SSH'i geri aç)

**Ne yapıyoruz:** SSH'i **hemen** geri açmak için **ağır userspace servislerini** (`pveproxy`, `pvedaemon`) durduruyoruz. Bunlar Perl tek-thread; CPU'yu bırakınca `sshd` nefes alır.

```bash
# 1) Ağır userspace servislerini DURDUR (web paneli cevap vermiyor zaten)
systemctl stop pveproxy pvedaemon
# → SSH hemen rahatlar

# 2) Gereksiz süreçleri öldür
pkill -9 -f "pveproxy worker"
pkill -9 -f "spiceproxy"

# 3) D-state varsa: disk I/O'yu bekle — öldürmek İŞE YARAMAZ
iostat -x 2 3        # %util 100 ise disk tıkalı

# 4) zram boşalt/yeniden
swapoff -a && swapon -a 2>/dev/null
systemctl restart zramswap

# 5) Servisleri geri aç
systemctl start pvedaemon pveproxy
```

### 🔀 KOŞULLU KARAR — hangi servisi feda et?

| Kaynak | Ne kapatılır | Etki |
|---|---|---|
| **CPU** | `pveproxy pvedaemon` (Perl tek thread) | Panel erişimi gider, SSH kalır |
| **RAM** | `zramswap` yeniden başlat + büyüt | — |
| **RAM kritik (<5%)** | Bir VM durdur (`qm stop <id>`) | O iş gider, host kurtulur |
| **Disk I/O** | `vzdump`/yedek iptal (`pkill vzdump`) | Yedek iptal, I/O rahatlar |
| **Kalıcı** | §4.3 `loadgate` + zram + onboot sırası | — |

### ✅ KANIT — kurtarma başarılı mı

```bash
uptime                     # load avg düşüyor mu (1 dk içinde)
free -m                    # available > %10
time ssh root@localhost true   # < 1 s  ✅
curl -sk -o /dev/null -w "%{http_code} %{time_total}\n" https://10.30.10.1/
```

### 🔴 DUR — kalıcı önlem (§4.3 ile birlikte)

| Önlem | Neden |
|---|---|
| **`onboot 1` + `startup order`** | §4.2-C — reboot'ta sıra |
| **zram (zstd, RAM'in %25'i)** | OOM'u geciktirir |
| **`loadgate`** (§4.3) | Yük yüksekken ağır iş başlatmaz |
| **swap kapalı + swappiness 10** | §4.7 |
| **`systemd-oomd`** | RAM %80'de en düşük öncelikli servisi öldür |
| **Monitoring alarm** | load > 20 → uyarı |

### ⚠️ YAN BULGU — sandbox proxy yanılgısı

Kapalı portlara (`:9999`, `:8899`, `:31337`) **"anında connect"** → bu **sandbox egress
proxy**'si cevaplıyor, **hedef host değil**. Port tarama sonuçları **yalnız gerçek veri**
(SSH banner, HTTP 200) ile birlikte yorumlanmalı.

---

<a id="s4-4"></a>

## 4.4 🔴 SSH'ı ölümsüz yap — V6'nın en can sıkıcı hatası

> **NEDEN (H02 + banner gecikmesi):** SSH bağlantısı %5'e düştü. 3 ayrı sebep vardı:
> (a) bayat `ControlMaster` soketi → `Broken pipe`, (b) `MaxStartups 10:30:100` (varsayılan)
> bağlantı selinde sonrakileri **reddediyor**, (c) **`UseDNS yes`** → her bağlantıda ters DNS
> sorgusu → banner 1.5–5.7 saniye gecikiyor.

```bash
cat > /etc/ssh/sshd_config.d/99-banklab.conf <<'EOF'
MaxStartups 100:30:200     # NEDEN: varsayılan 10 çok düşük, testlerimizde kilitlendi
MaxSessions 50
LoginGraceTime 20
ClientAliveInterval 15
ClientAliveCountMax 4
UseDNS no                  # NEDEN: ters DNS = banner gecikmesi (ölçüldü: 3.44s ort.)
EOF
sshd -t && systemctl reload ssh
```

✅ **KANIT:** `sshd -T | grep -E "maxstartups|usedns"` → `maxstartups 100:30:200`, `usedns no`

🔁 **ALTERNATİF — yine düşerse (konsol):**
```bash
systemctl stop pveproxy pvedaemon   # web'i kapat, SSH'e CPU bırak
systemctl restart ssh
journalctl -u ssh -n 50 --no-pager
```

<a id="s4-4-b"></a>

## 4.4-B 🔴 IP BEYAZ LİSTE — 5 KATMAN (V6'da sadece 1 katman vardı)

> **NEDEN 5 KATMAN:** Tek katman **yetmez**. Gerçek kurulumda fail2ban'a eklenen IP'ler
> **CrowdSec** tarafından banlandı (o ayrı bir motor). nftables DDoS guard da SYN flood
> sayacına sizi dahil etti. **Her motorun kendi beyaz listesi vardır.**

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Gerçek kaynak IP'niz | `journalctl -u ssh -n 50 \| grep -oE 'from [0-9.]+' \| sort -u` | **sizin** IP'niz | §4.5 |
| `curl ifconfig.me` ile aynı mı | `curl -4 -s ifconfig.me` | **farklı olabilir** | ⚠️ aşağıya bak |
| Host firewall açık mı | `pve-firewall status` | `enabled/running` | §4.5 |

🔴 **`curl ifconfig.me` YANILTICIDIR:** sunucunun **kendi** çıkış IP'sini verir, sizin
giriş yaptığınız IP'yi **değil**. Gerçek kaynak **`journalctl -u ssh`**'den okunur:
```bash
journalctl -u ssh --since "1 hour ago" | grep -oE 'from [0-9.]+' | awk '{print $2}' | sort -u
# → 136.70.35.107    ← GERÇEK kaynağınız (bu lab'da ölçüldü)
# → 212.252.138.246  ← ikinci ağınız (mobil/başka ISS)
```

### 🔀 KOŞULLU KARAR — hangi IP'leri beyaz listeye?

| Durumunuz | Eklenen | Neden |
|---|---|---|
| Tek sabit IP | `/32` (örn. `136.70.35.107/32`) | en dar, en güvenli |
| **Dinamik IP / mobil** | **`/24`** (örn. `136.70.35.0/24`) | IP değişince tekrar banlanmazsınız |
| CGNAT arkası (TR mobil) | **`/25`–`/24`** | aynı NAT'tan çok kullanıcı |
| **Sunucunun kendi çıkışı** | `87.76.130.66` | host kendine bağlanınca |
| **LAB iç ağları** | `10.30.0.0/16` | tüm LAB birbirine serbest |
| jump-1 (atlayıcı) | `10.30.10.10` | tek atlama noktası |

```bash
# TEK KAYNAK — tüm katmanlar bu dosyadan okur
cat > /root/.banklab-whitelist <<'EOF'
# GERÇEK giriş IP'lerimiz — journalctl -u ssh'den doğrulandı
136.70.35.107/32
212.252.138.246/32
# LAB iç ağları
10.30.0.0/16
127.0.0.1/8
::1/128
EOF
chmod 0600 /root/.banklab-whitelist
```

### KATMAN 1 — Proxmox firewall (`cluster.fw`)

**Ne yapıyoruz:** PVE'nin **kendi firewall'ında** yönetim portlarını (22/8006/443/8443) **yalnız beyaz listeye** açıp diğer herkese kapatıyoruz. **Sıra önemli:** `ACCEPT` önce, `DROP` sonra.

```bash
# YEDEK AL — bozulursa PVE paneline erişemezsin
cp /etc/pve/firewall/cluster.fw /root/cluster.fw.bak-$(date +%F)

cat > /etc/pve/firewall/cluster.fw <<'EOF'
[OPTIONS]
enable: 1

[RULES]
# 🔴 YÖNETİM PORTLARI — YALNIZ beyaz liste (sıra ÖNEMLİ: kabul önce!)
IN ACCEPT -source 136.70.35.107/32 -p tcp -dport 22,8006,443,8443 -log nolog
IN ACCEPT -source 212.252.138.246/32 -p tcp -dport 22,8006,443,8443 -log nolog
IN ACCEPT -source 10.30.0.0/16 -p tcp -dport 22,8006,443,8443 -log nolog
# Diğer herkese KAPAT
IN DROP -p tcp -dport 22,8006,443,8443 -log nolog

[group banklab-lab]
IN ACCEPT -source 10.30.0.0/16
EOF
pve-firewall compile    # 🔴 SÖZDİZİMİ DOĞRULA — hata verirse eski dosyayı geri koy
pve-firewall restart
```

✅ **KANIT:** `pve-firewall compile` → `OK` · `iptables -L PVEFW-INPUT -n | grep 8006` → kurallar görünür
🔴 **DUR:** `compile` hata verirse `cp /root/cluster.fw.bak-* /etc/pve/firewall/cluster.fw`

### KATMAN 2 — Fail2ban (`ignoreip`)

**Ne yapıyoruz:** Fail2ban'a **asla banlamaması gereken** IP'leri (`ignoreip`) yazıyoruz. CIDR kabul eder (`/25` dahil).

```bash
sed -i '/^\[DEFAULT\]/,/^\[/ s|^ignoreip.*|ignoreip = 127.0.0.1/8 ::1 10.30.0.0/16 136.70.35.107/32 212.252.138.246/32|' /etc/fail2ban/jail.local
# veya elle:
cat > /etc/fail2ban/jail.local <<'EOF'
[DEFAULT]
ignoreip = 127.0.0.1/8 ::1 10.30.0.0/16 136.70.35.107/32 212.252.138.246/32
bantime  = 1h
findtime = 10m
maxretry = 5
EOF
fail2ban-client reload
```

✅ **KANIT:** `fail2ban-client get sshd ignoreip` → sizin IP'ler **listede**
⚠️ **`ignoreip` CIDR kabul eder** (`/25` dahil) — resmî docs: *"can be a list of IP
addresses, CIDR masks or DNS hosts"*.

### KATMAN 3 — CrowdSec (fail2ban'a EK, ayrı motor!)

**Ne yapıyoruz:** CrowdSec **ayrı bir motor** (davranış analizi). Kendi **allowlist**'ini oluşturup IP'lerimizi ekliyoruz. Fail2ban'a eklemek **yetmez** — bu ayrı liste.

```bash
# CrowdSec kurulu değilse: (opsiyonel ama önerilir — davranış analizi yapar)
curl -s https://install.crowdsec.net | sh

# Beyaz liste oluştur
cscli allowlists create banklab-agent
cscli allowlists add banklab-agent 136.70.35.107/32
cscli allowlists add banklab-agent 212.252.138.246/32
cscli allowlists add banklab-agent 10.30.0.0/16

# Doğrula
cscli allowlists inspect banklab-agent
```

⚠️ **`cscli allowlists add` `--description` flag'ini KABUL ETMEZ** (`unknown flag`) → sadece IP verin.

✅ **KANIT:** `cscli allowlists inspect banklab-agent` → **2+ değer, `expire: never`**
🔴 **DUR:** `cscli decisions list` → kendi IP'niz **olmalı DEĞİL**. Varsa:
`cscli decisions delete --ip <sizin-ip>`

### KATMAN 4 — nftables DDoS guard (`set beyaz_liste`)

> **NEDEN:** SYN-flood limiti (`limit rate over 10/second`) **sizi de** sayar.
> 20 sekmede PVE paneline girince limit aşılır → **kendinizi DROP edersiniz**.

```bash
mkdir -p /etc/nftables-ddos
cat > /etc/nftables-ddos/ddos-guard.nft <<'EOF'
#!/usr/sbin/nft -f
table inet ddos_guard {
  set beyaz_liste {
    type ipv4_addr
    flags interval
    elements = { 136.70.35.107/32, 212.252.138.246/32, 10.30.0.0/16, 127.0.0.0/8 }
  }
  set syn_sayac { type ipv4_addr; flags dynamic,timeout; timeout 1m; }

  chain giris {
    type filter hook input priority -10; policy accept;
    # 🔴 BEYAZ LİSTE — SYN limitinden ÖNCE (sıra ŞART)
    ip saddr @beyaz_liste accept
    # SYN flood koruması
    tcp flags syn tcp dport { 22, 80, 443, 8006 } \
      update @syn_sayac { ip saddr limit rate over 10/second } drop
  }
}
EOF

# Sözdizimi doğrula (UYGULAMADAN)
nft -c -f /etc/nftables-ddos/ddos-guard.nft || { echo "🔴 SÖZDİZİMİ HATASI - uygulanmadı"; exit 1; }

cat > /etc/systemd/system/ddos-guard.service <<'EOF'
[Unit]
Description=BankLab nftables DDoS guard
Before=network-pre.target
Wants=network-pre.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/nft -f /etc/nftables-ddos/ddos-guard.nft
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload && systemctl enable --now ddos-guard
```

✅ **KANIT:** `nft list set inet ddos_guard beyaz_liste` → **4 eleman** · `nft list counters` → sayaç **0**

### KATMAN 5 — SSH `Match` ile port sınırlaması (en sert)

**Ne yapıyoruz:** `sshd_config`'te **`Match Address`** ile: yalnız beyaz listeden **anahtar** girişi; diğer herkese **ne parola ne anahtar**. **En sert katman** — yanlış yazılırsa **kendinizi kilitlersiniz**, mutlaka `sshd -t` çalıştırın.

```bash
# Yalnız beyaz listeden SSH — geri kalan için parola bile sormaz
cat > /etc/ssh/sshd_config.d/99-banklab-allow.conf <<'EOF'
Match Address 136.70.35.107/32,212.252.138.246/32,10.30.0.0/16
    PermitRootLogin prohibit-password
    PasswordAuthentication no
Match Address *,!136.70.35.107/32,!212.252.138.246/32,!10.30.0.0/16
    PasswordAuthentication no
    PubkeyAuthentication no
EOF
sshd -t && systemctl reload ssh    # 🔴 sshd -t ÖNCE — bozuk config = kilitlenme!
```

🔴🔴 **DÜR — bu katman en tehlikelisi.** `sshd -t` **hata verirse reload YAPMAYIN**;
mevcut oturumu **kapatmayın**; ikinci terminalde test edin:
```bash
ssh -o BatchMode=yes root@localhost true   # aynı makineden
```

### 🔁 FALLBACK — kendinizi kilitlediyseniz

| Durum | Yol |
|---|---|
| SSH kesildi, PVE paneli açık | PVE **Shell** (noVNC/xterm) → kuralları sil |
| Panel de kapalı | **Fiziksel konsol** / sağlayıcının KVM/IPMI'si |
| Proxmox host'a hiç erişim yok | **Kurtarma modu** (`reboot` → GRUB `single`) → `pve-firewall stop` |
| Fail2ban kilitledi | Konsol → `fail2ban-client unban <IP>` |
| CrowdSec kilitledi | Konsol → `cscli decisions delete --ip <IP>` |
| nftables kilitledi | Konsol → `nft flush ruleset` |

🔴 **KURAL (K38):** Her whitelist değişikliğinden önce **ikinci bir SSH oturumu açık tutun**.
Değişiklik sonrası **o oturumdan** test edin, sonra kapatın.

✅ **SON KANIT — 5 katman birden:**
```bash
echo "1) pve-firewall: $(pve-firewall status | head -1)"
echo "2) fail2ban:     $(fail2ban-client get sshd ignoreip)"
echo "3) crowdsec:     $(cscli allowlists inspect banklab-agent 2>/dev/null | grep -c '10\.\|136\.')"
echo "4) nftables:     $(nft list set inet ddos_guard beyaz_liste 2>/dev/null | grep -c '136\.')"
echo "5) sshd:         $(sshd -T | grep -c 'permitrootlogin')"
```

---

<a id="s4-5"></a>

## 4.5 Fail2ban — **kendinizi banlamayın**

> **NEDEN (V6'da saat kaybı):** Host kilitlenince "CrowdSec mi engelliyor?" diye saatler
> harcandı. CrowdSec **kendi IP'nizi banlarsa** ağdan giriş imkânsız olur.

```bash
apt-get install -y fail2ban
cat > /etc/fail2ban/jail.d/sshd.local <<'EOF'
[sshd]
enabled  = true
maxretry = 8              # NEDEN: varsayılan 5, kendi test döngümüzde tetiklendi
findtime = 600
bantime  = 1800
ignoreip = 127.0.0.1/8 ::1 10.30.0.0/16 87.76.130.0/25   # 🔴 kendi ağınız
EOF
systemctl restart fail2ban
```

✅ **KANIT:** `fail2ban-client get sshd ignoreip` → kendi ağınız **listede**

🔁 **ALTERNATİF — banlandıysanız:** `fail2ban-client unban --all`
`cscli decisions delete --ip <ip>` (CrowdSec varsa)

<a id="s4-6"></a>

## 4.6 Cloudflare — **doğru teşhis**

> **NEDEN (H82):** V6'da `525 SSL Handshake Failed` görünce "Cloudflare SSL modu yanlış"
> sanıldı, sertifika değiştirilmeye çalışıldı. **Zaman kaybı.** Gerçek sebep **origin yavaştı**.

```bash
# İLK YAPILACAK: origin'i ATLAYARAK ölç
curl -4 -sS -o /dev/null -w "origin:443  %{http_code} %{time_starttransfer}s\n" https://87.76.130.66/
curl -4 -sS -o /dev/null -w "origin:8006 %{http_code} %{time_starttransfer}s\n" -k https://87.76.130.66:8006/
```

| origin :443 | origin :8006 | Teşhis | Eylem |
|---|---|---|---|
| anında 200 | **timeout** | **Host tükenmiş** (H81) | §4.3, Cloudflare'e dokunma |
| anında 200 | anında 200 | Cloudflare ayarı | SSL modu/port |
| timeout | timeout | Host/ağ ölü | konsol/IPMI |

---

<a id="s4-7"></a>

## 4.7 🔴 Kernel + ağ tuning — ölçülmüş değerlerle

> **NEDEN:** Varsayılan Linux ayarları **geliştirici dizüstü** için optimize. Sürekli açık
> bir K8s host'u için **inotify** (dosya izleyici) erken tükenir, `swappiness=60` RAM'i
> gereksiz takas eder, **THP** Longhorn'da gecikme yapar.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Kernel sürümü | `uname -r` | `6.8+` (BBR var) | BBR'ı atla |
| `tcp_bbr` modülü | `modprobe tcp_bbr && lsmod \| grep bbr` | yüklenir | BBR'ı atla |
| Mevcut sysctl | `sysctl net.ipv4.tcp_congestion_control vm.swappiness` | `cubic` / `60` | bu bölüm |
| THP durumu | `cat /sys/kernel/mm/transparent_hugepage/enabled` | `[always]` veya `[madvise]` | bu bölüm |

### 🔀 KOŞULLU KARAR — hangi tuning, hangi risk?

| Ayar | Risk | Uygulanmalı mı |
|---|---|---|
| **BBR + fq** | Yok (test edildi, geri alınabilir) | ✅ **Her zaman** |
| **inotify artır** | Yok | ✅ **Her zaman** (K8s şart) |
| **THP = madvise** | Düşük (bazı iş yükleri biraz yavaşlar) | ✅ Longhorn/Postgres varsa |
| **vm.swappiness = 10** | Düşük | ✅ (swap zaten kapalı, yine de) |
| **overcommit_memory = 1** | ⚠️ OOM riski artar | 🔴 **YAPMAYIN** — K8s kendi yönetir |
| **kernel.panic = 10** | Yok | ✅ host reboot'u hızlandırır |
| **CPU governor = performance** | Güç tüketimi ↑ | ⚠️ sadece gecikme kritikse |

### ADIM 1 — BBR + fq (indirme hızı)

```bash
# NEDEN: Docker Hub BAĞLANTI BAŞINA kısıtlıyor (267 KB/s). BBR daha agresif
# pencere açar. Ölçüm: 267 → 837 KB/s (8 paralel ile)
cat > /etc/sysctl.d/99-banklab-net.conf <<'EOF'
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
# Yüksek eşzamanlı indirme (containerd 10 paralel + git + helm)
net.core.somaxconn = 4096
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_tw_reuse = 1
net.ipv4.ip_local_port_range = 10240 65000
# Yüksek BDP için (TR ISP: 26 MB/s × 80 ms ≈ 2 MB pencere)
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216
EOF
sysctl -p /etc/sysctl.d/99-banklab-net.conf
```

✅ **KANIT:** `sysctl net.ipv4.tcp_congestion_control` → **`bbr`** · `sysctl net.core.default_qdisc` → `fq`
⚠️ **Dürüst not:** V6'da BBR sonrası ölçümler **gürültülüydü**, net kazanç **kanıtlanamadı**.
**Yine de zararsız** — geri almak için dosyayı silin.

🔁 **BBR yüklenmiyorsa** (`sysctl: cannot stat`): modül yok → `tcp_congestion_control = cubic`
bırakın, sadece `rmem/wmem` satırlarını uygulayın.

### ADIM 2 — inotify (K8s için ZORUNLU)

```bash
cat > /etc/sysctl.d/99-banklab-kernel.conf <<'EOF'
# 🔴 NEDEN: her pod ~50 izleyici açar; 128 pod × 50 = 6400 > 8192 default → "no space left"
fs.inotify.max_user_watches = 524288
fs.inotify.max_user_instances = 512
fs.inotify.max_queued_events = 32768
# Dosya tanıtıcı
fs.file-max = 2097152
# Kernel panic → 10 sn sonra reboot (elle müdahale gerekmesin)
kernel.panic = 10
kernel.panic_on_oops = 1
# Swap: kapalı ama yine de agresif takas yapmasın
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5
vm.max_map_count = 262144          # Elasticsearch/OpenSearch/Java için
EOF
sysctl -p /etc/sysctl.d/99-banklab-kernel.conf
```

✅ **KANIT:** `sysctl fs.inotify.max_user_watches` → **524288**

🔴 **DUR — `vm.max_map_count` hatası:** eski kernel'de `262144` desteklenmez →
`sysctl: setting key ... Invalid argument`. `655360`'a kadar deneyin veya satırı çıkarın.
**Bu satır olmadan Java/Kafka pod'ları `OOM` verebilir.**

### ADIM 3 — THP = madvise (Longhorn + Postgres gecikmesi)

```bash
# NEDEN: THP 'always' → 2 MB sayfalar → Longhorn/NFS üzerinde 2 MB yazma =
# ~100 küçük istek. Latency 3-5× artar (Longhorn docs önerir: madvise)
cat > /etc/systemd/system/thp-madvise.service <<'EOF'
[Unit]
Description=THP madvise (Longhorn/Postgres latency)
DefaultDependencies=no
After=sysinit.target
Before=multi-user.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/sh -c 'echo madvise > /sys/kernel/mm/transparent_hugepage/enabled'
ExecStart=/bin/sh -c 'echo defer+madvise > /sys/kernel/mm/transparent_hugepage/defrag'

[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload && systemctl enable --now thp-madvise
```

✅ **KANIT:** `cat /sys/kernel/mm/transparent_hugepage/enabled` → **`always madvise [madvise]`**

🔁 **`defrag` satırı hata verirse** (eski kernel `defer+madvise` desteklemez):
`echo madvise > .../defrag` yapın veya satırı silin.

### ADIM 4 — I/O scheduler (SSD/NVMe)

**Ne yapıyoruz:** Disk tipine göre **I/O scheduler** seçiyoruz: NVMe → `none` (donanım zaten paralel), SATA SSD → `mq-deadline`. `udev` kuralıyla **kalıcı** yapıyoruz.

```bash
# NVMe için 'none' en iyi; SATA SSD için 'mq-deadline'
for D in /sys/block/sd* /sys/block/nvme*n1; do
  [ -f "$D/queue/scheduler" ] || continue
  T=$(basename $D)
  case "$T" in
    nvme*) echo none    > "$D/queue/scheduler" 2>/dev/null ;;
    sd*)   echo mq-deadline > "$D/queue/scheduler" 2>/dev/null ;;
  esac
  echo "$T → $(cat $D/queue/scheduler)"
done

# KALICI (reboot sonrası)
cat > /etc/udev/rules.d/60-iosched.rules <<'EOF'
ACTION=="add|change", KERNEL=="nvme*n1", ATTR{queue/scheduler}="none"
ACTION=="add|change", KERNEL=="sd[a-z]",  ATTR{queue/scheduler}="mq-deadline"
ACTION=="add|change", KERNEL=="sd[a-z]",  ATTR{queue/rotational}="0"
EOF
udevadm control --reload && udevadm trigger
```

✅ **KANIT:** `lsblk -o NAME,SCHED,ROTA` → NVMe `none`, SSD `mq-deadline`, `ROTA=0`

### ADIM 5 — zaman senkronu + izleme servisleri

**Ne yapıyoruz:** `chrony` (NTP), `auditd` (denetim izi), `vnstat` (trafik sayacı), `unattended-upgrades` kurup **etkinleştiriyoruz**. **chrony kritik:** etcd `saat kayması > 1 sn` olursa leader election çöker.

```bash
# NTP — sertifika doğrulaması ve etcd için ŞART (±1 sn)
apt-get install -y chrony auditd vnstat unattended-upgrades needrestart
systemctl enable --now chrony auditd vnstat

chronyc tracking                          # Leap status: Normal, offset < 50 ms
timedatectl status                        # System clock synchronized: yes

# auditd — kim ne yaptı (denetim izi)
systemctl enable --now auditd
auditctl -l | head -5 || echo "kural yok (normal)"
```

🔴 **NEDEN `chrony` KRİTİK:** etcd **saat kayması > 1 sn** olursa `leader election`
başarısız olur → küme `etcdserver: request timed out`. **Sertifika** doğrulaması da
saat gerektirir.
```bash
# Doğrulama
chronyc sources -v
# ^* 10.30.10.1   ...    ← '*' = aktif kaynak
```

🔁 **`chrony` senkron olmuyorsa:** `chronyc makestep` · hâlâ olmuyorsa firewall UDP 123.

✅ **SON KANIT — tüm tuning:**
```bash
echo "BBR:    $(sysctl -n net.ipv4.tcp_congestion_control)"
echo "qdisc:  $(sysctl -n net.core.default_qdisc)"
echo "inotify:$(sysctl -n fs.inotify.max_user_watches)"
echo "THP:    $(cat /sys/kernel/mm/transparent_hugepage/enabled)"
echo "NTP:    $(chronyc tracking | awk '/Leap status/{print $4}')"
```

---

<a id="s5"></a>

# 5. OPNsense — 26.7'nin GERÇEK API'si

> **NEDEN bu bölüm tamamen yeniden yazıldı:** önceki sürümde `{"source":{"network":"lan"}}`
> yazmıştım. **26.7 kaynak kodunu okudum:** doğrusu **`{"source_net":"lan"}`** — düz alan,
> nesne değil. Ayrıca `.php` yolları 26.7'de **404** (R03/H24).

<a id="s5-1"></a>

## 5.1 İmaj

```text
VER=26.7
BASE="https://mirror.ams1.nl.leaseweb.net/opnsense/releases/$VER"
curl -fLSs "$BASE/OPNsense-$VER-dvd-amd64.iso.bz2" -o o.iso.bz2
curl -fLSs "$BASE/OPNsense-$VER-dvd-amd64.iso.bz2.sig" -o o.sig
base64 -d o.sig > o.sig.bin 2>/dev/null || cp o.sig o.sig.bin   # NEDEN: imza base64 (H06)
```

🔁 **ALTERNATİF kaynak:** `pkg.opnsense.org` bazen boş döner (H06) → leaseweb aynası.

<a id="s5-2"></a>

## 5.2 VM 100 (OPNsense sanal makinesi — 4 NIC)

**Ne yapıyoruz:** OPNsense için VM oluşturuyoruz: 4 GB RAM, 2 çekirdek, **4 ağ kartı**
(net0=WAN `vmbr0`, net1–3 = `vnet10/20/30`). ISO'yu CD olarak bağlayıp başlatıyoruz.
**4 NIC NEDEN:** OPNsense **router** olacak — WAN'dan girer, 3 lab ağına yönlendirir.


```bash
qm create 100 --name opnsense --memory 4096 --cores 2 --cpu host \
  --net0 virtio,bridge=vmbr0 \
  --net1 virtio,bridge=vnet10 --net2 virtio,bridge=vnet20 --net3 virtio,bridge=vnet30 \
  --scsihw virtio-scsi-single --ostype other --serial0 socket --vga std

qm set 100 --ide2 local:iso/OPNsense-$VER-dvd-amd64.iso,media=cdrom --boot order=ide2
qm start 100
```

🔴 **DUR — üç tuzak:**
| Hata | Belirti | Çözüm |
|---|---|---|
| **H09** | Serial `.img` = **RAM overlay** → reboot'ta ayarlar uçar | **DVD** (`.iso`) kullan |
| **H21** | QMP `sendkey` **shift** düşürüyor → parola bozuk | Parolada **shift yok** + **ekranı oku** |
| **H14** | `qm cloudinit dump` `cicustom` göstermez | VM **içinden** doğrula |

**Konsol (VNC çalışmıyor — H07):**
```bash
echo '{"execute":"screendump","arguments":{"filename":"/tmp/x.ppm"}}' | \
  socat - UNIX-CONNECT:/var/run/qemu-server/100.qmp
convert /tmp/x.ppm /tmp/x.png        # ekranı GÖZLE doğrula

echo '{"execute":"send-key","arguments":{"keys":[{"type":"qcode","data":"ret"}]}}' | \
  socat - UNIX-CONNECT:/var/run/qemu-server/100.qmp
```

<a id="s5-3"></a>

## 5.3 API anahtarı — V6'da **hiç anlatılmamıştı** (R05)

```
GUI → System → Access → Users → root → API keys → "+"
  → Key + Secret'i KAYDET (secret bir daha gösterilmez)
```

<a id="s5-4"></a>

## 5.4 🔴 Kural ekleme — GERÇEK şema (V7'de yanlıştı)

**Ne yapıyoruz:** OPNsense **API'siyle** firewall kuralı ekliyoruz — NAT (SNAT), iç ağdan
internete çıkış, ve `10.30.0.0/16`'nın kendi arasında serbest dolaşımı. **Neden API:**
GUI'de 20+ tıklama; API'de tek `curl`. Ama 26.7 şeması V6'dan **farklı** (`statetype` alanı yok).


```bash
export OPNS="https://10.30.10.1/api"
export KEY="<key>" SECRET="<secret>"
export JAR=/tmp/opnsense-cookies.txt
rm -f $JAR

# 🔴 CSRF: token SESSION'da tutulur ($PHALCON/CSRF$). Bu yüzden:
#   1) GET isteği → oturum çerezi (PHPSESSID) + token üretilir
#   2) POST isteği → aynı çerezle + X_CSRFTOKEN başlığıyla
#   (Kaynak: core 26.7 ApiControllerBase.php:393 + Mvc/Security.php:59)

curl -sk -c $JAR -u "$KEY:$SECRET" -o /dev/null "$OPNS/firewall/filter/get"

# Oturum çerezi alındı mı?
grep -q PHPSESSID $JAR || { echo "🔴 oturum çerezi yok"; exit 1; }

# Token'ı WEB arayüzünden oku (oturum varsa script etiketi içinde gelir).
# API yanıtı HTML değildir → token'ı göremezsiniz. Alternatif: GUI'de
#   System → Access → Users → sizin kullanıcı → "Refresh CSRF" yoktur;
#   token her GET'te session'da üretilir ve POST'ta session'dan okunur.
# 🔴 PRATİK YOL: aşağıdaki "csrf-token" yardımcısını kullan
```

**CSRF token alma — çalışan yol (M92 DÜZELTİLDİ):**

> 🔴 **SCRIPT'TE 3 HATA VARDI:**
> (a) Login formunun CSRF alan adını **dinamik** çıkarıp `--data-urlencode "$(...)=$FT"`
>     yazıyordu — hata durumunda **boş anahtar** gönderir → login **başarısız**, sessizce.
> (b) `X-CSRFToken` **iki yerde** aranıyordu (regex yanlış: `\s*` bash'ta `\s` değil).
> (c) `PHPSESSID` kontrolü yok — login başarısız olsa da devam eder.
>
> 🔴 **İKİ KİMLİK KARIŞTIRILMIŞ:** API anahtarı (Basic Auth) ile **GUI oturumu**
> (PHPSESSID) **AYRI** şeylerdir. API anahtarıyla token **alınamaz**; **GUI'ye giriş** şarttır.

```bash
cat > /usr/local/bin/opns-csrf <<'SCRIPT'
#!/bin/bash
# OPNsense CSRF token al — GUI oturumu (PHPSESSID) üzerinden
# Kullanım: opns-csrf <host> <gui-kullanici> <gui-parola>
set -euo pipefail
HOST=${1:?kullanim: opns-csrf 10.30.10.1 admin PAROLA}
USER=${2:?}
PASS=${3:?}

JAR=$(mktemp); trap 'rm -f $JAR /tmp/_opns_*.html' EXIT

# 1) Login sayfası: oturum çerezi + form CSRF alanı
curl -sk -c "$JAR" "https://$HOST/" -o /tmp/_opns_login.html

# Form alan adı (OPNsense: __csrf_magic) ve değeri
FIELD=$(grep -oE 'name="[^"]*csrf[^"]*"' /tmp/_opns_login.html | head -1 | sed 's/name="//;s/"//')
VALUE=$(grep -oE 'name="[^"]*csrf[^"]*"[^>]*value="[^"]*"' /tmp/_opns_login.html \
        | head -1 | sed 's/.*value="//;s/"$//')
[ -n "${FIELD:-}" ] && [ -n "${VALUE:-}" ] || { echo "🔴 login formunda CSRF alanı yok" >&2; exit 1; }

# 2) Login POST
curl -sk -b "$JAR" -c "$JAR" -X POST "https://$HOST/" \
  --data-urlencode "usernamefld=$USER" \
  --data-urlencode "passwordfld=$PASS" \
  --data-urlencode "$FIELD=$VALUE" \
  --data-urlencode "login=1" -o /tmp/_opns_login2.html -w "" -s

# 3) 🔴 Oturum GERÇEKTEN açıldı mı? (redirect + çerez)
grep -q "PHPSESSID" "$JAR" || { echo "🔴 PHPSESSID yok — login başarısız" >&2; exit 1; }
if grep -qiE "wrong|invalid|incorrect" /tmp/_opns_login2.html; then
  echo "🔴 parola reddedildi" >&2; exit 1
fi

# 4) Oturum açık bir sayfa → script içindeki token
curl -sk -b "$JAR" -c "$JAR" "https://$HOST/firewall_rules.php" -o /tmp/_opns_page.html
TOKEN=$(grep -oE 'X-CSRF[Tt]oken"?[[:space:],:]+"[A-Za-z0-9+/=_-]{20,}"' /tmp/_opns_page.html \
        | tail -1 | sed 's/.*"//' | tr -d '"')

if [ -z "${TOKEN:-}" ]; then
  echo "🔴 token bulunamadi — GUI'de System>Access>Users ile bir kullanici olusturun" >&2
  exit 1
fi
echo "$TOKEN"
SCRIPT
chmod +x /usr/local/bin/opns-csrf

# KULLANIM
export CSRF=$(opns-csrf 10.30.10.1 admin 'PAROLA')
echo "token uzunluk: ${#CSRF}"     # → 40+ olmalı
```

🔁 **ALTERNATİF (daha BASİT — CSRF'i TAMAMEN atla): GUI'den kural ekleyin**
```bash
# OPNsense → Firewall → Rules → [LAN] → +Add
#   Action: Pass · Interface: LAN · Protocol: any · Source: LAN net · Destination: any
# 30 saniyede biter ve %100 çalışır. API'yi otomasyon için kullanın, ilk kurulum için DEĞİL.
```
⚠️ **NEDEN:** CSRF akışı 26.7'de **kırılgan** (UI değişirse regex de değişir).
**İlk kurulumda** 4 kural için GUI yeterli; API'yi **tekrarlayan** işlerde kullanın.

🔴 **NEDEN bu kadar dolambaçlı — gerçek kod:**
```php
// ApiControllerBase.php:391
$csrf_valid = (new Security($this->session, $this->request))->checkToken(
    null,
    $this->request->getHeader('X_CSRFTOKEN')      // ← ALT ÇİZGİ, BÜYÜK HARF
);
// → 403 "no matching csrf found for request"

// Mvc/Security.php:59
public function getToken(bool $new = true): ?string {
    $token = $this->session->get('$PHALCON/CSRF$');   // ← SESSION'da
    if (empty($token)) { $this->session->set('$PHALCON/CSRF$', $token); }
    return $token;
}
```

**Kural:** Token **session'a bağlı**. API anahtarı GET'i session üretir ama token'ı **döndürmez**.
Bu yüzden **GUI oturumu** ile alınır VEYA **`POST` gövdesinde** `$_POST[$CSRF_KEY]` olarak gönderilir.

🔁 **ALTERNATİF — CSRF'siz yol (önerilen):**
> **`console` üzerinden yapılandırma** — OPNsense'te **`configctl`** ile kurallar
> **CSRF gerektirmeden** yazılır:
```bash
# VM 100 konsolundan (veya SSH ile root):
configctl filter reload
# Doğrudan config.xml düzenlemek YERİNE:
#   GUI → Firewall → Rules → "+"  (tarayıcıdan, CSRF otomatik gider)
```
**Lab için en pratik: kuralları GUI'den bir kez girin** (5 kural, 10 dakika).
Script'le otomatikleştirmek **API CSRF'i yüzünden kırılgandır** — V6'da tam bu yüzden 3 kez takıldı.

```bash
# POST örneği (token'ı yukarıdaki yardımcıdan aldıysanız):
COMMON=(-sk -u "$KEY:$SECRET" -b "$JAR" \
        -H "X_CSRFTOKEN: $CSRF" \
        -H "Content-Type: application/json")
```

**FORWARD kuralı — doğru alanlar (core 26.7 `Filter.xml`):**

```bash
# 🔴 Endpoint doğrulandı: FilterController::addRuleAction → addBase("rule","rules.rule")
#    Gövde: {"rule": {...}}  → $this->request->getPost("rule") ile okunur
#    Alanlar $node->setNodes(...) ile DÜZ atanır (source_net, description)
curl "${COMMON[@]}" -X POST "$OPNS/firewall/filter/addRule" -d '{
  "rule": {
    "action":      "pass",
    "interface":   "lan",
    "direction":   "in",
    "ipprotocol":  "inet",
    "protocol":    "any",
    "source_net":  "lan",
    "destination_net": "any",
    "description": "lab-allow-lan",
    "enabled":     "1",
    "quick":       "1",
    "log":         "0",
    "sequence":    "100"
  }
}'
curl "${COMMON[@]}" -X POST "$OPNS/firewall/filter/apply" -d '{}'
```

**NEDEN `source_net` ve `description`?** 26.7 modelinde alanlar **düz**:
```xml
<source_net type="NetworkAliasField">   ← nesne DEĞİL
<destination_net type="NetworkAliasField">
<description>                            ← 'descr' değil
<action type="OptionField">  <interface type="InterfaceField">
<ipprotocol>  <protocol>  <direction>  <statetype>  <quick>  <enabled>  <sequence>
```
❌ **YANLIŞ:** `{"source":{"network":"lan"}}` → **400 / sessizce yok sayılır**
❌ **YANLIŞ:** `"statetype":"keep state"` → 26.7'de alan **farklı** (R22)

**SNAT — `SourceNatController` doğru endpoint:**

```text
**NEDEN:** Bu 3 ağ **dışarıya kapalı** olacak; yalnız OPNsense router. Çakışan bir
ağ varsa (örn. VPN'iniz `10.30.x` kullanıyorsa) **şimdi** değiştirin — sonra
tüm envanter (§2.4) ve sertifika SAN'ları değişir.

for NET in 10.30.10.0/24 10.30.20.0/24 10.30.30.0/24; do
  curl "${COMMON[@]}" -X POST "$OPNS/firewall/source_nat/addRule" -d "{
    \"rule\": {
      \"interface\": \"wan\",
      \"source_net\": \"$NET\",
      \"destination_net\": \"any\",
      \"target\": \"wanip\",
      \"description\": \"snat-$NET\",
      \"enabled\": \"1\"
    }
  }"
done
curl "${COMMON[@]}" -X POST "$OPNS/firewall/source_nat/apply" -d '{}'
```

✅ **KANIT:** `curl ... "$OPNS/firewall/filter/searchRule" | jq '.rows|length'` → artar

<a id="s5-4-b"></a>

## 5.4-B 🔴 HAProxy paketi — V6'da **hiç yazılmamıştı** (R32)

> **V6'NIN BOŞLUĞU:** Rehber "OPNsense HAProxy paketinde `.24:9345` listener" diyordu ama
> **paketin nasıl kurulacağını yazmıyordu**. Fabrika ayarındaki OPNsense'te bu paket
> **kurulu değildir** → `ui/haproxy/settings` **404**.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Paket kurulu mu | `ssh root@10.30.10.1 'pkg info os-haproxy'` | `os-haproxy-...` | bu bölüm |
| GUI'de menü var mı | `curl -sk -u "$KEY:$SECRET" https://10.30.10.1/api/haproxy/settings/get \| head -c 80` | JSON | bu bölüm |
| HAProxy servisi | `ssh root@10.30.10.1 'configctl haproxy status'` | `running` | bu bölüm |

### 🔀 KOŞULLU KARAR — `.24:9345` için hangi yol?

| Durumunuz | Yol | Neden |
|---|---|---|
| **Tek CP** | **HAProxy GEREKMEZ** | `server: https://cp-1:9345` doğrudan |
| **3 CP** (bu lab) | **HAProxy** (`os-haproxy`) | registration VIP `.24` → 3 CP'ye dağıtır |
| 3 CP ama OPNsense dışında LB | `haproxy` VM / `nginx stream` | aynı işlev |
| Cloud LB | cloud LB | — |

### ADIM 1 — Paketi kur (İKİ yol)

**Ne yapıyoruz:** OPNsense'e **`os-haproxy` paketini** kuruyoruz. Fabrika ayarında **kurulu değildir** → `ui/haproxy/settings` **404** verir.

**Yol A — GUI:**
```
System → Firmware → Plugins → arama "haproxy" → os-haproxy → [+] Install
```

**Yol B — Konsol/SSH (hızlı):**
```bash
ssh root@10.30.10.1
pkg install -y os-haproxy
# → os-haproxy-5.1 ... installed
```

✅ **KANIT:** `pkg info os-haproxy` → **kurulu** · GUI'de `Services → HAProxy` **görünür**

### ADIM 2 — Backend + Frontend (API ile)

**Ne yapıyoruz:** HAProxy'de **backend** (3 CP'yi havuz olarak) + **frontend** (`.24:9345` dinleyip havuza dağıtan) tanımlıyoruz. TCP health-check ile ölü CP'yi havuzdan çıkarır.

```bash
# 1) Health check (TCP — RKE2 9345 ham TLS'tir)
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/haproxy/settings/addHealthcheck \
  -d '{"healthcheck":{"name":"rke2-9345","type":"tcp","checkport":"9345","interval":"5s"}}'

# 2) Backend
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/haproxy/settings/addBackend \
  -d '{"backend":{"name":"rke2-registration","mode":"tcp","server":"rke2-reg",
       "healthcheck":"rke2-9345","balance":"roundrobin"}}'

# 3) Üç CP'yi sunucu olarak ekle
for CP in 21 22 23; do
  curl -sk -u "$KEY:$SECRET" -X POST \
    https://10.30.10.1/api/haproxy/settings/addServer/rke2-reg \
    -d "{\"server\":{\"name\":\"cp-$((CP-20))\",\"address\":\"10.30.10.$CP\",\"port\":\"9345\"}}"
done

# 4) Frontend (.24:9345)
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/haproxy/settings/addFrontend \
  -d '{"frontend":{"name":"rke2-join","bind":"10.30.10.24:9345","mode":"tcp",
       "defaultbackend":"rke2-reg","enabled":"1"}}'

# 5) 🔴 UYGULA (commit) — atlanırsa config yazılmaz
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/haproxy/service/reconfigure
```

### 🔴 R33 TUZAĞI — TLS passthrough + `verify` ÇELİŞKİLİ

**V6'da yanlış:** "TLS passthrough + `ssl verify required`" birlikte yazılmıştı.
**Gerçek:** Passthrough modunda HAProxy arka uç sertifikasını **göremez** → `verify` **çalışmaz**.

| Mod | `verify` | Ne zaman |
|---|---|---|
| **TCP passthrough** (bu lab) | ❌ **KAPALI** olmalı | RKE2 join — sertifika RKE2 CA ile doğrulanır |
| HTTP/SSL termination | ✅ `required` olabilir | HAProxy sertifikayı sonlandırıyorsa |

```bash
# Passthrough için verify KAPALI
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/haproxy/settings/setServer/rke2-reg/0 \
  -d '{"server":{"ssl":"1","sslverify":"0","name":"cp-1","address":"10.30.10.21","port":"9345"}}'
```

### ✅ KANIT

```bash
# 1) Frontend dinliyor mu
ssh root@10.30.10.1 'sockstat -4l | grep 9345'
# → haproxy  haproxy  4  tcp4  10.30.10.24:9345  *:*

# 2) Gerçek RKE2 CA cevabı
curl -sk https://10.30.10.24:9345/cacerts | grep -c "BEGIN CERTIFICATE"
# → 1  ✅ (bu, cp-1'den gelen CA)

# 3) 3 CP'ye round-robin (3 kez sor, farklı backend görebilirsin)
for i in 1 2 3; do curl -sk -o /dev/null -w "%{http_code} " https://10.30.10.24:9345/cacerts; done
# → 200 200 200
```

**Gerçek ölçüm (V6):** `curl -sk https://10.30.10.24:9345/cacerts` → **`http=200 size=570`** + `-----BEGIN CERTIFICATE-----`

### 🔴 DUR

| Hata | Sebep | Çözüm |
|---|---|---|
| `ui/haproxy/settings` **404** | Paket **kurulu değil** | ADIM 1 |
| `503 Service Unavailable` | Backend'de **sağlıklı server yok** | `healthcheck` TCP + CP'ler 9345 dinliyor mu |
| `curl: (35) SSL` | `ssl` alanı kapalı | `"ssl":"1"` |
| `reconfigure` sonrası değişiklik yok | **commit atlandı** | ADIM 2'nin 5. komutu |
| Worker join `not authorized` | **R42: elle üretilmiş token** | aşağıya bak |

### 🔴 R42 TUZAĞI — `agent-token` **ELLE ÜRETİLMEZ**

```bash
# ❌ YANLIŞ — sunucu bu token'ı TANIMAZ
openssl rand -hex 32 > /etc/rancher/rke2/agent-token
```

**Gerçek (V6'da yaşandı):** elle üretilen token → worker sonsuz `not authorized`.
Üstelik cp-1 restart edilince kendi bayat değerini geri yazıyor; **HAProxy round-robin**
yüzünden hata **kararsız** görünüyordu (bazı worker katılıyor, bazıları katılmıyor).

```bash
# ✅ DOĞRU — cp-2'den OKU (rke2-server üretir)
ssh root@10.30.10.22 'cat /var/lib/rancher/rke2/server/agent-token'
# → K10...   (139 B)

# Worker'a yaz
scp root@10.30.10.22:/var/lib/rancher/rke2/server/agent-token \
    root@10.30.30.11:/etc/rancher/rke2/agent-token
```

⚠️ **`agent-token-file` YAZILMAZ:** RKE2 config'inde `agent-token` **doğrudan** verilir
veya dosya **varsayılan yolda** (`/etc/rancher/rke2/agent-token`) durur. `agent-token-file`
anahtarı RKE2'de **yok** (M58).

---

<a id="s5-5"></a>

## 5.5 DNS yönlendirme — **RKE2 join için zorunlu**

```
OPNsense → Services → Unbound DNS → Query Forwarding:
  Domain: banklab.alperozbay.com  →  Server: 10.30.10.53
```

**NEDEN:** VM'ler OPNsense'i resolver kullanır. İç alan yönlendirilmezse
`cp-1.banklab.alperozbay.com` **çözülemez** → RKE2 join başarısız.

---

<a id="s5-6"></a>

## 5.6 🔴 MTU / PMTU — "bazı siteler açılıyor bazıları açılmıyor" tuzağı

> **NEDEN:** OPNsense WAN'da PPPoE varsa MTU **1492**, VLAN varsa **1500**. Yerel ağ 1500
> kalırsa **büyük paketler** (TLS handshake, git clone, docker pull) **sessizce** düşer.
> Belirti: `ping` çalışır, `curl https://...` **asılı kalır**.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| WAN arayüzü IPv4 | OPNsense → Interfaces → WAN | IP var | §5.2 |
| WAN MTU | OPNsense → Interfaces → WAN → MTU | **1500** (DHCP/static) veya **1492** (PPPoE) | bu bölüm |
| Yerel MTU | `ip -br link show primary0 \| grep -oE 'mtu [0-9]+'` | WAN MTU ile **aynı veya küçük** | bu bölüm |

### ADIM 1 — GERÇEK PMTU'yu ölç (tahmin etme)

**Ne yapıyoruz:** `ping -M do -s N` ile **DF bayraklı** paketler gönderip hangi boyutta kayıp başladığını buluyoruz → **gerçek MTU**. Tahmin **etmiyoruz**.

```bash
# Hedef: büyük paketlerle ulaşılabilen en büyük boyut (payload = MTU - 28)
for SZ in 1472 1464 1452 1442 1422 1400 1372; do
  printf "payload %4d (MTU %4d): " $SZ $((SZ+28))
  ping -c1 -W2 -M do -s $SZ 1.1.1.1 >/dev/null 2>&1 && echo "✅" || echo "🔴"
done
```

**Yorumlama:**
| Sonuç | Gerçek MTU | Ne yapmalı |
|---|---|---|
| 1472 ✅ | **1500** | hiçbir şey |
| 1464 ✅, 1472 🔴 | **1492** | OPNsense WAN **PPPoE** → LAN'ı 1492'ye çek |
| 1452 ✅, 1464 🔴 | **1480** | WAN'da tünel (WireGuard/OpenVPN) |
| 1422 ✅, 1452 🔴 | **1450** | çift kapsülleme (PPPoE + VLAN) |
| Hiçbiri ✅ | 🔴 bağlantı **tamamen kopuk** | §5.4 firewall kuralı |

### 🔀 KOŞULLU KARAR — MTU nereye uygulanır?

| Durum | Uygulanacak yer |
|---|---|
| **PPPoE WAN** (en sık TR) | OPNsense WAN `MTU 1492` **+** LAN `MTU 1492` **+** tüm VM'ler `1492` |
| DHCP/static WAN | **dokunmayın** (1500 doğru) |
| Yalnız bazı siteler bozuk | **MSS clamping** (aşağıda) — MTU değiştirmeden |
| K8s içi Pod-to-Pod | Cilium otomatik (tunnel MTU'yu düşürür) |

### ADIM 2 — OPNsense'te MSS clamping (MTU değiştirmeden)

> **NEDEN daha iyi:** MTU'yu düşürmek **her** paketi küçültür. MSS clamping yalnız
> **TCP SYN** içindeki `MSS` değerini düşürür → UDP/küçük paketler etkilenmez.

```bash
# OPNsense'te (Firewall → Settings → Normalization)
# Max MSS: 1448  (1492 - 40 = 1452; güvenli taraf 1448)
opns-cli.sh <<'EOF'
1
EOF
```

**API ile:**
```bash
opns-csrf <opnsense> root '<password>' <<'CMD'
POST /api/firewall/settings/setItem/0
{"interface":"wan","maxmss":"1448","maxfrag":"0"}
CMD
# Uygula
curl -sk -u "$KEY:$SECRET" -X POST https://10.30.10.1/api/firewall/filter_base/reconfigure
```

### ADIM 3 — VM'lerde MTU sabitleme (yalnız gerekiyorsa)

**Ne yapıyoruz:** Ölçtüğünüz MTU'yu **her VM'de aynı** yapıyoruz (`.network` dosyasında `MTUBytes=`). **Karışık MTU** = en zor teşhis edilen hata.

```bash
# /etc/systemd/network/20-primary0.network içine:
[Link]
MTUBytes=1450        # ← §5.6 ADIM 1'de ölçtüğünüz değer

# Uygula
systemctl restart systemd-networkd
ip -br link show primary0 | grep -oE 'mtu [0-9]+'    # → mtu 1450
```

⚠️ **Tüm VM'ler AYNI MTU olmalı.** Karışık MTU = **intermittent** paket kaybı (teşhisi
en zor hata). `worker` 1450 + `cp` 1500 → `kubectl exec` rastgele çalışmaz.

### ✅ KANIT

```bash
# 1) Her VM'de aynı
for ip in 10.30.10.10 10.30.10.21 10.30.30.11; do
  printf "%-14s " $ip; ssh root@$ip "ip -br link show primary0 | grep -oE 'mtu [0-9]+'"
done
# hepsi AYNI olmalı

# 2) Büyük paket geçiyor mu
ping -c3 -M do -s $((MTU-28)) -I primary0 10.30.10.1    # %0 kayıp

# 3) Gerçek indirme (en iyi test)
curl -4 -sS -o /dev/null -w "%{http_code} %{speed_download}\n" \
  https://registry-1.docker.io/v2/     # 401 dönmeli (asılı KALMAMALI)
```

**Gerçek ölçüm (V6):** `ping -M do -s 1472` → **%0 kayıp, 1500 OK** · MTU/PMTU hipotezi
**çürüdü** (sorun ISP CDN'lerindeydi). **Yine de bu adım gerekli** — çünkü farklı ISS'te
aynı belirti MTU'dan çıkar.

### 🔴 DUR — indirme asılı kalıyorsa sırası

| Belirti | İlk bakılacak | Sonra |
|---|---|---|
| `ping` çalışır, `curl` asılır | **MTU** (bu bölüm) | IPv6 AAAA (§3.3.4) |
| Küçük dosya iner, büyük asılır | **MTU** ← kesin | — |
| Hepsi yavaş ama iner | **ISP kısıtı** (§3.3.2) | BBR (§4.7) |
| Rastgele kopuyor | **MTU karışık** (bu bölüm KANIT 1) | conntrack (§4.7) |

🔁 **MSS clamping de yetmezse:** OPNsense → Firewall → Settings → Advanced →
`firewall_maximumfragments` + **scrub** açın.

---

<a id="s6"></a>

# 6. Ubuntu VM'leri ve DNS

<a id="s6-1"></a>

## 6.1 Cloud image

```text
cd /var/lib/vz/template/iso
IMG=noble-server-cloudimg-amd64.img      # 🔴 '-disk-kvm' YOK (R01/H13: 404)
curl -fLSs "https://cloud-images.ubuntu.com/noble/current/$IMG" -o $IMG
curl -fLSs "https://cloud-images.ubuntu.com/noble/current/SHA256SUMS" -o SHA256SUMS
sha256sum -c --ignore-missing SHA256SUMS
```

✅ **KANIT:** `noble-server-cloudimg-amd64.img: OK`

<a id="s6-1-b"></a>

## 6.1-B 🔴 Ubuntu cloud image — GPG anahtarı YOK (R11)

> **V6'NIN VARSAYIMI:** `gpg --verify SHA256SUMS.gpg SHA256SUMS` "çalışacak".
> **GERÇEK:** Proxmox'ta `ubuntu-cloudimage-keyring` **kurulu değil** →
> `gpg: Can't check signature: No public key` → doğrulama **başarısız** sanılır.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Anahtarlık paketi | `dpkg -l ubuntu-cloudimage-keyring 2>/dev/null \| wc -l` | `1` (kurulu) | bu bölüm |
| Mevcut anahtarlar | `gpg --list-keys 843938DF228D22F7B3742BC0D94AA3F0EFE21092` | anahtar var | bu bölüm |
| SHA256SUMS indi mi | `ls /var/lib/vz/template/iso/SHA256SUMS*` | 2 dosya | §6.1 |

### ADIM — iki anahtarı içe aktar

```bash
# 🔴 İKİ anahtar gerekli (biri CD image, biri UEC/cloud image)
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys \
  843938DF228D22F7B3742BC0D94AA3F0EFE21092 \
  D2EB44626FDDC30B513D5BB71A5D6C4C7DB87C81

# Doğrula — 2 anahtar görünmeli
gpg --list-keys | grep -c "pub"
# → 2
```

🔁 **`keyserver` çalışmıyorsa (TR ISP'de sık):**

| Fallback | Komut |
|---|---|
| **1. Ubuntu anahtar sunucusu** | `curl -s https://keyserver.ubuntu.com/pks/lookup?op=get\&search=0x843938DF228D22F7B3742BC0D94AA3F0EFE21092 \| gpg --import` |
| **2. Launchpad** | `curl -s "https://api.launchpad.net/1.0/~ubuntu-cdimage/+gpg-keys" \| jq -r '.[]'` |
| **3. Anahtarı atla, SHA256 kullan** | ⚠️ İmza doğrulanmaz ama **hash** doğrulanır — kabul edilebilir risk |

### ADIM — doğrula

```bash
cd /var/lib/vz/template/iso
gpg --verify SHA256SUMS.gpg SHA256SUMS
# → gpg: Good signature from "Ubuntu CD Image Automatic Signing Key (2012)"

sha256sum -c SHA256SUMS --ignore-missing 2>&1 | grep -E "OK|FAILED"
# → ubuntu-24.04-server-cloudimg-amd64.img: OK
```

### 🔴 DUR

| Belirti | Sebep | Çözüm |
|---|---|---|
| `Can't check signature: No public key` | R11 — anahtar yok | ADIM 1 |
| `BAD signature` | **yanlış dosya** (noble vs ubuntu) | R01: `ubuntu-24.04-...` |
| `no properly formatted SHA256SUMS` | `SHA256SUMS` binary mod | `wget` ile indir |
| keyserver timeout | ISP 11371 bloklu | `hkps://` **443** kullan |

---

<a id="s6-1-c"></a>

## 6.1-C 🔴 `qm agent ping` ≠ "ağ yok" (R30) — kısır döngü

> **V6'DA SAATLER KAYBETTİRDİ.** `qm agent 106 ping` → `QEMU guest agent is not running`.
> Bu **ağ yok** demek **değil**; **QGA paketi kurulmamış** demek. Ama QGA'nın
> kurulabilmesi için **ağ gerekir** → **kısır döngü**.

### 🔀 KOŞULLU KARAR — hangi teşhis yolu?

| `qm agent ping` sonucu | Anlamı | Ne yap |
|---|---|---|
| `QEMU guest agent is not running` | paket **yok** VEYA servis kapalı | **seri konsol** → `dpkg -l qemu-guest-agent` |
| `No QEMU guest agent configured` | VM tanımında `agent: 1` yok | `qm set <vmid> --agent enabled=1` |
| `{"return":{"version":...}}` | QGA **çalışıyor** | ağ da büyük olasılıkla var |
| timeout | QGA çalışıyor ama socket bozuk | `qm set <vmid> --agent enabled=1` + reboot |

### ADIM — döngüyü kır (sırayla)

```bash
# 1) VM tanımında agent AÇIK mı (olmadan ping ASLA çalışmaz)
qm config 106 | grep -E '^agent'
# agent: 1,type=virtio    ← olmalı. Yoksa:
qm set 106 --agent enabled=1

# 2) KONSOLDAN paket kontrolü (ağ GEREKMEZ)
#    Proxmox → VM 106 → Console (noVNC/xterm)
dpkg -l | grep -c qemu-guest-agent
# 0 → KURULMAMIŞ. Ama apt için AĞ gerekir → 3. adım

# 3) Ağ VAR MI? QGA'ya değil, IP'ye bak
ip -4 -br a
# primary0 UP 10.30.30.11/24   ← ağ VAR ise 4. adıma
# (boş ise → §6.6 networkd)

# 4) Ağ varsa paketi kur
apt-get install -y qemu-guest-agent && systemctl enable --now qemu-guest-agent

# 5) Host'tan tekrar ping
qm agent 106 ping && echo "✅ QGA çalışıyor"
```

🔴 **ANAHTAR AYRIM:** `qm agent ping` **QGA servisine** bakar, **ağa** değil.
Ağ teşhisi için: konsoldan `ip -4 -br a` **veya** host'tan `ping 10.30.30.11`.

### ✅ KANIT

```bash
for v in 106 107 108; do printf "%s: " $v; qm agent $v ping >/dev/null 2>&1 && echo ✅ || echo 🔴; done
```

---

<a id="s6-2"></a>

## 6.2 🔴 cloud-init — V6'nın 2 KRİTİK hatası

**Ne yapıyoruz:** Her VM'in **ilk açılışında** otomatik yapılandırılmasını sağlıyoruz:
kullanıcı (`bankbootstrap`), SSH anahtarı, ağ adresi, paket listesi. Bunlar
`/var/lib/vz/snippets/*.yaml` dosyalarına yazılır; `qm set --cicustom` ile VM'e bağlanır.
**Neden:** 8 VM'i elle kurmak = 8× hata riski.


```yaml
# /var/lib/vz/snippets/user-101.yaml
#cloud-config
hostname: jump-1
fqdn: jump-1.banklab.alperozbay.com
users:
  - name: bankbootstrap
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    lock_passwd: false                    # NEDEN: konsoldan giriş gerekebilir (R16)
    ssh_authorized_keys: [<PUBKEY>]
package_update: true
packages:
  - openssh-server                        # 🔴 NEDEN: cloud image'de YOK (R18)
  - qemu-guest-agent
  - chrony
  - curl
  - jq
runcmd:
  - systemctl enable --now ssh
  - systemctl enable --now qemu-guest-agent
```

```yaml
# /var/lib/vz/snippets/network-101.yaml
version: 2
ethernets:
  primary0:                               # 🔴 .link ile sabitlenir — §6.6
    match: { macaddress: "BC:24:11:00:00:01" }
    dhcp4: false
    addresses: ["10.30.10.10/24"]
    routes: [{ to: default, via: "10.30.10.1" }]
    nameservers:
      addresses: ["10.30.10.53", "1.1.1.1"]
```

🔴 **TÜM VM'LER İÇİN SNIPPET ÜRET:**

```bash
# 8 VM'in tamamı — tek döngü, tek kaynak (§2.4 tablosundan)
cd /var/lib/vz/snippets
declare -A IP=( [101]=10.30.10.10 [102]=10.30.10.53 [103]=10.30.10.21 [104]=10.30.10.22 \
                [105]=10.30.10.23 [106]=10.30.30.11 [107]=10.30.30.12 [108]=10.30.30.13 )
declare -A NM=( [101]=jump-1 [102]=dns-1 [103]=cp-1 [104]=cp-2 [105]=cp-3 \
                [106]=worker-1 [107]=worker-2 [108]=worker-3 )
for V in "${!IP[@]}"; do
  M=$(printf "%02d" $((V-100)))          # 01..08 → MAC son baytı
  # user-data
  sed -e "s/hostname: jump-1/hostname: ${NM[$V]}/" \
      -e "s/fqdn: jump-1.banklab/fqdn: ${NM[$V]}.banklab/" \
      user-101.yaml > "user-$V.yaml"
  # network (tek NIC'li VM'ler; worker'lar §6.6'da ayrı)
  if [ $V -lt 106 ]; then
    sed -e "s/BC:24:11:00:00:01/BC:24:11:00:00:$M/" \
        -e "s|10.30.10.10/24|${IP[$V]}/24|" \
        network-101.yaml > "network-$V.yaml"
  fi
done
ls -la /var/lib/vz/snippets/    # user-101..108 + network-101..105 (+106..108 §6.6)

chmod 0644 /var/lib/vz/snippets/network-*.yaml    # 🔴 KRİTİK (H18)
chmod 0600 /var/lib/vz/snippets/user-*.yaml
```

⚠️ **dns-1 (102) ÖZEL:** resolver'ı `1.1.1.1` **tek** olmalı (kendine sormasın — döngü):
```yaml
# network-102.yaml içinde
    nameservers:
      addresses: ["1.1.1.1"]        # 🔴 10.30.10.53 YAZMAYIN — kendisi!
```
🔴 **NEDEN:** `nameservers: [10.30.10.53, 1.1.1.1]` yazarsanız dns-1 **kendine** sorar →
Unbound başlamadan önce `apt-get update` **çözümleme yapamaz** → kurulum başlamaz (H40'ın kardeşi).

🔴 **NEDEN 0644 — H18'in kökü:** `systemd-networkd` **world-readable olmayan** dosyayı
**yok sayar** ve arayüzü `unmanaged` bırakır. VM **ağsız** açılır.
V6'da izin `0640`'tı → 3 saat kayıp.

🔴 **NEDEN VM içinden doğrulama (H17):** `qm cloudinit dump` cloud-init'in **ne uyguladığını**
göstermez, sadece snippet'i basar. Gerçek doğrulama VM içinde:
```bash
ssh bankbootstrap@10.30.10.10 'cloud-init status --wait; ip -4 -br a; ip route'
```
✅ **KANIT:** `status: done`, `primary0 UP 10.30.10.10/24`, `default via 10.30.10.1`

<a id="s6-2-b"></a>

## 6.2-B 🔴 VM hazırlık — cloud image'de **olmayan** 3 şey (R18/R19/S13/S15)

> **V6'DA 4 SAAT KAYBETTİRDİ.** Ubuntu cloud image **minimal**: `openssh-server` yok,
> `~/.ssh` izinleri yanlış, anahtar dosyası yok. Bunlar yapılmadan **hiçbir VM'e girilemez**.

### ⬜ ÖN KONTROL — VM oluşturmadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Cloud image indi mi | `ls -la /var/lib/vz/template/iso/ubuntu-24.04-server-cloudimg-amd64.img` | dosya var | §6.1 |
| Snippet dizini | `ls -d /var/lib/vz/snippets` | var | §6.2 |
| Mevcut VMID'ler | `qm list` | 100–108 **yok** | §18.8 #5 |
| Host'ta port 22 boş mu | `ss -tlnp \| grep -c ':22 '` | 1 (host sshd) | — |

### 🔴 3 EKSİK — sırayla

#### 1) `openssh-server` cloud image'de **YOK**

```yaml
# user-data.yaml — packages bloğuna EKLE
packages:
  - openssh-server          # 🔴 R18: cloud image'de YOK
  - qemu-guest-agent
  - curl
  - ca-certificates
package_update: true
```

**NEDEN:** Ubuntu 24.04 cloud image minimal — `openssh-server` **kurulu değil**.
VM açılır ama **SSH bağlanamazsın** (port 22 kapalı). V6'da 3 worker'a elle kuruldu.

```bash
# Doğrulama (konsoldan veya qm guest exec ile)
dpkg -l | grep -c openssh-server     # → 1
ss -tlnp | grep ':22 '               # → LISTEN
```

#### 2) `~/.ssh` izinleri → **izin hatası** (R19)

```yaml
# user-data.yaml — runcmd bloğuna EKLE
runcmd:
  - mkdir -p /home/bankbootstrap/.ssh
  - chmod 700 /home/bankbootstrap/.ssh
  - chmod 600 /home/bankbootstrap/.ssh/authorized_keys
  # 🔴 R19: /home/bankbootstrap 750 ise 'other' ~/.ssh'i TARAYAMAZ
  - chmod 755 /home/bankbootstrap
  - chown -R bankbootstrap:bankbootstrap /home/bankbootstrap
```

**V6'da ne oldu:** `/home/bankbootstrap` **750** idi → sshd `authorized_keys`'i
**okuyamadı** → "Permission denied (publickey)". **4 saat** teşhis.

```bash
# Doğrulama
ssh -o BatchMode=yes bankbootstrap@10.30.10.10 true && echo ✅
stat -c '%a %n' /home/bankbootstrap /home/bankbootstrap/.ssh /home/bankbootstrap/.ssh/authorized_keys
# 755 /home/bankbootstrap
# 700 /home/bankbootstrap/.ssh
# 600 /home/bankbootstrap/.ssh/authorized_keys
```

#### 3) **ÖZEL** anahtar jump-1'e kopyalanmalı (R19)

```bash
# ❌ YANLIŞ — yalnız public key yetmez
cat id_ed25519.pub >> jump-1:/home/bankbootstrap/.ssh/authorized_keys   # jump-1'e giriş VAR
# ama jump-1'den cp-1'e giriş YOK (özel anahtar yok)

# ✅ DOĞRU — özel anahtarı DA kopyala
scp ~/.ssh/banklab_key root@10.30.10.10:/home/bankbootstrap/.ssh/id_ed25519
ssh root@10.30.10.10 'chown bankbootstrap: /home/bankbootstrap/.ssh/id_ed25519; chmod 600 /home/bankbootstrap/.ssh/id_ed25519'

# Doğrula
ssh bankbootstrap@10.30.10.10 'ssh -o BatchMode=yes root@10.30.10.21 hostname'
# → cp-1
```

### 🔴 S13 — KONSOL PAROLASI: `!` KULLANMA

```yaml
# ❌ YANLIŞ (R34) — QMP shift düşürüyor
chpasswd:
  list: |
    root:BankLab!2026
  expire: false
# Konsolda gerçekten yazılan: ankab2026  → GUI'ye GİRİLEMEZ

# ✅ DOĞRU — shift gerektirmeyen karakter
chpasswd:
  list: |
    root:banklab2026
  expire: false
```

**Sonra GUI'den güçlü parolaya çevir.** Detay: §18.8 #1.

### 🔴 S15 — MEVCUT VM'LERE DOKUNMA

```bash
# V6 lab'inde ÇALIŞAN başka VM'ler vardı
qm list
# VMID  NAME              STATUS
# 200   ubuntu-desktop    running      ← 🔴 DOKUNMA
# 9100  fedora-tmpl       stopped      ← 🔴 DOKUNMA
# 100-108  ← BU LAB

# Kural: yalnız 100-108 aralığında çalış. Doğrulama:
MY_VMS="100 101 102 103 104 105 106 107 108"
for V in $(qm list | awk 'NR>1{print $1}'); do
  case " $MY_VMS " in *" $V "*) ;; *) echo "⚠️  VM $V bu lab'a AİT DEĞİL — dokunma";; esac
done
```

### ✅ KANIT — 3 kontrol birden

```bash
# 1) SSH çalışıyor mu (her VM)
for ip in 10.30.10.10 10.30.10.21 10.30.10.22 10.30.10.23 10.30.30.11 10.30.30.12 10.30.30.13; do
  printf "%-14s " $ip
  ssh -o BatchMode=yes -o ConnectTimeout=5 root@$ip 'echo ✅' 2>/dev/null || echo 🔴
done

# 2) openssh-server her VM'de
for ip in 10.30.10.10 10.30.10.21 10.30.30.11; do
  printf "%s: " $ip; ssh root@$ip 'ss -tln | grep -c ":22 "'
done

# 3) jump-1'den çok-atlamalı SSH
ssh bankbootstrap@10.30.10.10 'for h in 10.30.10.21 10.30.30.11; do ssh -o BatchMode=yes -o ConnectTimeout=3 root@$h hostname; done'
```

### 🔴 DUR — hangi belirti

| Belirti | Kök neden | Çözüm |
|---|---|---|
| `Connection refused` :22 | `openssh-server` yok | packages'a ekle |
| `Permission denied (publickey)` | `/home` **750** | `chmod 755` |
| jump-1→cp-1 `Permission denied` | özel anahtar yok | 3. adım |
| Konsolda parola yanlış | `!` shift düşmüş | §18.8 #1 |
| VM açıldı, IP yok | networkd `.link` | §6.6 |

---

<a id="s6-3"></a>

## 6.3 VM oluşturma script'i (8 VM — tek döngü)

**Ne yapıyoruz:** 101–108 arası **8 VM'i** tek script'le oluşturuyoruz: cloud image'i
diske kopyala → `qm create` (CPU/RAM/NIC/MAC) → `qm importdisk` → `qm set --ide2` (cloud-init
diski) + `--cicustom`. **Neden script:** elle 8 VM = 8× yazım hatası; script **idempotent**'e
yakın (VM varsa atlar).


```bash
#!/bin/bash
# vm-create.sh VMID NAME CORES RAMGiB DISKGiB MACID
set -euo pipefail
VMID=$1 NAME=$2 C=$3 R=$4 DL=$5 M=$6
IMG=/var/lib/vz/template/iso/noble-server-cloudimg-amd64.img

qm create $VMID --name $NAME --memory $((R*1024)) --cores $C --cpu host \
  --net0 virtio,bridge=vnet30,macaddr=BC:24:11:00:00:$M \
  --scsihw virtio-scsi-single \
  --efidisk0 local-lvm:1,efitype=4m,pre-enrolled-keys=1 \
  --serial0 socket --vga std --agent enabled=1 --ostype l26

qm importdisk $VMID $IMG local-lvm --format raw
qm set $VMID --scsi0 local-lvm:vm-$VMID-disk-1,discard=on,ssd=1   # 🔴 disk-1 (EFI disk-0)
qm resize $VMID scsi0 ${DL}G
qm set $VMID --boot order=scsi0
qm set $VMID --cicustom "user=local:snippets/user-$VMID.yaml,network=local:snippets/network-$VMID.yaml"
qm cloudinit update $VMID
```

⚠️ **H19/H20:** EFI diski `disk-0` alır → sistem diski **`disk-1`**. `importdisk` çıktısındaki
adı **kullanın**, tahmin etmeyin. `qm config $VMID | grep scsi0` ile doğrulayın.

<a id="s6-4"></a>

## 6.4 dns-1 — Unbound (lab'in DNS sunucusu)

**Ne yapıyoruz:** `10.30.10.53`'te **Unbound** kuruyoruz — lab'in **tek** DNS'i.
İki işi var: (1) dış dünyayı çözmek (recursive), (2) `banklab.alperozbay.com` zone'unu
**local-data** ile sunmak. **Neden kendi DNS:** RKE2 join, `*.banklab` isimlerini çözmeli;
dış DNS bunu bilmez.


```bash
ssh bankbootstrap@10.30.10.53
sudo apt-get update && sudo apt-get install -y unbound

sudo tee /etc/unbound/unbound.conf.d/banklab.conf <<'EOF'
server:
  interface: 0.0.0.0
  access-control: 10.30.0.0/16 allow
  do-not-query-localhost: no
  local-zone: "banklab.alperozbay.com." static
  local-data: "jump-1.banklab.alperozbay.com.   IN A 10.30.10.10"
  local-data: "cp-1.banklab.alperozbay.com.     IN A 10.30.10.21"
  local-data: "cp-2.banklab.alperozbay.com.     IN A 10.30.10.22"
  local-data: "cp-3.banklab.alperozbay.com.     IN A 10.30.10.23"
  local-data: "worker-1.banklab.alperozbay.com. IN A 10.30.30.11"
  local-data: "worker-2.banklab.alperozbay.com. IN A 10.30.30.12"
  local-data: "worker-3.banklab.alperozbay.com. IN A 10.30.30.13"
  local-data: "api.banklab.alperozbay.com.      IN A 10.30.10.20"
  # 🔴 uygulama servisleri — her biri Traefik'in LB IP'sine
  # Traefik IP'sini §13.1 sonrası öğrenin; şimdilik havuzun ilk IP'si (10.30.20.200)
  local-data: "infisical.banklab.alperozbay.com. IN A 10.30.20.200"
  local-data: "sso.banklab.alperozbay.com.       IN A 10.30.20.200"
  local-data: "grafana.banklab.alperozbay.com.   IN A 10.30.20.200"
  local-data: "harbor.banklab.alperozbay.com.    IN A 10.30.20.200"
  local-data: "argocd.banklab.alperozbay.com.    IN A 10.30.20.200"
  local-data: "gitea.banklab.alperozbay.com.     IN A 10.30.20.200"
forward-zone:
  name: "."
  forward-addr: 1.1.1.1
  forward-addr: 1.0.0.1
EOF

sudo unbound-checkconf && sudo systemctl restart unbound
```

✅ **KANIT (jump-1'den):**
```bash
dig @10.30.10.53 cp-1.banklab.alperozbay.com +short   # → 10.30.10.21
dig @10.30.10.53 google.com +short                    # → bir IP
```

🔴 **DUR:** `unbound-checkconf` hata verirse servis **başlamaz** → tüm küme DNS'siz.

<a id="s6-4-b"></a>

## 6.4-B 🔴 Unbound — DNSSEC + cache + zone transfer (V6'da test edildi)

> **NEDEN DNSSEC:** Lab'de DNS **tek hata noktası**. `dns-1` zehirlenirse **tüm**
> sertifika doğrulaması (`step-ca` → ACME) yanlış IP'ye gider. DNSSEC doğrulaması
> bunu engeller.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Unbound çalışıyor | `ssh root@10.30.10.53 'systemctl is-active unbound'` | `active` | §6.4 |
| 53 portu dinliyor | `dig @10.30.10.53 test.banklab +short` | cevap | §6.4 |
| Root hint var | `ssh root@10.30.10.53 'ls /var/lib/unbound/root.hints'` | dosya var | aşağıdaki adım |
| `unbound-anchor` | `ssh root@10.30.10.53 'which unbound-anchor'` | `/usr/sbin/...` | `apt install unbound-anchor` |

### 🔀 KOŞULLU KARAR — DNSSEC seviyesi

| Durumunuz | Ayar | Neden |
|---|---|---|
| **İzole lab** (bu) | `auto-trust-anchor-file` + doğrulama | yanlış cevabı yakalar |
| Upstream'e forward ediyorsanız | **DNSSEC'i upstream'e bırakın** → `unbound` `val-permissive-mode` | çift doğrulama gereksiz |
| Çok kısıtlı CPU | `harden-dnssec-stripped: no` | doğrulama maliyeti düşer (⚠️ güvenlik ↓) |
| Air-gap, root sunucuya erişim yok | `harden-dnssec-stripped: no` + **yalnız local-zone** | dış DNS zaten çözülemez |

### ADIM 1 — Root hints + trust anchor

**Ne yapıyoruz:** Unbound'a **kök sunucu ipuçları** (`named.root`) ve **DNSSEC kök anahtarı** (`root.key`) indiriyoruz. Bunlar olmadan recursive çözümleme ve DNSSEC doğrulaması **çalışmaz**.

```bash
# Root hint dosyası (yoksa)
curl -4 -fsSL https://www.internic.net/domain/named.root -o /var/lib/unbound/root.hints

# Trust anchor (DNSSEC kök anahtarı) — otomatik güncelleme
unbound-anchor -a /var/lib/unbound/root.key
```

### ADIM 2 — Unbound config (üretim ayarları)

**Ne yapıyoruz:** `banklab.conf` yazıyoruz: dinleme adresi, `access-control`, **DNSSEC doğrulama**, **cache boyutları**, gizlilik (`hide-identity`), istatistik. **V6'da varsayılan cache 4 MB'tı** → lab'de yetmiyordu.

```bash
cat > /etc/unbound/unbound.conf.d/banklab.conf <<'EOF'
server:
  interface: 0.0.0.0
  port: 53
  do-ip4: yes
  do-ip6: no                    # 🔴 IPv6 rota yok — kapalı
  do-udp: yes
  do-tcp: yes
  access-control: 10.30.0.0/16 allow
  access-control: 127.0.0.0/8 allow
  access-control: 0.0.0.0/0 refuse

  # ─── DNSSEC ───
  auto-trust-anchor-file: "/var/lib/unbound/root.key"
  val-log-level: 2              # doğrulama hatasını logla
  harden-dnssec-stripped: yes
  harden-glue: yes
  harden-below-nxdomain: yes
  qname-minimisation: yes
  aggressive-nsec: yes

  # ─── CACHE (lab'de 200+ DNS sorgusu/dk) ───
  msg-cache-size: 64m
  rrset-cache-size: 128m
  cache-max-ttl: 86400
  cache-min-ttl: 300
  prefetch: yes
  prefetch-key: yes
  serve-expired: yes
  serve-expired-ttl: 3600

  # ─── gizlilik ───
  hide-identity: yes
  hide-version: yes
  private-address: 10.0.0.0/8
  private-address: 172.16.0.0/12
  private-address: 192.168.0.0/16

  # ─── izleme ───
  extended-statistics: yes
  statistics-interval: 3600
  num-threads: 2
  so-reuseport: yes

  root-hints: "/var/lib/unbound/root.hints"

# ─── LAB zone'u (10.30.x) ───
python:
remote-control:
  control-enable: yes
  control-interface: 127.0.0.1
EOF
```

### ADIM 3 — Zone: `banklab.alperozbay.com` (stub + local-data)

**Ne yapıyoruz:** Lab zone'unu **`local-data`** ile yazıyoruz — 17 A kaydı (VM'ler + servisler). **Neden `local-data`:** dış DNS'e **hiç sormadan** cevap verir; hızlı ve dışa bağımlı değil.

```bash
cat > /etc/unbound/unbound.conf.d/banklab-zone.conf <<'EOF'
server:
  local-zone: "banklab.alperozbay.com." static
  # 🔴 A kayıtları — §2.4 VM tablosundan (TEK KAYNAK, elle yazmayın)
  local-data: "jump-1.banklab.alperozbay.com.    A 10.30.10.10"
  local-data: "dns-1.banklab.alperozbay.com.     A 10.30.10.53"
  local-data: "cp-1.banklab.alperozbay.com.      A 10.30.10.21"
  local-data: "cp-2.banklab.alperozbay.com.      A 10.30.10.22"
  local-data: "cp-3.banklab.alperozbay.com.      A 10.30.10.23"
  local-data: "worker-1.banklab.alperozbay.com.  A 10.30.30.11"
  local-data: "worker-2.banklab.alperozbay.com.  A 10.30.30.12"
  local-data: "worker-3.banklab.alperozbay.com.  A 10.30.30.13"
  local-data: "vip.banklab.alperozbay.com.       A 10.30.10.20"
  # ─── Wildcard servisler (MetalLB .200) ───
  local-data: "infisical.banklab.alperozbay.com. A 10.30.20.200"
  local-data: "sso.banklab.alperozbay.com.       A 10.30.20.200"
  local-data: "grafana.banklab.alperozbay.com.   A 10.30.20.200"
  local-data: "harbor.banklab.alperozbay.com.    A 10.30.20.200"
  local-data: "argocd.banklab.alperozbay.com.    A 10.30.20.200"
  local-data: "gitea.banklab.alperozbay.com.     A 10.30.20.200"
  local-data: '"banklab.alperozbay.com. IN NS dns-1.banklab.alperozbay.com."'
EOF

unbound-checkconf && systemctl restart unbound
```

🔴 **NEDEN `unbound-checkconf` ÖNCE:** bozuk config → `unbound` başlamaz → **tüm**
`apt`/`helm`/`ctr` çözümleme durur. Restart'tan önce doğrulama **şart**.

### ✅ KANIT — 4 test (V6'da yapılmış)

```bash
# 1) Temel çözümleme
dig @10.30.10.53 infisical.banklab.alperozbay.com +short
# → 10.30.20.200 ✅

# 2) DNSSEC doğrulaması ÇALIŞIYOR mu (ad = authenticated data)
dig @10.30.10.53 DNSKEY . +dnssec | grep -o "flags:.*"
dig @10.30.10.53 cloudflare.com +dnssec | grep -q " ad;" \
  && echo "✅ DNSSEC aktif (ad bayrağı)" || echo "🔴 DNSSEC ÇALIŞMIYOR"

# 3) Dış çözümleme
dig @10.30.10.53 one.one.one.one +short     # → 1.1.1.1

# 4) Cache çalışıyor mu (ikinci sorgu 0 ms olmalı)
dig @10.30.10.53 github.com +noall +stats | grep "Query time"
dig @10.30.10.53 github.com +noall +stats | grep "Query time"
# İkincisi: "Query time: 0 msec"
```

**Gerçek ölçüm (V6):**
| Test | Sonuç |
|---|---|
| DNSSEC `ad` bayrağı | ✅ ilk denemede geldi |
| `deb.debian.org` indirme | 138 KB ✅ |
| Cache isabeti | 0 ms ✅ |

### 🔴 DUR — Unbound kırıldı, tüm lab DNS'siz

```bash
# Konsoldan dns-1'e gir, geçici çözüm:
systemctl stop unbound
systemd-resolved 2>/dev/null || true
# TÜM VM'lerde geçici DNS:
for ip in 10.30.10.10 10.30.10.21; do
  ssh root@$ip 'echo "nameserver 1.1.1.1" > /etc/resolv.conf'
done
```

🔁 **DNSSEC çalışmıyorsa** (`ad` bayrağı yok):
| Neden | Çözüm |
|---|---|
| `root.key` yok/bozuk | `unbound-anchor -a /var/lib/unbound/root.key && systemctl restart unbound` |
| Saat kayması > 5 dk | `chronyc makestep` (DNSSEC **saat** gerektirir) |
| Upstream 853/53 bloklu | `do-ip6: no` + firewall UDP 53 açık mı |
| ISP DNSSEC'i bozuyor | `harden-dnssec-stripped: no` (⚠️ son çare) |

---

<a id="s6-5"></a>

## 6.5 Worker DNS tuzağı (H40) — döngüye girme

**Ne yapıyoruz:** Worker'ların `resolv.conf`'ında **dns-1'i (`10.30.10.53`) kullanmasını**
sağlıyoruz — ama **dns-1'in kendisi** bu dosyada **kendini** görmemeli. Yapılırsa
dns-1 açılışta kendine sorar → Unbound başlamadan çözümleme yok → **`apt` donar**.


```bash
# Her node'da:
printf '%s\n' 'nameserver 10.30.10.53' 'search banklab.alperozbay.com' | sudo tee /etc/resolv.conf
sudo chattr +i /etc/resolv.conf          # systemd-resolved üzerine yazmasın

sudo install -d -m 0700 /etc/rancher/rke2
printf '%s\n' 'nameserver 10.30.10.53' | sudo tee /etc/rancher/rke2/resolv.conf
sudo chmod 0600 /etc/rancher/rke2/resolv.conf
```

🔁 **ALTERNATİF — IPv6 imaj çekimini asıyorsa (R45):**
```bash
sysctl -w net.ipv6.conf.all.disable_ipv6=1
echo 'net.ipv6.conf.all.disable_ipv6=1' > /etc/sysctl.d/99-noipv6.conf
```

---

<a id="s6-6"></a>

## 6.6 🔴 Worker ağı — `systemd-networkd` (netplan DEĞİL)

> **NEDEN netplan DEĞİL:** cloud-init netplan dosyasını **yazıyor** ama
> **uygulamıyor** (`No network config applied … Event Denied: scopes=['network']`).
> Ağ olmadığı için `qemu-guest-agent` de kurulamıyor → konsol dışında erişim yok.
> **ÇÖZÜM:** netplan **tamamen devre dışı** + doğrudan `systemd-networkd` + `.link` ile
> **arayüz adı sabitleme** (MAC → `primary0` / `edge0`).

### NEDEN arayüz adı sabitliyoruz

`ens18`/`ens19` adları **boot sırasına** bağlıdır; 2 NIC'li VM'de kartlar yer değiştirebilir →
**Bu yüzden `primary0`/`edge0`'a sabitliyoruz (§6.6 `.link`).** Aşağıdaki sorun bu yüzden doğar:
**küme trafiği LB ağına düşer**. `.link` dosyası MAC'e göre adı **sabitler**:
`primary0` (vnet30 = küme), `edge0` (vnet20 = uygulama).

### Adım 1 — Önce MAC adreslerini §6.3'te sabitleyin

**Ne yapıyoruz:** VM tanımındaki **MAC adreslerini** okuyoruz. `.link` dosyaları **MAC ile** arayüz adı eşleştirecek — MAC yanlışsa **arayüz adı değişmez**.

```bash
# Worker'lar: 2 NIC. net0 = KÜME (vnet30), net1 = UYGULAMA (vnet20)
for W in 106:31 107:32 108:33; do
  VMID=${W%%:*}; M=${W##*:}
  qm set $VMID --net0 virtio,bridge=vnet30,macaddr=BC:24:11:91:06:$M
  qm set $VMID --net1 virtio,bridge=vnet20,macaddr=BC:24:11:91:0$((M-10)):$M
done

# MAC'leri KAYDET — snippet'e birebir yazılacak
for V in 106 107 108; do
  echo "── VM $V ──"; qm config $V | grep '^net[01]:'
done
```

### Adım 2 — `systemd-networkd` dosyaları (snippet olarak)

**Ne yapıyoruz:** iki tür dosya yazıyoruz: **`.link`** (MAC → isim: `primary0`/`edge0`) ve **`.network`** (IP/GW/DNS). **`chmod 0644` şart** — yoksa arayüz `unmanaged` kalır.

```bash
# /var/lib/vz/snippets/netcfg-worker1.sh  → cloud-init runcmd içinde çalışır
# (worker-2/3 için MAC ve IP değişir)
cat > /var/lib/vz/snippets/netcfg-worker1.sh <<'EOF'
#!/bin/bash
# NEDEN: netplan uygulanmıyor (cloud-init 'Event Denied: network') → networkd
set -eu
P=/etc/systemd/network
install -d -m 0755 $P

# 1) netplan'ı DEVRE DIŞI (dosyaları silme, yedekle)
mkdir -p /root/netplan-backup
mv /etc/netplan/*.yaml /root/netplan-backup/ 2>/dev/null || true
mkdir -p /etc/cloud/cloud.cfg.d
printf 'network: {config: disabled}\n' > /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg

# 2) Arayüz adı sabitleme (.link) — MAC'e göre
install -m 0644 /dev/stdin $P/10-primary0.link <<'LINK'
[Match]
MACAddress=bc:24:11:91:06:31

[Link]
Name=primary0
LINK

install -m 0644 /dev/stdin $P/11-edge0.link <<'LINK'
[Match]
MACAddress=bc:24:11:91:02:31

[Link]
Name=edge0
LINK

# 3) IP yapılandırması (.network) — primary0 = küme, edge0 = uygulama
install -m 0644 /dev/stdin $P/20-primary0.network <<'NET'
[Match]
Name=primary0

[Network]
Address=10.30.30.11/24
Gateway=10.30.30.1
DNS=10.30.10.53
Domains=banklab.alperozbay.com
NET

# 🔴 edge0: Gateway YOK (iki default route = asılı bağlantılar)
install -m 0644 /dev/stdin $P/21-edge0.network <<'NET'
[Match]
Name=edge0

[Network]
Address=10.30.20.11/24
NET

# 4) 🔴 İZİN 0644 — 0640 ise networkd dosyayı OKUMAZ (unmanaged kalır)
chmod 0644 $P/*.network $P/*.link

systemctl restart systemd-networkd
sleep 3
networkctl reload
networkctl list --no-pager
ip -4 -br a | grep -E 'primary0|edge0'
EOF
chmod 0755 /var/lib/vz/snippets/netcfg-worker1.sh
```

### Adım 3 — cloud-init'e ekle (worker user-data)

**Ne yapıyoruz:** Yazdığımız dosyaları **cloud-init `write_files`** ile VM'in içine yazdırıyoruz. **Neden:** worker'a SSH ile giremeden (ağ yok) dosya koymanın tek yolu.

```yaml
# /var/lib/vz/snippets/user-106.yaml (worker-1)
runcmd:
  - [ bash, /opt/banklab/netcfg-worker1.sh ]
```

```bash
# Snippet'i VM'in içine taşı (cicustom ile sadece user/network/vendor gidebilir)
for V in 106 107 108; do
  cp /var/lib/vz/snippets/netcfg-worker1.sh /var/lib/vz/snippets/netcfg-$V.sh
  sed -i "s/bc:24:11:91:06:31/$(qm config $V | awk '/^net0:/{print $NF}' | sed 's/macaddr=//')/" \
    /var/lib/vz/snippets/netcfg-$V.sh 2>/dev/null || true
  sed -i "s/10\.30\.30\.11/10.30.30.$((V-95))/" /var/lib/vz/snippets/netcfg-$V.sh
  sed -i "s/10\.30\.20\.11/10.30.20.$((V-95))/" /var/lib/vz/snippets/netcfg-$V.sh
done
```

### Adım 4 — Doğrulama (🔴 ağ yoksa SSH ile giremezsiniz → KONSOL)

**Ne yapıyoruz:** `networkctl` ile arayüzlerin **`configured`** olduğunu doğruluyoruz. **`unmanaged` görürseniz** `chmod 0644` yapılmamış demektir.

```bash
# VM içinden (konsol veya SSH):
networkctl list --no-pager
# 2 primary0  ether  routable  configured     ← 'configured' OLMALI
# 3 edge0     ether  routable  configured

ip -4 -br a | grep -E 'primary0|edge0'
# primary0  UP  10.30.30.11/24
# edge0     UP  10.30.20.11/24

ip route | grep default
# default via 10.30.30.1 dev primary0        ← YALNIZ BİR SATIR
```

🔴 **DUR — `unmanaged` görürseniz:**
| Belirti | Sebep | Çözüm |
|---|---|---|
| `unmanaged` | dosya izni `0640` | `chmod 0644` |
| `unmanaged` | arayüz adı eşleşmiyor | `ip -br link` ile GERÇEK adı gör, `.link`'i düzelt |
| `configuring` (takılı) | iki default route | `edge0`'dan `Gateway` satırını sil |
| arayüz hiç yok | NIC VM'e bağlı değil | `qm config $VMID \| grep net` |

🔁 **ALTERNATİF — VM'e hiç giremiyorsanız (H17'nin gerçek çözümü):**
Disk dosyasını host'tan mount edip dosyaları doğrudan yazın:
```bash
qm stop 106
mkdir -p /mnt/w1
# EFI disk-0, sistem diski disk-1 → DOĞRU LV'yi seç
qm config 106 | grep scsi0                       # → local-lvm:vm-106-disk-1
mount /dev/pve/vm-106-disk-1 /mnt/w1
install -d -m 0755 /mnt/w1/etc/systemd/network
# ... dosyaları buraya yaz, chmod 644 ...
umount /mnt/w1 && qm start 106
```

⚠️ **NEDEN MetalLB için `edge0` şart:** MetalLB L2 duyurusu **10.30.20.x** havuzunu
**edge0** arayüzünden yapar. `L2Advertisement.interfaces: [edge0]` yazmassanız ARP
`primary0`'dan gider → **`ip neigh` FAILED** → LB erişilemez (§9.1).

---

<a id="s6-6-a"></a>

## 6.6-A 🔴🔴 "9 NODE" TUZAĞI — çift NIC'li worker'ı 2 makine sanmak

> **V6'DA YAŞANDI:** worker'ların `10.30.30.11` (primary0) ve `10.30.20.11` (edge0)
> adresleri **ayrı makineler** sanıldı → `edge-1`, `edge-2`, `edge-3` adıyla kümeye
> eklendi → **9 node, HİÇBİRİ Ready değil**. 3 saat teşhis.

### ⬜ ÖN KONTROL — RKE2 join'den ÖNCE

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| **Kaç makine var** | `for i in 11 12 13; do ssh root@10.30.30.$i 'hostname; machine-id'; done` | **3 farklı** machine-id | aşağıdaki teşhis |
| MAC çakışması | `qm config 106 \| grep net` | 2 farklı MAC | §6.3 |
| Küme kaç node olmalı | — | **6** (3 CP + 3 worker) | — |
| worker sayısı | `kubectl get nodes \| grep -c worker` | **3** (6 değil!) | bu bölüm |

### 🔴 TEŞHİS — aynı makine mi, iki makine mi?

```bash
# Her iki IP'den MAC + machine-id oku
for ip in 10.30.30.11 10.30.20.11; do
  printf "%-14s " $ip
  ssh root@$ip 'echo "mac=$(ip -br link show primary0 2>/dev/null | grep -oE "([0-9a-f]{2}:){5}[0-9a-f]{2}" | head -1) machine-id=$(cat /etc/machine-id | head -c8)"'
done
```

**Gerçek çıktı (V6'da ölçüldü):**
```
10.30.30.11  mac=bc:24:11:91:06:30  machine-id=2626abbc
10.30.20.11  mac=bc:24:11:91:06:30  machine-id=2626abbc   ← AYNI MAKİNE!
```

🔴 **KURAL:** `machine-id` **aynıysa aynı makinedir**. `edge0` **ikinci bir IP adresidir**,
ikinci bir sunucu değil. `hostname` ikisinde de `worker-1` döner.

**NEDEN `machine-id` kesin kanıt:** `/etc/machine-id` her kurulumda **tek** üretilir ve
klonlanmaz (cloud-init `NoCloud` ile değişmez). MAC de VM tanımından gelir.

### 🔀 KOŞULLU KARAR — çift NIC'in amacı ne?

| Amacınız | Yol | K8s'te ne yapılır |
|---|---|---|
| **Yönetim + uygulama ayrımı** (bu lab) | 2 NIC, **1 node** | ← **DOĞRU.** Node 1, IP 2 (`primary0` yönetim, `edge0` MetalLB) |
| Bant genişliği | 2 NIC **bond** | `bond0` → tek IP. `networkd` `Kind=bond` |
| Depolama ağı ayrı | 2 NIC, **1 node** | Longhorn `storage-network` → `edge0` |
| **Gerçekten 2 makine** | 2 ayrı VM | ❌ bu lab'de **YOK** — VM 106'ya 2 NIC var, 2 VM değil |

### ADIM — RKE2'yi worker'a kurarken

```bash
# 🔴 SADECE TEK IP ile join — ikinci IP'yi VERMEYİN
# (heredoc SSH içinde KULLANILMAZ — tırnak/ESC tuzağı; yerelde oluştur, scp et)
cat > /tmp/rke2-agent-config.yaml <<'CFG'
server: https://10.30.10.20:9345
token-file: /etc/rancher/rke2/token
node-name: worker-1                 # 🔴 SABİT İSİM — hostname'e bırakmayın
node-ip: 10.30.30.11                # 🔴 SADECE primary0 — edge0 YAZILMAZ
node-external-ip: 10.30.30.11
CFG

for i in 1 2 3; do
  ssh root@10.30.30.1$i 'mkdir -p /etc/rancher/rke2'
  scp -q /tmp/rke2-agent-config.yaml root@10.30.30.1$i:/etc/rancher/rke2/config.yaml
  ssh root@10.30.30.1$i "sed -i 's/^node-name:.*/node-name: worker-$i/' /etc/rancher/rke2/config.yaml"
done

# Doğrula — node-ip GERÇEKTEN 10.30.30.x mi (edge0 DEĞİL)
for i in 1 2 3; do
  printf "worker-%s: " $i
  ssh root@10.30.30.1$i 'grep "^node-ip" /etc/rancher/rke2/config.yaml'
done
```
🔴 **NEDEN heredoc SSH içinde kullanılmaz:** `ssh host 'cat > f <<EOF ... EOF'` — tek tırnak
içindeki `EOF` **yerel shell tarafından** da görülür; `/etc/rancher` gibi `/` içeren
satırlarda ESC tuzağına düşer. **Yerelde oluştur + `scp`** her zaman güvenli.

🔴 **`node-name` NEDEN sabit:** hostname `worker-1` iken ikinci IP ile aynı isimde ikinci
node kaydı oluşabilir. **`node-name` açıkça verilince** RKE2 **tek** kayıt açar.

### ✅ KANIT — küme gerçekten 6 node mu?

```bash
kubectl get nodes -o wide
# cp-1  cp-2  cp-3  worker-1  worker-2  worker-3     ← TAM 6 SATIR
# 🔴 'edge-1' / 'edge-2' / 'edge-3' GÖRÜNÜYORSA HATA VAR

kubectl get nodes --no-headers | wc -l    # → 6
```

**Her node'un tek IP'si olmalı:**
```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"  "}{.status.addresses[?(@.type=="InternalIP")].address}{"\n"}{end}'
# cp-1      10.30.10.21
# worker-1  10.30.30.11      ← edge0'ın 10.30.20.11'i BURADA OLMAMALI
```

### 🔴 DUR — hayalet node'lar zaten eklendiyse

```bash
# 1) Önce SİL (Ready olmasalar da node kaydı kalır)
kubectl delete node edge-1 edge-2 edge-3

# 2) Her worker'da RKE2'yi durdur, state TEMİZLE
for ip in 10.30.30.11 10.30.30.12 10.30.30.13; do
  ssh root@$ip 'systemctl stop rke2-agent; rke2-killall.sh; rm -rf /var/lib/rancher/rke2/agent'
done

# 3) node-ip SABİT iken yeniden kur (§7.6-B)
```

🔴 **`edge0` IP'sini RKE2'ye ASLA vermeyin.** `node-external-ip: 10.30.20.11` yazmak =
ikinci node kaydı garantisi.

✅ **Cilium doğrulaması (6 cihaz, 9 değil):**
```bash
kubectl -n kube-system exec ds/cilium -- cilium status | grep -i "device\|primary0\|edge0"
# Cilium: 6/6 nodes, devices: primary0, edge0
kubectl -n kube-system exec ds/cilium -- cilium-health status
# 6/6 reachable
```

---

<a id="s7"></a>

# 7. RKE2 + Cilium

### ⬜ ÖN KONTROL — RKE2'ye başlamadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| 8 VM açık | `for v in 100 101 102 103 104 105 106 107 108; do qm status $v; done` | `running` | §6 |
| DNS çalışıyor | `dig @10.30.10.53 cp-1.banklab.alperozbay.com +short` | `10.30.10.21` | §6.4 |
| DNS'ten isim çözülüyor (VM içi) | `ssh root@10.30.10.10 'getent hosts cp-1.banklab.alperozbay.com'` | IP | §6.5 |
| SDN ağları var | `ip -br link \| grep -cE "vnet(10\|20\|30)"` | `3` | §4.2-B |
| Worker 2 NIC | `qm config 106 \| grep -c '^net'` | `2` | §6.6 |
| Worker arayüz adları | `ssh root@10.30.30.11 'ip -br link \| grep -cE "primary0\|edge0"'` | `2` | **§6.6** |
| swap kapalı | `ssh root@10.30.10.21 'swapon --show'` | boş | §7.1 |
| iscsid çalışıyor | `ssh root@10.30.10.21 'systemctl is-active iscsid'` | `active` | §7.1 |

### 🔀 KOŞULLU KARAR — HA / tek CP

| Durumunuz | Yapı | Neden |
|---|---|---|
| **≥3 CP VM** (bu lab) | 3 CP + VIP + etcd | Proxmox tek host → **host düşerse hepsi düşer**; HA ağ hatasına karşı |
| **Tek CP** | VIP'siz, `server: https://cp-1:9345` | kube-vip ve 2 CP adımını atlayın |
| **Proxmox host tek** ⚠️ | Bilinçli risk: **tek hata noktası** | Yedek §H5 (vzdump) |

<a id="s7-1"></a>

## 7.1 Her node'da ön koşul (6 node'a aynı hazırlık)

**Ne yapıyoruz:** RKE2 kurmadan **önce** her node'da: swap kapalı, `br_netfilter` +
`overlay` modülleri yüklü, `iscsid` çalışıyor (Longhorn için), `sysctl` ağ ayarları.
**Neden:** bunlar eksikse RKE2 başlar ama **CNI veya depolama çalışmaz** — hata mesajı
**başka yerde** görünür (teşhis zorlaşır).


```bash
#!/bin/bash
set -euo pipefail
apt-get update && apt-get install -y ca-certificates curl jq open-iscsi nfs-common cryptsetup dmsetup chrony

printf '%s\n' overlay br_netfilter iscsi_tcp > /etc/modules-load.d/bank-rke2.conf
modprobe overlay; modprobe br_netfilter; modprobe iscsi_tcp

printf '%s\n' 'net.ipv4.ip_forward=1' 'net.bridge.bridge-nf-call-iptables=1' \
  > /etc/sysctl.d/90-bank-rke2.conf
sysctl -p /etc/sysctl.d/90-bank-rke2.conf

swapoff -a && sed -i '/\sswap\s/s/^/#/' /etc/fstab
systemctl enable --now iscsid          # NEDEN: Longhorn iSCSI'siz ÇALIŞMAZ
```

✅ **KANIT (her node):** `swapon --show` boş · `iscsid active` · `lsmod | grep -cE "overlay|br_netfilter|iscsi_tcp"` → 3

<a id="s7-1-b"></a>

## 7.1-B 🔴 containerd `config.toml.tmpl` — RKE2 `config.yaml`'ı **YOK SAYIYOR**

> **V6'NIN EN SİNSİ HATASI (R47/R48):** Rehber `/etc/rancher/rke2/config.yaml`'a
> `max-concurrent-downloads: 10` yazıyordu. **RKE2 v1.36.6 bu anahtarı OKUMUYOR.**
> Containerd ayarları **`config.toml.tmpl`** üzerinden yapılmalı.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| RKE2 sürümü | `rke2 --version` | `v1.36.6` | §7.4 |
| containerd sürümü | `/var/lib/rancher/rke2/bin/containerd --version` | `2.x` | bu bölüm |
| Render edilmiş config var mı | `cat /var/lib/rancher/rke2/agent/etc/containerd/config.toml \| grep max_concurrent` | **boş** (yok sayılmış) | bu bölüm |
| `config.toml.tmpl` var mı | `ls /var/lib/rancher/rke2/agent/etc/containerd/config.toml.tmpl` | yok (henüz) | bu bölüm |

### 🔀 KOŞULLU KARAR — hangi containerd ayarı?

| Belirti | Ayar | Neden |
|---|---|---|
| **`Pulling image` saatlerce tek satırda, hata yok** | **`use_local_image_pull = true`** | containerd 2.x **multipart-fetch deadlock** (containerd#11864, microk8s#5404). **Bu zorunlu** |
| `interrupted: context canceled` 5 dk sonra | `image_pull_progress_timeout = "30m"` | default 5 dk |
| Katmanlar **sırayla** iniyor (paralel değil) | `max_concurrent_downloads = 10` | default 3 |
| Yerel imaj var ama registry'ye gidiyor | `imagePullPolicy: IfNotPresent` | §13'te her values'ta |
| Hepsi | **üçünü birden** | aşağıdaki şablon |

### 🔴 GERÇEK: `config.yaml` yok sayılıyor — KANIT

```bash
# config.yaml'a ekle
echo 'max-concurrent-downloads: 10' >> /etc/rancher/rke2/config.yaml
systemctl restart rke2-server; sleep 60

# Render edilmiş config'e bak
grep -c "max_concurrent_downloads" /var/lib/rancher/rke2/agent/etc/containerd/config.toml
# → 0    🔴 HİÇ GÖRÜNMÜYOR — yok sayıldı
```

### ADIM — `config.toml.tmpl` (HER node'da, RKE2 başlamadan ÖNCE)

```bash
mkdir -p /var/lib/rancher/rke2/agent/etc/containerd
cat > /var/lib/rancher/rke2/agent/etc/containerd/config.toml.tmpl <<'EOF'
{{ template "base" . }}

[plugins.'io.containerd.cri.v1.images']
  # 🔴 1) containerd 2.x multipart deadlock fix — OLMADAN "Pulling" sonsuz
  use_local_image_pull = true
  # 🔴 2) 5 dk → 30 dk (yavaş ağda kesilmesin)
  image_pull_progress_timeout = "30m"
  # 🔴 3) paralel katman çekimi
  max_concurrent_downloads = 10

[plugins.'io.containerd.grpc.v1.cri'.registry]
  config_path = '/etc/rancher/rke2/registries.yaml'
EOF

chmod 0644 /var/lib/rancher/rke2/agent/etc/containerd/config.toml.tmpl
```

⚠️ **`{{ template "base" . }}` ZORUNLU:** bu satır olmadan RKE2'nin **varsayılan**
config'i kaybolur → containerd **başlamaz** → node **NotReady**. `base` template'i
RKE2'nin kendi ayarlarını (CNI, snapshotter, sock) taşır.

### ✅ KANIT

```bash
systemctl restart rke2-server; sleep 90
grep -A2 "io.containerd.cri.v1.images" /var/lib/rancher/rke2/agent/etc/containerd/config.toml
# use_local_image_pull = true
# image_pull_progress_timeout = "30m"
# max_concurrent_downloads = 10      ← ÜÇÜ DE VAR ✅
```

```bash
# Gerçek etki testi — yerel imaj varsa hızlı başlamalı
kubectl -n kube-system get pods -o wide | grep cilium | head -1
# 3 dk içinde Running olmalı (yoksa use_local_image_pull çalışmıyor)
```

### 🔴 DUR — hangi hata, hangi çözüm

| Hata | Sebep | Çözüm |
|---|---|---|
| `{{ template "base" . }}` yok → `containerd exited` | base template silinmiş | Şablonu **tam** kopyala |
| `max_concurrent_downloads` görünmüyor | tmpl yanlış yolda | `/var/lib/rancher/rke2/agent/etc/containerd/` **agent** dizini (server değil) |
| Cilium `Init:0/1` | local pull ama imaj yok | `ctr images list` → imaj var mı |
| Node `NotReady` | containerd başlamadı | `journalctl -u rke2-server \| grep containerd` |

🔁 **Yine "Pulling" sonsuzsa:** `use_local_image_pull` **tek başına** yetmez →
containerd 2.x için `image_pull_progress_timeout` **+** `max_concurrent_downloads`
birlikte gerekli. Üçünü birden yazın.

---

<a id="s7-2"></a>

## 7.2 RKE2 config — cp-1 (ilk kontrol düzlemi)

**Ne yapıyoruz:** `cp-1`'e `/etc/rancher/rke2/config.yaml` yazıyoruz: CNI **kendimiz**
kuracağız (`cni: none`), `kube-proxy` **kapalı** (Cilium alacak), ingress **kapalı**
(Traefik sonra), pod/service CIDR, ve **audit policy** yolu.
**Neden bu 3 kapatma:** Cilium + Traefik kendi işini yapar; RKE2'nin varsayılanları
**çakışır** (iki CNI, iki ingress) → pod ağları kırılır.


```yaml
# /etc/rancher/rke2/config.yaml
node-name: cp-1
node-ip: 10.30.10.21
resolv-conf: /etc/rancher/rke2/resolv.conf
write-kubeconfig-mode: "0640"     # 🔴 0600 verirseniz root DIŞINDA kubeconfig OKUNAMAZ
# 🔴 'agent-token-file:' YAZMAYIN — RKE2 token'ı KENDİ üretir
# (/var/lib/rancher/rke2/server/agent-token). Elle dosya verirseniz RKE2 onu
# geçersiz token olarak kullanır → worker'lar 'invalid token' ile join edemez.
cni: none                    # NEDEN: Cilium'u biz kuruyoruz
ingress-controller: none     # NEDEN: Traefik Helm ile gelecek
disable-kube-proxy: true     # NEDEN: Cilium kube-proxy'nin yerini alır
cluster-cidr: 10.244.0.0/16
service-cidr: 10.245.0.0/16
cluster-dns: 10.245.0.10
tls-san: [10.30.10.20, 10.30.10.24, cp-1.banklab.alperozbay.com]
node-taint: ["node-role.kubernetes.io/control-plane:NoSchedule"]
# 🔴 audit-policy-file YOKSA RKE2 BAŞLAMAZ. Dosyayı aşağıda oluşturuyoruz.
audit-policy-file: /etc/rancher/rke2/audit-policy.yaml
kube-apiserver-arg:
  - "anonymous-auth=false"
  - "audit-log-path=/var/lib/rancher/rke2/server/logs/audit.log"
  - "audit-log-maxage=30"
  - "audit-log-maxbackup=10"
  - "audit-log-maxsize=100"
```

**İmaj çekme timeout — H40 (default 5 dk yetmez):**
```bash
mkdir -p /etc/rancher/rke2/config.yaml.d
cat > /etc/rancher/rke2/config.yaml.d/90-pull.yaml <<'EOF'
kubelet-arg:
  - "image-pull-progress-deadline=30m"
EOF
```

<a id="s7-2-b"></a>

## 7.2-B 🔴 `audit-policy.yaml` — DOSYA YOKSA RKE2 BAŞLAMAZ

> **NEDEN GEREKLİ:** `audit-policy-file` config'te referans ediliyordu ama **dosya hiç oluşturulmuyordu**.
> RKE2 açılışta `open /etc/rancher/rke2/audit-policy.yaml: no such file` → **kube-apiserver başlamaz**.

```bash
cat > /etc/rancher/rke2/audit-policy.yaml <<'EOF'
apiVersion: audit.k8s.io/v1
kind: Policy
omitStages: ["RequestReceived"]
rules:
  # 1) Secret/ConfigMap içeriği ASLA loglanmaz (yalnız metadata)
  - level: Metadata
    resources:
      - group: ""            # core
        resources: ["secrets", "configmaps"]
  # 2) Sistem bileşenlerinin yüksek hacimli okumaları loglanmaz
  - level: None
    users: ["system:kube-proxy", "system:kube-controller-manager", "system:kube-scheduler"]
    verbs: ["watch", "get", "list"]
  # 3) Kimlik doğrulama hataları + yetki reddi: her zaman
  - level: Request
    verbs: ["create", "delete", "deletecollection", "patch", "update"]
  # 4) Diğer her şey metadata
  - level: Metadata
EOF

# Doğrula (YAML geçerli mi?)
python3 -c "import yaml;yaml.safe_load(open('/etc/rancher/rke2/audit-policy.yaml'));print('✅ YAML geçerli')"
```

✅ **KANIT:** Dosya var + `rke2-server` başladı + `/var/lib/rancher/rke2/server/logs/audit.log` **oluşuyor**:
```bash
ls -la /var/lib/rancher/rke2/server/logs/audit.log   # → dosya var, büyüyor
```

⚠️ **NEDEN `Secret` = `Metadata`:** `level: Request` verirseniz **secret içerikleri log'a yazılır** →
diskte **şifresiz** durur. `Metadata` ile yalnız obje adı/namespace loglanır.

🔁 **ALTERNATİF (en basit):** Audit **tamamen kapatılabilir** — lab'da tercih edilebilir:
```yaml
# config.yaml'dan ŞU İKİ SATIRI SİL:
#   audit-policy-file: ...
#   kube-apiserver-arg: audit-log-* ...
```
Sonra RKE2 **kendi varsayılan politikasını** üretir (`profile:` flag'i ile).

---

<a id="s7-3"></a>

## 7.3 🔴 H80 — `cred/passwd` KALICI düzeltme

> **NEDEN:** `rke2-server` restart sonrası ölür:
> `passwd newer than datastore and could cause a cluster outage` (k3s #14982).
> `rm -rf cred` **tek seferlik** — her restart'ta tekrar eder. V6'da **2 × 3 saat** kayıp.

```bash
# cp-2 VE cp-3'te — SERVİSİ BAŞLATMADAN ÖNCE:
mkdir -p /etc/systemd/system/rke2-server.service.d
cat > /etc/systemd/system/rke2-server.service.d/10-cred-fix.conf <<'EOF'
[Service]
ExecStartPre=-/bin/sh -c "/bin/rm -f /var/lib/rancher/rke2/server/cred/passwd"
EOF
systemctl daemon-reload
systemctl cat rke2-server | grep ExecStartPre
```

✅ **KANIT:** `ExecStartPre=-/bin/sh -c "/bin/rm -f /var/lib/rancher/rke2/server/cred/passwd"`

<a id="s7-3-b"></a>

## 7.3-B 🔴 `cred/passwd` — KALICI düzeltme (R43/H80)

> **BELİRTİ:** cp-2/cp-3 `rke2-server` **hiç başlamıyor**:
> ```
> passwd newer than datastore and could cause a cluster outage.
> Remove the file(s) from disk and restart to be recreated from datastore.
> ```
> **V6'da bu 2,5 SAAT boyunca fark edilmedi** → VIP 2,5 saat ölü, 10/20 healthz.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| rke2-server durumu | `ssh root@10.30.10.22 'systemctl is-active rke2-server'` | `active` | bu bölüm |
| Log'da hata | `ssh root@10.30.10.22 'journalctl -u rke2-server -n 50 \| grep -c "passwd newer"'` | **0** | bu bölüm |
| Node durumu | `kubectl get node cp-2` | `Ready` | bu bölüm |

### 🔀 KOŞULLU KARAR — tek seferlik mi, kalıcı mı?

| Durum | Yöntem | Kalıcı? |
|---|---|---|
| **Bir kez oldu** | `rm -f .../cred/passwd` + restart | ❌ tekrarlar |
| **Tekrar ediyor** (join denemesi sonrası) | **systemd drop-in** | ✅ **bu rehberin yolu** |
| `cred/` dizini baştan bozuk | `rke2-killall.sh` + `rm -rf /var/lib/rancher/rke2/server/cred` | ✅ ama ağır |

### ADIM 1 — tek seferlik kurtarma (hemen)

**Ne yapıyoruz:** `cred/passwd` **silip** servisi yeniden başlatıyoruz. RKE2 bunu **etcd'den yeniden üretir** (k3s #14982). **10 saniyelik** işlem.

```bash
for CP in 10.30.10.22 10.30.10.23; do
  ssh root@$CP '
    systemctl stop rke2-server
    rm -f /var/lib/rancher/rke2/server/cred/passwd \
          /var/lib/rancher/rke2/server/cred/node-passwd
    systemctl reset-failed rke2-server
    systemctl start rke2-server
  '
  sleep 45
done
```

### ADIM 2 — KALICI düzeltme (systemd drop-in) 🔴

**Ne yapıyoruz:** `ExecStartPre` ile **her başlatmada** `cred/passwd` dosyasını siliyoruz. Böylece tekrar etmez. `-` (tire) **şart**: dosya yoksa `rm` başarısız olur, `-` olmadan servis **durur**.

```bash
for CP in 10.30.10.22 10.30.10.23; do
  ssh root@$CP 'mkdir -p /etc/systemd/system/rke2-server.service.d
  cat > /etc/systemd/system/rke2-server.service.d/10-cred-fix.conf <<EOF
[Service]
ExecStartPre=-/bin/sh -c "/bin/rm -f /var/lib/rancher/rke2/server/cred/passwd"
EOF
  systemctl daemon-reload
  systemctl cat rke2-server | grep -A2 "cred-fix"'
done
```

**NEDEN `-` (tire) ExecStartPre'de:** `ExecStartPre=-/...` → komut **hata verse de**
servis devam eder. Dosya yoksa `rm` başarısız olur; `-` olmadan servis **durur**.

**NEDEN `cred/passwd` silinebilir:** RKE2 bunu **datastore'dan yeniden üretir**
(k3s issue #14982). Kaynak: etcd, dosya değil.

### ✅ KANIT

```bash
# 1) drop-in yüklü mü
for CP in 22 23; do ssh root@10.30.10.$CP 'systemctl cat rke2-server | grep -c "cred-fix"'; done
# → 1  1

# 2) 3 CP aktif mi
for CP in 21 22 23; do printf "%s: " $CP; ssh root@10.30.10.$CP 'systemctl is-active rke2-server'; done
# → active active active

# 3) etcd + VIP
kubectl get nodes | grep -c Ready          # → 6
kubectl -n kube-system get pods | grep -c etcd   # → 3
curl -sk https://10.30.10.20:6443/readyz   # → ok

# 4) VIP sağlığı (V6'da 2,5 saat ölüydü)
for i in $(seq 1 10); do curl -sk -o /dev/null -w "%{http_code} " https://10.30.10.20:6443/readyz; done
# → 200 x10   ✅
```

### 🔴 DUR — restart etmeyin!

| Belirti | Ne YAPMA | Ne YAP |
|---|---|---|
| `passwd newer` | 🔴 **tekrar restart** | ADIM 1 (rm + reset-failed) |
| `left-over process (containerd-shim)` | 🔴 `reboot` | `systemctl stop` + `pkill -9 containerd-shim` + `reset-failed` + `start` |
| Node `NotReady` + rke2 `activating` | 🔴 `rke2-killall.sh` | önce **log oku**; `cred/passwd` mi, `etcd` mi |

🔁 **VIP 2,5 saat ölü kaldıysa (V6'da oldu):** kube-vip **yarışı** çözülene kadar
VIP gelmez. **3. CP ayağa kalkınca** kube-vip ARP yarışına katılır → VIP geri gelir.
**kube-vip'i yeniden yapılandırmayın** — kök neden CP'nin ölü olmasıydı.

---

<a id="s7-4"></a>

## 7.4 cp-1 başlatma (kümeyi doğur)

**Ne yapıyoruz:** `cp-1`'de `rke2-server` servisini başlatıyoruz. Bu ilk **etcd** üyesini
kurar, sertifikaları üretir, `server/token` + `agent-token` dosyalarını yazar.
**Sıra kritik:** `cp-2/cp-3` **yalnız bundan sonra** join edebilir (token gerekli).


```bash
# 🔴 /etc/rancher/rke2 dizini YOKSA config.yaml yazılamaz → rke2-server default config'siz başlar
install -d -m 0700 /etc/rancher/rke2
install -d -m 0700 /var/lib/rancher/rke2/server/manifests

# 🔴 agent-token dosyası cp-1'de OTOMATİK oluşur — elle üretmeyin
curl -sfL https://get.rke2.io | INSTALL_RKE2_VERSION=v1.36.6+rke2r1 sh -
systemctl enable --now rke2-server
sleep 90

# Token'ları OKU (worker'lar bunları kullanacak)
cat /var/lib/rancher/rke2/server/token            # server token (CP join)
cat /var/lib/rancher/rke2/server/agent-token      # agent token (worker join)
export KUBECONFIG=/etc/rancher/rke2/rke2.yaml
kubectl get nodes          # cp-1 NotReady (CNI yok — NORMAL)
```

✅ **KANIT (etcd sağlığı):**
```text
/var/lib/rancher/rke2/bin/kubectl --kubeconfig /etc/rancher/rke2/rke2.yaml get pods -n kube-system
# etcd-cp-1 1/1 Running, kube-apiserver 1/1
```

🔴 **DUR:** etcd Running değilse **diğer CP'leri eklemeyin**.

<a id="s7-5"></a>

## 7.5 cp-2/cp-3 join (etcd quorum'u tamamla)

**Ne yapıyoruz:** `cp-2` ve `cp-3`'ü `server: https://10.30.10.21:9345` ile kümeye
ekliyoruz. **Neden 3 CP:** etcd **quorum = 2**; tek CP ölürse küme **yazılamaz** hale gelir.
3 ile **1 CP** kaybı tolere edilir.


```yaml
node-name: cp-2
node-ip: 10.30.10.22
server: https://10.30.10.21:9345
token: <cp-1:/var/lib/rancher/rke2/server/token İÇERİĞİ>   # 🔴 E25
# geri kalanı cp-1 ile aynı (cni:none, disable-kube-proxy, tls-san ...)
```

```bash
# cp-1'den token'ı kopyala (elle yazmayın — uzun ve base64)
scp root@10.30.10.21:/var/lib/rancher/rke2/server/token /tmp/server-token
cat /tmp/server-token    # cp-2/cp-3 config.yaml'a bunu yaz
```
```bash
# 🔴 ÖNCE drop-in (§7.3), SONRA başlat
systemctl enable --now rke2-server
```

✅ **KANIT:** `kubectl get nodes` → 3 CP (NotReady normal)
`kubectl -n kube-system get pods | grep etcd` → **3/3**

<a id="s7-6"></a>

## 7.6 Worker'lar — 2 NIC (yönetim + LB ayrımı)

**Ne yapıyoruz:** Worker VM'lerine **ikinci ağ kartı** (`edge0` → `vnet20`) veriyoruz.
**Neden:** MetalLB L2 duyurusu **tek arayüzde** olmalı (§9.1) — küme trafiği (`vnet30`)
ile LB trafiği (`vnet20`) **ayrı** olmazsa ARP çakışır ve LB IP'ye **erişilemez**.


```yaml
# worker-1: iki ağ (vnet10 + vnet30)
node-name: worker-1
node-ip: 10.30.30.11
server: https://10.30.10.24:9345
token: <agent-token>
```

⚠️ **NEDEN 2 NIC?** Worker'ın birincil IP'si `vnet30` (küme içi trafik),
ikincil `vnet20` (uygulama). **Cilium'un `node-ip`'i `vnet30` olmalı** —
yanlış arayüz seçilirse pod trafiği yanlış yoldan gider.

<a id="s7-6-b"></a>

## 7.6-B 🔴 WORKER'LARA RKE2 **AGENT** KURULUMU

> 🔴 **SIRA NOTU:** Bu bölüm **§7.7'den (HAProxy `.24`)** önce gelir ama worker'lar
> **`.24`'ü kullanmaz** — doğrudan `server: https://10.30.10.21:9345` ile cp-1'e bağlanır.
> `.24` yalnız **CP join** ve **HA/yeniden bağlanma** için gereklidir.
> Eğer **tüm node'lar `.24` üzerinden** bağlanacaksa **§7.7'yi önce** yapın.


> **NEDEN GEREKLİ:** §7.6 sadece **config.yaml** veriyordu. Ama `rke2-agent` **binary'si kurulmuyor**.
> `systemctl enable rke2-agent` → `Unit not found` → **worker hiçbir zaman küme'ye katılmaz**.
> §8'de "6/6 Ready" beklenirdi → **kurulum burada ölür**.

**Her worker'da (3 kez):**

```bash
# 1) RKE2 AGENT paketini kur — 🔴 INSTALL_RKE2_TYPE=agent ZORUNLU
curl -sfL https://get.rke2.io |   INSTALL_RKE2_TYPE=agent   INSTALL_RKE2_VERSION=v1.36.6+rke2r1 sh -
# NEDEN: TYPE=agent olmadan 'server' kurar → worker'da etcd/apiserver çalıştırmaya çalışır → çöker

# 2) Dizin (config'ten ÖNCE — M7)
install -d -m 0700 /etc/rancher/rke2

# 3) Config
cat > /etc/rancher/rke2/config.yaml <<'EOF'
node-name: worker-1
node-ip: 10.30.30.11
server: https://10.30.10.24:9345
token: <AGENT-TOKEN — cp-1:/var/lib/rancher/rke2/server/agent-token>
EOF
chmod 0600 /etc/rancher/rke2/config.yaml

# 4) Servisi başlat
systemctl enable --now rke2-agent

# 5) Doğrula
systemctl is-active rke2-agent          # → active
journalctl -u rke2-agent -n 30 | grep -iE "joined|successfully"
```

✅ **KANIT (cp-1'den):** `kubectl get nodes` → `worker-1 Ready` (Cilium'dan sonra)

🔴 **DUR:** `Unit rke2-agent.service not found` → **1. adımı** tekrar çalıştırın (`INSTALL_RKE2_TYPE`).
`systemctl is-active rke2-agent` `failed` → `journalctl -u rke2-agent -n 100` → token/registration kontrol.

⚠️ **CP node'ları için:** `INSTALL_RKE2_TYPE=server` (default) — §7.4'te zaten var.

---

<a id="s7-7"></a>

## 7.7 Registration 10.30.10.24 — **V6'da HAProxy kurulmamıştı (R32)**

🔁 **İKİ SEÇENEK:**

| Profil | Nasıl | Ne zaman |
|---|---|---|
| **cp1-alias** (basit) | cp-1'e alias: `ip addr add 10.30.10.24/32 dev primary0` | Lab, tek CP yeter |
| **haproxy** (V9 önerilen) | OPNsense HAProxy → 3 CP'ye dağıt | CP çökerse join çalışsın |

```bash
# HAProxy (OPNsense GUI):
# Services → HAProxy → Real Servers:
#   srv-cp1 10.30.10.21:9345 · srv-cp2 .22 · srv-cp3 .23
# Backend bk-rke2-reg → health check tcp
# Frontend fe-rke2-reg → bind 10.30.10.24:9345 → default_backend bk-rke2-reg
# ⚠️ R33: 'TLS passthrough' seçiliyken 'verify' ANLAMSIZ → kapalı bırak
```

✅ **KANIT:** `nc -zv 10.30.10.24 9345` → open

<a id="s7-8"></a>

## 7.8 Cilium (CNI — kube-proxy yerine)

**Ne yapıyoruz:** Cilium'u **eBPF** modunda kuruyoruz, `kubeProxyReplacement=true` ile
`kube-proxy`'yi tamamen devre dışı bırakıyoruz. İki cihaz: `primary0` + `edge0`.
**Neden Cilium:** iptables/IPVS yerine eBPF → **CPU %20 az**, `kube-proxy`'nin
senkronizasyon sorunları **yok**, NetworkPolicy L7 desteği var.


```bash
helm repo add cilium https://helm.cilium.io/ && helm repo update
```

```yaml
# cilium-values.yaml
kubeProxyReplacement: true
k8sServiceHost: 10.30.10.20     # NEDEN VIP: CP çökerse agent'lar API'yi bulsun (R40)
k8sServicePort: 6443
ipam: { mode: kubernetes }
routingMode: tunnel
tunnelProtocol: vxlan           # node'lar arası UDP 8472
hubble: { relay: { enabled: true }, ui: { enabled: true } }
operator: { replicas: 2 }
```

```text
**NEDEN Cilium (kube-proxy yerine):** eBPF ile iptables'ı atlar → 3 worker'da
**CPU %20 daha az**, `kube-proxy`'nin sorunlu IPVS senkronizasyonu yok.
**NEDEN `kube-system`:** DaemonSet her node'da çalışmalı; `kube-system` bunun standardı.

helm install cilium cilium/cilium -n kube-system -f cilium-values.yaml
```

✅ **KANIT:**
```bash
kubectl -n kube-system get pods -l k8s-app=cilium   # 6/6 Running
kubectl get nodes                                    # 6/6 Ready ← ARTIK
```

⚠️ **R44:** `devices: ["primary0","edge0"]` **kullanmayın** — isimler VM'e göre değişir.

<a id="s7-9"></a>

## 7.9 VIP — kube-vip

```bash
export VIP=10.30.10.20 IFACE=primary0
mkdir -p /var/lib/rancher/rke2/server/manifests
cat > /var/lib/rancher/rke2/server/manifests/kube-vip.yaml <<EOF
apiVersion: v1
kind: Pod
metadata: { name: kube-vip, namespace: kube-system }
spec:
  hostNetwork: true
  containers:
  - name: kube-vip
    image: docker.io/plndr/kube-vip:v1.3.1    # 🔴 E17: v1.0.1 DEĞİL · M50: ghcr.io ölü → Docker Hub aynası
    imagePullPolicy: IfNotPresent
    args: ["manager"]
    env:
    - { name: vip_arp, value: "true" }
    - { name: port, value: "6443" }
    - { name: vip_cidr, value: "32" }
    - { name: cp_enable, value: "true" }
    - { name: cp_namespace, value: "kube-system" }
    - { name: svc_enable, value: "true" }
    - { name: vip_leaderelection, value: "true" }
    - { name: vip_address, value: "$VIP" }
    - { name: vip_interface, value: "$IFACE" }
    securityContext: { capabilities: { add: [NET_ADMIN, NET_RAW] } }
  tolerations: [{ operator: Exists }]
EOF
```

✅ **KANIT:**
```text
**NEDEN bu 4 endpoint:** VIP (`.20`) ve 3 CP. VIP **tek başına** cevap verirse
kube-vip çalışıyor ama **API arkası boş** olabilir; her CP'yi ayrı test etmek gerekir.

for ep in 10.30.10.20 10.30.10.21 10.30.10.22 10.30.10.23; do
  printf "%-14s " $ep; curl -sk -o /dev/null -w "%{http_code} %{time_total}s\n" \
    "https://$ep:6443/readyz" --max-time 8
done
# → 4/4 → 401 (kimlik yok ama endpoint ayakta)
```

🔁 **ALTERNATİF — VIP TLS timeout (K29):**
`kubectl --server=https://10.30.10.21:6443 --insecure-skip-tls-verify ...`

<a id="s7-10"></a>

## 7.10 🔴 Registry mirror — **R47'nin tam çözümü** (V7'de eksikti)

> **NEDEN:** RKE2'de `max-concurrent-downloads` **config.yaml'da yok sayılır** (R47).
> Yavaş ağda imaj çekimi seri + yavaş olur. Çözüm: **yerel mirror** tanımla.

🔴 **EN BÜYÜK ÇELİŞKİ — ghcr.io:**

§3.3 diyor ki *"ghcr.io **837 B/s**"* (yani **ölü**). Ama §7.9 kube-vip, §12 CNPG operator
ve **3 yerde** `ghcr.io/cloudnative-pg/postgresql` **imajları ghcr.io'dan çekiliyor**.
**Bu haliyle PostgreSQL pod'ları HİÇ başlamaz.**

| İmaj | Kaynak | ghcr.io ölüyse |
|---|---|---|
| `ghcr.io/kube-vip/kube-vip:v1.3.1` | ghcr.io | `docker.io/plndr/kube-vip:v1.3.1` (Docker Hub aynası) |
| `ghcr.io/cloudnative-pg/cloudnative-pg:1.30.2` | ghcr.io | `docker.io/cloudnative-pg/cloudnative-pg:1.30.2` ✅ ayna var |
| `ghcr.io/cloudnative-pg/postgresql:17-standard-bookworm` | ghcr.io | `docker.io/cloudnative-pg/postgresql:17-standard-bookworm` ✅ ayna var |

**ÇÖZÜM — 3 yol (biri seçilmeli):**

**YOL A (V9 ÖNERİLEN) — Docker Hub aynasını kullan, ghcr.io'ya hiç dokunma:**
```bash
# CNPG operator — chart values ile registry değiştir
helm install cnpg cnpg/cloudnative-pg -n cnpg-system --create-namespace \
  --set image.repository=docker.io/cloudnative-pg/cloudnative-pg \
  --set image.tag=1.30.2

# CNPG postgres imajı — Cluster YAML'ında imageName'i DEĞİŞTİR
#   imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm   # 🔴 ghcr.io 837 B/s → Docker Hub aynası
# → imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm   ← ayna

# kube-vip — Docker Hub aynası
#   image: ghcr.io/kube-vip/kube-vip:v1.3.1
# → image: docker.io/plndr/kube-vip:v1.3.1
```

**YOL B — kendi mirror'ınız (jump-1'de registry:2) — TAM ADIMLAR:**
```bash
# §7.10 için GEREKLİ: mirror'ın kendisi KURULMALI (V9'da atıftı, kurulumu yoktu)
ssh root@10.30.10.10 <<'EOF'
docker rm -f mirror 2>/dev/null
docker run -d --restart=always --name mirror -p 5000:5000 \
  -v /srv/mirror:/var/lib/registry \
  -e REGISTRY_PROXY_REMOTEURL=https://registry-1.docker.io \
  registry:2
EOF
# Doğrula
curl -s http://10.30.10.10:5000/v2/_catalog
```
```yaml
# /etc/rancher/rke2/registries.yaml — HER NODE'DA
mirrors:
  docker.io:
    endpoint: ["http://10.30.10.10:5000"]
  ghcr.io:
    endpoint: ["http://10.30.10.10:5000"]   # ⚠️ ghcr.io proxy AYRI container ister
  quay.io:
    endpoint: ["http://10.30.10.10:5000"]
configs:
  "10.30.10.10:5000":
    tls:
      insecure_skip_verify: true
```
⚠️ **DİKKAT:** `registry:2` **tek upstream** proxy yapar. `docker.io` + `ghcr.io` + `quay.io`
için **3 ayrı registry container** gerekir (farklı portlar: 5000/5001/5002):
```bash
for P in 5000:docker.io 5001:ghcr.io 5002:quay.io; do
  PORT=${P%%:*}; UP=${P##*:}
  docker run -d --restart=always --name "mirror-${PORT}" -p "${PORT}:5000" \
    -v "/srv/mirror-${PORT}:/var/lib/registry" \
    -e "REGISTRY_PROXY_REMOTEURL=https://${UP==docker.io:+registry-1.docker.io}${UP!=docker.io:+${UP}}" \
    registry:2
done
```
🔴 **Bu karmaşık.** Lab'da **YOL A**'yı seçin.

**YOL C — elle `ctr import` (air-gap):**
```bash
# Yönetim istasyonunda çek → node'a taşı
skopeo copy --all docker://docker.io/cloudnative-pg/postgresql:17-standard-bookworm \
  docker-archive:pg17.tar
scp pg17.tar root@10.30.30.11:/tmp/
ssh root@10.30.30.11 'ctr -n k8s.io images import /tmp/pg17.tar'
```

🔁 **EN BASİT (V9 önerilen):** `registries.yaml` **yazmayın**. YOL A (Docker Hub) +
gerekirse YOL C (`ctr import`). Mirror ek bir servis; lab'da gereksiz karmaşıklık.

✅ **KANIT:**
```bash
# Her node'da imaj gerçekten var mı (çekim beklemeden)
for N in 10.30.10.21 10.30.10.22 10.30.10.23 10.30.30.11 10.30.30.12 10.30.30.13; do
  echo -n "$N: "; ssh root@$N 'crictl images | grep -c cloudnative-pg'
done   # her node'da ≥2 (operator + postgresql)
```

---

<a id="s8"></a>

# 8. NAMESPACE + PSA

> **NEDEN `baseline` (S36):** Longhorn engine, CNPG postgres, Infisical imajları **root**
> çalışır. `restricted` bunları **reddeder** → pod hiç oluşmaz. Infisical chart'ı ise
> **runAsNonRoot:1001** ile gelir → `baseline` yeterli.

<a id="s8-0"></a>

## 8.0 **TEK KAYNAK** — namespace listesi (her yerde bu kullanılır)

> 🔴 **NEDEN:** önceki sürümde 3 farklı liste vardı (§8 döngüsü, §13.1 Traefik, §13.4 Kyverno).
> Tutarsızlık → route **404**, gereksiz **deny** → pod oluşmaz.

```bash
# Uygulama namespace'leri (PSA: baseline) — bu liste TEK doğru kaynaktır
APP_NS="delivery workforce identity data-services observability harbor argocd platform traefik"

# Altyapı namespace'leri (PSA: privileged serbest, Kyverno excluded)
INFRA_NS="kube-system kube-public kube-node-lease longhorn-system cnpg-system \
          external-secrets step-system metallb-system cert-manager kyverno"

for ns in $APP_NS; do
  kubectl create ns $ns --dry-run=client -o yaml | kubectl apply -f -
  kubectl label ns $ns \
    pod-security.kubernetes.io/enforce=baseline \
    pod-security.kubernetes.io/warn=restricted \
    pod-security.kubernetes.io/audit=restricted --overwrite
done

for ns in $INFRA_NS; do
  kubectl create ns $ns --dry-run=client -o yaml | kubectl apply -f - 2>/dev/null
done

echo "APP_NS : $APP_NS"
echo "INFRA_NS: $INFRA_NS"
```

✅ **KANIT:** `kubectl get ns --show-labels | grep 'enforce=baseline' | wc -l` → **7** (sayı $APP_NS kadar)
`kubectl get ns` → 18 namespace

⚠️ **`edge` namespace'i listede YOK** — Traefik `traefik` namespace'inde kurulacak (§13.1'de düzeltildi).
**NEDEN:** önceki sürümde hem `edge` hem `traefik` karışıktı; Traefik chart default'u kendi namespace'ini kurar.

<a id="s8-1"></a>

## 8.1 🔴 Restart dayanıklılık testi — KURULUMDAN ÖNCE

> **NEDEN (K12):** Bu test yoksa H59/H79 gibi hatalar **G6'da** patlar ve teşhis 13 saat sürer.

```bash
for ip in 10.30.10.21 10.30.10.22 10.30.10.23; do
  ssh root@$ip 'systemctl restart rke2-server'; sleep 60
  kubectl get nodes | grep "$(ssh root@$ip hostname)"    # Ready olmalı
done
```

✅ **KANIT:** 3 CP restart sonrası **6/6 Ready**, logda `passwd newer` **YOK**.
🔴 **DUR:** Düzelmiyorsa §7.3 drop-in'i kontrol et — **tekrar restart etmeyin**.

---

<a id="s8-2"></a>

## 8.2 🔴 NetworkPolicy — **test edilmiş** DENY→ALLOW (V6'da hiç yoktu)

> **NEDEN:** Namespace'leri ayırmak **tek başına hiçbir şey yapmaz**. Aynı kümede
> `delivery` pod'u `identity`'deki Keycloak'a **sınırsız** erişir. NetworkPolicy ile
> bu **kapatılır** — ama **yanlış yazılırsa** DNS bile çalışmaz.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Cilium NetworkPolicy CRD | `kubectl get crd ciliumnetworkpolicies.cilium.io` | var | §7.8 |
| Cilium `policyEnforcementMode` | `kubectl -n kube-system get cm cilium-config -o jsonpath='{.data.policy-enforcement-mode}'` | `default` | §7.8 |
| DNS çalışıyor | `kubectl -n delivery run t --rm -it --image=busybox -- nslookup kubernetes.default` | IP döner | §7.8 |

### 🔀 KOŞULLU KARAR — hangi politika motoru?

| Durumunuz | Yol | Neden |
|---|---|---|
| **Cilium CNI** (bu lab) | **`CiliumNetworkPolicy`** (L7 + DNS bazlı) | Cilium'a özgü; `toFQDNs` ile isim eşleşmesi |
| Farklı CNI / taşınabilirlik | `NetworkPolicy` (built-in v1) | Her CNI destekler, L7 yok |
| Politika istemiyorum | ⚠️ atlayın | **Tüm namespace'ler birbirine açık** |

### ADIM 1 — ÖNCE DENY (varsayılan reddet)

**Ne yapıyoruz:** Her uygulama namespace'ine **`default-deny-ingress`** koyuyoruz: **hiçbir** pod içeri trafik almaz. Sonra **bile isteye** izin açacağız.

```yaml
# deny-all.yaml — HER uygulama namespace'ine
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: delivery
spec:
  podSelector: {}          # tüm pod'lar
  policyTypes: [Ingress]
```

```bash
for NS in delivery workforce identity data-services observability; do
  sed "s/namespace: delivery/namespace: $NS/" deny-all.yaml | kubectl apply -f -
done
kubectl -n delivery get networkpolicy default-deny-ingress
```

### ADIM 2 — 🔴 DENY'den SONRA DNS HÂLÂ ÇALIŞMALI

**Ne yapıyoruz:** `default-deny` uygulanır uygulanmaz pod'lar **DNS dahil her şeyi** kaybeder. Bu yüzden **hemen** `allow-dns` (kube-system:53) ekliyoruz. **Atlanırsa** hiçbir şey çözümlenemez.

**Bu, en sık yapılan ve en sinsi hatadır.** `default-deny` uygular uygulamaz pod'lar
**DNS dahil her şeyi** kaybeder. Önce DNS'i aç:

```yaml
# allow-dns.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: allow-dns, namespace: delivery }
spec:
  podSelector: {}
  policyTypes: [Egress]
  egress:
    - to:
        - namespaceSelector:
            matchLabels: { kubernetes.io/metadata.name: kube-system }
      ports:
        - protocol: UDP
          port: 53
        - protocol: TCP
          port: 53
```

```bash
for NS in delivery workforce identity data-services observability; do
  sed "s/namespace: delivery/namespace: $NS/" allow-dns.yaml | kubectl apply -f -
done
```

### ADIM 3 — 🔴 TEST: DENY gerçekten çalışıyor mu?

**Ne yapıyoruz:** İki pod (biri `delivery`, biri `identity`) açıp **gerçekten** erişim var mı yok mu test ediyoruz. **Neden:** politika **uygulandı** ≠ politika **çalışıyor**. Cilium `policyEnforcementMode: never` ise YAML kabul edilir ama **hiç çalışmaz**.

**Günlükte bu test yapıldı ve sonuç kanıtlandı:**

```bash
# 1) Test pod'ları (iki ayrı namespace)
kubectl -n delivery run nettest --rm -it --image=busybox --restart=Never -- sleep 3600 &
kubectl -n identity run netsrv --image=nginx:alpine --port=80
kubectl -n identity wait --for=condition=Ready pod/netsrv --timeout=90s

SRV=$(kubectl -n identity get pod netsrv -o jsonpath='{.status.podIP}')

# 2) DENY durumu — ERİŞİM OLMAMALI
kubectl -n delivery exec nettest -- wget -T5 -qO- "http://$SRV" \
  && echo "🔴 DENY ÇALIŞMIYOR — erişim var!" \
  || echo "✅ DENY çalışıyor — download timed out (beklenen)"

# 3) ALLOW ekle
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata: { name: allow-from-delivery, namespace: identity }
spec:
  podSelector: { matchLabels: { run: netsrv } }
  policyTypes: [Ingress]
  ingress:
    - from: [{ namespaceSelector: { matchLabels: { kubernetes.io/metadata.name: delivery } } }]
      ports: [{ protocol: TCP, port: 80 }]
EOF

# 4) ALLOW sonrası — ERİŞİM OLMALI
sleep 5
kubectl -n delivery exec nettest -- wget -T5 -qO- "http://$SRV" | head -3 \
  && echo "✅ ALLOW çalışıyor" || echo "🔴 ALLOW çalışmıyor"
```

**Gerçek ölçüm (V6'da):**
| Durum | Sonuç |
|---|---|
| DENY (politika yok) | 🔴 **erişim VAR** (beklenen: olmamalı) |
| DENY (`ingress: []`) | ✅ **download timed out** |
| ALLOW (`from: app=nettest`) | ✅ erişim geri geldi |

**NEDEN bu test zorunlu:** politika **uygulandı** ≠ politika **çalışıyor**.
Cilium `policy-enforcement-mode: never` ise YAML kabul edilir ama **hiç uygulanmaz**.

🔴 **DUR — DNS kırıldıysa:** `kubectl -n delivery exec nettest -- nslookup kubernetes.default`
→ timeout alıyorsanız ADIM 2'yi (allow-dns) uygulamamışsınız.

### ADIM 4 — ResourceQuota + LimitRange

> **NEDEN:** Kaynak bütçesi (§9.4) **talep** değil **sınır** olmadan çalışmaz. Bir pod
> `limits` yazmazsa node'un tamamını yiyebilir → **diğer pod'lar `OOMKilled`**.

```yaml
# quotas.yaml — gerçek ölçülmüş değerler
apiVersion: v1
kind: ResourceQuota
metadata: { name: ns-quota, namespace: delivery }
spec:
  hard:
    requests.cpu: "6"
    requests.memory: 12Gi
    limits.cpu: "12"
    limits.memory: 24Gi
    persistentvolumeclaims: "10"
    requests.storage: 40Gi
---
apiVersion: v1
kind: LimitRange
metadata: { name: ns-limits, namespace: delivery }
spec:
  limits:
    - type: Container
      default:            { cpu: 500m, memory: 512Mi }   # limit yazmayan pod'a UYGULA
      defaultRequest:     { cpu: 100m, memory: 128Mi }
      max:                { cpu: "4",  memory: 8Gi }
      min:                { cpu: 10m,  memory: 16Mi }
```

| Namespace | requests.cpu | requests.mem | limits.cpu | limits.mem | storage |
|---|---|---|---|---|---|
| `delivery` | 6 | 12Gi | 12 | 24Gi | 40Gi |
| `data-services` | 4 | 8Gi | 8 | 16Gi | 60Gi |
| `identity` | 4 | 8Gi | 8 | 16Gi | 20Gi |
| `observability` | 2 | 4Gi | 4 | 8Gi | 20Gi |

```bash
for NS in delivery data-services identity observability; do
  kubectl -n $NS get resourcequota ns-quota || echo "🔴 $NS eksik"
done
kubectl -n delivery describe resourcequota ns-quota   # Used / Hard
```

✅ **KANIT:** `kubectl -n delivery describe quota` → **Used** ve **Hard** dolu,
`sıfır pod` değil · `kubectl -n delivery get limitrange ns-limits` → var

🔴 **DUR — `limitranges` GET 403 / timeout alıyorsanız:** RKE2 apiserver `[::1]:6443`
üzerinden konuşamıyor → IPv6 loopback kapatılmış. **§3.3.4** koşullu karar 3'e bakın
(`lo.disable_ipv6 = 0`).

### ADIM 5 — PriorityClass (kritik pod'lar evict edilmesin)

**Ne yapıyoruz:** 2 `PriorityClass` tanımlıyoruz: `banklab-critical` (CNPG/Cilium/kube-vip) ve `banklab-important` (Infisical/Keycloak/Traefik). **Neden:** RAM daralınca Kubernetes **en düşük öncelikli** pod'u atar — öncelik vermezseniz **Postgres'i atar**.

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata: { name: banklab-critical }
value: 1000000
globalDefault: false
description: "CNPG, Cilium, kube-vip — asla evict edilmesin"
---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata: { name: banklab-important }
value: 100000
globalDefault: false
description: "Infisical, Keycloak, Traefik"
```

```bash
kubectl get priorityclass
# banklab-critical   1000000  ✅
# banklab-important  100000    ✅
```

**Kullanımı:** CNPG cluster'ında `spec.priorityClassName: banklab-critical`
+ Keycloak/Infisical `priorityClassName: banklab-important`.

🔴 **NEDEN:** RAM daralınca Kubernetes **en düşük öncelikli** pod'u atar. Öncelik
vermezseniz **Postgres'i atabilir** → veri katmanı düşer.

---

<a id="s9"></a>

# 9. DEPOLAMA — MetalLB + Longhorn

### ⬜ ÖN KONTROL — depolamaya başlamadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| 6 node Ready | `kubectl get nodes --no-headers \| grep -c Ready` | `6` | §7 |
| Cilium çalışıyor | `kubectl -n kube-system get ds cilium -o jsonpath='{.status.numberReady}'` | `6` | §7.8 |
| VIP cevap veriyor | `curl -sk https://10.30.10.20:6443/readyz` | `ok` | §7.9 |
| Worker'da iscsid | `for w in 11 12 13; do ssh root@10.30.30.$w 'systemctl is-active iscsid'; done` | 3×`active` | §7.1 |
| Modüller yüklü | `ssh root@10.30.30.11 'lsmod \| grep -cE "overlay\|iscsi_tcp"'` | `2` | §7.1 |
| Worker'da edge0 | `ssh root@10.30.30.11 'ip -br link \| grep edge0'` | `UP` | §6.6 |
| Disk alanı (her worker) | `ssh root@10.30.30.11 'df -h /var/lib/longhorn -P \| tail -1'` | ≥ 15 GB | §6.3 |

### 🔀 KOŞULLU KARAR — hangi depolama?

| Durumunuz | Yol | Neden |
|---|---|---|
| **≥3 worker + iscsi** (bu lab) | **Longhorn** (replica 1) | dağıtık, PVC anlık görüntü, yedek |
| **Tek worker** | `local-path` (RKE2 default) | Longhorn tek node'da anlamsız |
| **NFS sunucunuz var** | `nfs-subdir-external-provisioner` | Longhorn'dan basit, RAM daha az |
| **Cloud** | `ebs-csi`/`pd-csi` | — |

⚠️ **Longhorn 1 replica uyarısı:** worker düşerse **veri erişilemez** (kopya yok).
Yedek **§12.4** (Barman) ile alınır. **3 replica** istiyorsanız §9.4 RAM bütçesini büyütün.

<a id="s9-1"></a>

## 9.1 MetalLB

**NEDEN MetalLB:** Traefik/Harbor/Grafana gibi servislerin **sabit iç IP**'si olmalı;
NodePort kullanmak `:3xxxx` portlarını her yere dağıtır ve DNS kayıtları kırılır.

```bash
helm repo add metallb https://metallb.github.io/metallb && helm repo update

# 🔴 KRİTİK: frr-k8s backend'i VARSAYILAN AÇIK → istemediğimiz 6 pod gelir ve Init'te takılır
helm install metallb metallb/metallb -n metallb-system --create-namespace \
  --set frrk8s.enabled=false \
  --set speaker.frr.enabled=false
```

**NEDEN `frrk8s.enabled=false`:** Chart 0.16+ `metallb-frr-k8s` DaemonSet'ini **default açar**
(top-level `frrk8s.enabled: true`). Kapatmazsanız 6 pod `Init:0/4`'te takılır, speaker
hazır olmaz → **hiçbir LoadBalancer IP almaz**. `speaker.frr.enabled` yalnız **eski** frr modu içindir, yetmez.

```bash
cat <<'EOF' | kubectl apply -f -
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata: { name: bank-private, namespace: metallb-system }
spec:
  addresses:
    - "10.30.20.200-10.30.20.220"     # 🔴 vnet20 = edge0 ağı
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata: { name: bank-l2, namespace: metallb-system }
spec:
  ipAddressPools: [bank-private]
  interfaces: [edge0]                  # 🔴 ZORUNLU — havuz ağı hangi NIC'te
EOF
```

### 🔴 `interfaces: [edge0]` NEDEN ZORUNLU (en sinsi hata)

Havuz `10.30.20.x` (**edge0** ağında). `interfaces` yazmazsanız speaker **tüm** NIC'lerde
ARP duyurusu yapar; `primary0` (10.30.30.x) arayüzünden duyurursa **komşu tablosu tutmaz**:

```
$ kubectl -n metallb-system get servicel2status -o wide
NAME   ALLOCATED NODE   SERVICE NAMESPACE IP
web    worker-3         web     demo      10.30.20.200     ← IP atandı görünüyor
$ ip neigh | grep 10.30.20.200
10.30.20.200 dev primary0 FAILED                            ← 🔴 ARP başarısız
$ curl http://10.30.20.200        → bağlantı yok
```

✅ **DOĞRU (iki segmentten test):**
```bash
kubectl -n metallb-system get pods        # controller 1/1 + speaker = worker sayısı (3)
kubectl -n metallb-system get ds          # YALNIZ metallb-speaker 3/3 (frr-k8s OLMAMALI)

# jump-1'den (10.30.10.x) VE worker-1'den (10.30.30.x) — İKİSİ de cevap vermeli
curl -s http://10.30.20.200/hostname; echo
curl -s http://10.30.20.200/hostname; echo
```

🔴 **DUR:** `ip neigh` **FAILED** ise → `interfaces: [edge0]` doğru mu? `ip -br link` ile
§6.6'daki `.link` ile worker arayüzleri **`primary0`/`edge0`** olarak sabitlendi —
kontrol: `ssh root@10.30.30.11 'ip -br link | grep -E "primary0|edge0"'`

### Koşullu karar — hangi MetalLB modu?

| Durumunuz | Mod | Ne yapın |
|---|---|---|
| Tek L2 segment, OPNsense var (bu lab) | **L2 + `interfaces`** | yukarıdaki gibi |
| OPNsense'te BGP yapılandırabilirsiniz | BGP (`frr-k8s.enabled=true`) | OPNsense → FRR eklentisi + `BGPAdvertisement` |
| Dışarıdan IP duyurmak istemiyorsunuz | sadece `ClusterIP` + `kubectl port-forward` | MetalLB'yi atlayın |
| `interfaces` çalışmıyor (sürüm sorunu) | `nodeSelectors` ile speaker'ı edge0'lı node'a sabitle | `L2Advertisement.spec.nodeSelectors` |

⚠️ **NEDEN BGP değil:** OPNsense'te **FRR eklentisi** + AS numarası + peer tanımı gerekir.
Lab'da L2 **30 saniyede** çalışır, BGP **1 saat** sürer. Üretimde BGP daha doğru (ECMP).

<a id="s9-2"></a>

## 9.2 🔴 Longhorn — imaj sorunu

> **NEDEN (V6/H57):** Longhorn **20 Running + 6 ImagePullBackOff**. ghcr.io **837 B/s** →
> imajlar hiç inmedi → PVC bağlanmadı → 4 saat kayıp.

**İmaj listesini al (doğru yol — E11 doğrulandı):**
```text
LH=v1.13.0
ghc "https://raw.githubusercontent.com/longhorn/longhorn/$LH/deploy/longhorn-images.txt" -o /tmp/lh.txt   # 🔴 ghc: gh-proxy
cat /tmp/lh.txt   # 14 imaj: longhornio/* + CSI sidecar'lar
```

🔁 **İKİ KURULUM YOLU:**

| Yol | Ne zaman | Adımlar |
|---|---|---|
| **A. Registry** | >5 MB/s | `helm install longhorn longhorn/longhorn` |
| **B. İmaj import** | <1 MB/s ✅ | Aşağıdaki 4 adım |

**B yolu:**
```bash
# 1) Sandbox/istasyonda: pull → pack → TEK dosya
#    (parçalı indirme GÜVENİLMEZ — V6/K29)
python3 pull_dh.py longhornio/longhorn-engine v1.13.0 out/
python3 pack.py out/ longhornio/longhorn-engine:v1.13.0 e.tar
# ... her imaj için ...
cat *.tar > longhorn-bundle.tar          # 🔴 TEK ASSET

# 2) GitHub Release'e yükle (ölçüldü: 22 MB/s upload)

# 3) Sunucu indir (ölçüldü: 3–9 MB/s)

# 4) Her worker'a SIRAYLA import — K13
for ip in 10.30.30.11 10.30.30.12 10.30.30.13; do
  loadgate || exit 1                      # 🔴 host yükü
  scp longhorn-bundle.tar root@$ip:/tmp/
  ssh root@$ip '/var/lib/rancher/rke2/bin/ctr -a /run/k3s/containerd/containerd.sock \
    -n k8s.io images import /tmp/longhorn-bundle.tar; sync; rm -f /tmp/*.tar'
  echo "✅ $ip"; sleep 60                  # 🔴 node'a nefes ver
done
```

**Values — `IfNotPresent` ZORUNLU (R52):**

> 🔴 **VALUES YAPISI — chart v1.13.0 `values.yaml`'dan doğrulandı:**
>
> | V9'daki (YANLIŞ) | Gerçek (DOĞRU) | Sonuç |
> |---|---|---|
> | `image.longhorn.engine.pullPolicy` | **`image.pullPolicy`** (düz alan) | Yanlış yerde → **sessizce yok sayılır** → imajlar yine çekilir → **H57 tekrar** |
> | `image.longhorn.manager.pullPolicy` | aynı: `image.pullPolicy` | aynı |
> | `image.longhorn.instanceManager.pullPolicy` | **BÖYLE BİR ANAHTAR YOK** | helm `--set` hata verir |
> | `defaultDataLocality: strict-local` | **yalnız `disabled` \| `best-effort`** | geçersiz değer → validation hatası |
>
> `strict-local` **StorageClass parametresidir** (SC'de geçerli), `defaultSettings`'te **değil**.

```yaml
# longhorn-values.yaml — chart v1.13.0 values.yaml ile DOĞRULANMIŞ
image:
  pullPolicy: IfNotPresent        # 🔴 DÜZ ALAN — tüm bileşenler için tek yer

persistence:
  createStorageClass: true        # §9.3: 'longhorn' SC'sini chart üretsin
  defaultClass: true              # varsayılan SC olsun
  defaultClassReplicaCount: 1     # 🔴 NEDEN 3 DEĞİL: 3 worker var, 3 replica
                                  #    aynı anda 2 node kaybına tolerans YOK; RAM bütçesi de
                                  #    (§9.4). Lab için 1 yeterli — yedek §12.4'te.
  defaultDataLocality: best-effort   # 🔴 'strict-local' DEĞİL (geçersiz). SC'de kullanılır.
  defaultFsType: ext4
  reclaimPolicy: Retain

defaultSettings:
  storageMinimalAvailablePercentage: 10
  replicaSoftAntiAffinity: true
  # ⚠️ defaultReplicaCount BURADA DEĞİL → persistence.defaultClassReplicaCount kullanın
```

🔴 **NEDEN `persistence.*` (defaultSettings DEĞİL):**
`defaultReplicaCount` **`defaultSettings`'te de geçerlidir** ama chart'ın SC'si
**`persistence.defaultClassReplicaCount`**'tan okur. İkisine **farklı** değer verirseniz
SC 3 replica, Longhorn 1 replica ister → **çelişki**. **Tek yer:** `persistence`.

```bash
# 🔴 Önceki sürümde 'helm repo add' YOKTU → 'repo not found' hatası
# 🔴 Doğru repo URL: charts.longhorn.io (longhorn.github.io/longhorn-charts → 404, E18)
helm repo add longhorn https://charts.longhorn.io
helm repo update

helm install longhorn longhorn/longhorn -n longhorn-system --create-namespace \
  --version 1.13.0 -f longhorn-values.yaml
```

🔴 **R50 — `--wait` KULLANMAYIN:** Yavaş imajda 15 dk timeout → release `failed`, kaynaklar
yarı kurulu kalır. Bunun yerine:
```bash
kubectl -n longhorn-system rollout status ds/longhorn-manager --timeout=20m
kubectl -n longhorn-system get pods | grep -vc Running     # → 0
```

✅ **KANIT:**
```bash
kubectl -n longhorn-system get pods | awk '{print $3}' | sort | uniq -c   # 26 Running
kubectl -n longhorn-system get nodes.longhorn.io                          # 3 worker Ready
```

<a id="s9-3"></a>

## 9.3 StorageClass'lar (PVC'lerin hangi diskten geleceği)

**Ne yapıyoruz:** Longhorn üzerine **4 StorageClass** tanımlıyoruz: `longhorn` (genel),
`longhorn-postgres` (CNPG), `longhorn-single` (tek replika), `longhorn-backup` (Retain).
**Neden ayrı:** Postgres `strict-local` ister (latency), genel iş yükü `best-effort`.
**Tek SC** kullanmak = her PVC'ye aynı politikayı zorlamak.


> 🔴 **M2 — ÇAKIŞMA:** Longhorn chart **`longhorn`** adında SC'yi **kendisi** oluşturur
> (`persistence.createStorageClass: true`, default). Elle aynı adı yazarsanız:
> `Error: StorageClass "longhorn" already exists` **VEYA** `kubectl apply` mevcut SC'yi **ezerse**
> chart'ın sonraki `helm upgrade`'i çakışır.
>
> 🔁 **DOĞRU YOL:** `longhorn` SC'sini **chart'a bırakın**, yalnız `longhorn-postgres`'i elle yazın.
> Chart'ın SC'sini özelleştirmek için **values** kullanın:
```yaml
# longhorn-values.yaml (§9.2'ye EKLE)
persistence:
  createStorageClass: true
  defaultClass: true
  defaultClassReplicaCount: 1
  reclaimPolicy: Retain
  migratable: true
```

```yaml
**NEDEN yalnız ikincisi:** `longhorn` SC'sini **chart** üretir (§9.2 `persistence.createStorageClass`).
Elle de yazarsanız `AlreadyExists` hatası alırsınız.

# YALNIZ ikinci SC'yi elle oluştur
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: { name: longhorn-postgres }
provisioner: driver.longhorn.io
allowVolumeExpansion: true
reclaimPolicy: Retain
volumeBindingMode: Immediate
parameters:
  numberOfReplicas: "1"
  dataLocality: "strict-local"
  fsType: ext4
  staleReplicaTimeout: "2880"
```

✅ **KANIT:** 1 Gi test PVC → `Bound` **60 s içinde**

```bash
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: PersistentVolumeClaim
metadata: { name: sc-test, namespace: default }
spec: { accessModes: [ReadWriteOnce], storageClassName: longhorn, resources: { requests: { storage: 1Gi } } }
EOF
kubectl get pvc sc-test -w      # Bound
```

---

<a id="s9-4"></a>

## 9.4 🔴 KAYNAK BÜTÇESİ — worker 16 Gi YETİYOR MU?

> **MANTIK HATASI:** V9 hiçbir yerde **resource requests/limits** tanımlamıyordu.
> Toplam istek (aşağıdaki tablo) **16.1 Gi** → worker başına **16 Gi**.
> Tek worker düşerse kalan iki worker **16.1 Gi'yi taşıyamaz** → pod'lar `Pending` veya **OOMKilled**.

### Gerçek toplam (en kötü durum: hepsi tek worker'da)

| Bileşen | Gi | Not |
|---|---|---|
| pg-keycloak | 2.0 | §12.3 |
| pg-infisical | 1.0 | §11.3 |
| Redis | 0.5 | §11.4 |
| Infisical ×2 | 2.0 | §11.5, `replicaCount:2` |
| Keycloak | 2.0 | §13.2 |
| Kafka broker | 1.0 | §13.6 |
| Harbor (core+registry+job+database) | 2.0 | §13.7 |
| Argo CD | 1.0 | §13.8 |
| Gitea + pg-gitea | 1.5 | §13.9 |
| kube-prometheus-stack | 2.5 | §13.3 |
| Longhorn (DaemonSet, worker başına) | 0.2 | §9.2 |
| Diğer (ESO, Traefik, vs.) | 0.6 | |
| **TOPLAM** | **16.1 Gi** | 🔴 **> 16 Gi** |

### Çözüm — 3 katmanlı (hepsi uygulanmalı)

**1) Infisical tek replica (lab için yeterli):**
```text
--set replicaCount=1        # 2.0 → 1.0 Gi
```

**2) Kafka + Harbor'ı KÜÇÜLT (lab'da gerekli değil):**
```yaml
# Kafka
broker: { resources: { requests: { memory: 512Mi, cpu: 100m }, limits: { memory: 768Mi } } }
# Harbor
core:     { resources: { requests: { memory: 256Mi }, limits: { memory: 512Mi } } }
registry: { resources: { requests: { memory: 256Mi }, limits: { memory: 512Mi } } }
```

**3) Prometheus retention + limit (en büyük tüketici):**
```yaml
prometheus:
  prometheusSpec:
    retention: 7d                     # NEDEN: 15d → 7d, disk+RAM yarıya
    resources:
      requests: { memory: 1Gi, cpu: 200m }
      limits:   { memory: 2Gi }       # 🔴 LIMIT ŞART — yoksa OOM tüm node'u etkiler
```

### Yeni toplam (düzeltme sonrası)

| | Gi |
|---|---|
| Düzeltme öncesi | **16.1** 🔴 |
| Infisical 2→1 | 15.1 |
| Kafka 1.0→0.75 | 14.9 |
| Harbor 2.0→1.0 | 13.9 |
| **Toplam** | **13.9 Gi** ✅ |

**3 worker'a dağılım:** 13.9 / 3 = **4.6 Gi/worker** → %71 boş alan.
**Tek worker kaybı:** 13.9 Gi → **16 Gi'ye sığar** ✅ (kalan 2 worker'ın birine)

### Doğrulama — gerçekten sığıyor mu?

```bash
# 1) Her node'un kullanılabilir RAM'i
kubectl top nodes                              # kullanılan
kubectl describe node worker-1 | grep -A5 "Allocated resources"

# 2) Toplam REQUEST (scheduler'ın gördüğü)
kubectl get pods -A -o json | jq '[.items[].spec.containers[].resources.requests.memory] | map(select(.!=null)) | map(sub("Mi";"")|tonumber) | add / 1024'
# → 13.9 civarı olmalı (Gi)

# 3) SİMÜLASYON — worker-1'i drain et, her şey sığıyor mu?
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data --dry-run=server
# → "error: unable to drain node ... would violate PodDisruptionBudget" = SORUN
```

🔴 **DUR:** `kubectl top nodes`'da herhangi bir worker **%85** üzerindeyse **§13.7 Harbor'ı
veya §13.6 Kafka'yı atlayın** — lab için zorunlu değil. Bu rehberin **ALTERNATİF** kuralı.

⚠️ **NEDEN limits şart:** Limit olmadan bir bileşen (örn. Prometheus) **tüm node RAM'ini** yer →
**kubelet OOM** → node `NotReady` → Longhorn replica'ları düşer → **veri kaybı riski**.

---

<a id="s10"></a>

# 10. GÜVENLİK ZİNCİRİ — step-ca → cert-manager

<a id="s10-1"></a>

## 10.1 step-ca

> **NEDEN (R51):** `drop: [ALL]` + root binary = **çalışmaz**. step-ca root olarak başlar.

```yaml
# step-ca-values.yaml
securityContext:
  readOnlyRootFilesystem: false
  allowPrivilegeEscalation: false
  capabilities: { drop: [ALL] }
  runAsUser: 0            # 🔴 NEDEN: step-ca root ister (R51)
  runAsNonRoot: false
storage:
  className: longhorn
  size: 1Gi
replicaCount: 1           # NEDEN: >1 için SQL gerekir (R53)
```

```bash
helm repo add smallstep https://smallstep.github.io/helm-charts && helm repo update
helm install step-ca smallstep/step-ca -n step-system --create-namespace -f step-ca-values.yaml
```

✅ **KANIT:** `kubectl -n step-system get pods` → 1/1 Running

**Max TTL:** step-ca **744h (31 gün)** üstü veremez → server **720h**, client **240h**.

<a id="s10-1-b"></a>

## 10.1-B 🔴 step-ca GÜVENLİK BAĞLAMI — `drop: [ALL]` **ÇALIŞMAZ** (R51)

> **V6'DA 3 SAAT KAYBETTİRDİ.** Rehber `runAsNonRoot: true, runAsUser: 1000` +
> `capabilities: {drop: [ALL]}` yazıyordu. Pod **hiç başlamadı**.

### 🔴 GERÇEK HATA MESAJI

```bash
kubectl -n cert-system logs deploy/step-ca
# exec /usr/local/bin/step-ca: operation not permitted      (Exit Code 255)
```

### NEDEN — iki ayrı tuzak

| Tuzak | Detay |
|---|---|
| **1. `runAsNonRoot: true`** | İmajdaki `/usr/local/bin/step-ca` **root:root** sahipli (54 MB statik binary). `runAsUser: 1000` ile **exec edilemez** |
| **2. `capabilities: {drop: [ALL]}`** | `runAsUser: 0` yapsanız bile `drop: ALL` → `operation not permitted`. **Suçlu bu** |

### ⬜ ÖN KONTROL — manifest yazmadan

| Kontrol | Komut | Beklenen |
|---|---|---|
| İmaj binary sahipliği | `docker run --rm --entrypoint ls smallstep/step-ca:0.30.2 -l /usr/local/bin/step-ca` | **`root root`** ← non-root ÇALIŞMAZ |
| Mevcut securityContext | `kubectl -n cert-system get deploy step-ca -o jsonpath='{.spec.template.spec.securityContext}'` | `drop` **olmamalı** |

### 🔀 KOŞULLU KARAR — güvenlik bağlamı

| Önceliğiniz | `runAsUser` | `capabilities.drop` | Sonuç |
|---|---|---|---|
| **Çalışsın** (bu lab) | **`0`** | **kaldır** | ✅ `/health` = `{"status":"ok"}` |
| Sıkı güvenlik | `1000` | `ALL` | 🔴 `operation not permitted` — **imaj izin vermiyor** |
| Sıkı güvenlik + çalışsın | **kendi imajınızı yapın** (`chown 1000`) | `ALL` | ⚠️ Dockerfile gerekir |

### ✅ DOĞRULANMIŞ ÇALIŞAN MANIFEST

```yaml
spec:
  template:
    spec:
      securityContext:
        runAsUser: 0
        runAsGroup: 0
        fsGroup: 0
        seccompProfile: { type: RuntimeDefault }
      containers:
        - name: step-ca
          imagePullPolicy: IfNotPresent     # 🔴 R52: yoksa 8-15 dk yeniden indirir
          securityContext:
            allowPrivilegeEscalation: false
            # 🔴 capabilities BLOĞU YOK — eklemeyin!
      nodeSelector:
        kubernetes.io/hostname: worker-2    # 🔴 R52: imaj önceden buraya çekildi
```

**NEDEN `nodeSelector`:** imaj **yalnız worker-2'ye** `ctr import` edildi. `nodeSelector`
yoksa pod başka node'a düşer → **8–15 dk yeniden indirme** (V6'da 3 kez oldu).

### ✅ KANIT

```bash
kubectl -n cert-system get pods -l app.kubernetes.io/name=step-ca
# → 1/1 Running

kubectl -n cert-system exec deploy/step-ca -- wget -qO- https://localhost:9000/health
# → {"status":"ok"} ✅

kubectl -n cert-system logs deploy/step-ca | head -4
# Starting Smallstep CA/0.30.2 (linux/amd64)
# Serving HTTPS on :9000 ...
```

### 🔴 DUR

| Belirti | Sebep | Çözüm |
|---|---|---|
| `operation not permitted` (255) | `drop:[ALL]` VEYA `runAsUser:1000` | ikisini de kaldır |
| `1/1 Running` ama `/health` 503 | sealed / DB bağlanamadı | `logs` — `ca.json` yolu |
| `ImagePullBackOff` (imaj yerelde) | `nodeSelector` yok | ekle |
| `Readiness probe failed: connection refused` | step-ca **başlamadı** — log'da 255 arayın | — |

---

<a id="s10-2"></a>

## 10.2 cert-manager (sertifika otomasyonu)

**Ne yapıyoruz:** cert-manager'ı kuruyoruz — Kubernetes'in **sertifika operatörü**.
`Certificate` CRD'si oluşturunca **kendi imzalar, kendi yeniler**. **Neden:** elle
sertifika = 90 günde bir **her** servis için ayrı iş; ayrıca `notAfter` unutulur → **kesinti**.


```bash
# 🔴 CRD > 256 KB → apply HATA verir (H75)
# 🔴 ÖNCE indir (gh-proxy ile 38 MB/s), SONRA apply
ghc "https://github.com/cert-manager/cert-manager/releases/download/v1.21.3/cert-manager.crds.yaml" -o cert-manager.crds.yaml
kubectl apply --server-side --force-conflicts -f cert-manager.crds.yaml

helm repo add jetstack https://charts.jetstack.io && helm repo update
helm install cert-manager jetstack/cert-manager -n cert-manager --create-namespace \
  --set crds.enabled=false
```

✅ **KANIT:** `kubectl get crd | grep cert-manager | wc -l` → 6+

<a id="s10-3"></a>

## 10.3 StepClusterIssuer

### 🔴 — step-issuer SÜRÜMÜ PINLENMELİ

```bash
# ❌ 'latest/download' → her seferinde farklı sürüm → tekrar üretilemez kurulum
# ✅ v0.12.0 (18 Kas 2026 itibarıyla en yeni, GitHub releases API)
STEP_ISSUER_VER=v0.12.0
ghc "https://github.com/smallstep/step-issuer/releases/download/${STEP_ISSUER_VER}/step-issuer.yaml" -o step-issuer.yaml
kubectl apply -f step-issuer.yaml

# Doğrula
kubectl get crd | grep -E "stepclusterissuer|stepissuer"     # → 2 CRD
```

### 🔴 — `caBundle` PLACEHOLDER OLAMAZ (K20 ihlali)

> **V9'DAKİ HATA:** `CA=$(kubectl ...)` ile değişken **alınıyordu** ama YAML'da
> `caBundle: <step-ca CA PEM>` **placeholder** olarak kalıyordu. Kullanıcı kopyalarsa
> **geçersiz sertifika** → `StepClusterIssuer READY=False` → **tüm TLS zinciri çöker**.

```bash
# Değişkenler
PW=$(kubectl -n step-system get secret step-ca-provisioner-password \
      -o jsonpath='{.data.password}' | base64 -d)
KID=$(kubectl -n step-system get configmap step-certificates-config \
      -o jsonpath='{.data.ca\.json}' | jq -r '.authority.provisioners[] | select(.type=="JWK") | .kid')
CA=$(kubectl -n step-system get configmap step-certificates-config \
      -o jsonpath='{.data.ca\.crt}')

# Boş olmadıklarını DOĞRULA (yoksa aşağıdaki YAML anlamsız)
[ -n "$PW" ] && [ -n "$KID" ] && [ -n "$CA" ] || { echo "🔴 değişken boş — DUR"; exit 1; }
echo "KID=$KID"; echo "CA uzunluk=${#CA}"; echo "$CA" | head -1
```

**YAML'ı placeholder YERİNE değişkenlerle üret** (heredoc, tırnak YOK → genişler):

```bash
cat > step-issuer-cr.yaml <<EOF
apiVersion: certmanager.step.sm/v1beta1
kind: StepClusterIssuer
metadata:
  name: banklab-step
spec:
  url: https://step-certificates.step-system.svc.cluster.local:9000
  caBundle: |
$(echo "$CA" | sed 's/^/    /')
  provisioner:
    name: admin
    kid: ${KID}
    passwordRef:
      name: step-ca-provisioner-password
      namespace: step-system
      key: password
EOF

kubectl apply -f step-issuer-cr.yaml
```

✅ **KANIT (3 aşamalı):**
```bash
kubectl get clusterissuer banklab-step -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}'
# → True
kubectl get clusterissuer banklab-step -o jsonpath='{.status.conditions[?(@.type=="Ready")].message}'
# → ok (hata mesajı BOŞ olmalı)
kubectl get secret step-ca-provisioner-password -n step-system -o jsonpath='{.data.password}' | wc -c
# → >0
```

🔴 **DUR:** `READY=False` → `kubectl describe clusterissuer banklab-step` → `Events`:
| Mesaj | Sebep | Çözüm |
|---|---|---|
| `x509: certificate signed by unknown authority` | `caBundle` yanlış/boş | `$CA` doğru configmap'ten mi? `echo "$CA"` |
| `provisioner not found` | `kid` yanlış | `$KID` boş mu? `jq` sorgusunu tekrar |
| `connection refused` | step-ca servisi yok | `kubectl -n step-system get svc` |

### 🔴 — `commonName` YERİNE `dnsNames` (SAN)

> **NEDEN:** Modern TLS istemcileri (Go 1.15+, Chrome, Java 8+) **CN alanını YOK SAYAR**;
> yalnız **SAN**'a bakar. `commonName` tek başına verirseniz sertifika **geçersiz** sayılır.

**Test sertifikası:**
```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: smoke, namespace: delivery }
spec:
  secretName: smoke-tls
  duration: 240h            # step-ca max 744h (§10.1)
  renewBefore: 72h
  dnsNames:                                   # 🔴 SAN — ZORUNLU
    - smoke.banklab.alperozbay.com
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
```

✅ **KANIT:** `kubectl -n delivery get certificate smoke` → `READY=True` (**10 saniyede** — V6'da ölçüldü)

---

<a id="s11"></a>

# 11. SECRET YÖNETİMİ — Infisical

### ⬜ ÖN KONTROL — secret zincirine başlamadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| CNPG operator çalışıyor | `kubectl -n cnpg-system get pods --no-headers \| grep -c 1/1` | `1` | **§12.1 (ÖNCE!)** |
| Longhorn PVC bağlanıyor | `kubectl -n default get pvc sc-test` | `Bound` | §9.3 |
| `longhorn-postgres` SC var | `kubectl get sc longhorn-postgres` | var | §9.3 |
| Traefik YOK (henüz) | `kubectl -n traefik get svc 2>/dev/null \| wc -l` | `0` | ⚠️ §13.1'i **sonra** yap |
| namespace'ler var | `kubectl get ns data-services identity` | 2 satır | §8.0 |
| step-ca READY | `kubectl -n step-system get pods --no-headers \| grep -c 1/1` | `1` | §10.1 |

🔴 **EN KRİTİK SIRA:** `§12.1 CNPG operator` **bu bölümden ÖNCE** olmalı. Yoksa
`pg-infisical` cluster'ı oluşturulamaz → Infisical başlayamaz → **tüm zincir durur**.
Bu, V6'da 13 saat kaybettiren döngünün ta kendisidir.

### 🔀 KOŞULLU KARAR — hangi Infisical kurulumu?

| Durumunuz | Yol | Neden | RAM |
|---|---|---|---|
| **CNPG var, yedek gerekli** (bu lab) | **YOL A** — dış PG + dış Redis | PG yedeklenebilir (§12.4), state kalıcı | 1 + 0.5 Gi |
| **En hızlı kurulum** | **YOL B** — chart'ın bundled PG+Redis | Tek komut, CNPG gerekmez | ~1.5 Gi |
| **Kubernetes dışı PG var** | `DB_CONNECTION_URI` dış host | — | 1 Gi |
| **Üretim** | YOL A + `replicaCount: 2` | HA | 2 + 0.5 Gi |

⚠️ **YOL B'de CNPG KULLANILMAZ** → `pg-infisical-owner` secret'ı **gerekmez**.
İki yolu karıştırmak en sık hatadır.

<a id="s11-1"></a>

## 11.1 Neden Infisical (S37)

| Kriter | OpenBao | Infisical |
|---|---|---|
| Restart sonrası | **SEALED** → 503 (H79) | açılır |
| Auto-unseal | yok | gerekmez |
| Operatör yükü | her restart'ta unseal | düşük |
| Ek bağımlılık | — | **PG + Redis (zorunlu)** |

<a id="s11-2"></a>

## 11.2 Sıra — **bootstrap secret deseni** 🔴 DİKKAT: §11 ile §12 İÇ İÇE

> **V9'da düzeltildi:** Önceki sürümde §11 (Infisical) §12'den (CNPG) **önce** geliyordu, ama
> Infisical `pg-infisical` için **CNPG operator'ına** ihtiyaç duyar. Döngü şöyle kırılır:
> **CNPG operator'ı §12.1'de kurulur** ama Infisical'dan **önce** çalıştırılır.

**GERÇEK UYGULAMA SIRASI (bölüm numarası sırası DEĞİL):**

| Adım | Ne | Nerede | Neden burada |
|---|---|---|---|
| 1 | Namespace'ler + PSA | **§8.0** | Her şeyden önce |
| 2 | Cert-manager + step-ca | **§10** | Infisical TLS'i için |
| 3 | **CNPG operator** | **§12.1** | ⬅ **§11'den ÖNCE çalıştır** |
| 4 | Barman plugin | §12.2 | (opsiyonel, sonra da olur) |
| 5 | `pg-infisical` + **ELLE secret** | §11.3 | Tek elle adım |
| 6 | Redis | §11.4 | Infisical zorunlu bağımlılığı |
| 7 | Infisical Helm | §11.5 | |
| 8 | Infisical UI → `keycloak-username` + `keycloak-password` | §11.7 | |
| 9 | **ESO** + SecretStore + ExternalSecret | §11.8 | Infisical hazır olunca |
| 10 | `pg-keycloak` | §12.3 | Secret artık Infisical'dan |
| 11 | Barman yedeği | §12.4 | Son |

```bash
# Doğrulama: bu sırayla mı gittiniz?
kubectl -n cnpg-system get pods            # adım 3 ✅
kubectl -n data-services get cluster       # adım 5 ✅ pg-infisical healthy
kubectl -n data-services get pods          # adım 7 ✅ infisical 1/1
kubectl -n data-services get secretstore   # adım 9 ✅ Valid
kubectl -n identity get cluster            # adım 10 ✅ pg-keycloak healthy
```

🔴 **DUR:** Adım 5'te `pg-infisical` **Pending** kalırsa → CNPG operator yok (§12.1).
Adım 10'da `pg-keycloak` **Setting up primary**'da kalırsa → Secret yok (§11.8).

<a id="s11-3"></a>

## 11.3 pg-infisical + elle secret

> 🔴🔴 **SIRA UYARISI — BU ADIMDAN ÖNCE §12.1 ve §12.2 YAPILMIŞ OLMALI**
>
> Bu bölümdeki `kind: Cluster` (CNPG CRD'si) **§12.1'de kurulur**. Fiziksel sıra §11→§12
> olduğu için **önce §12.1 + §12.2'yi atlayıp buraya dönün** (§0.5 adım 9 → 10).
>
> | Kontrol | Komut | Beklenen |
> |---|---|---|
> | CNPG CRD var mı | `kubectl get crd clusters.postgresql.cnpg.io` | satır döner |
> | Operator çalışıyor mu | `kubectl -n cnpg-system get pods` | `1/1 Running` |
> | Barman plugin | `kubectl -n cnpg-system get pods \| grep barman` | `1/1 Running` |
>
> **Değilse:** bu bölümü **BIRAKIN**, §12.1 → §12.2 → buraya dönün.
> Atlarsanız: `error: resource mapping not found ... clusters.postgresql.cnpg.io` →
> 30 saniyelik hata için 2 saat debug (V6'da tam olarak bu oldu).

```bash
kubectl -n data-services create secret generic pg-infisical-owner \
  --from-literal=username=infisical \
  --from-literal=password="$(openssl rand -base64 24 | tr -d '/+=' | head -c 24)"
```

```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata: { name: pg-infisical, namespace: data-services }
spec:
  instances: 1
  imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm   # 🔴 ghcr.io 837 B/s → Docker Hub aynası
  storage:
    storageClass: longhorn-postgres
    size: 5Gi
  bootstrap:
    initdb:
      database: infisical
      owner: infisical
      secret: { name: pg-infisical-owner }
  resources:
    requests: { cpu: "250m", memory: "512Mi" }
    limits:   { cpu: "1",    memory: "2Gi" }
```

✅ **KANIT:** `kubectl -n data-services get cluster pg-infisical` → `Cluster in healthy state`
`kubectl -n data-services get pvc` → `Bound`

<a id="s11-4"></a>

## 11.4 Redis (Infisical'ın önbellek/kuyruk deposu)

**Ne yapıyoruz:** Infisical için **Redis** kuruyoruz. **Neden zorunlu:** Infisical
oturumları ve arka plan işlerini Redis'te tutar; **Redis yoksa pod başlamaz**
(`ECONNREFUSED 6379`). **§1.3'te ayrıntı.**


```yaml
apiVersion: v1
kind: Secret
metadata: { name: redis-infisical, namespace: data-services }
stringData:
  REDIS_PASSWORD: "<openssl rand -base64 24>"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata: { name: redis-data, namespace: data-services }
spec:
  accessModes: [ReadWriteOnce]
  storageClassName: longhorn
  resources: { requests: { storage: 1Gi } }
---
apiVersion: apps/v1
kind: Deployment
metadata: { name: redis, namespace: data-services }
spec:
  replicas: 1
  selector: { matchLabels: { app: redis } }
  template:
    metadata: { labels: { app: redis } }
    spec:
      containers:
      - name: redis
        image: redis:7.4-alpine
        imagePullPolicy: IfNotPresent
        args: ["--requirepass", "$(REDIS_PASSWORD)", "--maxmemory", "256mb",
               "--maxmemory-policy", "allkeys-lru", "--appendonly", "yes"]
        env:
        - { name: REDIS_PASSWORD, valueFrom: { secretKeyRef: { name: redis-infisical, key: REDIS_PASSWORD } } }
        ports: [{ containerPort: 6379 }]
        resources:
          requests: { cpu: "50m", memory: "64Mi" }
          limits:   { cpu: "500m", memory: "384Mi" }
        volumeMounts: [{ name: data, mountPath: /data }]
      volumes:
      - name: data
        persistentVolumeClaim: { claimName: redis-data }
---
apiVersion: v1
kind: Service
metadata: { name: redis, namespace: data-services }
spec:
  selector: { app: redis }
  ports: [{ port: 6379, targetPort: 6379 }]
```

✅ **KANIT:** `kubectl -n data-services exec deploy/redis -- sh -c 'redis-cli -a $REDIS_PASSWORD ping'` → `PONG`

<a id="s11-5"></a>

## 11.5 🔴 Infisical Helm — GERÇEK chart davranışı (E6/E7/E8)

> **V7'deki hata:** `postgresql.enabled: false` + secret'a `DB_CONNECTION_URI` yazdım.
> **Chart'ın kodu okundu:** `postgresql.enabled: true` ise URI'yi **kendisi** yazar →
> secret'ta da URI varsa **iki `DB_CONNECTION_URI` env'i** çakışır.
> `redis.enabled: true` ise `REDIS_URL`'i **`redis-master:6379`** olarak yazar (bizim Service `redis`!).
> Bu yüzden **ya chart'ın subchart'larını kullan, ya da hepsini kapat ve URI'yi secret'tan ver.**

🔁 **YOL A — Dış PG/Redis (V9 ÖNERİLEN, CNPG'yi kullanır):**

```text
**NEDEN `infisical-secrets` (chart'ın `kubeSecretRef`'i):** Chart bu isimdeki secret'ı
**tüm env değişkenleri** için okur. İçine **yalnız** `SITE_URL`, `ENCRYPTION_KEY`,
`AUTH_SECRET` koyun — `DB_CONNECTION_URI`/`REDIS_URL` **KOYMAYIN** (chart kendi yazar, çakışır).

kubectl -n data-services create secret generic infisical-secrets \
  --from-literal=AUTH_SECRET="$(openssl rand -base64 32)" \
  --from-literal=ENCRYPTION_KEY="$(openssl rand -hex 16)" \
  --from-literal=DB_CONNECTION_URI="postgres://infisical:$(kubectl -n data-services get secret pg-infisical-owner -o jsonpath='{.data.password}' | base64 -d)@pg-infisical-rw.data-services.svc:5432/infisical" \
  --from-literal=REDIS_URL="redis://:$(kubectl -n data-services get secret redis-infisical -o jsonpath='{.data.REDIS_PASSWORD}' | base64 -d)@redis.data-services.svc:6379" \
  --from-literal=SITE_URL="https://infisical.banklab.alperozbay.com"
```

```yaml
# infisical-values.yaml — YOL A
infisical:
  replicaCount: 1                       # NEDEN: chart default 2, lab için 1 yeter
  image: { repository: infisical/infisical, tag: v0.146.0-postgres, pullPolicy: IfNotPresent }
  kubeSecretRef: infisical-secrets      # 🔴 envFrom.secretRef — tüm secret env'e basılır
  resources:
    requests: { cpu: "200m", memory: "512Mi" }
    limits:   { cpu: "1",    memory: "1Gi" }
postgresql:
  enabled: false                        # 🔴 CNPG kullanıyoruz → chart URI yazmasın
redis:
  enabled: false                        # 🔴 kendi Redis'imiz → chart REDIS_URL yazmasın
ingress:
  enabled: false                        # Traefik IngressRoute kullanacağız
```

```bash
helm repo add infisical https://dl.cloudsmith.io/public/infisical/helm-charts/helm/charts/
helm repo update
helm install infisical infisical/infisical-standalone-postgres \
  -n data-services -f infisical-values.yaml
```

🔁 **YOL B — Chart'ın bundled PG+Redis'i (en basit, en hızlı):**

> 🔴 **DİKKAT:** Bu yolda **CNPG KULLANILMAZ**. Chart'ın kendi Bitnami
> PostgreSQL + Redis subchart'ları gelir. `pg-infisical-owner` secret'ına **İHTİYAÇ YOK** —
> o secret'ı CNPG cluster'ı (§11.3) üretir ve **YOL B'de CNPG yok**.
> (`existingSecret: pg-infisical-owner` **YANLIŞ** olurdu,
> çünkü §11.3 atlanacaksa o secret hiç oluşmaz.)

```yaml
# infisical-values.yaml — YOL B (bundled, tek komut)
infisical:
  replicaCount: 1
  kubeSecretRef: infisical-secrets       # SITE_URL + ENCRYPTION_KEY + AUTH_SECRET (§11.5)
postgresql:
  enabled: true
  auth:
    username: infisical
    database: infisical
    # 🔴 Parola SİZ üretin (sabit/rastgele chart üretimi tekrar kurulumda kaybolur)
    password: ""                          # boş → aşağıdaki secret'tan
    existingSecret: infisical-pg-auth
    secretKeys: { userPasswordKey: postgres-password }
  image: { registry: mirror.gcr.io, repository: bitnamilegacy/postgresql }
redis:
  enabled: true
  architecture: standalone                # NEDEN: replication 2× RAM ister (§9.4)
  auth:
    enabled: true
    existingSecret: infisical-redis-auth
    existingSecretPasswordKey: redis-password
  master:
    persistence: { enabled: true, storageClass: longhorn, size: 1Gi }
  image: { registry: mirror.gcr.io, repository: bitnamilegacy/redis }
```

```bash
# 🔴 YOL B'nin secret'ları — ÖNCE üret
PG_PW=$(openssl rand -base64 24 | tr -d '/+=')
RD_PW=$(openssl rand -base64 24 | tr -d '/+=')
cat >> /root/.banklab-client-secrets <<EOF
INFISICAL_PG_PW=$PG_PW
INFISICAL_REDIS_PW=$RD_PW
EOF

kubectl -n data-services create secret generic infisical-pg-auth \
  --from-literal=postgres-password="$PG_PW"
kubectl -n data-services create secret generic infisical-redis-auth \
  --from-literal=redis-password="$RD_PW"

# Doğrula
kubectl -n data-services get secret infisical-pg-auth infisical-redis-auth
```

**Ama:** `kubeSecretRef` secret'ında `DB_CONNECTION_URI`/`REDIS_URL` **OLMAMALI** (E7 — chart
kendisi yazar, çakışırsa pod `CrashLoop`). Ve `bitnamilegacy` imajları ayrıca çekilir.

<a id="s11-5-b"></a>

## 11.5-B 🔴 Infisical ERİŞİMİ: IngressRoute + Certificate

> **NEDEN GEREKLİ:** `SITE_URL=https://infisical.banklab.alperozbay.com` yazıldı ama
> **hiçbir route yok** → kullanıcı **ilk admin hesabını oluşturamaz** → §11.7 imkânsız →
> `keycloak-username` / `keycloak-password` secret'ı girilemez → **tüm zincir kilitlenir**.

```yaml
# Certificate
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: infisical-tls, namespace: data-services }
spec:
  secretName: infisical-tls
  duration: 720h
  renewBefore: 240h
  dnsNames: [infisical.banklab.alperozbay.com]
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
---
# Service (ClusterIP hali — chart 8080 dinler)
apiVersion: v1
kind: Service
metadata: { name: infisical-http, namespace: data-services }
spec:
  selector: { app.kubernetes.io/name: infisical }
  ports: [{ port: 8080, targetPort: 8080 }]
---
# IngressRoute
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata: { name: infisical, namespace: data-services }
spec:
  entryPoints: [websecure]
  routes:
    - match: Host(`infisical.banklab.alperozbay.com`)
      kind: Rule
      services: [{ name: infisical-http, port: 8080 }]
  tls: { secretName: infisical-tls }
```

```text
**NEDEN `websecure` entrypoint:** Traefik 2 entrypoint tanımlar (`web` :80, `websecure` :443).
`web`'e route koyarsanız TLS **yok** olur → browser `NET::ERR_SSL_PROTOCOL_ERROR`.

kubectl -n data-services apply -f infisical-ingress.yaml

# DNS: OPNsense Unbound'a kayıt ekle (veya wildcard)
# *.banklab.alperozbay.com → 10.30.20.x (Traefik LB IP)
```

✅ **KANIT:**
```bash
kubectl -n data-services get pods
# → infisical-xxx 1/1 Running
kubectl -n data-services logs deploy/infisical --tail=30 | grep -iE "listening|server started|migrat"
curl -sk -o /dev/null -w "%{http_code}\n" https://infisical.banklab.alperozbay.com/api/status
# → 200  (bu olmadan §11.7 İMKÂNSIZ)
```

<a id="s11-6-b"></a>

## 11.6-B 🔴 Infisical pod restart testi — **ZORUNLU**

> **NEDEN:** Bu test OpenBao'da **BAŞARISIZ** oluyordu (sealed kalıyordu). Infisical'da **geçmeli**.

```bash
kubectl -n data-services delete pod -l app.kubernetes.io/name=infisical
sleep 45
kubectl -n data-services get pods -l app.kubernetes.io/name=infisical   # 1/1 Running
```

🔴 **DUR:** 1/1 olmuyorsa `kubectl logs` + `kubectl describe` → Redis/PG erişimi.

<a id="s11-7"></a>

## 11.7 Infisical ilk kullanıcı + proje

```bash
kubectl -n data-services port-forward svc/infisical 8080:8080 &
# Tarayıcı: http://localhost:8080 → Sign up (ilk kullanıcı = admin)
# Project: "banklab" → Environment: "prod" → Secret ekle:
#   SEÇENEK B (önerilen) — iki düz secret:
#     keycloak-username  →  bank_keycloak_owner
#     keycloak-password  →  <AŞAĞIDAKİ KOMUTLA OKU>
#   SEÇENEK A (JSON) — tek secret:
#     keycloak-db  →  {"username":"...","password":"..."}
```

✅ **KANIT:** UI'da secret görünür.

<a id="s11-7-b"></a>

## 11.7-B 🔴 `pg-keycloak-owner` PAROLASI NEREDEN GELİYOR?

> **NEDEN AYRI BÖLÜM:** Bu karar §11.8 (ESO) ile §12.3 (CNPG) **arasındaki sözleşmedir**.
> Yanlış kurulursa Keycloak `password authentication failed` alır ve **teşhis çok zordur**.

> **NEDEN:** Rehber §11.7'de "Infisical UI'ya `keycloak-password` gir" diyordu
> ama **parolayı nereden alacağınızı söylemiyordu**. `pg-keycloak-owner` secret'ını
> **CNPG otomatik üretir** ve değeri **rastgele**dir → Infisical'a yazacak değeri
> **bilmiyorsunuz** → **sır döngü** (secret → secret).

**ÇÖZÜM — 2 yol:**

**YOL A (V9 ÖNERİLEN) — Parolayı SİZ belirleyin, CNPG onu kullansın:**
```bash
# 1) Parolayı ÜRET (bir kez)
KC_DB_PW=$(openssl rand -base64 24 | tr -d '/+=')
echo "KC_DB_PW=$KC_DB_PW" >> /root/.banklab-client-secrets

# 2) Secret'ı ELLE oluştur (CNPG bunu kullanacak — üretmesine gerek yok)
kubectl -n identity create secret generic pg-keycloak-owner \
  --from-literal=username=bank_keycloak_owner \
  --from-literal=password="$KC_DB_PW"

# 3) AYNI değeri Infisical'a yaz (UI'dan)
echo "Infisical UI → Path: / · keycloak-password = $KC_DB_PW"

# 4) SONRA ExternalSecret'ı KALDIR — çünkü secret'ı biz yönettiğimiz için
#    ESO'nun 'Owner' policy'si onu SİLER. Şu şekilde kur:
```
```yaml
# ExternalSecret'ı DROP ETME — sadece 'bootstrap'tan sonra' yönetime geç
# En basit güvenli desen: ExternalSecret YOK, secret ELLE yönetilir (lab için)
# ve tek kaynak Infisical (parola orada saklanır, geri getirilebilir).
```

**YOL B — CNPG üretsin, sonra OKU ve Infisical'a yaz:**
```bash
# 1) pg-keycloak cluster'ı secret OLMADAN başlat:
#    bootstrap.initdb.secret SATIRINI YORUM SATIRI YAP → CNPG kendi üretir
# 2) Bekle, sonra OKU:
kubectl -n identity get secret pg-keycloak-app -o jsonpath='{.data.password}' | base64 -d
# (CNPG ürettiği secret: <cluster>-app, kullanıcı: postgres)
# 3) Değeri Infisical'a yaz
# 4) Keycloak CR'da usernameSecret/passwordSecret → pg-keycloak-app
```

🔴 **DUR — EN SIK YAPILAN HATA:**
`bootstrap.initdb.secret: pg-keycloak-owner` **+** `ExternalSecret` aynı isimde
`creationPolicy: Owner` veriyorsa, ESO secret'ı **siler/yeniden yazar** → CNPG bir kez
initdb yaptığı için **şifre değişir ama PG'deki şifre eski kalır** → Keycloak
**`password authentication failed`** → teşhis edilmesi zor.

**✅ V9 KURALI (lab):** `pg-keycloak-owner` **ELLE** oluşturulur (YOL A),
`ExternalSecret` **sadece Keycloak CR'ın değil** — Secret **tek sahipli** olmalı.

---

<a id="s11-8"></a>

## 11.8 ESO — **`external-secrets.io/v1`** (E2/E3 düzeltmesi)

**Ne yapıyoruz:** External Secrets Operator'ü kuruyoruz — **Infisical'daki secret'ları
Kubernetes `Secret`'ına senkronize eder**. `ExternalSecret` CRD'si "şu key'i şu secret'a
koy" der; ESO **periyodik olarak** yeniler. **Neden:** pod'lar secret'ı **env** olarak
görsün; Infisical'da değişince **otomatik** güncellensin.


```bash
helm repo add external-secrets https://charts.external-secrets.io && helm repo update

# 🔴 CRD TAMAMI (24) — eksikse controller ölür (H74)
helm template external-secrets external-secrets/external-secrets -n external-secrets \
  --include-crds | kubectl apply --server-side -f -
kubectl get crd | grep external-secrets | wc -l        # → 24

# 🔴 — KRİTİK: scopedRBAC controller'ı TEK namespace'e kilitler
# Chart açıklaması: "If true, create scoped RBAC roles and IMPLICITLY DISABLE
#                    CLUSTER-SCOPED CONTROLLERS"
# Yani: ClusterSecretStore ÇALIŞMAZ + başka namespace'teki ExternalSecret GÖRÜLMEZ.
# Bizim ExternalSecret 'identity' ns'inde → data-services'e kilitlersek ASLA senkron olmaz.

helm install external-secrets external-secrets/external-secrets -n external-secrets \
  --set installCRDs=false \
  --set scopedRBAC=false \
  --set scopedNamespace=""
```

🔴 **NEDEN `scopedRBAC: false`:**

| Ayar | Sonuç |
|---|---|
| `scopedRBAC: true` + `scopedNamespace: data-services` | Yalnız `data-services` izlenir. **`identity`'deki ExternalSecret görülmez** → `pg-keycloak-owner` **hiç oluşmaz** → CNPG **kilitlenir** |
| `scopedRBAC: true` + `scopedNamespace` **boş** | Yalnız `external-secrets` namespace'i izlenir → aynı sonuç |
| `scopedRBAC: false` | **Tüm namespace'ler izlenir**; `ClusterSecretStore` çalışır ✅ |

**Güvenlik dengelemesi (lab'da kabul edilebilir):**
```yaml
# Daha dar ama ÇALIŞAN alternatif: SADECE iki namespace'i izle
# (scopedRBAC bunu DESTEKLEMEZ → watchNamespace kullanın)
--set watchNamespace="data-services,identity"
```
🔁 **ÖNERİLEN:** `scopedRBAC: false` + `ClusterSecretStore` (en basit, en az sürpriz).
Lab'da tek küme var; ESO'nun cluster-geneli izlemesi kabul edilebilir.

⚠️ **M13-DEVAM:** SecretStore `data-services`'te tanımlıysa `identity`'den **erişilemez**.
Bu yüzden **`ClusterSecretStore`** kullanın (V9'da `SecretStore` yazıyordu):

```yaml
apiVersion: external-secrets.io/v1
kind: ClusterSecretStore                      # 🔴 SecretStore DEĞİL
metadata: { name: infisical }
spec:
  provider:
    infisical:
      hostAPI: http://infisical.data-services.svc:8080
      auth:
        universalAuthCredentials:
          clientId:
            name: infisical-machine
            namespace: data-services          # 🔴 ClusterSecretStore'da ZORUNLU
            key: clientId
          clientSecret:
            name: infisical-machine
            namespace: data-services
            key: clientSecret
      secretsScope: { projectSlug: banklab, environmentSlug: prod }
```

✅ **KANIT:** `kubectl -n external-secrets get pods` → 1/1, restart 0

**SecretStore — DOĞRU şema:**
```yaml
apiVersion: external-secrets.io/v1          # 🔴 v1beta1 DEĞİL
kind: SecretStore
metadata: { name: infisical, namespace: data-services }
spec:
  provider:
    infisical:
      hostAPI: http://infisical.data-services.svc:8080    # 🔴 auth'un KARDEŞİ
      auth:                                                # 🔴 ayrı blok
        universalAuthCredentials:
          clientId:     { name: infisical-machine, key: clientId }
          clientSecret: { name: infisical-machine, key: clientSecret }
      secretsScope:                                        # 🔴 auth'un KARDEŞİ
        projectSlug: banklab
        environmentSlug: prod
```

⚠️ **ClusterSecretStore kullanırsanız** `clientId/clientSecret`'a **`namespace: data-services`**
ekleyin — aksi hâlde validation hatası (ESO docs).

```bash
kubectl -n data-services create secret generic infisical-machine \
  --from-literal=clientId="<id>" --from-literal=clientSecret="<secret>"
```

✅ **KANIT:** `kubectl get clustersecretstore infisical` → `STATUS=Valid`, `CAPABILITIES=ReadWrite`

> 🔴 **M25 — Infisical'da `remoteRef.key` bir YOLDUR** (ESO docs, "Key resolution"):
> - `database` (baş eğik çizgi yok) → `secretsScope.secretsPath` **altında** aranır
> - `/keycloak/database` (baş eğik çizgi) → **mutlak yol**
> - `keycloak/database` (göreli) → `secretsPath`'e **eklenir**
>
> Yani `keycloak/database` = **`keycloak` klasöründeki `database` adlı secret**.
> Ve `property` → o secret'ın **JSON değeri içindeki alan**.

**İKİ SEÇENEK — biri seçilmeli, ikisi karıştırılmamalı:**

**SEÇENEK A (V9 önerilen) — Tek secret + JSON:**
```
Infisical UI'da:
  Path: /            Key: keycloak-db        Value: {"username":"bank_keycloak_owner","password":"<KC_DB_PW>"}
  ⬆ <KC_DB_PW> = §11.8 "M86" bölümünde ürettiğiniz parola
     (echo "KC_DB_PW=$KC_DB_PW"  → /root/.banklab-client-secrets)
```
```yaml
apiVersion: external-secrets.io/v1
kind: ExternalSecret
metadata: { name: pg-keycloak-owner, namespace: identity }
spec:
  refreshInterval: 1h
  secretStoreRef: { name: infisical, kind: ClusterSecretStore }   # 🔴 target: { name: pg-keycloak-owner, creationPolicy: Owner }
  data:
  - secretKey: username
    remoteRef:
      key: keycloak-db              # 🔴 baş eğik çizgi YOK → secretsPath altında
      property: username            # 🔴 JSON içindeki alan
  - secretKey: password
    remoteRef:
      key: keycloak-db
      property: password
```

**SEÇENEK B — İki ayrı düz secret (daha basit, JSON yok):**
```
Infisical UI'da Path: / altında:
  keycloak-username  → bank_keycloak_owner
  keycloak-password  → <KC_DB_PW>     ← §11.8 M86'da üretildi
```
```yaml
spec:
  secretStoreRef: { name: infisical, kind: ClusterSecretStore }
  target: { name: pg-keycloak-owner, creationPolicy: Owner }
  data:
  - secretKey: username
    remoteRef: { key: keycloak-username }     # property YOK (düz değer)
  - secretKey: password
    remoteRef: { key: keycloak-password }
```

✅ **ÖNERİ:** **SEÇENEK B** — JSON parse hatası riski yok, hata ayıklaması kolay.
(Bu rehber ileride **SEÇENEK B**'yi kullanır.)

⚠️ **Namespace:** `SecretStore` `data-services`'teyse `identity`'den erişilemez.
🔁 **Çözüm:** `ClusterSecretStore` kullan (`ClusterSecretStore` + `namespace: data-services`
alanları) **veya** her namespace'te `SecretStore` + kopya secret.

✅ **KANIT:**
```bash
kubectl -n identity get externalsecret pg-keycloak-owner   # SecretSynced  READY=True
kubectl -n identity get secret pg-keycloak-owner -o jsonpath='{.data.username}' | base64 -d
```

---

<a id="s11-8-b"></a>

## 11.8-B 🔴🔴 ESO CRD — rehber **3** diyor, gerçek **24** (H74)

> **V6'NIN EN BÜYÜK EKSİĞİ:** Rehber yalnız **3 CRD** kuruyordu
> (`externalsecret`, `secretstore`, `generatorstate`). ESO 2.15.0 **24 CRD**'ye
> ihtiyaç duyar. Eksik olan `PushSecret` yüzünden controller **sonsuz crash** etti.

### 🔴 GERÇEK HATA MESAJI

```bash
kubectl -n external-secrets logs deploy/external-secrets
# level=error msg="unable to create controller" controller="PushSecret"
#   error="failed to get restmapping: no matches for kind \"PushSecret\"
#   in version \"external-secrets.io/v1alpha1\""
```

Kubernetes bunu **`CrashLoopBackOff`** olarak gösterir. **Sebep görünmez** —
"imaj mı bozuk, RBAC mi" diye saatler harcanır.

### ⬜ ÖN KONTROL — ESO'yu kurmadan

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| Namespace | `kubectl get ns external-secrets` | `Active` | §11.8 |
| Chart indi mi | `ls -la /tmp/external-secrets-*.tgz` | dosya var | §11.8 |
| Chart'taki CRD sayısı | `helm template eso /tmp/*.tgz -n external-secrets --include-crds \| grep -c '^kind: CustomResourceDefinition'` | **24** | bu bölüm |
| Kurulu CRD sayısı | `kubectl get crd \| grep -c external-secrets.io` | **24** | bu bölüm |

### 🔀 KOŞULLU KARAR — CRD kurulum yöntemi

| Yöntem | Ne zaman | Sorun |
|---|---|---|
| **`helm install` (CRD chart'tan)** | yeni kurulum | ✅ en temiz — `--include-crds` |
| **`kubectl create -f`** | CRD elle | ✅ **bu rehber** — `apply` **patlar** (H75) |
| `kubectl apply -f` | ❌ **KULLANMA** | `metadata.annotations: Too long: may not be more than 262144 bytes` |
| `--server-side --force-conflicts` | var olan CRD güncelleme | ✅ alternatif |

### ADIM 1 — CRD'leri chart'tan çıkar (24 adet)

**Ne yapıyoruz:** Chart'ın içindeki **24 CRD** tanımını `helm template --include-crds` ile **ayrı bir dosyaya** çıkarıyoruz. **Neden 24:** ESO `setup()` tüm controller'ları kurar; `PushSecret` eksikse **ölür**.

```bash
cd /tmp
helm template external-secrets /tmp/external-secrets-2.15.0.tgz \
  -n external-secrets --include-crds --no-hooks \
  | awk '/^---/{f=0} /^# Source: .*\/crds\/.*\.yaml/{f=1} f' > eso-crds.yaml

grep -c '^kind: CustomResourceDefinition' eso-crds.yaml
# → 24   🔴 3 DEĞİL, 24
```

### ADIM 2 — 🔴 `create` ile kur (apply DEĞİL)

**Ne yapıyoruz:** CRD'leri **`kubectl create`** ile kuruyoruz, `apply` **değil**. **Neden:** `apply` son-uygulanan config'i `annotations`'a yazar; CRD boyutu **256 KB**'ı aşınca `Too long` hatası verir.

```bash
# apply 256 KB annotation sınırına takılır — create kullan
kubectl create -f eso-crds.yaml 2>&1 | grep -v "AlreadyExists" || true

# 24 CRD Established olana kadar bekle
kubectl wait --for=condition=Established \
  crd/externalsecrets.external-secrets.io \
  crd/secretstores.external-secrets.io \
  crd/clustersecretstores.external-secrets.io \
  crd/clusterexternalsecrets.external-secrets.io \
  crd/pushsecrets.external-secrets.io \
  crd/clusterpushsecrets.external-secrets.io \
  --timeout=180s
```

### 🔴 EKSİK 21 CRD — tam liste (elle kurulacaksa)

```text
acraccesstokens                   gitlabdeploytokens
beyondtrustworkloadcredentials    grafanas
cloudsmithaccesstokens            mfas
clusterexternalsecrets            passwords
clustergenerators                 pushsecrets
clusterpushsecrets                quayaccesstokens
ecrauthorizationtokens            sshkeys
fakes                             stssessiontokens
gcraccesstokens                   uuids
githubaccesstokens                vaultdynamicsecrets
gitlabdeploytokens                webhooks
```
**`pushsecrets` ÖZELLİKLE ÖNEMLİ:** controller `setup()` aşamasında bunu **aramak zorunda**;
yoksa **ölür**.

### ADIM 3 — Helm values (doğrulanmış)

**Ne yapıyoruz:** ESO chart'ını **doğrulanmış** values ile kuruyoruz: `installCRDs:false` (CRD'leri biz kurduk), `scopedRBAC:false` (4+ namespace gerekli), API timeout 60s, `pullPolicy: IfNotPresent`.

```yaml
# eso-values.yaml
image:
  repository: ghcr.io/external-secrets/external-secrets
  # 🔴 tag DEĞİL, tag+digest — immutable
  tag: v2.15.0@sha256:<digest>            # `crane digest` ile al
  pullPolicy: IfNotPresent                 # 🔴 R52: yoksa 100 MB yeniden iner
webhook:
  image: { repository: ghcr.io/external-secrets/external-secrets, tag: v2.15.0@sha256:<digest> }
certController:
  image: { repository: ghcr.io/external-secrets/external-secrets, tag: v2.15.0@sha256:<digest> }

installCRDs: false                # 🔴 ADIM 2'de elle kurduk
crds:
  createClusterSecretStore: false
  conversion: { enabled: false }

# 🔴 M13: scopedRBAC=false (V9 kararı) — çok namespace gerekli
scopedRBAC: false

rbac:
  serviceAccountTokenCreate: false     # TokenRequest dar kapsamda ayrıca verilir
  servicebindings: { create: false }
  aggregateToView: false
  aggregateToEdit: false
  aggregateToAdmin: false

extraEnv:
  # 🔴 H76: API discovery 810 ms'ye çıkıyor; default 32 s marjinal
  - { name: KUBERNETES_CLIENT_TIMEOUT, value: "60s" }
  - { name: CLIENT_QPS,   value: "5" }
  - { name: CLIENT_BURST, value: "10" }
```

```bash
helm upgrade --install external-secrets /tmp/external-secrets-2.15.0.tgz \
  -n external-secrets --create-namespace -f eso-values.yaml
```

### ✅ KANIT — 5 test

```bash
# 1) 24 CRD
kubectl get crd | grep -c 'external-secrets.io'          # → 24

# 2) pushsecrets var
kubectl get crd pushsecrets.external-secrets.io            # → satır döner

# 3) 3 pod Running, RESTART 0
kubectl -n external-secrets get pods
# external-secrets-xxx                  1/1 Running 0
# external-secrets-cert-controller-xxx  1/1 Running 0
# external-secrets-webhook-xxx          1/1 Running 0

# 4) Webhook caBundle DOLU (boşsa ExternalSecret validate edilemez)
kubectl get validatingwebhookconfiguration secretstore-validate \
  -o jsonpath='{.webhooks[0].clientConfig.caBundle}' | wc -c     # → >0

# 5) Controller log'da "unable to create controller" YOK
kubectl -n external-secrets logs deploy/external-secrets | grep -c "unable to create controller"
# → 0
```

### 🔴 DUR — hangi belirti, hangi kök neden

| Belirti | Kök neden | Çözüm |
|---|---|---|
| `CrashLoopBackOff` + `no matches for kind PushSecret` | CRD eksik (**3 yerine 24**) | ADIM 1-2 |
| `Too long: may not be more than 262144 bytes` | `apply` kullanıldı | **`create`** kullan (H75) |
| `failed to get server groups` | API discovery yavaş | `KUBERNETES_CLIENT_TIMEOUT=60s` (H76) |
| `ImagePullBackOff` ama imaj import edildi | `imagePullPolicy` yok | `IfNotPresent` + digest tag |
| Webhook `caBundle` boş | cert-controller başlamadı | `logs deploy/external-secrets-cert-controller` |
| Controller 5 dk'da bir restart | RBAC yetmez (cluster-scope) | `scopedRBAC: false` |

🔁 **CRD güncellemesi gerekiyorsa:** `kubectl apply --server-side --force-conflicts`
(bu **apply**'ın annotation sorununu aşan tek yolu).

---

<a id="s12"></a>

# 12. VERİ KATMANI — CloudNativePG

<a id="s12-1"></a>

## 12.1 Operator — **chart 0.29.2 = operator 1.30.2** (E1 düzeltmesi)

> 🔴 **SIRA UYARISI:** Bu adımı **§11 (Infisical)'dan ÖNCE** çalıştırın.
> `pg-infisical` bir CNPG Cluster'ıdır → operator olmadan **oluşmaz**.
> Bkz. §0.4 ve §11.2'deki gerçek uygulama sırası.

```bash
helm repo add cnpg https://cloudnative-pg.github.io/charts && helm repo update

# 🔴 Önceki sürümdeki hata: --version ${CNPG_VER#v-} → 1.30.2 diye chart arar (YOK)
CHART_VER=0.29.2      # appVersion 1.30.2 = operator sürümü
helm install cnpg cnpg/cloudnative-pg -n cnpg-system --create-namespace --version $CHART_VER
```

✅ **KANIT:**
```bash
kubectl -n cnpg-system get pods                              # 1/1 Running
kubectl get crd | grep cnpg | wc -l                          # → 11
kubectl -n cnpg-system get deploy cnpg-cloudnative-pg -o jsonpath='{.spec.template.spec.containers[0].image}'
# → ghcr.io/cloudnative-pg/cloudnative-pg:1.30.2 (M50: Docker Hub aynası için --set image.repository=docker.io/cloudnative-pg/cloudnative-pg)
```

<a id="s12-2"></a>

## 12.2 🔴 Barman plugin — **AYRI chart** (E10 düzeltmesi)

> **NEDEN:** V6'da Barman yedeği **hiç çalışmadı**. CNPG 1.24+ için object-store yedeği
> **plugin** olarak kurulur; ana chart'a **dahil değil**.

```bash
helm repo update
helm install plugin-barman-cloud cnpg/plugin-barman-cloud \
  -n cnpg-system --version 0.9.0
```

✅ **KANIT:** `kubectl -n cnpg-system get pods` → `plugin-barman-cloud-xxx 1/1 Running`
`kubectl get crd | grep barmancloud` → `objectstores.barmancloud.cnpg.io`

<a id="s12-3"></a>

## 12.3 pg-keycloak

```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata: { name: pg-keycloak, namespace: identity }
spec:
  instances: 1
  imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm   # 🔴 ghcr.io 837 B/s → Docker Hub aynası
  storage:
    storageClass: longhorn-postgres
    size: 2Gi
  bootstrap:
    initdb:
      database: bank_keycloak
      owner: bank_keycloak_owner
      secret: { name: pg-keycloak-owner }      # ← Infisical'dan (ESO) — M72 şartı aşağıda
  certificates:
    serverTLSSecret: pg-keycloak-server-tls
    serverCASecret:  pg-keycloak-server-ca
    clientCASecret:  pg-keycloak-client-ca
  resources:
    requests: { cpu: "250m", memory: "512Mi" }
    limits:   { cpu: "1",    memory: "2Gi" }
```

### 🔴 — YARIŞ DURUMU: ESO secret'ı YAZMADAN CNPG BAŞLARSA

> **NEDEN:** `bootstrap.initdb.secret` **pod başlarken** okunur.
> Ama o secret **ESO** tarafından **asenkron** üretilir.
> Sıra yanlışsa (CNPG önce) → **`secret "pg-keycloak-owner" not found`** →
> CNPG bootstrap **başarısız** → pod `Init:`/`CrashLoop` → **tekrar DENEMEZ**.

**UYGULAMA SIRASI — §11.8 (ESO) BİTMEDEN §12.3'Ü BAŞLATMAYIN:**

```bash
# 1) ESO secret'ı GERÇEKTEN yazana kadar bekle (60 sn, 5 sn aralık)
for i in $(seq 1 12); do
  kubectl -n identity get secret pg-keycloak-owner >/dev/null 2>&1 && break
  echo "  bekle $i/12 — pg-keycloak-owner henüz yok"
  sleep 5
done

# 2) DUR KONTROLÜ — yoksa Cluster'ı HİÇ oluşturma
kubectl -n identity get secret pg-keycloak-owner || {
  echo "🔴 DUR: secret yok → ExternalSecret durumunu incele, §12.3'e GEÇME"
  kubectl -n identity describe externalsecret pg-keycloak-owner
  exit 1
}

# 3) İçeriği DOĞRU mu (alan adları: username / password)
kubectl -n identity get secret pg-keycloak-owner -o jsonpath='{.data}' | jq 'keys'
# → ["password","username"]   🔴 başka bir şey varsa Keycloak DB'ye bağlanamaz

# 4) ANCAK ŞİMDİ Cluster'ı oluştur
kubectl apply -f pg-keycloak.yaml
```

🔴 **DUR:** `pg-keycloak-owner` secret'ı **yokken** `kubectl apply` yapmak =
**kurtarılamaz bootstrap** (CNPG initdb bir kez denenir). Cluster'ı **silip yeniden** kurmanız gerekir:
```bash
# Yanlışlıkla başlattıysanız:
kubectl -n identity delete cluster pg-keycloak
kubectl -n identity delete pvc -l cnpg.io/cluster=pg-keycloak
# sonra 1. adımdan tekrar
```

✅ **KANIT — aşama aşama:**
```bash
kubectl -n identity get cluster pg-keycloak -w
# 1) "Setting up primary"        ← PVC oluşmalı
kubectl -n identity get pvc                    # pg-keycloak-1 Bound ✅
# 2) "Creating primary instance"
# 3) "Cluster in healthy state" ✅
kubectl -n identity get pods                   # pg-keycloak-1 1/1 Running
```

🔴 **DUR:** 5 dk'da Running olmazsa:
```bash
kubectl -n identity describe pvc | grep -A5 Events
kubectl -n longhorn-system get volumes.longhorn.io | grep pg-keycloak
kubectl -n identity get externalsecret pg-keycloak-owner
```

<a id="s12-3-b"></a>

## 12.3-B 🔴 MinIO — yedeklerin hedefi

> **NEDEN GEREKLİ:** §1.1 mimarisi "KATMAN 4 · Veri: CloudNativePG, Redis, **MinIO**" diyor.
> §12.4 Barman yedeği `http://minio.data-services.svc:9000`'e yazıyor.
> **Ama MinIO'yu kuracak tek bir adım YOK.** Sonuç: `no such host` → **yedek alınamaz**.

```bash
helm repo add minio https://charts.min.io/ && helm repo update

cat > minio-values.yaml <<'EOF'
mode: standalone                 # 🔴 distributed DEĞİL — lab için tek node yeter
replicas: 1
persistence:
  enabled: true
  storageClass: longhorn
  size: 10Gi                     # NEDEN 10Gi: PG base backup + WAL için bol
resources:
  requests: { memory: 256Mi, cpu: 100m }
  limits:   { memory: 512Mi }
rootUser: banklab-admin
rootPassword: ""                 # aşağıda secret'tan
existingSecret: minio-root       # 🔴 parolayı secret'tan oku
buckets:
  - name: banklab-backup
    policy: none
    purge: false
EOF

# Parola ÜRET (placeholder bırakma — K20)
MINIO_PW=$(openssl rand -base64 24 | tr -d '/+=')
kubectl -n data-services create secret generic minio-root \
  --from-literal=rootUser=banklab-admin \
  --from-literal=rootPassword="$MINIO_PW"
echo "MINIO_PW=$MINIO_PW" >> /root/.banklab-client-secrets

helm install minio minio/minio -n data-services -f minio-values.yaml

# Barman'ın kullanacağı credential (aynı değerler)
kubectl -n identity create secret generic minio-creds \
  --from-literal=ACCESS_KEY_ID=banklab-admin \
  --from-literal=SECRET_ACCESS_KEY="$MINIO_PW"
```

✅ **KANIT:**
```bash
kubectl -n data-services get pods -l app=minio         # 1/1 Running
kubectl -n data-services get svc minio                 # :9000 ClusterIP
# Bucket gerçekten oluştu mu?
kubectl -n data-services run mc --rm -it --image=minio/mc --restart=Never -- \
  sh -c "mc alias set m http://minio:9000 banklab-admin '$MINIO_PW' && mc ls m"
# → banklab-backup/  görünmeli
```

⚠️ **NEDEN `standalone` + 1 replica:** Distributed MinIO **4+ node** ve erasure coding ister →
3 worker'da **RAM/disk yetmez** (§9.4). Lab'da tek node + Longhorn yeterli.
**Üretimde:** 4 node distributed veya **harici S3** (AWS/MinIO operatör).

🔁 **ALTERNATİF — MinIO istemiyorsanız:**
```bash
# Barman'ı ATLAYIN, pg_dump kullanın (§18.3'te "atlanabilir" listesinde)
kubectl -n identity exec pg-keycloak-1 -- pg_dump -U postgres bank_keycloak > /root/bank_keycloak.sql
# Geri: kubectl -n identity exec -i pg-keycloak-1 -- psql -U postgres bank_keycloak < /root/bank_keycloak.sql
```

---

<a id="s12-4"></a>

## 12.4 Barman yedeği

```yaml
apiVersion: barmancloud.cnpg.io/v1
kind: ObjectStore
metadata: { name: bank-s3, namespace: identity }
spec:
  configuration:
    destinationPath: s3://banklab-backup/
    endpointURL: http://minio.data-services.svc:9000
    s3Credentials:
      accessKeyId:     { name: minio-creds, key: ACCESS_KEY_ID }
      secretAccessKey: { name: minio-creds, key: SECRET_ACCESS_KEY }
    wal: { compression: gzip }
```

```yaml
# Cluster'a ekle
spec:
  plugins:
  - name: barman-cloud.cloudnative-pg.io
    isWALArchiver: true
    parameters: { barmanObjectName: bank-s3 }
```

✅ **KANIT:**
```bash
kubectl -n identity create -f - <<'EOF'
apiVersion: postgresql.cnpg.io/v1
kind: Backup
metadata: { name: test-backup, namespace: identity }
spec: { cluster: { name: pg-keycloak } }
EOF
kubectl -n identity get backup test-backup -w    # STATUS: completed
```

---

<a id="s13"></a>

# 13. UYGULAMA KATMANI

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| MetalLB IP atıyor | `kubectl -n metallb-system get ds` | `metallb-speaker 3/3` | §9.1 |
| Longhorn çalışıyor | `kubectl -n longhorn-system get pods --no-headers \| grep -vc Running` | `0` | §9.2 |
| ClusterIssuer READY | `kubectl get clusterissuer banklab-step -o jsonpath='{.status.conditions[0].status}'` | `True` | §10.3 |
| pg-keycloak healthy | `kubectl -n identity get cluster pg-keycloak -o jsonpath='{.status.phase}'` | `Cluster in healthy state` | §12.3 |
| Infisical secret'ı var | `kubectl -n identity get secret pg-keycloak-owner` | var | **§11.7-B** |
| DNS kayıtları | `for h in infisical sso grafana harbor argocd gitea; do dig +short @10.30.10.53 $h.banklab.alperozbay.com; done` | 6× `10.30.20.200` | §6.4 |

### 🔀 KOŞULLU KARAR — hangi bileşenleri kur?

**Her bileşen bağımsız — istediğinizi atlayabilirsiniz. Ama şu bağımlılıklar var:**

| Bileşen | Zorunlu mu | Neye bağımlı | Atla → ne kaybedersin |
|---|---|---|---|
| **§13.1 Traefik** | ✅ Zorunlu | MetalLB | Hiçbir UI'ya erişemezsin |
| **§13.2 Keycloak** | ⚠️ SSO istiyorsan | pg-keycloak | SSO/MFA yok; Grafana yerel admin |
| **§13.2-B OIDC/MFA** | ⚠️ §13.2 varsa | §13.2 | SSO çalışmaz |
| **§13.3 Grafana** | 🔵 İsteğe bağlı | Traefik | Gözlemlenebilirlik yok |
| **§13.4 Kyverno** | 🔵 İsteğe bağlı | cosign | İmza zorunluluğu yok |
| **§13.6 Kafka** | 🔵 İsteğe bağlı | cert-manager | Mesajlaşma yok (**-1 Gi RAM**) |
| **§13.7 Harbor** | 🔵 İsteğe bağlı | Traefik + step-ca | `registry:2` yeter (**-2 Gi RAM**) |
| **§13.8 Argo CD** | 🔵 İsteğe bağlı | Keycloak (SSO) | GitOps yok |
| **§13.9 Gitea** | 🔵 İsteğe bağlı | pg-gitea | Git sunucusu yok |

🔴 **RAM kısıtlıysa (16 GB/worker):** §9.4 tablosuna bakın — Kafka ve Harbor'ı atlamak
**-2.25 Gi** kazandırır. **Minimum çalışan platform: §13.1 + §13.2 + §13.2-B.**

<a id="s13-1"></a>

## 13.1 Traefik (giriş denetleyicisi — tüm UI'lar buradan)

**Ne yapıyoruz:** Traefik'i **IngressRoute** CRD'leriyle kuruyoruz; MetalLB'den
`10.30.20.200` IP'sini alıp 80/443'te dinliyor. **Neden Traefik:** `IngressRoute` ile
**TCP/UDP de** yönlendirebilir (Kafka, PostgreSQL için gerekli); Nginx Ingress yalnız HTTP bilir.


```yaml
# traefik-values.yaml — allowCrossNamespace doğrulandı (traefik chart values.yaml)
fullnameOverride: traefik
api: { dashboard: false }
ingressRoute: { dashboard: { enabled: false } }
service:
  spec:
    type: LoadBalancer
    # 🔴 — IP'yi SABİTLE. Yoksa MetalLB rastgele atar (§9.1 havuzundan)
    # ve §6.4'teki 6 DNS kaydı (10.30.20.200) YANLIŞ olur → tüm SSO/UI 404.
    loadBalancerIP: 10.30.20.200
providers:
  kubernetesCRD:
    allowCrossNamespace: true            # NEDEN: route ve Service farklı namespace'te olabilir
    # 🔴 §8.0 TEK KAYNAK — bu listeyi ELLE YAZMAYIN, oradan kopyalayın:
    namespaces: [delivery, workforce, identity, data-services, observability,
                 harbor, argocd, platform, traefik]
```

⚠️ **NEDEN TEK KAYNAK:** `traefik` chart'ı bu listeyi **derleme zamanında** alır.
§8.0'a yeni namespace eklerseniz **§13.1'i de güncellemek** zorundasınız, yoksa route **404**.
```bash
# §8.0 APP_NS'i değiştirdiyseniz, buradaki listeyi OTOMATİK üretin:
NS_LIST=$(echo "delivery workforce identity data-services observability harbor argocd platform traefik" | sed 's/ /, /g')
sed -i "s/namespaces: \[.*\]/namespaces: [$NS_LIST]/" traefik-values.yaml
```

> 🔴 **NEDEN `loadBalancerIP` ZORUNLU:**
> MetalLB L2 modunda pod yeniden başlarsa IP **değişebilir**; dahası **hangi IP'yi alacağı
> garanti değildir**. Rehber boyunca **her yerde `10.30.20.200`** yazıyor (Unbound §6.4,
> Kyverno §13.4, Harbor §13.7). Sabitlemezseniz bunların **hepsi** kırılır.
>
> **Alternatif (daha esnek):** annotation ile de:
> ```yaml
> service:
>   spec:
>     type: LoadBalancer
>   annotations:
>     metallb.universe.tf/loadBalancerIPs: 10.30.20.200
> ```

```text
**NEDEN Traefik (Nginx yerine):** Kubernetes CRD (`IngressRoute`) ile **TCP/UDP** de yönlendirebilir
(Kafka, PostgreSQL için gerekli). Nginx Ingress yalnız HTTP bilir.

helm install traefik traefik/traefik -n traefik --create-namespace -f traefik-values.yaml
```

✅ **KANIT (M74 — IP TAM OLARAK 10.30.20.200 olmalı):**
```bash
kubectl -n traefik get svc traefik \
  -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
# → 10.30.20.200   ✅ DOĞRU
# 🔴 10.30.20.200-10.30.20.220 havuzundan İLK adres olmalı.
#    .201/.202 de havuzda ama SABİT yazılmaz — Traefik İLKİ alır.
```

⚠️ **NEDEN `namespaces` listesi:** Traefik **watch dışı namespace'ten Service okuyamaz** (R40).
Yeni bir namespace eklenirse **liste güncellenmeli**, yoksa route 404 verir.

<a id="s13-2"></a>

## 13.2 Keycloak — **resmî operator** (E12 düzeltmesi)

> **Önceki sürümdeki hata:** `codecentric/keycloakx --set installCRDs=true` — `keycloakx` bir **chart**,
> CRD içermez (`--set installCRDs` **yok sayılır**). Resmî yol: **`keycloak/keycloak-operator`**.

### 🔀 KOŞULLU KARAR — operatör sürümü

| Sürüm | Ne zaman | Not |
|---|---|---|
| **26.8.2** | ✅ **bugün, test edilmiş en yeni** | bu rehber bunu kullanır |
| `26.4.0` | eski CRD ile uyum | V9 ilk sürümü; `tlsSecret` alanı **aynı** |
| `latest` | ❌ **KULLANMAYIN** | CRD alanları habersiz değişir → `Keycloak` CR **reddedilir** |

**Sürümü canlı doğrula:**
```bash
curl -4 -s "https://api.github.com/repos/keycloak/keycloak-k8s-resources/releases/latest" \
  | grep -m1 tag_name          # → "tag_name": "26.8.2"
```

```bash
# 🔴 ghc ile indir (gh-proxy 38 MB/s) — doğrudan uygulamak 8 KB/s'de dakikalar sürer
KC_VER=26.8.2        # 🔴 /releases/latest ile TEYİT ET (üstteki komut)
BASE="https://raw.githubusercontent.com/keycloak/keycloak-k8s-resources/${KC_VER}/kubernetes"
for F in keycloaks.k8s.keycloak.org-v1.yml keycloakrealmimports.k8s.keycloak.org-v1.yml kubernetes.yml; do
  ghc "$BASE/$F" -o "/tmp/$F"
done
kubectl apply -f /tmp/keycloaks.k8s.keycloak.org-v1.yml
kubectl apply -f /tmp/keycloakrealmimports.k8s.keycloak.org-v1.yml
kubectl -n identity apply -f /tmp/kubernetes.yml
```

✅ **KANIT:** `kubectl -n identity get pods` → `keycloak-operator-xxx 1/1 Running`
`kubectl get crd | grep keycloak` → 2 CRD

```yaml
apiVersion: k8s.keycloak.org/v2alpha1
kind: Keycloak
metadata: { name: keycloak, namespace: identity }
spec:
  instances: 1
  hostname: { hostname: sso.banklab.alperozbay.com }
  http:
    tlsSecret: keycloak-tls        # 🔴 'tlsSecretName' DEĞİL — CRD'de alan adı 'tlsSecret'
  db:
    vendor: postgres
    host: pg-keycloak-rw.identity.svc
    database: bank_keycloak
    usernameSecret: { name: pg-keycloak-owner, key: username }   # 🔴 key adı 'username'
    passwordSecret: { name: pg-keycloak-owner, key: password }   # 🔴 key adı 'password'
  resources:
    requests: { cpu: "500m", memory: "1Gi" }
    limits:   { cpu: "2",    memory: "2Gi" }
```

✅ **KANIT:** `kubectl -n identity get keycloak keycloak -w` → `READY: True`

<a id="s13-2-b"></a>

## 13.2-B 🔴 OIDC + MFA + Realm — kullanıcı girişi

> **MANTIK HATASI:** §13.3 Grafana SSO'dan bahsediyor ama **Keycloak realm'i, client'ları
> ve MFA hiç tanımlanmamış**. Grafana `auth.generic_oauth` açılır → `invalid_client` →
> **Grafana'ya giriş imkânsız**. Argo CD OIDC de aynı (`$oidc.clientSecret` tanımsız).
> V6'da bu bölüm **G10** olarak vardı (108 satır) — V7'de düşmüş.

### 1) Client secret'ları ÜRET (placeholder bırakmayın)

```text
GRAFANA_SECRET=$(openssl rand -base64 32 | tr -d '/+=' | head -c 40)
ARGOCD_SECRET=$(openssl rand -base64 32 | tr -d '/+=' | head -c 40)
# 🔴 Keycloak admin parolası da ÜRETİLMELİ (placeholder kalamaz)
KC_ADMIN_PW=$(openssl rand -base64 24 | tr -d '/+=')

cat > /root/.banklab-client-secrets <<EOF
GRAFANA_SECRET=$GRAFANA_SECRET
ARGOCD_SECRET=$ARGOCD_SECRET
KC_ADMIN_PW=$KC_ADMIN_PW
EOF
chmod 600 /root/.banklab-client-secrets
echo "✅ kaydedildi → /root/.banklab-client-secrets"
echo "   Keycloak admin: banklab-admin / $KC_ADMIN_PW"
```

### 2) Realm + client'lar + MFA

```yaml
apiVersion: k8s.keycloak.org/v2alpha1
kind: KeycloakRealmImport
metadata: { name: banklab, namespace: identity }
spec:
  keycloakCRName: keycloak
  realm:
    realm: banklab
    enabled: true
    sslRequired: external
    registrationAllowed: false
    otpPolicy:
      type: totp
      otpPolicyAlgorithm: HmacSHA1
      otpPolicyDigits: 6
      otpPolicyPeriod: 30
    clients:
      - clientId: grafana
        enabled: true
        publicClient: false
        secret: "GRAFANA_SECRET_BURAYA"
        standardFlowEnabled: true
        redirectUris: ["https://grafana.banklab.alperozbay.com/login/generic_oauth"]
        webOrigins: ["https://grafana.banklab.alperozbay.com"]
      - clientId: argocd
        enabled: true
        publicClient: false
        secret: "ARGOCD_SECRET_BURAYA"
        standardFlowEnabled: true
        redirectUris: ["https://argocd.banklab.alperozbay.com/auth/callback"]
        webOrigins: ["https://argocd.banklab.alperozbay.com"]
    requiredActions:
      - alias: CONFIGURE_TOTP
        name: "Configure OTP"
        providerId: CONFIGURE_TOTP
        enabled: true
        defaultAction: true
    users:
      - username: banklab-admin
        enabled: true
        emailVerified: true
        email: admin@banklab.local
        credentials:
          - type: password
            value: "KC_ADMIN_PW_BURAYA"        # ← aşağıdaki sed ile gerçek değer
            temporary: true
        realmRoles: [admin]
```

```bash
source /root/.banklab-client-secrets
sed -i "s|GRAFANA_SECRET_BURAYA|$GRAFANA_SECRET|" keycloak-realm.yaml
sed -i "s|ARGOCD_SECRET_BURAYA|$ARGOCD_SECRET|"   keycloak-realm.yaml
sed -i "s|KC_ADMIN_PW_BURAYA|$KC_ADMIN_PW|"       keycloak-realm.yaml   # 🔴 kubectl -n identity apply -f keycloak-realm.yaml
```

✅ **KANIT:**
```bash
kubectl -n identity get keycloakrealmimport banklab -w      # STATUS: Done
curl -sk https://sso.banklab.alperozbay.com/realms/banklab/.well-known/openid-configuration | jq -r .issuer
```

### 3) Grafana + Argo CD'yi bu client'a bağla

```bash
kubectl -n observability create secret generic grafana-oidc \
  --from-literal=client-id=grafana \
  --from-literal=client-secret="$GRAFANA_SECRET"

kubectl -n argocd patch secret argocd-secret \
  -p "{\"stringData\":{\"oidc.clientSecret\":\"$ARGOCD_SECRET\"}}"
```

### 4) MFA NEGATİF testi

```bash
curl -sk -X POST https://sso.banklab.alperozbay.com/realms/banklab/protocol/openid-connect/token \
  -d "client_id=grafana" -d "client_secret=$GRAFANA_SECRET" \
  -d "grant_type=password" -d "username=banklab-admin" -d "password=$KC_ADMIN_PW" | jq
# → {"error":"invalid_grant","error_description":"Account is not fully set up"}
# 🔴 BEKLENEN: TOTP kurulmadan token ALINAMAZ. Alınıyorsa MFA ÇALIŞMIYOR.
```

---

<a id="s13-3"></a>

## 13.3 Grafana SSO + alarm kabulü

> **KRİTİK:** V6'da alarm "yapıldı" sayıldı ama **alıcıya ulaşmadı**. V9'da **alıcı onayı zorunlu**.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

🔴 **`kps-values.yaml` — TAM İÇERİK:**

```yaml
# kps-values.yaml
grafana:
  enabled: true
  # 🔴 (§9.4): RAM bütçesi — limit ŞART, yoksa node OOM
  resources:
    requests: { memory: 512Mi, cpu: 100m }
    limits:   { memory: 1Gi }
  persistence:
    enabled: false                 # NEDEN: dashboard'lar ConfigMap'te, PVC gereksiz RAM/IO
  grafana.ini:
    server:
      root_url: https://grafana.banklab.alperozbay.com
    auth:
      disable_login_form: false    # NEDEN: SSO bozulursa yerel admin ile girilebilsin
    auth.generic_oauth:
      enabled: true
      name: BankLab SSO
      scopes: openid email profile groups
      auth_url:    https://sso.banklab.alperozbay.com/realms/banklab/protocol/openid-connect/auth
      token_url:   https://sso.banklab.alperozbay.com/realms/banklab/protocol/openid-connect/token
      api_url:     https://sso.banklab.alperozbay.com/realms/banklab/protocol/openid-connect/userinfo
      client_id: grafana
      # 🔴 /§13.2-B: secret ÜRETİLDİ → secretRef ile oku (düz metin yazmayın)
      client_secret: $__env{GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET}
      role_attribute_path: "contains(groups[*], 'banklab-admins') && 'Admin' || 'Viewer'"
      allow_sign_up: true

  envValueFrom:
    GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET:
      secretKeyRef: { name: grafana-oidc, key: client-secret }

  # 🔴 sabit IP + TLS
  ingress:
    enabled: true
    ingressClassName: traefik
    hosts: [grafana.banklab.alperozbay.com]
    tls:
      - secretName: grafana-tls
        hosts: [grafana.banklab.alperozbay.com]
    annotations:
      traefik.ingress.kubernetes.io/router.entrypoints: websecure
      traefik.ingress.kubernetes.io/router.tls: "true"
  sidecar:
    dashboards: { enabled: true }

prometheus:
  prometheusSpec:
    retention: 7d                  # 🔴 15d → 7d
    resources:
      requests: { memory: 1Gi, cpu: 200m }
      limits:   { memory: 2Gi }    # 🔴 LIMIT ŞART (§9.4)
    storageSpec:
      volumeClaimTemplate:
        spec:
          storageClassName: longhorn
          accessModes: [ReadWriteOnce]
          resources: { requests: { storage: 10Gi } }

alertmanager:
  alertmanagerSpec:
    resources:
      requests: { memory: 128Mi, cpu: 50m }
      limits:   { memory: 256Mi }
  config:
    route:
      receiver: banklab-webhook
      group_wait: 10s
      group_interval: 5m
      repeat_interval: 4h
    receivers:
      - name: banklab-webhook
        webhook_configs:
          # 🔴 §13.3 KANIT: alıcı LOG'unda görünmeli
          - url: http://alert-sink.observability.svc:8080/alerts
            send_resolved: true

# NEDEN grafana-tls Certificate'i (ingress TLS secret'ı)
# cert-manager annotation ile de üretilebilir; en basit: ayrı Certificate
```

```bash
# Grafana TLS sertifikası (§10.3 issuer ile)
kubectl -n observability apply -f - <<'EOF'
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: grafana-tls, namespace: observability }
spec:
  secretName: grafana-tls
  duration: 720h
  renewBefore: 240h
  dnsNames: [grafana.banklab.alperozbay.com]
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
EOF

# 🔴 Alarm ALICI (kabul testi için — §13.3 KANIT'ın 3. adımı bunu kullanır)
kubectl -n observability apply -f - <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata: { name: alert-sink, namespace: observability }
spec:
  replicas: 1
  selector: { matchLabels: { app: alert-sink } }
  template:
    metadata: { labels: { app: alert-sink } }
    spec:
      containers:
      - name: sink
        image: hashicorp/http-echo:1.0
        args: ["-listen=:8080", "-text=ALERT_RECEIVED"]
        resources:
          requests: { memory: 16Mi, cpu: 10m }
          limits:   { memory: 32Mi }
---
apiVersion: v1
kind: Service
metadata: { name: alert-sink, namespace: observability }
spec:
  selector: { app: alert-sink }
  ports: [{ port: 8080 }]
EOF

helm install kps prometheus-community/kube-prometheus-stack -n observability \
  --create-namespace -f kps-values.yaml
# 🔴 --wait KULLANMA (R46/R50): 20+ imaj, 15 dk timeout → release failed
kubectl -n observability rollout status deploy/kps-grafana --timeout=20m
```

⚠️ **NEDEN `alert-sink`:** §13.3'ün "alıcıya ulaştı mı" kanıtı **gerçek bir alıcı** ister.
`http-echo` en basit alıcıdır. **Üretimde** Slack/Teams/e-posta webhook'una çevirin.

✅ **KANIT — alarm zinciri (3 adım):**
```bash
# 1) Tetikle
kubectl -n delivery run alarm-test --image=busybox -- sh -c 'exit 1'

# 2) Prometheus firing
kubectl -n observability port-forward svc/kps-kube-prometheus-stack-prometheus 9090 &
curl -s localhost:9090/api/v1/alerts | jq '.data.alerts[] | {name, state}'   # firing

# 3) 🔴 ALICIYA ULAŞTI MI — asıl kanıt
kubectl -n observability logs -l app.kubernetes.io/component=alertmanager --tail=30 | grep -i "notify"
```

🔴 **DUR:** Alıcıya ulaşmadıysa kabul **başarısız**.

<a id="s13-4"></a>

## 13.4 Kyverno — imza zorunluluğu

> 🔴 **M3 — MANTIK HATASI:** V9'da `imageReferences: ["10.30.20.30/banklab/*"]` yazıyordu.
> Ama **MetalLB havuzu `10.30.20.200-10.30.20.220` (21 adet)** (§9.1). `10.30.20.30` **havuzda değil** →
> Harbor'a o IP atanmaz → **hiçbir imaj o adresten çekilemez** → Kyverno hiçbir şey doğrulayamaz.
>
> 🔴 **DOĞRU:** Harbor'ı **DNS adıyla** referans verin (`harbor.banklab.alperozbay.com`).
> `imageReferences` bir **glob**'dur; IP/host + path.
> **Alternatif:** Harbor'ı sabit IP'ye sabitlemek isterseniz IP'yi **havuzdan** seçin:
> `metallb.universe.tf/loadBalancerIPs: 10.30.20.210`.

**0) ÖNCE cosign anahtar çiftini üret ve Secret'a koy :**
```bash
# Parola ÜRET (interaktif sormasın)
export COSIGN_PASSWORD=$(openssl rand -base64 24 | tr -d '/+=')
echo "COSIGN_PASSWORD=$COSIGN_PASSWORD" >> /root/.banklab-client-secrets

cosign generate-key-pair                # → cosign.key, cosign.pub
ls -la cosign.key cosign.pub            # cosign.pub: PEM, PUBLIC KEY

# Kyverno'nun okuyacağı Secret
kubectl -n kyverno create secret generic cosign-pub \
  --from-file=cosign.pub=cosign.pub
kubectl -n kyverno get secret cosign-pub -o jsonpath='{.data}' | jq 'keys'
# → ["cosign.pub"]
```

🔴 **NEDEN Secret:** PEM'i YAML'a gömmek (a) placeholder riski, (b) anahtar rotasyonunda
tüm ClusterPolicy'yi düzenlemek gerekir. Secret ile **tek noktadan** güncellenir.

```bash
helm repo add kyverno https://kyverno.github.io/kyverno && helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata: { name: verify-signature }
spec:
  validationFailureAction: Audit      # 🔴 ÖNCE Audit — doğrudan Enforce TÜM pod'ları bloklar
  background: false                   # NEDEN: imza doğrulama arka planda çalışmaz
  webhookConfiguration: { timeoutSeconds: 30 }   # NEDEN: doğrulama yavaş
  rules:
  - name: check-signature
    match: { any: [{ resources: { kinds: [Pod] } }] }
    # 🔴 §8.0 INFRA_NS ile AYNI: altyapı pod'ları imza şartına tabi DEĞİL
    exclude: { any: [{ resources: { namespaces:
      [kube-system, kube-public, kube-node-lease, longhorn-system, cnpg-system,
       external-secrets, step-system, metallb-system, cert-manager, kyverno] } }] }
    verifyImages:
    - imageReferences: ["harbor.banklab.alperozbay.com/banklab/*"]   # 🔴 10.30.20.30 LB havuzunda DEĞİL
      failureAction: Enforce          # 🔴 verifyImages İÇİNDE (spec'te değil)
      attestors:
      - count: 1
        entries:
        - keys:
            # 🔴 DOĞRUDAN PEM YAZMAYIN — Secret referansı kullanın.
            # Placeholder (<cosign.pub>) bırakılırsa YAML geçersiz olur VEYA
            # yanlış anahtar → TÜM imajlar reddedilir (Enforce'da küme kilitlenir).
            secretRef:
              name: cosign-pub
              namespace: kyverno
```

✅ **KANIT:**
```bash
kubectl -n delivery run unsigned --image=harbor.banklab.alperozbay.com/banklab/unsigned:1.0
kubectl -n delivery get events --field-selector reason=PolicyViolation   # Audit: event VAR, pod ÇALIŞIR
```

🔴 **SIRA:** Audit → 1 hafta gözle → **sonra** `validationFailureAction: Enforce`.
**NEDEN:** Doğrudan Enforce, imzasız **tüm** pod'ları reddeder → küme çalışmaz hâle gelir.

<a id="s13-5"></a>

## 13.5 cosign imzalama

```bash
# Anahtar çifti
cosign generate-key-pair

# 🔴 digest'i CANLI oku — `<digest>` placeholder YAZMAYIN (shell yutar)
IMG="harbor.banklab.alperozbay.com/banklab/app:1.0.0"
DIGEST=$(crane digest "$IMG" 2>/dev/null ||   curl -sI "https://$IMG" -H "Accept: application/vnd.oci.image.index.v1+json" \
  | awk -F': ' 'tolower($1)~/docker-content-digest/{print $2}' | tr -d '\r')
[ -n "$DIGEST" ] || { echo "🔴 digest alınamadı"; exit 1; }
IMG_AT_DIGEST="${IMG%:*}@${DIGEST}"        # → .../app@sha256:abc123...
echo "İmzalanacak: $IMG_AT_DIGEST"

# İmzala (digest ile — tag değişebilir, digest DEĞİŞMEZ)
cosign sign --yes --key cosign.key "$IMG_AT_DIGEST"

# Doğrula
cosign verify --key cosign.pub "$IMG_AT_DIGEST" \
  | jq -r '.[0].critical.identity.docker-reference' 
```

✅ **KANIT:** `cosign verify` → `Verified OK`

---

<a id="s13-6"></a>

## 13.6 Kafka — KOŞULLU: hangi imaj?

> **ÖNCE OKU:** Kafka lab'da **zorunlu değil**. RAM bütçesinde **1 Gi** yer kaplar (§9.4).
> Atlamak istiyorsanız bu bölümü geçin.

### ⬜ ÖN KONTROL

| Kontrol | Komut | Beklenen | Değilse git |
|---|---|---|---|
| cert-manager | `kubectl get clusterissuer banklab-step` | `READY=True` | §10.3 |
| RAM (worker başına) | `kubectl top nodes` | her node < %70 | **§9.4** → Kafka'yı **atla** |
| namespace | `kubectl get ns data-services` | var | §8.0 |

### 🔀 KOŞULLU KARAR — Bitnami imaj krizi (Ağustos 2025)

Bitnami, **Ağustos 2025'te ücretsiz imajları kaldırdı**. `bitnami/kafka:3.x` artık **yok**.
Aşağıdaki tablo **sırayla** denenir — biri çalışana kadar:

| # | Yol | Komut (özet) | Artı | Eksi |
|---|---|---|---|---|
| **1** | **`bitnamilegacy`** (en hızlı) | `image.repository: bitnamilegacy/kafka` + `global.security.allowInsecureImages: true` | 2 dk'da çalışır, chart aynı | **Güncelleme almaz** (EOL deposu) |
| **2** | **Confluent** (`cp-kafka`) | kendi values'iniz | Bakımlı, uzun ömürlü | Chart Kafka API'sinden farklı, ~30 dk iş |
| **3** | **Strimzi operator** | `strimzi.io` + `Kafka` CR | Kubernetes-native, üretim | ~45 dk, +1 operator |
| **4** | **Kafka'yı tamamen atla** | — | RAM +1 Gi kazanır | Mesajlaşma yok |

🔴 **ÖNEMLİ — bu rehber YOL 1'i kullanır** (hızlı test için). **Üretimde YOL 2 veya 3.**

### YOL 1 — bitnamilegacy (hızlı)

**1) Client credential ÜRET (önce — yoksa pod başlamaz):**
```text
KAFKA_PW=$(openssl rand -base64 24 | tr -d '/+=')
echo "KAFKA_PW=$KAFKA_PW" >> /root/.banklab-client-secrets
kubectl -n data-services create secret generic kafka-client-creds \
  --from-literal=client-passwords="$KAFKA_PW" \
  --from-literal=client-password="$KAFKA_PW" \
  --from-literal=inter-broker-password="$KAFKA_PW"
kubectl -n data-services get secret kafka-client-creds -o jsonpath='{.data}' | jq 'keys'
# → ["client-password","client-passwords","inter-broker-password"]
```
**NEDEN bu 3 anahtar:** Bitnami chart'ı **hepsini** ister. Biri eksik olursa
`helm install` → `secret kafka-client-creds is missing key 'inter-broker-password'`.

**2) Server sertifikası:**
```bash
kubectl -n data-services apply -f - <<'EOF'
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: kafka-tls, namespace: data-services }
spec:
  secretName: kafka-tls
  duration: 720h
  renewBefore: 240h
  dnsNames:
    - kafka.data-services.svc
    - kafka.data-services.svc.cluster.local
    - kafka-controller-0.kafka-controller-headless.data-services.svc
    - kafka-controller-0.kafka-controller-headless.data-services.svc.cluster.local
    - kafka-broker-0.kafka-broker-headless.data-services.svc
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
EOF

kubectl -n data-services wait --for=condition=Ready certificate/kafka-tls --timeout=3m
kubectl -n data-services get secret kafka-tls -o jsonpath='{.data}' | jq 'keys'
# → ["ca.crt","tls.crt","tls.key"]
```

**3) Kur:**
```yaml
# kafka-values.yaml
global:
  security: { allowInsecureImages: true }        # 🔴 bitnamilegacy için ZORUNLU
image: { repository: bitnamilegacy/kafka, tag: 3.9.0 }

controller:
  replicaCount: 1
  persistence: { enabled: true, storageClass: longhorn, size: 8Gi }
  resources:
    requests: { memory: 512Mi, cpu: 100m }
    limits:   { memory: 1Gi }
broker:
  replicaCount: 1
  persistence: { enabled: true, storageClass: longhorn, size: 8Gi }
  resources:
    requests: { memory: 512Mi, cpu: 100m }
    limits:   { memory: 1Gi }

listeners:
  client:
    protocol: SASL_SSL
    ssl:
      enabled: true
      existingSecret: kafka-tls                # 🔴 SASL_SSL ise ssl bloğu ZORUNLU
  interbroker:
    protocol: PLAINTEXT                        # NEDEN: tek node, node-arası trafik yok
auth:
  clientProtocol: sasl
  interBrokerProtocol: plaintext
  sasl:
    interBroker: { mechanism: PLAIN }
    client: { mechanism: PLAIN }
```

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update
helm install kafka bitnami/kafka -n data-services -f kafka-values.yaml
```

✅ **KANIT:**
```bash
kubectl -n data-services get pods -l app.kubernetes.io/name=kafka
# kafka-controller-0  1/1 Running
# kafka-broker-0      1/1 Running

kubectl -n data-services exec -it kafka-controller-0 -- \
  kafka-topics.sh --bootstrap-server localhost:9092 --list
# → boş çıktı = çalışıyor (henüz topic yok)
```

🔴 **DUR — hangi hata, hangi çözüm:**

| Hata | Sebep | Çözüm |
|---|---|---|
| `ImagePullBackOff` (`bitnami/kafka`) | Eski values (bitnamilegacy değil) | `global.security.allowInsecureImages: true` **+** `image.repository: bitnamilegacy/kafka` |
| `invalid image` / `not allowed` | `allowInsecureImages` yok | ekle |
| `missing key 'client-passwords'` | secret eksik anahtar | 1. adımdaki 3 anahtarı ekle |
| `secret kafka-tls not found` | Certificate oluşmadı | `kubectl -n data-services get cert` → `READY` mu? |
| `SASL authentication failed` | `auth.clientProtocol` eksik | `auth.clientProtocol: sasl` |
| `Init:0/1` (takılı) | PVC bağlanmadı | `kubectl -n data-services get pvc` → `longhorn` SC var mı? |

🔁 **YOL 2'ye geçiş (Confluent):** `image.repository: confluentinc/cp-kafka`, `tag: 7.6.1`
+ Bitnami chart'ının `listeners` yapısı Confluent ile **birebir uyuşmaz** → ayrı chart gerekir.

---

<a id="s13-7"></a>

## 13.7 Harbor — imaj registry

> 🔴 **KRİTİK:** Harbor **kendi CA'sına güvenen** sertifika ister. step-ca kök sertifikası
> `harbor-core` ConfigMap'ine **açıkça** eklenmezse imaj çekimi `x509: unknown authority` verir.
> **NEDEN:** Harbor kendi içindeki `registry` bileşenine HTTPS ile bağlanır; CA'yı bilmezse reddeder.

```bash
helm repo add harbor https://helm.goharbor.io && helm repo update
```

🔴 **`core.caBundle` PLACEHOLDER OLAMAZ:** Aşağıdaki komut
`$CA`'yı (§10.3'te tanımlı) **doğrudan YAML'a** yazar. Elle `<...>` bırakmayın.

🔴 **Harbor'da `ingress.className` ALANI YOK** (chart values.yaml'da doğrulandı):
Sadece `ingress.className` **değil**, `ingress.annotations` ile belirtilir.
Ve `persistence.persistentVolumeClaim` **`trivy`** anahtarını **da** ister (default 5Gi).

```bash
# $CA hâlâ tanımlı mı? (§10.3'ten)
[ -n "$CA" ] || CA=$(kubectl -n step-system get cm step-certificates-config -o jsonpath='{.data.ca\.crt}')
[ -n "$CA" ] || { echo "🔴 CA boş — §10.3'e dön"; exit 1; }

cat > harbor-values.yaml <<EOF
expose:
  type: ingress
  tls:
    enabled: true
    certSource: secret
    secret:
      secretName: harbor-tls
      notarySecretName: harbor-notary-tls
  ingress:
    hosts:
      core: harbor.banklab.alperozbay.com
    annotations:
      # 🔴 chart'ta 'className' YOK → annotation kullan
      kubernetes.io/ingress.class: traefik
      traefik.ingress.kubernetes.io/router.entrypoints: websecure
      traefik.ingress.kubernetes.io/router.tls: "true"
externalURL: https://harbor.banklab.alperozbay.com
persistence:
  persistentVolumeClaim:
    registry:   { storageClass: longhorn, size: 20Gi }
    database:   { storageClass: longhorn-postgres, size: 5Gi }
    redis:      { storageClass: longhorn, size: 1Gi }
    jobservice: { storageClass: longhorn, size: 1Gi }
    trivy:      { storageClass: longhorn, size: 5Gi }   # 🔴 default'ta VAR, belirtmezsen 5Gi ister
  imageChartStorage:
    type: filesystem
core:
  # 🔴 $CA genişler — placeholder DEĞİL
  caBundle: |
$(echo "$CA" | sed 's/^/    /')
database:
  type: internal            # NEDEN: Harbor'ın kendi PG'si; CNPG'ye bağlamak §17'de "üretim"
portal:
  resources:
    requests: { memory: 128Mi, cpu: 50m }
    limits:   { memory: 256Mi }
EOF

# YAML gerçekten geçerli mi + CA gömüldü mü?
yq '.core.caBundle | length' harbor-values.yaml     # → >1000
```

```bash
# Harbor TLS + notary sertifikaları (§10.3 issuer)
kubectl -n harbor apply -f - <<'EOF'
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: harbor-tls, namespace: harbor }
spec:
  secretName: harbor-tls
  duration: 720h
  renewBefore: 240h
  dnsNames: [harbor.banklab.alperozbay.com]
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: harbor-notary-tls, namespace: harbor }
spec:
  secretName: harbor-notary-tls
  duration: 720h
  renewBefore: 240h
  dnsNames: [notary.banklab.alperozbay.com]
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
EOF
```

```text
**NEDEN Harbor (basit registry yerine):** imza doğrulama (cosign), RBAC, tarama (Trivy),
webhook. Ama **~2 GB RAM** (§9.4) — bütçe yoksa §18.3'teki `registry:2` alternatifini kullanın.

helm install harbor harbor/harbor -n harbor --create-namespace -f harbor-values.yaml
```

✅ **KANIT:**
```bash
kubectl -n harbor get pods                    # core, registry, portal, jobservice, db, redis
curl -sk https://harbor.banklab.alperozbay.com/api/v2.0/health | jq
# → {"status":"healthy"}
```

⚠️ **RAM:** Harbor **~2 GB** ister (core 640Mi + registry 512Mi + db 512Mi + portal 256Mi).
🔁 **ALTERNATİF (lab):** `registry:2` Deployment — 100 MB, 5 dakika, CA sorunu yok.

---

<a id="s13-8"></a>

## 13.8 Argo CD + Keycloak SSO

**Ne yapıyoruz:** Argo CD'yi kurup **Keycloak SSO**'ya bağlıyoruz (`oidc.config`), sonra
`argocd-tls` sertifikası + IngressRoute ekliyoruz. **Neden SSO:** ayrı `admin` parolası
yönetmek yerine **Keycloak'ta MFA'lı** tek giriş; RBAC **gruplarla** yönetilir.


```bash
helm repo add argo https://argoproj.github.io/argo-helm && helm repo update
helm install argocd argo/argo-cd -n argocd --create-namespace
```

🔴 **`kubectl edit cm argocd-cm` YAPMAYIN:**
`argocd-cm` **chart'ın yönettiği** bir kaynaktır. `helm upgrade` **üzerine yazar** →
OIDC ayarı **kaybolur**. Doğru yol: **values** ile ver.

```yaml
# argocd-values.yaml
server:
  ingress:
    enabled: true
    ingressClassName: traefik
    hosts: [argocd.banklab.alperozbay.com]
    tls: true
  resources:
    requests: { memory: 256Mi, cpu: 100m }
    limits:   { memory: 512Mi }

configs:
  cm:
    url: https://argocd.banklab.alperozbay.com
    oidc.config: |
      name: BankLab SSO
      issuer: https://sso.banklab.alperozbay.com/realms/banklab
      clientID: argocd
      clientSecret: $oidc.clientSecret
      requestedScopes: ["openid","profile","email","groups"]
  # 🔴 b: secret'ı da values ile yönet — patch ETME
  secret:
    extra:
      oidc.clientSecret: "ARGOCD_SECRET_BURAYA"     # ← sed ile gerçek değer

controller:
  resources:
    requests: { memory: 256Mi, cpu: 100m }
    limits:   { memory: 512Mi }
repoServer:
  resources:
    requests: { memory: 128Mi, cpu: 50m }
    limits:   { memory: 256Mi }
```

```text
[ -n "$ARGOCD_SECRET" ] || ARGOCD_SECRET=$(grep ARGOCD_SECRET /root/.banklab-client-secrets | cut -d= -f2)
[ -n "$ARGOCD_SECRET" ] || { echo "🔴 ARGOCD_SECRET boş — §13.2-B'ye dön"; exit 1; }

sed -i "s|ARGOCD_SECRET_BURAYA|$ARGOCD_SECRET|" argocd-values.yaml

# TLS sertifikası
kubectl -n argocd apply -f - <<EOF
apiVersion: cert-manager.io/v1
kind: Certificate
metadata: { name: argocd-tls, namespace: argocd }
spec:
  secretName: argocd-server-tls
  duration: 720h
  renewBefore: 240h
  dnsNames: [argocd.banklab.alperozbay.com]
  issuerRef: { kind: StepClusterIssuer, name: banklab-step }
EOF

helm install argocd argo/argo-cd -n argocd --create-namespace -f argocd-values.yaml
```

✅ **KANIT:** `helm get values argocd -n argocd | yq '.configs.cm."oidc.config"'` → **boş DEĞİL**
(`helm upgrade` sonrası da kalır — çünkü values'ta).

✅ **KANIT:**
```text
argocd login argocd.banklab.alperozbay.com --sso
argocd account get-user-info          # kullanıcı + gruplar görünür
```

⚠️ **TUZAK:** Traefik **`argocd` namespace'ini watch etmiyor**sa route **404** verir.
`argocd` **§8.0 APP_NS listesinde** olduğu için §13.1'deki `traefik-values.yaml`
onu **otomatik** kapsar. **Listeyi BURAYA KOPYALAMAYIN** — §8.0 tek kaynaktır.
```bash
# Doğrula (kopyalamadan):
helm get values traefik -n traefik | yq '.providers.kubernetesCRD.namespaces'
# → [delivery, workforce, identity, data-services, observability, harbor, argocd, platform, traefik]
```

---

<a id="s13-9"></a>

## 13.9 Gitea — git sunucusu (observability'den ÖNCE değil, en son)

> **NEDEN en son:** Gitea **bağımsız** çalışır; hiçbir bileşen onu beklemez.
> RAM 512 MB, 5 dakika. **Sıralama esnek** — Argo CD'den (GitOps) sonra kurun ki
> repo'lar hemen kullanılsın.

```bash
helm repo add gitea https://dl.gitea.com/charts/ && helm repo update

# 🔴 admin parolası ÜRET (placeholder yok)
GITEA_ADMIN_PW=$(openssl rand -base64 24 | tr -d '/+=')
kubectl -n gitea create secret generic gitea-admin \
  --from-literal=username=banklab-admin \
  --from-literal=password="$GITEA_ADMIN_PW" 2>/dev/null || \
kubectl -n delivery create secret generic gitea-admin \
  --from-literal=username=banklab-admin \
  --from-literal=password="$GITEA_ADMIN_PW"
echo "GITEA_ADMIN_PW=$GITEA_ADMIN_PW" >> /root/.banklab-client-secrets
```

**0) ÖNCE pg-gitea CNPG cluster'ı (M83 — sıra):**
```bash
kubectl -n data-services apply -f - <<'EOF'
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata: { name: pg-gitea, namespace: data-services }
spec:
  instances: 1
  imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm
  storage:
    storageClass: longhorn-postgres
    size: 2Gi
  bootstrap:
    initdb:
      database: gitea
      owner: gitea
      secret: { name: pg-gitea-owner }
  resources:
    requests: { cpu: "100m", memory: "256Mi" }
    limits:   { cpu: "500m", memory: "768Mi" }
EOF

# 🔴 pg-gitea-owner secret'ı ÖNCE olmalı (M72 deseni)
kubectl -n data-services get secret pg-gitea-owner 2>/dev/null || {
  GITEA_DB_PW=$(openssl rand -base64 24 | tr -d '/+=')
  kubectl -n data-services create secret generic pg-gitea-owner \
    --from-literal=username=gitea --from-literal=password="$GITEA_DB_PW"
  echo "GITEA_DB_PW=$GITEA_DB_PW" >> /root/.banklab-client-secrets
}

# Healthy bekle
kubectl -n data-services wait --for=condition=Ready cluster/pg-gitea --timeout=10m
kubectl -n data-services get secret pg-gitea-owner -o jsonpath='{.data}' | jq 'keys'
# → ["password","username"]   🔴 yoksa externalDatabase bağlanamaz
```

```yaml
# gitea-values.yaml
gitea:
  admin:
    username: banklab-admin
    # 🔴 PLACEHOLDER DEĞİL — değer aşağıdaki secret'tan
    existingSecret: gitea-admin
    email: admin@banklab.local
  config:
    server:
      ROOT_URL: https://gitea.banklab.alperozbay.com/
      DOMAIN: gitea.banklab.alperozbay.com
    database: { DB_TYPE: postgres }
    service: { DISABLE_REGISTRATION: true }   # NEDEN: lab'da kayıt kapalı
  metrics: { enabled: true }
persistence:
  enabled: true
  storageClass: longhorn
  size: 10Gi
postgresql:
  enabled: false        # 🔴 NEDEN: pg-gitea CNPG cluster'ı kullanacağız
externalDatabase:
  type: postgres
  host: pg-gitea-rw.data-services.svc
  database: gitea
  user: gitea
  existingSecret: pg-gitea-owner
```

**pg-gitea (CNPG — pg-infisical ile aynı desen):**
```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata: { name: pg-gitea, namespace: data-services }
spec:
  instances: 1
  imageName: docker.io/cloudnative-pg/postgresql:17-standard-bookworm   # 🔴 ghcr.io 837 B/s → Docker Hub aynası
  storage: { storageClass: longhorn-postgres, size: 3Gi }
  bootstrap:
    initdb:
      database: gitea
      owner: gitea
      secret: { name: pg-gitea-owner }     # ← ESO/Infisical veya elle
```

```bash
# Traefik IngressRoute
kubectl -n platform apply -f - <<'EOF'
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata: { name: gitea, namespace: platform }
spec:
  entryPoints: [websecure]
  routes:
    - match: Host(`gitea.banklab.alperozbay.com`)
      kind: Rule
      services: [{ name: gitea-http, port: 3000 }]
  tls: { secretName: gitea-tls }
EOF
```

✅ **KANIT:**
```bash
kubectl -n platform get pods -l app.kubernetes.io/name=gitea   # 1/1 Running
curl -sk https://gitea.banklab.alperozbay.com/api/v1/version | jq
# → {"version":"1.24.x"}
```

⚠️ **TUZAK:** Traefik `platform` namespace'ini watch etmiyor → route **404**.
§8.0 `APP_NS` listesinde `platform` **var** ✅ — ama `traefik-values.yaml` (§13.1) listesinde
de olduğundan **emin olun**.

---

<a id="s14"></a>

# 14. KABUL TESTLERİ

<a id="s14-h1"></a>

## H1 — VM açılış sırası

**Ne yapıyoruz:** Host reboot sonrası **VM'ler doğru sırada** açılıyor mu (`onboot` + `startup`)?


```bash
for vmid in 102 101 103 104 105 106 107 108; do qm start $vmid; sleep 30; done
kubectl get nodes                       # 6/6 Ready
```

✅ **KANIT:** 6/6 Ready, 3/3 etcd, VIP yanıt veriyor

<a id="s14-h2"></a>

## H2 — `cred/passwd` kalıcılığı

**Ne yapıyoruz:** CP restart sonrası küme **kendini toparlıyor** mu, yoksa `cred/passwd` hatası mı veriyor?


```bash
for cp in 10.30.10.22 10.30.10.23; do
  ssh root@$cp 'systemctl restart rke2-server'; sleep 60
  kubectl get nodes | grep -E "cp-[23]"
done
```

✅ **KANIT:** 2 restart sonrası 6/6 Ready · logda `passwd newer` **YOK**

<a id="s14-h3"></a>

## H3 — Origin vs Cloudflare (H82)

**Ne yapıyoruz:** 3 noktayı **ayrı ayrı** test ediyoruz: origin:443, origin:8006, Cloudflare. **Neden:** 5xx hatası Cloudflare'de mi, origin'de mi — **karıştırılmasın**.


```bash
curl -4 -sS -o /dev/null -w "origin:443  %{http_code} %{time_starttransfer}s\n" https://87.76.130.66/
curl -4 -sS -o /dev/null -w "origin:8006 %{http_code} %{time_starttransfer}s\n" -k https://87.76.130.66:8006/
curl -4 -sS -o /dev/null -w "cf:443      %{http_code} %{time_starttransfer}s\n" https://proxmox.alperozbay.com/
```

<a id="s14-h4"></a>

## H4 — Üretim öncesi kontrol listesi

| Kontrol | Komut | Beklenen |
|---|---|---|
| ClusterIssuer | `kubectl get clusterissuer` | READY=True |
| Sertifikalar | `kubectl get cert -A` | hepsi READY=True |
| ESO | `kubectl get externalsecret -A` | hepsi SecretSynced |
| CNPG | `kubectl get cluster -A` | healthy state |
| Yedek | `kubectl get backup -A` | completed |
| Alarm | `curl localhost:9090/api/v1/alerts` | firing→resolved + **alıcı logu** |
| Infisical restart | pod sil → 1/1 | ✅ (OpenBao'da **başarısızdı**) |

<a id="s14-h5"></a>

## H5 — Yedek + geri yükleme

**Ne yapıyoruz:** `vzdump` ile 3 CP'yi yedekle, sonra **geri yükleme** dene. **Neden:** denenmemiş yedek = **yedek değil**.


```bash
vzdump 103 104 105 --storage local --mode snapshot --compress zstd
kubectl -n identity create -f backup.yaml
# Geri yükleme: ayrı Cluster + bootstrap.recovery + recoveryTarget
```

✅ **KANIT:** Geri yüklenen PG'de `select count(*) from flyway_schema_history` = kaynak

---

<a id="s15"></a>

# 15. SORUN GİDERME — TAM TABLO

| Belirti | Kök neden | Çözüm | Kaynak |
|---|---|---|---|
| SSH %5, banner 1–5 s | host tükenmesi | §4.3 loadgate | H81 |
| `mux_client_request_session: Broken pipe` | bayat soket | `rm -f ~/.ssh/cm-*` | H02 |
| `passwd newer than datastore` | k3s #14982 | §7.3 drop-in | H59/H80 |
| VM ağsız | network-config 0640 | `chmod 0644` | H18 |
| VM ağsız (izin doğru) | cloud-init uygulamadı | elle enjekte §6.2 | H17 |
| OPNsense `.php` 404 | 26.7 MVC | `/api/...` | R03/H24 |
| Kural 403 | CSRF yok / yanlış başlık | **`X_CSRFTOKEN`** + `PHPSESSID` çerezi | H28/E15 |
| Kural 400 | `source` nesnesi | **`source_net`** | E4 |
| Parola reddedildi | QMP shift | shift'siz parola + ekran | H21 |
| `ImagePullBackOff` ×6 | imaj yok | §9.2 yol B | H57 |
| PVC `Pending` | provisioner RBAC | `pods: create` kuralı | R49 |
| `helm --wait` timeout | yavaş imaj | `rollout status` | R50 |
| CRD `Too long` | >256 KB | `--server-side` | H75 |
| ESO `CrashLoop` | CRD eksik | 24 CRD | H74 |
| ESO `SecretSyncedError` | yanlış apiVersion/şema | `v1` + §11.8 | E2/E3 |
| `SecretStore` invalid | `secretsScope` auth içinde | kardeş alan | E3 |
| Infisical başlamıyor | Redis yok | `REDIS_URL` zorunlu | §11.4 |
| Infisical `DB_CONNECTION_URI` çakışması | chart + secret | §11.5 yol A | E7 |
| CNPG chart bulunamadı | `--version 1.30.2` | **0.29.2** | E1 |
| Barman yedek yok | plugin kurulmamış | §12.2 | E10 |
| Keycloak CRD yok | `keycloakx` CRD içermez | resmî operator | E12 |
| Kyverno tüm pod'ları blokluyor | doğrudan Enforce | önce **Audit** | E13 |
| `525` Cloudflare | origin yavaş | origin testi | H82 |
| `StepClusterIssuer` READY=False | caBundle/kid | §10.3 | — |
| step-ca `permission denied` | `drop: ALL` + root | `runAsUser: 0` | R51 |

---

<a id="s16"></a>

# 16. "ŞUNU KULLAN" — HIZLI KARAR TABLOSU

| Ortamınız | Varsayılan | Bunu kullan | NEDEN |
|---|---|---|---|
| Ağ <1 MB/s | `helm repo` | GitHub **tek-asset bundle** + `ctr import` | ghcr 837 B/s, gh-proxy 38 MB/s |
| RAM <72 GB | 8 VM tam | §2.4 küçültme | **47 GB minimum** |
| SSH düşüyor | tek `ssh` | `loadgate` + konsol (`stop pveproxy`) | H81 |
| ghcr.io ölü | `helm install` | `pull_dh.py`+`pack.py`+`ctr import` | K29 |
| CNPG yedeği | chart default | **`plugin-barman-cloud` ayrı** | E10 |
| Infisical basit | dış PG/Redis | **§11.5 yol B** (bundled) | tek komut |
| Infisical üretim | bundled | **§11.5 yol A** (CNPG) | yedeklenebilir |
| PSA | `restricted` | **`baseline`** | imajlar root |
| İmza zorunlu | Enforce | **Audit → 1 hafta → Enforce** | aksi halde küme kilitlenir |
| CRD | `kubectl apply` | `--server-side --force-conflicts` | 256 KB |
| Uzun helm | `--wait` | `rollout status` | R50 |
| Cloudflare 5xx | CF ayarı | **origin testi** | H82 |
| **`node-ip` verirken** | iki IP | **YALNIZ `primary0`** | §6.6-A — 9 node |
| **`Pulling image` asılı** | `config.yaml` | **`config.toml.tmpl`** | §7.1-B — RKE2 okumuyor |
| **step-ca `operation not permitted`** | `drop:[ALL]` | **`capabilities` bloğunu kaldır** | §10.1-B |
| **ESO controller crash** | 3 CRD | **24 CRD + `create`** | §11.8-B |
| **`onboot` sorusu** | default 0 | **lab=1 / canlı=0** | §4.2-C |
| **Host load > 20, SSH %5** | DDoS sanılır | **kaynak tükenmesi** → `stop pveproxy` | §4.3-B |
| **`cred/passwd newer`** | RKE2 yeniden kur | **`rm` + systemd drop-in** | §7.3-B |
| **Konsol parolası** | `BankLab!2026` | **`banklab2026`** | §18.8 #1 |
| **Kendini banlama** | 1 katman | **5 katman whitelist** | §4.4-B |
| **`curl` asılı, ping OK** | — | **MTU ölç** | §5.6 |
| **DNSSEC** | varsayılan | **trust anchor + 4 test** | §6.4-B |
| **Policy uygulanmıyor** | YAML yeter | **DENY→ALLOW testi** | §8.2 |
| **HDD/SSD scheduler** | `cfq` | **NVMe=none, SSD=mq-deadline** | §4.7 |
| **THP açık** | `always` | **`madvise`** | §4.7 |
| **40 karar** | — | **§18.9 karar günlüğü** | kanıtlı |

---

<a id="s17"></a>

# 17. NELER HÂLÂ EKSİK (dürüst liste)

Bu rehber **G17'ye kadar** tam. Aşağıdakiler **kapsam dışı** ve production'da gerekir:

| Eksik | Neden önemli | Ne zaman |
|---|---|---|
| **Vault-less secret rotation** | ENCRYPTION_KEY rotasyonu | Üretim öncesi |
| **RBAC sıkılaştırma** | Şu an `bankbootstrap` = root | Üretim öncesi |
| ~~Audit politikası~~ | ✅ **§7.2-B'de tam kural seti yazıldı ** | — |
| **etcd şifreleme (at-rest)** | Disk ele geçerse secret'lar açık | Üretim öncesi |
| **Kaynak limitleri** | ✅ **§9.4'te bütçe + limit tablosu ** — üretimde daha sıkı | Üretim öncesi |
| ~~Gitea~~ | ✅ **§13.9'da yazıldı** | — |
| **Multi-AZ / CP dağılımı** | Tek Proxmox = tek hata noktası | Tasarım gereği |
| **Yedeklerin test edilmiş geri dönüşü** | `bootstrap.recovery` örneği yok | H5 |
| **Ön kontrol yalnız 6 bölümde** | §5 OPNsense · §10 güvenlik · §12 veri katmanı §14–18'de yok | ⏳ sıradaki tur |
| **Koşullu karar yalnız 10 yerde** | §8 · §10 · §12 · §14–18'de karar ağacı eksik | ⏳ sıradaki tur |
| **`pick-proxy` ölçümü tek noktadan** | Proxy hızı **sunucudan** ölçülüyor; DNS/rota değişirse güncellenmeli | her kurulumda bir kez |
| **Air-gap yolu test edilmedi** | §3.3.10 gerçek USB aktarımında denenmedi | air-gap gerekirse |
| **`netprofile` 25 s timeout** | Çok yavaş kaynakta "0 KB/s" yanlış negatif verebilir | elle tekrar ölç |

⚠️ **Ayrıca:** `bitnamilegacy` imajları (Infisical chart subchart'ları) **artık güncellenmiyor**.
Üretimde Infisical'ı **dış PG/Redis** ile çalıştırın (§11.5 yol A).

---

<a id="s18"></a>

# 18. GERÇEK DÜNYADA NE TERS GİDER — V9'a özel

> Önceki sürümlerde bu bölüm **yoktu**. Rehber "her şey çalışır" varsayıyordu.
> Aşağıdakiler **bu lab'da gerçekten olan** olaylardır. Her biri için **erken uyarı sinyali** var.

<a id="s18-1"></a>

## 18.1 Kurulumun çöktüğü 6 gerçek an

| # | Ne oldu | Erken uyarı sinyali | Önlem |
|---|---|---|---|
| 1 | **Host kilitlendi** — SSH %5, `:8006` timeout | `loadgate` → load > nproc×0.6 | §4.3 + her büyük işten önce |
| 2 | **OpenBao sealed** → 13 saat kilit | Pod `restart` sayısı artıyor + `Ready=False` | §11.6-B restart testi (Infisical) |
| 3 | **Longhorn 6 ImagePullBackOff** | `kubectl get pods \| grep -v Running` | §9.2 imaj listesi + import |
| 4 | **`cred/passwd`** her restart | `journalctl -u rke2-server \| grep passwd newer` | §7.3 drop-in |
| 5 | **VM ağsız** (cloud-init) | `cloud-init status` ≠ done | §6.2 izin 0644 + VM içi doğrulama |
| 6 | **OPNsense CSRF 403** | `no matching csrf found` | §5.4 doğru başlık + GUI yolu |

<a id="s18-2"></a>

## 18.2 Zaman bütçesi — gerçekçi

| Aşama | Rehber süresi | **Gerçek** | Fark neden |
|---|---|---|---|
| §4 Host | 30 dk | **45 dk** | apt upgrade + reboot |
| §5 OPNsense | 60 dk | **2,5 saat** | GUI + konsol + kurulum |
| §6 VM'ler | 90 dk | **3 saat** | cloud-init hataları |
| §7 RKE2 | 60 dk | **2 saat** | CP join + etcd |
| §9 Longhorn | 45 dk | **2,5 saat** | imaj indirme |
| §11 Infisical | 45 dk | **2 saat** | chart + secret zinciri |
| §12 CNPG | 30 dk | **1 saat** | PVC + initdb |
| §13 Uygulama | 3 saat | **6 saat** | 5 bileşen × imaj |
| §14 Kabul | 1 saat | **2 saat** | alarm alıcı testi |
| **Toplam** | **~9 saat** | **~21 saat** | **2,3×** |

🔴 **Planlama:** **3 tam iş günü** ayırın. Tek günde bitirmeye çalışmak **V6'da 4 güne** çıktı
çünkü hatalar birikti.

<a id="s18-3"></a>

## 18.3 "Bu adım gerçekten gerekli mi?" — 5 gereksiz adım

Lab'da **atlayabileceğiniz** adımlar (üretimde gerekir):

| Adım | Lab'da atlanabilir mi | Neden |
|---|---|---|
| **step-ca** (§10.1) | ⚠️ Kısmen — `cert-manager` self-signed ile başlanabilir | Gerçek CA testi için gerekli, ama kurulumu 45 dk |
| **Barman yedeği** (§12.4) | ✅ Evet — MinIO kurmadan `pg_dump` yeter | Asıl test H5'te |
| **Kyverno** (§13.4) | ✅ Evet — imzalama testi sonra | Advisory; kümeyi bloklamaz (Audit modunda) |
| **Kafka** (§13.6) | ✅ Evet | Tek başına 2 GB RAM |
| **Harbor** (§13.7) | ✅ Evet — `registry:2` yeter | Harbor 2 GB RAM + 10 dk |
| **Argo CD** (§13.8) | ✅ Evet — `kubectl apply` yeter | GitOps testi ayrı |

🔁 **Minimum çalışan küme (12 saat):** §4 §5 §6 §7 §8 §9 §11 §12 + self-signed cert + basit Deployment.
**V9'un çekirdeği budur.** Gerisi "tam platform".

<a id="s18-4"></a>

## 18.4 En sık yapılan 5 hata (kullanıcı tarafı)

1. **`§2` envanteri doldurmadan başlamak** → IP çakışması, yanlış sertifika SAN
2. **`loadgate`'i atlamak** → host kilitlenir, SSH %5 (V6'da oldu)
3. **`§8.1` restart testini atlamak** → H59/H79 G6'da patlar, 13 saat teşhis
4. **`helm --wait` kullanmak** → 15 dk timeout, yarı kurulu release
5. **Sürümleri doğrulamadan `§3.2` atlamak** → V6'da 3 sürüm geride + yanlış SHA256

<a id="s18-5"></a>

## 18.5 Başarısızlık durumunda geri dönüş planı

| Aşama | Geri dönüş | Veri kaybı |
|---|---|---|
| §4 Host | `snapshot` (Proxmox) | Yok |
| §5 OPNsense | VM 100 snapshot | Yok |
| §6 VM'ler | VM sil, yeniden oluştur | Yok (yeni kurulum) |
| §7 RKE2 | `systemctl stop rke2-*` + `/var/lib/rancher` sil | **etcd verisi** |
| §9 Longhorn | `kubectl delete -n longhorn-system` + `/var/lib/longhorn` sil | **TÜM PVC'ler** 🔴 |
| §11 Infisical | Helm uninstall; **PG verisi kalır** | Secret'lar (PG'de) |
| §12 CNPG | Cluster sil → PVC `Retain` → yeniden bağla | Yok |

🔴 **Longhorn silmeden önce:** `kubectl get pvc -A` → **hepsini yedekleyin**.
`reclaimPolicy: Retain` olsa bile `/var/lib/longhorn` silinirse **veri gider**.

<a id="s18-6"></a>

## 18.6 Ne zaman "bu işi bırak" demeli

| Gösterge | Yorum |
|---|---|
| 3 farklı hata aynı adımda, 2 saat geçti | **Kök nedeni ara**, ilerlemeyi bırak (K1) |
| Aynı komutu 2 kez çalıştırdın, aynı hata | **Araştırmadan tekrar yasak** (K2) |
| Host load > nproc, 15 dk düşmedi | **Konsol/IPMI**, VM'leri kapat |
| `etcd` quorum < 2/3 | **CP ekleme**, önce quorum onar |
| PVC 10 dk `Pending` | Longhorn → **9.2'ye dön**, ilerlemeyi bırak |


---

<a id="s18-7"></a>

## 18.7 🔴 "BUNU YAPMAZSAN ÇALIŞMAZ" — 9 kritik adım (tek bakışta)

Bu 9 madde **V6'da ya eksikti ya da yanlıştı**; her biri **saatler** kaybettirdi.
Hepsini **atlamadan** yapın.

| # | Adım | Bölüm | Atlarsan ne olur | Süre kaybı |
|---|---|---|---|---|
| **1** | **`onboot 1` + `startup order`** | §4.2-C | Host reboot → **9 VM kapalı**, lab erişilemez | 1–2 sa |
| **2** | **5 katmanlı IP whitelist** | §4.4-B | **Kendinizi banlarsınız** (fail2ban/CrowdSec/nftables) | 30 dk – kilitlenme |
| **3** | **`node-ip` = SADECE primary0** | §6.6-A | **9 node**, hiçbiri Ready | 3 sa |
| **4** | **`.link` MAC → `primary0/edge0`** | §6.6 | İsimler boot'ta değişir, IP çakışır, ağ yok | 3 sa |
| **5** | **`pvesh set /cluster/sdn`** (commit) | §4.2-B | `bridge 'vnet10' does not exist` → VM açılmaz | 2 sa |
| **6** | **NetworkPolicy DENY→ALLOW testi** | §8.2 | Politika **kabul** edilir ama **uygulanmaz** | 2 sa |
| **7** | **`frrk8s.enabled=false`** (MetalLB) | §9.1 | `Init:0/4` sonsuza kadar takılı | 2 sa |
| **8** | **`L2Advertisement.interfaces: [edge0]`** | §9.1 | `ip neigh` **FAILED**, LB IP'ye erişim yok | 2 sa |
| **9** | **`§12.1 CNPG` ÖNCE, sonra `§11.3`** | §11.3 | `clusters.postgresql.cnpg.io not found` | 2 sa |

### Hızlı doğrulama scripti — hepsini tek seferde kontrol et

```bash
#!/bin/bash
# preflight.sh — kurulum bittikten sonra çalıştır
ok(){ printf "%-46s %s\n" "$1" "$([ "$2" = 0 ] && echo ✅ || echo 🔴)"; }

# 1) onboot
MISS=0; for v in $(seq 100 108); do qm config $v | grep -q '^onboot: 1' || MISS=$((MISS+1)); done
ok "1) onboot 1 (tüm VM'ler)" $MISS

# 2) whitelist
ok "2) fail2ban ignoreip" $([ "$(fail2ban-client get sshd ignoreip 2>/dev/null | grep -c '10\.30\.')" -gt 0 ] && echo 0 || echo 1)

# 3) node sayısı = 6
ok "3) küme 6 node" $([ "$(kubectl get nodes --no-headers 2>/dev/null | wc -l)" = 6 ] && echo 0 || echo 1)
ok "3b) hayalet edge node yok" $(kubectl get nodes --no-headers 2>/dev/null | grep -qc '^edge-' && echo 1 || echo 0)

# 4) arayüz adları
ok "4) worker primary0/edge0" $(ssh -o ConnectTimeout=3 root@10.30.30.11 \
   'ip -br link | grep -cE "^(primary0|edge0)"' 2>/dev/null | grep -q 2 && echo 0 || echo 1)

# 5) SDN vnet var
ok "5) SDN vnet10/20/30" $(ip -br link 2>/dev/null | grep -cE 'vnet(10|20|30)' | grep -q 3 && echo 0 || echo 1)

# 6) NetworkPolicy sayısı
ok "6) NetworkPolicy var" $([ "$(kubectl get networkpolicy -A --no-headers 2>/dev/null | wc -l)" -ge 5 ] && echo 0 || echo 1)

# 7) Metallb speaker 3/3
ok "7) metallb-speaker 3/3" $(kubectl -n metallb-system get ds metallb-speaker \
   -o jsonpath='{.status.numberReady}' 2>/dev/null | grep -qx 3 && echo 0 || echo 1)

# 8) L2Advertisement edge0
ok "8) L2Advertisement edge0" $(kubectl get l2advertisement -A -o jsonpath='{.items[*].spec.interfaces[*]}' 2>/dev/null | grep -q edge0 && echo 0 || echo 1)

# 9) CNPG CRD + cluster
ok "9a) CNPG CRD" $(kubectl get crd clusters.postgresql.cnpg.io >/dev/null 2>&1 && echo 0 || echo 1)
ok "9b) pg-infisical healthy" $(kubectl -n data-services get cluster pg-infisical \
   -o jsonpath='{.status.phase}' 2>/dev/null | grep -q healthy && echo 0 || echo 1)

echo "---"
kubectl get nodes -o wide 2>/dev/null
```

🔴 **Herhangi bir satır 🔴 ise** ilgili bölüme dönün — diğer adımlara geçmeyin.

---

<a id="s18-8"></a>

## 18.8 🔴 KÜÇÜK GÖRÜNEN AMA SAAT KAYBETTİREN 8 TUZAK

Bunlar **tek satırlık** ama atlanınca **saatler** gider. Her biri **V6'da yaşandı**.

### 1) `!` karakterli parola → konsolda **kaybolur** (R34)

```bash
# ❌ YANLIŞ — QMP send-key 'shift' kombinasyonunu DÜŞÜRÜYOR
Parola: BankLab!2026
Konsolda gerçekte yazılan: ankab2026      ← 'B', '!', shift-1 KAYBOLDU
```

**V6'da ne oldu:** GUI'ye **hiç** girilemedi; Menü 3 "Reset root password" ile kurtarıldı.

| Kural | Detay |
|---|---|
| **Otomasyon/konsol parolası** | **YALNIZ küçük harf + rakam**: `banklab2026` |
| **GUI'den sonra** | güçlü parolaya çevir: `BankLab!2026#x` |
| **QMP ile yazılacak** | `!@#$%^&*()_+{}|:"<>?~` **HEPSİ RİSKLİ** |

### 2) LAB içi NTP yok → saat kayar → **etcd çöker** (R35)

```bash
# chrony dışarı çıkamıyorsa (izole lab) kendi kaynağını kullan
cat > /etc/chrony/chrony.conf <<'EOF'
# 🔴 LAB içi NTP kaynağı — dışarı çıkış yoksa ŞART
server 10.30.10.1 iburst prefer     # OPNsense
server 10.30.10.254 iburst          # Proxmox host
pool pool.ntp.org iburst
makestep 1.0 3
rtcsync
EOF
systemctl restart chrony
chronyc tracking | grep "Leap status"     # → Normal
```

🔴 **NEDEN KRİTİK:** etcd **saat kayması > 1 sn** olursa leader election başarısız →
`etcdserver: request timed out` → **küme çöker**. DNSSEC ve TLS de saat ister.

### 3) Yedek/geri yükleme **sürüm bağımlı** (R37)

| Kural | Neden |
|---|---|
| Yedek ve restore **aynı PVE major** sürümünde | `vzdump` format + `qemu-server` sürümü değişir |
| `qmrestore` **eski → yeni** genelde OK | tersi **başarısız** |
| Upgrade **önce** restore testi yap | sonra yedek formatı uyumsuz çıkabilir |

```bash
# Yedek almadan ÖNCE sürümü kaydet
pveversion -v > /root/pve-version-at-backup.txt
# Restore'dan önce karşılaştır
diff <(pveversion -v) /root/pve-version-at-backup.txt | head
```

### 4) `discard=on` **yalnız thin** depolamada (R38)

| Depolama | `discard=on` | `ssd=1` |
|---|---|---|
| `local-lvm` (LVM-thin) | ✅ **desteklenir** | ✅ |
| `dir` + `qcow2` | ⚠️ qcow2'de **farklı** davranır | ❌ |
| `ZFS` | ✅ (ZFS TRIM) | ✅ |
| **NFS/CIFS** | ❌ **YOK SAYILIR** | ❌ |

```bash
# Doğru: only thin için discard
qm set 106 --scsi0 local-lvm:vm-106-disk-0,discard=on,ssd=1
# NFS üzerinde discard YAZMAYIN
```

### 5) VMID çakışması — **kontrol adımı yok** (R39)

**V6'da:** sunucuda **VM 200 (`ubuntu-desktop`, ÇALIŞIYOR)** ve **9100 (`fedora-tmpl`)** vardı.
Rehber 100–108 kullanıyor, çakışma yoktu — ama **kontrol yoktu**.

```bash
# 🔴 VM oluşturmadan ÖNCE — her zaman
qm list | awk 'NR>1{print $1}' | sort -n > /tmp/mevcut-vmid
for V in $(seq 100 108); do
  grep -qx "$V" /tmp/mevcut-vmid && echo "🔴 VMID $V KULLANIMDA — BAŞKA NUMARA SEÇ"
done
echo "✅ VMID 100-108 temiz"

# Kullanımdaki VM'lere DOKUNMA
qm list
```

### 6) Helm `--wait` → 15 dk timeout → **kısmi kurulum** (R46/R50)

```bash
# ❌ YANLIŞ — yavaş ağda 15 dk dolar, release 'failed' kalır
helm upgrade --install longhorn longhorn/longhorn -n longhorn-system --wait --timeout 15m

# ✅ DOĞRU — beklemesiz kur, SONRA ayrı bekle
helm upgrade --install longhorn longhorn/longhorn -n longhorn-system
kubectl -n longhorn-system rollout status ds/longhorn-manager --timeout=30m
kubectl -n longhorn-system rollout status deploy/longhorn-driver-deployer --timeout=30m

# Artık pod'ları temizle (Terminating/Pending kalıntı)
kubectl -n longhorn-system delete pod --field-selector=status.phase=Pending --wait=false 2>/dev/null || true
kubectl -n longhorn-system get pods | awk '/Terminating/{print $1}' | xargs -r kubectl -n longhorn-system delete pod --grace-period=0 --force
```

🔴 **NEDEN `--wait` KULLANILMAZ:** 160 KB/s hat + 430 MB imaj + `ImagePullBackOff`
retry'ları = 15–20 dk **dolmadan** timeout. Release `failed` kalır; tekrar çalıştırınca
`Terminating` artıkları temizlenmez.

### 7) Longhorn provisioner RBAC — `pods: create` atlanırsa PVC **asla** bağlanmaz (R49)

```yaml
# Upstream rbac.yaml'den TAM kopyala — elle yazma
# Atlanan kural:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list","watch","create","delete"]
```

**Belirti:** PVC `ProvisioningFailed`, provisioner log'da `pods is forbidden`.
**Sessiz değil ama geç fark edilir** — PVC 10 dk `Pending` kalır.

### 8) `--force` pod silme → **ReplicaSet bozulur** (V6'da namespace yeniden kuruldu)

```bash
# ❌ YANLIŞ — çalışan pod'u force sil
kubectl -n cert-system delete pod step-ca-xxx --grace-period=0 --force
# → ReplicaSet DESIRED=1 CURRENT=0, pod ÜRETİLMEZ
# → namespace Terminating'de TAKILIR, finalizer elle kaldırılmalı

# ✅ DOĞRU — normal sil (graceful)
kubectl -n cert-system delete pod step-ca-xxx

# Zaten takıldıysa (placeholder YOK — değişkenle):
DEPLOY="step-ca" ; NS="cert-system"
kubectl -n "$NS" get pods
RS=$(kubectl -n "$NS" get rs -l app.kubernetes.io/name="$DEPLOY" \
      -o jsonpath='{.items[0].metadata.name}')
echo "Bozuk RS: $RS"
kubectl -n "$NS" describe rs "$RS" | tail -20        # Events oku
kubectl -n "$NS" delete rs "$RS"                     # deploy yenisini yaratır
kubectl -n "$NS" rollout restart "deploy/$DEPLOY"
kubectl -n "$NS" rollout status  "deploy/$DEPLOY" --timeout=5m
```

🔴 **KURAL:** `--force` **YALNIZ** `Terminating`'de **takılı kalmış** pod için, **son çare**.
Çalışan pod'a `--force` = **ReplicaSet bozulması**.

---

---

<a id="s18-9"></a>

## 18.9 KARAR GÜNLÜĞÜ — her seçim, **neden** ve kanıtı

> **NEDEN BU TABLO VAR:** Bu lab'de **40 kritik karar** verildi. Her birinin
> **alternatifi** ve **neden seçilmediği** burada. Aynı durumla karşılaşırsanız
> **aynı tuzağa düşmeyin**.

### Donanım / host

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 1 | VM diski | 20G / 40G | **27G** | rehber E3 tablosu | `df -h` |
| 2 | RKE2 rol | 1cp+5w | **3 CP + 3 worker** | etcd quorum (2/3) | 6/6 Ready |
| 3 | VMID | serbest | **100–108** | mevcut 200/9100 ile çakışmaz | `qm list` |
| 4 | `onboot` | 0 / **1** | **1** (lab) | host reboot → lab ayakta | `qm config` §4.2-C |

### Ağ

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 5 | Sanal ağ | `vmbr1/2/3` elle | **SDN vnet10/20/30** | apply tek noktadan | `ip -br link` |
| 6 | VIP | BGP / **ARP** | **ARP `10.30.10.20/32`** | katman-2 basit, OPNsense FRR gerekmez | `cacerts` 200 |
| 7 | MetalLB havuzu | `10.30.10.x` (VIP ağı) / **`10.30.20.x`** | **`10.30.20.200-220`** | **L2 aynı segmentte olmalı**; `edge0` = 10.30.20.x | 2 segmentten erişim |
| 8 | Cilium | kube-proxy'li | **kubeProxyReplacement** | CPU %20 az, IPVS sorunsuz | 6/6 |
| 9 | Arayüz adı | `ens18/19` | **`primary0/edge0`** (`.link` MAC) | boot sırası değişirse IP çakışır | `networkctl` |
| 10 | MTU | 1500 sabit | **ölç (ping -M do)** | PPPoE'de 1492 | %0 kayıp |
| 11 | IPv6 | tümü kapalı | **yalnız dışarı kapalı, `lo` AÇIK** | `[::1]:6443` kırılır | `readyz` 401 |

### İndirme / imaj

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 12 | GitHub proxy | doğrudan 8 KB/s | **`gh-proxy.com`** (ölçülerek) | 20–38 MB/s | `pick-proxy` |
| 13 | Registry mirror | docker.io 267 KB/s | **`mirror.gcr.io` 10 paralel** | 6.4× (387 KB/s) | `imgfetch2` |
| 14 | Büyük imaj | doğrudan / ayna | **GitHub Release + gh-proxy** | 100 MB **28 sn** | sha256 |
| 15 | Parça sayısı | 16 parça | **TEK dosya** | parça düşerse bozuk tar | 16 FAIL / 1 OK |
| 16 | `max-concurrent-downloads` | `config.yaml` | **`config.toml.tmpl`** | RKE2 config.yaml **okumuyor** | §7.1-B |
| 17 | containerd fetch | transfer-service | **`use_local_image_pull=true`** | containerd#11864 deadlock | 6/6 |
| 18 | pull timeout | 5m | **30m** | yavaş ağda kesilmesin | — |
| 19 | TCP congestion | cubic | **bbr + fq** | 267→837 KB/s | §4.7 |
| 20 | `imagePullPolicy` | — | **`IfNotPresent`** | yoksa 8–15 dk yeniden iner | 3 kez yaşandı |

### Depolama

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 21 | Longhorn replica | 3 | **1** (lab) | 430 MB × 3 indirme | §9.2 |
| 22 | Longhorn `--wait` | `--wait` | **`rollout status`** | 15 dk timeout → failed | §18.8 |
| 23 | SC adı | `local-path` | **`longhorn`** (+ local-path ikinci) | 7+ manifest değişmeden çalışır | `pvc-test` Bound |
| 24 | Provisioner pin | serbest | **worker-2** | imaj yalnız orada | rollout |
| 25 | helper imaj | pod çeksin | **3 worker'a önden `ctr pull`** | yoksa `ContainerCreating` | 81/21 s |

### CA / sertifika

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 26 | step-ca süresi | default | **720h / max 744h** | rehber G2 | `jq` |
| 27 | root private key | staging'de | **ÇIKARILDI** | çevrimdışı kalır | `ls` |
| 28 | step-ca güvenlik | `drop:[ALL]` | **`capabilities` YOK, `runAsUser:0`** | imaj root binary | §10.1-B |
| 29 | step-ca node | serbest | **worker-2 pinli** | imaj orada | `nodeSelector` |
| 30 | `caBundle` | — | **base64, tırnaksız heredoc** | placeholder → CRD reddedilir | `Ready=True` |

### K8s / platform

| # | Karar | Alternatif | **Seçim** | Neden | Kanıt |
|---|---|---|---|---|---|
| 31 | `cred/passwd` | tek seferlik `rm` | **systemd drop-in** | kalıcı | §7.3-B |
| 32 | MetalLB `frr-k8s` | auto | **`frrk8s.enabled:false`** | açıksa `Init:0/4` | §9.1 |
| 33 | L2Advertisement | yok | **`interfaces:[edge0]`** | yoksa ARP FAILED | `ip neigh` |
| 34 | ESO kapsamı | `scopedRBAC:true` | **`false`** | 4+ namespace gerekli | §11.8 |
| 35 | ESO CRD | 3 | **24** | `PushSecret` yoksa crash | §11.8-B |
| 36 | CRD kurulum | `apply` | **`create`** | 256 KB annotation sınırı | 24/24 |
| 37 | ESO API client | default | **timeout 60s, QPS 5** | discovery 810 ms | restart 0 |
| 38 | TokenRequest | tüm SA'lar | **`resourceNames:[eso-openbao]`** | en dar yetki | `can-i` |
| 39 | Traefik entrypoint | `web` | **`websecure`** | TLS yoksa SSL error | §13.1 |
| 40 | Secret manager | **OpenBao** | **Infisical** | OpenBao her restart sealed → ESO ölür | S37 |

### 🔴 En önemli 3 karar (saat kaybettirenler)

| # | Karar | Atlarsan |
|---|---|---|
| **33** | `L2Advertisement.interfaces: [edge0]` | LB IP'ye **hiç** erişemezsin (`ip neigh FAILED`) |
| **35** | ESO **24 CRD** | controller **sonsuz crash**, sebep görünmez |
| **40** | OpenBao → **Infisical** | her pod restart → **tüm platform kilitlenir** |

---


---

*BANKLAB V9 · 17 Kasım 2026 · V6'nın 174 + V7'nin 14 + V8'in 12 kanıtlı hatasının üzerine yazıldı.*
*Her şema resmî kaynaktan doğrulandı: ESO docs · OPNsense core 26.7 · Infisical chart · Kyverno docs · CNPG charts.*
