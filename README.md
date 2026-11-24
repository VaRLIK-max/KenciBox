# 🔒 KenciBox

**Windows 11 (x64) için Sıfır Bağımlılıklı, Hızlı ve Güvenli Oyun Kasası & Sandbox Başlatıcısı**

![Platform](https://img.shields.io/badge/Platform-Windows%2011%20x64-0078D4?style=flat-square&logo=windows)
![Runtime](https://img.shields.io/badge/.NET-8.0%20WPF%20(Self--Contained)-512BD4?style=flat-square&logo=dotnet)
![Isolation](https://img.shields.io/badge/Sandbox-AppContainer%20%2B%20Windows%20Sandbox-10B981?style=flat-square)
![Privileges](https://img.shields.io/badge/Yetki-asInvoker%20(Y%C3%B6netici%20Gerekmez)-2563EB?style=flat-square)
![Release](https://img.shields.io/badge/S%C3%BCr%C3%BCm-v1.0.1-38BDF8?style=flat-square)

**KenciBox**, özellikle **itch.io** ve bağımsız (indie) kaynaklardan indirilen `.exe` oyunları ve uygulamaları, ana bilgisayarınıza (Masaüstü, Belgeler, tarayıcı verileri, `%APPDATA%`, yerel ağ ve pano) erişemeden güvenle oynayabilmeniz için geliştirilmiş modern bir **Windows 11 Oyun Kasası ve İzolasyon Başlatıcısıdır**.

Üçüncü parti sandbox sürücüsü, servis kurulumu veya yönetici (`UAC`) yetkisi gerektirmez; doğrudan Windows'un yerel çekirdek izolasyon mekanizmalarını (**AppContainer**, **Job Object**, **NTFS ACL** ve isteğe bağlı **Windows Sandbox Hyper-V VM**) kullanır.

---

## ✨ Öne Çıkan Özellikler

### 🛡️ 1. İki Katmanlı İzolasyon Mimarisi
| Özellik | ⚡ Hızlı Mod (AppContainer — Varsayılan) | 🧊 Tam Kilit Modu (Windows Sandbox `.wsb`) |
| :--- | :--- | :--- |
| **Teknoloji** | Windows Yerel AppContainer + Job Object + Mitigation | İzole Hyper-V Sanal Makinesi (Windows Sandbox) |
| **GPU / FPS Performansı** | Tam donanım hızlandırmalı DirectX / Vulkan | vGPU destekli izole sanal makine |
| **İnternet / Ağ Erişimi** | **Varsayılan KAPALI** (`CapabilityCount = 0`) | **KAPALI** (`<Networking>Disable</Networking>`) |
| **Disk Erişimi** | Yalnızca seçilen oyun klasörü (`ReadAndExecute` veya sınırlı `Modify`) | Oyun klasörü salt-okunur (`ReadOnly=true`) eşlenir |
| **Pano & Aygıtlar** | Pano okuma/yazma + UI kanca/mesaj engeli | Pano, mikrofon, kamera, yazıcı kapalı + `ProtectedClient` |
| **Açılış Hızı** | Anında (milisaniye düzeyinde) | ~5–10 sn (tam VM önyüklemesi) |

### 🔐 2. Oyun Kasası & `.kbox` Çift-Tıklama Koruması
- Oyun klasörünü veya `.exe` dosyasını pencereye **sürükleyip bırakın**; KenciBox gerçek PE mimarisini (`x64`/`x86`), sembolik bağlantıları (`ReparsePoint`) ve **NTFS hardlinklerini** (`nNumberOfLinks > 1`) tarayıp ikonuyla birlikte kütüphaneye ekler.
- **`.kbox` Çift-Tıklama Kilidi:** Oyunu diskte `Oyun.exe.kbox` olarak kilitler; dosya gezgininden yanlışlıkla çift tıklansa bile çalışmaz. Yalnızca KenciBox üzerinden başlatıldığında geçici olarak açılır, oturum boyunca konak salt-okunur kilidiyle (`FileShare.Read`) korunur ve oyun kapandığında (veya beklenmedik çökmede) otomatik yeniden kilitlenir.
- **Acil Kurtarma:** Ayarlar sekmesindeki **Tüm `.kbox` Kilitlerini Aç** butonuyla tüm oyunlar tek tıkla eski haline döndürülebilir.

### 🧪 3. Canlı 4/4 Güvenlik Doğrulaması (`--selftest`)
Uygulama içindeki **Güvenlik Testi** sekmesinden KenciBox'ın kendisini gerçek bir sıfır-yetkili AppContainer içinde çalıştırarak şu 4 katmanı canlı kanıtlayabilirsiniz:
1. **Ağ / Loopback Engeli:** `127.0.0.1` yerel dinleyicisine bağlantının çekirdek tarafından `WSAEACCES (10013)` ile engellenmesi.
2. **Kullanıcı Dizini Okuma Engeli:** Masaüstü ve `%LOCALAPPDATA%` okumasının `UnauthorizedAccessException` ile reddedilmesi.
3. **Dış Dizin Yazma Engeli:** Ana `%TEMP%` ve Masaüstü yazımının engellenmesi.
4. **Pano & Ortam Yalıtımı:** Pano okuma/yazmanın `ERROR_ACCESS_DENIED (5)` ile reddedilmesi ve hassas ortam değişkenlerinin gizlenmesi (`USERNAME=SandboxUser`).

### 🧹 4. Akıllı Süreç Takibi & Çökme Kurtarma
- Launcher → asıl oyun (Unreal/Unity vb.) geçişlerini canlı takip eder; ana süreç kapandıktan sonra geride kalan penceresiz arka plan yardımcılarını otomatik sonlandırır.
- Geçici AppContainer profilleri, NTFS ACL izinleri ve `.kbox` kilitleri `WriteThrough` + `.bak` yedekli atomik kurtarma kaydı (`recovery.json`) ile korunur; ani elektrik kesintisi veya çökme sonrası sonraki açılışta otomatik temizlenir.
- Antivirüs (Bitdefender, Windows Defender vb.) ile tam uyumludur; zararlı yazılımları antivirüsten gizlemez, standart dosya taramasına açık tutar.

---

## 📦 Kurulum ve Kullanım

1. [**Releases (v1.0.1)**](../../releases/tag/v1.0.1) sayfasından **`KenciBox-v1.0.1-win-x64.zip`** dosyasını indirin.
2. Arşivi dilediğiniz bir klasöre çıkarın ve **`KenciBox.exe`** dosyasını çalıştırın (kurulum veya .NET Runtime indirmeniz gerekmez).
3. Herhangi bir `.exe` dosyasını veya oyun klasörünü KenciBox penceresine sürükleyip bırakın.
4. Sağdaki denetçiden çalışma modunu (**Hızlı Mod** veya **Tam Kilit**), **`.kbox` Çift-Tıklama Koruması**'nı ve gerekirse sınırlı yazma/ağ ayarlarını seçip **Başlat**'a tıklayın.

---

## 🔍 Güvenlik Denetimi ve Sınırlar

KenciBox, bağımsız kırmızı/mavi takım güvenlik denetiminden geçmiş; Mod B `.wsb` enjeksiyon/hijack, NTFS hardlink ACL yayılımı, UNC/ADS yol atlatmaları, TOCTOU yarış koşulları ve çökme kurtarma uç durumları kapatılarak **12/12 AV-güvenli güvenlik testi** ve **35/35 regresyon testi** ile doğrulanmıştır.

Ayrıntılı yama tablosu ve Windows kullanıcı-modu (Mod A) doğal sınırları için [SECURITY_AUDIT_REPORT.md](SECURITY_AUDIT_REPORT.md) dosyasına bakabilirsiniz.

---

## 💻 Sistem Gereksinimleri
- **İşletim Sistemi:** Windows 11 (64-bit / `x64`)
- **Yetki:** Standart kullanıcı (`asInvoker` — Yönetici izni gerekmez)
- **İsteğe Bağlı:** Mod B (Tam Kilit) için Windows Pro/Enterprise üzerinde *Windows Sandbox* özelliğinin açık olması gerekir (Mod A tüm Windows 11 sürümlerinde ek özellik gerekmeden çalışır).

## 🛠️ Kaynaktan Derleme

```powershell
dotnet publish KenciBox.csproj -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:PublishReadyToRun=true -p:EnableCompressionInSingleFile=false -p:IncludeNativeLibrariesForSelfExtract=true
```
