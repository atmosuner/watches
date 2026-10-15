# Handoff: Saat Defterim — arayüz yeniden tasarımı

## Overview
`atmosuner/watches` reposundaki tek dosyalık `index.html` (kişisel saat kataloğu, 57 saat) için yeni arayüz. Veri ve davranışlar korunuyor; yalnızca arayüz, düzen ve görsel tasarım değişiyor. Dil Türkçe, mobil öncelikli, açık/koyu tema.

## About the Design Files
`Saat Defterim.html` HTML ile yazılmış bir **tasarım referansıdır** — ama bu proje zaten build adımı olmayan tek bir statik HTML dosyası olduğu için, prototip vanilla HTML/CSS/JS ile yazıldı ve doğrudan üretim koduna dönüştürülmeye çok yakındır. Hedef: repodaki `index.html`'i bu tasarımla değiştirmek, **veri bloğunu mevcut `index.html`'den aynen alarak**. Framework eklenmemeli; tek dosya, bağımlılıksız kalmalı (yalnızca Google Fonts).

## Fidelity
**High-fidelity.** Renkler, tipografi, boşluklar ve etkileşimler nihai. Birebir uygulanmalı.

## Görev (Claude Code için kısa yol)
1. Repodaki mevcut `index.html`'in `<script>` bölümünden şu veri tanımlarını **değiştirmeden** al: `W, CATS, SITES, TARGET, MICRO, META, COLS, BUD, IMGQ, COLEN, LINKS, IMGS, ABOUT, TECH, SPEC, SEED`.
2. `Saat Defterim.html`'in `<head>` + `<body>` işaretlemesini ve `<style>`'ını yeni `index.html` olarak kullan.
3. Prototip veriyi `window.SD` üzerinden (`watches-data.js`) okuyor. Tek dosya için: `<script src="watches-data.js">` satırını kaldır, yerine mevcut veri tanımlarını koy ve en sonda `window.SD = {W:W, CATS:CATS, SITES:SITES, META:META, COLS:COLS, BUD:BUD, LINKS:LINKS, IMGS:IMGS, ABOUT:ABOUT, TECH:TECH, SPEC:SPEC, SEED:SEED};` ekle. Uygulama kodu (IIFE) değişmeden çalışır.
4. Eski arayüz kodunu (render, buildRows, renderSheet, renderPuzzle vb.) sil; yenisi IIFE içinde.
5. localStorage anahtarları aynı kalmalı: `saatSecim2` (istek listesi), `saatSahip2` (bende var), `saatNot2` (notlar). Yeni tek anahtar: `saatTema` (auto/light/dark). İlk açılışta `saatSecim2` yoksa `SEED` ile doldurulur (eski davranış).
6. Test: mobil (360 px), tablet (768), masaüstü (1280+); açık ve koyu tema; fotoğraf yüklenemezse kadran çizimi; klavyeyle gezinme.

## Screens / Views
Tek sayfa, üstte yapışkan 3 sekme: **KATALOG · LİSTE · PARÇA**. Sekme altında 3 px vurgu çizgisi.

### Masthead
- Üst satır mono 11.5 px, harf aralığı .07em: `57 SAAT · 8 KATEGORİ · BİLEK 170 MM`
- Başlık "Saat Defterim": Source Serif 4, 600, `clamp(38px, 8vw, 60px)`, line-height 1, altında 2 px ink çizgi.
- Sağda tema düğmesi (Otomatik → Açık → Koyu döngüsü), 40 px yükseklik.
- Lede (muted, max 62ch).

### 1. Katalog
- Arama alanı (46 px) + mobilde "FİLTRE n" düğmesi; masaüstünde (≥900 px) sağda sıralama select'i (220 px) ve filtre paneli her zaman açık.
- Filtre paneli (surface zemin, 1 px çizgi): satır başlığı mono 11 px (masaüstünde 120 px sol sütun). Satırlar: Kategori (sayılarla), Bilek uyumu (Rahat/Sınırda/Büyük/Veri yok), Fiyat USD (BUD anahtarları), Kadran rengi (renk noktalı chip'ler), ardından kesikli çizgi altında Göster (Tüm/İstek listem/Bende var) + Marka select (+ mobilde sıralama).
- Chip: min-height 36, 1 px çizgi, köşesiz; seçili = ink zemin, bg yazı. Aynı chip'e tekrar basmak filtreyi kaldırır.
- Sıralama: Fiyat yüksekten (varsayılan) / düşükten / İsim A–Z / Bileğe en uygun (L2L artan, veri yok sona).
- Izgara: `repeat(auto-fill, minmax(min(100%,290px),1fr))`; kartlar arası çizgiler (üst+sol ızgara kenarı, kartlarda sağ+alt). Mobilde 1, masaüstünde 3–4 sütun.
- **Kart** (flex kolon, eşit yükseklik):
  - Üst satır mono 11.5: `№ 044` (W sırası, 3 hane) · sağda kadran noktası (9 px, `w.dial`) + `META[k][0]` büyük harf.
  - Fotoğraf: 4:3 tepsi, `--photo` zemin, 16 px iç boşluk, `object-fit:contain`, `mix-blend-mode:multiply`. Sol üstte bayrak: "İSTEK LİSTEMDE" (vurgu) veya "BENDE VAR" (koyu).
  - Marka · kategori mono 12 px muted (büyük harf YOK — Türkçe İ sorunu).
  - Model: Source Serif 4 600, 25 px.
  - Karakter: `ABOUT[k][0]`, serif italik 16 px, 3 satır clamp, min-height 3 satır.
  - Ölçü satırı (kesikli üst çizgi, 3 sütun): Ø MM (`SPEC.dia` "mm" atılmış) · L2L MM (uyum rengiyle + alt etiket) · SU (`wr`).
  - Fiyat mono 14.5 px + varsa "doğrulanmalı" etiketi (mono 10.5, vurgu rengi kenarlık, başta *). Fiyat satırı `margin-top:auto` ile hep alta yaslı.
  - Alt eylemler: 3 eşit hücre, 46 px: İSTEK (kalp, basılıysa dolu, metin LİSTEDE) / BENDE (onay) / KIYASLA (iki sütun ikonu).
- Boş sonuç: "Eşleşen saat yok" + "Filtreleri temizle".

### 2. Detay paneli
- Mobil: alttan sheet, max-height 94dvh, üstte tutamak çubuğu. ≥900 px: sağdan panel, `min(620px, 94vw)`, tam yükseklik. Arka plan rgba(14,13,11,.5).
- Açılış: transform translateY/translateX 100% → 0, .28 s `cubic-bezier(.2,.8,.2,1)`; reduce-motion'da yok.
- Yapışkan üst bar: önceki/sonraki (44 px kare), `n / N` (filtrelenmiş listeye göre), kapat.
- İçerik sırası: 4:3 büyük fotoğraf (tıkla → tam ekran zoom, `bigUrl()` ile yüksek çözünürlük) · `№ · Marka` / kategori · model serif 28–36 px · fiyat mono 17 + doğrulanmalı · Kadran: nokta + `w.dn` · 3 düğme (İstek listesi / Bende var / Kıyasla) · Resmî site ↗ (`LINKS[k] || SITES[w.b]`; SNKL23K için "Satıcı").
- **17 cm bileğe uyum**: 18 çubuk (38–56 mm, 1 mm/çubuk), ≤46 yeşil, ≤48 sarı, üstü kırmızı; ölçünün çubuğu tam boy ink. Eksen etiketleri 38/46/48/56. Metin: "Rahat. / Sınırda. Almadan önce bilekte dene. / Büyük." + L2L değeri. L2L yoksa çubuk gizli, yerine kesikli notta açıklama.
- **Hakkında**: Karakter (serif italik 18), Koleksiyondaki yeri, Dikkat edilecekler — boş olanlar gösterilmez.
- **Teknik bilgiler** (`SPEC` + `TECH` birleşimi, TECH öncelikli): 6 grup başlığı mono "01 KASA … 06 KAYIŞ / BİLEZİK"; satırlar iki sütun (etiket %38 / değer). Bilinmeyen "—". Başlıkta "x / 13 alan"; yarıdan fazlası eksikse uyarı notu.
- **Notlarım**: textarea, her girişte `saatNot2`'ye kaydeder, "Kaydedildi" geri bildirimi.
- **Aynı kategoride**: fiyatı en yakın 3 saat.

### 3. Liste ve karşılaştırma
- Karşılaştırma tablosu (2a): kolon sayısı = max(2, seçili); boş kolon çizgili "Bir saat daha ekle". Masaüstü (≥760): 160 px etiket sütunu + saat sütunları. Mobil: etiket satırı tam genişlik, değerler altında N sütun — **yatay kaydırma yok**.
- Başlık hücresi: kare fotoğraf, marka, model (tıkla → detay), sağ üstte × (karşılaştırmadan çıkar).
- Gruplar: GENEL (kategori, fiyat — en düşük alt çizgi vurgusu, kadran), 01 KASA (malzeme, çap, kalınlık, L2L + uyum etiketi), 02 KADRAN, 03 BEZEL VE CAM, 04 MEKANİZMA, 05 SU GEÇİRMEZLİK, 06 KAYIŞ / BİLEZİK.
- Altında "İSTEK LİSTEM" ve "BENDE VAR" satır listeleri (toplam fiyat aralığı özetiyle). Satır: 64–72 px kare foto · ad · (masaüstünde Ø / L2L / Su / Fiyat sütunları) · Kıyasla + Çıkar düğmeleri (44 px).
- Karşılaştırma seti en fazla 4 (aşılırsa toast). Bellekte tutulur (kalıcı değil).
- **Alt tepsi**: karşılaştırmada ≥1 saat varken ve Liste sekmesinde değilken sabit alt bar (ink zemin): 4 slot küçük foto, "YAN YANA GÖR n/4" (2'den az ise "1 SAAT DAHA SEÇ", pasif), temizle ×.

### 4. Koleksiyonum (Parça)
- Sayaç serif 44 px "n / 8 PARÇA" + 8 bölmeli ilerleme çubuğu.
- 8 kategori = 8 gerçek jigsaw parça (mevcut `pzEdge`/`pzPath` mantığı, `clipPath objectBoundingBox`). <600 px: 2 sütun, aksi 4 sütun.
- Dolu parça: foto tepsisi zemin, saatin fotoğrafı, 2 px ink kontur, altta etiket kutusu (kategori + saat adı, +n). Boş: kesikli kontur, "+", kategori, "n aday listede" / "Boş parça".
- Dolu parçaya tıklama → detay; boş → katalog o kategoriyle filtrelenir.
- 8/8: konturlar vurgu rengine döner, altında "Koleksiyon tamam" kutusu (damga "8 / 8", hafif giriş animasyonu, reduce-motion'da yok). "Bende var" basıldığında toast: "<kategori> parçası yerine oturdu".

## Interactions & Behavior
- İstek listesi ve Bende var karşılıklı dışlayıcı (eski davranış): birini işaretlemek diğerini kaldırır.
- Yeniden çizim sonrası odak aynı düğmeye geri döner (`focusSig/refocus`).
- Klavye: Esc detayı/zoom'u kapatır; detayda ←/→ önceki/sonraki; Tab panel içinde döngüde.
- Fotoğraf hatası: `onerror` → `dialSVG(w)` çizimi + "FOTOĞRAF YÜKLENEMEDİ" etiketi; zoom devre dışı.
- `prefers-reduced-motion`: tüm geçiş/animasyonlar kapalı.
- Arama: marka+model+kategori+kalibre+kadran adında, Türkçe küçük harf karşılaştırma, 120 ms debounce.

## State
`view` (katalog|liste|koleksiyon), `F` {q, cat, brand, budget, col, fit, only}, `sort`, `sel`, `own`, `cmp[]`, `cur` (açık detay anahtarı), `shown[]` (detay gezinmesi için filtrelenmiş sıra), `theme`.

## Design Tokens
Açık tema:
- bg `#efede8` · surface `#f7f5f1` · raise `#fbfaf7` · ink `#1b1a18` · muted `#5c5851` · faint `#837e75`
- line `#c9c4ba` · line2 `#dcd8cf` · photo tepsisi `#e6e3dc`
- vurgu (sinyal turuncusu) `#a44e1b`, yumuşak `rgba(164,78,27,.10)`
- uyum metin: ok `#4f6e3f` · mid `#8a6a17` · big `#a2463a`; çubuk: `#9aa58f` / `#cdb88f` / `#cbaaa2`

Koyu tema:
- bg `#161513` · surface `#1d1c19` · raise `#22211e` · ink `#ebe7df` · muted `#aaa59a` · faint `#857f75`
- line `#3b3833` · line2 `#2c2a26` · photo `#e6e3dc` (ürün fotoğrafları beyaz zeminli olduğu için açık kalır)
- vurgu `#e8955c` (üzerinde yazı `#1b1a18`) · ok `#9dbb88` · mid `#dcbb66` · big `#e8968a`; çubuk `#6d8061` / `#9c8857` / `#94635b`

Tipografi:
- Başlıklar: Source Serif 4 (600; italik 400 karakter cümleleri için)
- Gövde: IBM Plex Sans 400/500/600, 15 px / 1.5
- Veri, etiketler: IBM Plex Mono 400/500 (10.5–17 px; etiketlerde harf aralığı .04–.07em)

Diğer: köşe yarıçapı 0 (tool-watch hissi), gölge yalnızca panel/toast `0 18px 50px rgba(20,18,15,.18)`, odak halkası 2 px vurgu + 2 px offset, dokunma hedefleri ≥44 px, kenar boşluğu mobil 16 px / ≥720 px 32 px, max içerik genişliği 1320 px.

Kadran rengi aileleri (filtre noktaları): Siyah `#141516`, Beyaz/krem `#efeadf`, Gri/gümüş `#9aa0a6`, Mavi `#2a4f86`, Yeşil `#3e5b45`, Bordo/kırmızı `#8e2430`, Turuncu/sarı/şampanya `#d9a13a`, Diğer iki tonlu.

## Assets
- Fotoğraflar: `IMGS` içindeki dış adresler (marka/bayi siteleri), `referrerpolicy="no-referrer"`, lazy.
- İkonlar: satır içi basit SVG (kalp, onay, iki sütun, ok, büyüteç, ×).
- Fallback kadran çizimi: mevcut `dialSVG` mantığı (bezel, sq, oct, acc alanlarını kullanır).

## Files
- `Saat Defterim.html` — tam prototip (tüm görünümler ve davranışlar).
- `watches-data.js` — mevcut `index.html`'den çıkarılmış veri (`window.SD`), değiştirilmemiş. Üretimde repodaki orijinal veri bloğu esas alınmalı.
