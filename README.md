# Clip Prep Tools

YouTube otomasyon pipeline'ım için arka plan video kliplerini ping-pong loop formatına dönüştüren araç.

## Ne İşe Yarar?

Stock video sitelerinden (Pixabay, Pexels, vb.) bulduğum kısa klipleri sonsuz döngüde dikişsiz oynayacak hale getirir. Sonuç klip Drive'a yüklenir ve ana pipeline tarafından arka plan olarak kullanılır.

## Ping-Pong Mantığı

Normal loop: `1→2→3→4→5 | 1→2→3→4→5 | ...`  
Her birleşim noktasında ani sıçrama görünür (5'ten 1'e zıplama).

Ping-pong loop: `1→2→3→4→5→4→3→2→1 | 1→2→3→4→5→4→3→2→1 | ...`  
Klip ileri oynar, sonra geri sarar, sonra tekrar ileri. Birleşim noktalarında aynı frame iki kez geçer (5→5 ve 1→1) ama gözle algılanmaz. Sonuç: sonsuz akan, dikişsiz video.

## Kullanım

1. Pixabay/Pexels'te beğendiğin klibin sayfa URL'ini kopyala
2. Bu repo'da → **Actions** sekmesi → sol menüden **Clip Ping-Pong Hazırlayıcı**
3. Sağ üstte **Run workflow** butonu
4. Formu doldur:
   - **video_url**: Yapıştırdığın link
   - **output_name** (opsiyonel): Dosya adı, örn. `zen_ocean_03`. Boş bırakırsan otomatik isim verir.
5. **Run workflow** tıkla, ~2-3 dk bekle
6. Yeşil tik gelince → run sayfasına gir → **en alta in** → **Artifacts** bölümünden `pingpong-clip` ZIP'ini indir
7. ZIP içinden `.mp4` dosyasını çıkar
8. İlgili kanalın Drive klasörüne yükle (aşağıya bak)

## Desteklenen Kaynaklar

yt-dlp tabanlı, çoğu video sitesini destekler:

- ✅ Pixabay (sayfa URL'i veya direkt CDN linki)
- ✅ Pexels
- ✅ YouTube (ama telif riski → sadece Creative Commons / kendi videoların)
- ✅ Direkt `.mp4` URL'leri
- ✅ Vimeo, Dailymotion, ve düzinelerce başka site

## İyi Klip Seçim İpuçları

**Süre**: 5-15 saniye ideal. Çok kısa (3 sn altı) tekrarı belli eder, çok uzun (30 sn üstü) reverse adımında RAM tüketir.

**İçerik**:
- ✅ Sürekli, döngüsel hareket: dalgalar, ateş, bulutlar, yağmur, sis, parçacık efektleri
- ✅ Yavaş zoom veya pan
- ✅ Soyut/dokulu içerik (renk geçişleri, ışık huzmeleri, bokeh)
- ❌ Belirgin başlangıç/bitiş olan içerik (insan yürüyor, araç geçiyor)
- ❌ Yazı veya logo içeren klipler (ters gösterilince anlaşılır)
- ❌ Gün doğumu/batımı gibi tek yönlü ışık değişimi (geri sarınca tuhaf görünür)
- ❌ Konuşan/yüzü görünen insan (mimik geri sarılınca rahatsız edici)

**Çözünürlük**: Minimum 1920x1080. Pipeline 1080p'ye encode ediyor, daha düşük source upscale olur ve bulanır.

## Drive Klasör Yapısı

Ana Drive'ında her kanal için bir tane **müzik** klasörü, bir tane **klip** klasörü:

```
Drive/
  ├── coding-music/      (mevcut - Suno müzikleri)
  ├── coding-clips/      (yeni - ping-pong klipler)
  ├── zen-music/
  ├── zen-clips/
  ├── vault-music/
  ├── vault-clips/
  ├── chakra-music/
  ├── chakra-clips/
  ├── beach-music/
  ├── beach-clips/
  ├── summer-music/
  ├── summer-clips/
  ├── pets-music/
  ├── pets-clips/
  ├── breathe-music/
  └── breathe-clips/
```

**Klip stoku hedefi**: Her `*-clips/` klasöründe **minimum 20-30 klip**. Daha az olursa videolar arasında tekrar belirginleşir. Düzenli olarak yeni klip eklemeye devam et.

## Kanal Başına Klip Stili Önerileri

| Kanal | İçerik teması | Pixabay/Pexels arama önerileri |
|-------|---------------|-------------------------------|
| **coding** | Lofi, çalışma odası havası | `rain window`, `coffee steam`, `desk lofi`, `cyberpunk city night`, `keyboard typing close` |
| **zen** | Doğa, sakin, ruhani | `forest mist`, `mountain clouds`, `temple incense`, `bamboo wind`, `meditation candle` |
| **vault** | Dark, hard-hitting, sigma | `dark city night`, `gym lifting`, `motivational fire`, `wolf snow`, `lone figure rain` |
| **chakra** | Mistik, frekans, healing | `mandala spinning`, `crystal glow`, `aurora abstract`, `sacred geometry`, `light particles` |
| **beach** | Tropikal, yaz, parti | `palm tree wind`, `tropical waves`, `pool sunset`, `beach party`, `tropical drink` |
| **summer** | Şık, lounge, sunset | `rooftop sunset`, `infinity pool`, `mediterranean coast`, `golden hour villa` |
| **pets** | Sıcak, hayvanlar | `cat sleeping`, `puppy playing`, `aquarium fish`, `kitten close up` |
| **breathe** | Sakin, minimalist, nefes | `slow waves`, `cloud time lapse`, `gentle ripple water`, `soft fog` |

## Çıktı Özellikleri

- Codec: H.264 (libx264)
- Kalite: CRF 18 (görsel olarak kayıpsız sayılır)
- Ses: kaldırıldı (-an)
- Format: MP4, yuv420p (her player'da oynar)
- Süre: orijinalin 2 katı (örn. 10 sn klip → 20 sn ping-pong)

## Sınırlar / Dikkat

- **30 sn üstü kliplere dikkat**: Reverse filter tüm video stream'ini RAM'e alır, GitHub Actions runner'larında ~2GB bellek var. Çok uzun klip OOM verebilir. 15-20 sn altında tut.
- **Telifli içerik**: yt-dlp YouTube'dan da indirir ama YouTube'a yüklersen Content ID yer. Sadece free stock kaynaklarını kullan.
- **Artifact saklama süresi**: 7 gün. Bu süre içinde indirip Drive'a yüklemen lazım, sonra GitHub siler.
- **Public repo**: Bu repo public olduğu için Actions dakikası ücretsiz/sınırsız. Private yapma.

## Ana Pipeline ile İlişki

Bu repo bir **prep tool** — sadece klip hazırlar, hiçbir yere upload yapmaz, hiçbir API key gerekmez.

İş akışı:
1. Burada (clip-prep-tools): klip indir → ping-pong → ZIP indir
2. Lokal: ZIP'i aç, varsa minor düzenleme yap
3. Drive: ilgili kanalın `*-clips/` klasörüne yükle
4. Ana pipeline (yt1saatoto): video oluştururken Drive'dan rastgele klip çeker

## Lokal Test (Opsiyonel)

GitHub Actions'tan başka, klibi lokal makinende de işlemek istersen (yt-dlp ve ffmpeg yüklü olmalı):

```bash
# Klibi indir
yt-dlp -f "bv*[ext=mp4]/b[ext=mp4]/b" -o "input.%(ext)s" "VIDEO_URL"

# Ping-pong oluştur
ffmpeg -i input.mp4 \
  -filter_complex "[0:v]split[a][b];[b]reverse[r];[a][r]concat=n=2:v=1[out]" \
  -map "[out]" -c:v libx264 -preset fast -crf 18 -pix_fmt yuv420p -an \
  pingpong.mp4
```

## TODO

- [ ] Drive'a 8 kanalın `*-clips/` klasörlerini oluştur
- [ ] Her kanal için minimum 20 ping-pong klip topla
- [ ] Ana pipeline'da `pixabay_handler` yerine `drive_handler` clip fetcher implementasyonu
