# Vega IPTV — iOS ve Android

Masaüstü sürümünün mobil karşılığı. Tek kod tabanı, iki platform.

Uygulama kanal listesi içermez. Kendi yasal aboneliğinizin M3U bağlantısını
veya Xtream Codes bilgilerinizi eklemeniz gerekir.

## Oynatma motoru

Görüntüyü **libmpv** çözer; paket uygulamayla birlikte gelir, telefona
ayrıca bir şey kurulmaz. Bu, masaüstü sürümündeki FFmpeg motorunun mobil
karşılığıdır ve önemli bir pratik sonucu var: iPhone'un yerleşik oynatıcısı
ham MPEG-TS akışlarını (Xtream panellerinin `.ts` adresleri) açamaz, libmpv
açar. Böylece HLS vermeyen sağlayıcılarda da kanallar çalışır.

## iPhone'a nasıl kurulur

Windows exe'sinde olduğu gibi, iOS uygulaması da yalnızca macOS'ta
derlenebilir. Ama Mac'iniz olmadan da kurabilirsiniz: derlemeyi bulutta
yapıp çıkan dosyayı Windows bilgisayarınızdan telefona yüklersiniz.

### 1. Uygulamayı bulutta derleyin

1. Bu klasörü GitHub'a yükleyin (yeni bir depo oluşturup dosyaları atın).
2. Depoda **Actions** sekmesine gidin.
3. Soldan **Uygulama paketi** iş akışını seçin, **Run workflow** deyin.
4. Yaklaşık 10 dakika sonra sayfanın altındaki **Artifacts** bölümünden
   `VegaIPTV-ios-imzasiz` dosyasını indirin. İçinden `VegaIPTV.ipa` çıkar.

Kendi bilgisayarınıza Flutter, Xcode ya da Android Studio kurmanız gerekmez.

### 2. Telefona yükleyin

Apple imzasız uygulama çalıştırmaz. İki yol var:

**Ücretsiz — Sideloadly** (Windows bilgisayar gerekir)

1. `sideloadly.io` adresinden Sideloadly'yi Windows'a kurun.
2. iPhone'u USB ile bağlayın.
3. `.ipa` dosyasını pencereye sürükleyin, Apple ID'nizi girin, Start deyin.
4. iPhone'da **Ayarlar > Genel > VPN ve Cihaz Yönetimi** bölümünden
   kendi Apple ID'nize güvenin.

Ücretsiz Apple ID ile imzalanan uygulamalar **7 gün** çalışır, sonra
yenilemek gerekir; aynı anda en fazla 3 yan yüklü uygulama tutulabilir.
AltStore kurarsanız yenilemeyi Wi-Fi üzerinden otomatik yapar.

**Ücretli — Apple Developer hesabı** (99 dolar/yıl)

Sertifika 1 yıl geçerli olur ve 3 uygulama sınırı kalkar. TestFlight'a
yüklerseniz telefona doğrudan kurulur, bilgisayara hiç gerek kalmaz.

### Android (en hızlı yol)

1. github.com'da hesap açın, **New repository** ile yeni bir depo
   oluşturun (Private seçebilirsiniz).
2. **uploading an existing file** bağlantısına tıklayın, bu klasörün
   içindeki her şeyi sürükleyip bırakın, **Commit changes** deyin.
3. Derleme kendiliğinden başlar. **Actions** sekmesinden ilerlemeyi
   görebilirsiniz, yaklaşık 7 dakika sürer.
4. Bitince deponun sağ tarafındaki **Releases** bölümünde
   *Android (son derleme)* görünür. Bu sayfayı **telefonunuzun
   tarayıcısından** açın ve `app-release.apk` dosyasına dokunun.
5. "Bilinmeyen kaynak" uyarısını onaylayın. Kurulum biter.

Android tarafında imzalama derdi ve süre sınırı yok; uygulama kalıcıdır.

Not: `.github` klasörü gizli olduğu için sürükle-bırak sırasında
görünmeyebilir. Görünmüyorsa Windows'ta Gezgin > Görünüm > Gizli öğeler
seçeneğini açın; bu klasör olmadan derleme başlamaz.

## Neler yapabilir

- M3U bağlantısı ve Xtream Codes hesabı ekleme, birden çok liste
- Kanal arama (Türkçe harflere duyarlı), kategori süzme, favoriler
- XMLTV yayın akışı: kanal satırında o an oynayan program
- Kanal logoları, liste önbelleği (uygulama anında açılır)
- Tam ekran, yatay mod, önceki/sonraki kanal
- Ekran kapalıyken sesin devam etmesi
- İzlerken ekranın sönmemesi

## TV'de izleme

**AirPlay:** Denetim Merkezi > Ekran Yansıtma > Apple TV veya AirPlay
destekli televizyon. Görüntü ve ses TV'ye gider, kod gerekmez.

**Chromecast:** Henüz yok. Uygulama içi AirPlay seçici de yok; yansıtma
şimdilik Denetim Merkezi üzerinden yapılıyor. İkisi de eklenebilir, ayrı
bir iş.

## Ayarlar

**Xtream için HLS** açıkken kanallar `.m3u8` olarak istenir; mobil ağlarda
daha kararlıdır. Bazı paneller yalnızca `.ts` verir — kanallar açılmıyorsa
bu ayarı kapatın.

**Arka planda ses** ekran kapandığında sesin devam etmesini sağlar.

**Tarayıcı kimliği** yalnızca bir HTTP başlığıdır; sağlayıcınız belirli bir
değer istiyorsa buraya yazın.

## Proje yapısı

```
lib/main.dart                 Giriş, ses oturumu kurulumu
lib/models/models.dart        Channel, Source, Programme
lib/services/playlist.dart    M3U ayrıştırıcı, Xtream istemcisi
lib/services/epg.dart         XMLTV ayrıştırıcı
lib/services/library.dart     Durum: kaynaklar, favoriler, süzme, önbellek
lib/ui/home_page.dart         Kanal listesi, arama, kategoriler
lib/ui/player_page.dart       Oynatıcı ve kontroller
lib/ui/sources_page.dart      Liste ekleme ve yönetme
lib/ui/settings_page.dart     Ayarlar
lib/ui/theme.dart             Koyu tema
tool/patch_ios.sh             Info.plist ayarları (http izni, arka plan ses)
tool/patch_android.sh         Manifest ayarları (internet izni, http)
.github/workflows/paket.yml   Bulutta .ipa ve .apk üretimi
```

`ios/` ve `android/` klasörleri depoda tutulmaz; derleme sırasında
`flutter create` ile üretilir, sonra yukarıdaki betikler ayarları uygular.
Böylece Xcode proje dosyalarını elle taşımak gerekmez.

## Bilinmesi gerekenler

Bu kod **derlenmiş olarak test edilmedi.** Masaüstü sürümünü burada
çalıştırıp deneyebiliyordum; Flutter için gereken araçlar bu ortamda yok.
Kod yapısal olarak denetlendi, ayrıştırıcı mantığı ayrıca test edildi ve
paket sürümleri belgelerden doğrulandı — ama ilk derlemede küçük düzeltmeler
gerekebilir. Actions sayfası hatayı satır numarasıyla gösterir; o çıktıyı
paylaşırsanız düzeltilir.

Yerelde çalıştırmak isterseniz (Flutter kuruluysa):

```
flutter create --platforms=ios,android --org com.vega --project-name vega_iptv /tmp/iskelet
cp -R /tmp/iskelet/ios ./ios && cp -R /tmp/iskelet/android ./android
bash tool/patch_ios.sh && bash tool/patch_android.sh
flutter pub get && flutter run
```

## Not

Bu uygulama yalnızca bir oynatıcıdır. İzlediğiniz yayınların kaynağı ve
yasallığı sizin sorumluluğunuzdadır.
