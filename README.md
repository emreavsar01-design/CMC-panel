[README.md](https://github.com/user-attachments/files/27162736/README.md)
# CMC-panel# Kripto Canlı Panel

Cloudflare Worker proxy üzerinden CoinMarketCap API'sini kullanan, gerçek zamanlı kripto para takip paneli.

## Özellikler

- **242 token** — ağ bazında listeleme (aynı token farklı ağlarda ayrı satır olarak görünür)
- **Canlı fiyat verisi** — USD ve TRY karşılığı, 10 dakikada bir otomatik yenileme
- **Değişim oranları** — 1 saatlik, 24 saatlik ve 7 günlük
- **Borsa listelemeleri** — Binance, OKX, Paribu, BTCTurk
- **Durum bilgisi** — Platform - Banka / Platform - Taurus / Platform / Taurus / Delist
- **Filtreleme** — Borsa ve durum bazında chip filtreleri, arama kutusu
- **Sıralama** — CMC sırası, fiyat, değişim, hacim, piyasa değeri
- **CSV Export** — Tüm veriyi Excel'de açılabilir `.csv` formatında indir

## Dosya Yapısı

```
├── index.html               # Ana panel (tarayıcıda açılır)
├── kripto-canli-panel.html  # index.html ile özdeş yedek kopya
└── worker.js                # Cloudflare Worker — API proxy
```

## Kurulum

### 1. Cloudflare Worker

1. [Cloudflare Dashboard](https://dash.cloudflare.com) → **Workers & Pages** → **Create**
2. `worker.js` içeriğini editöre yapıştır
3. **Settings → Variables and Secrets** bölümünde şu secret'ı ekle:

| İsim | Değer |
|------|-------|
| `CMC_API_KEY` | CoinMarketCap Pro API anahtarın |

4. Worker'ı deploy et, URL'yi not al (örn. `https://kripto-proxy.xxx.workers.dev`)

### 2. Panel

`index.html` içindeki `WORKER_URL` satırını kendi Worker URL'inle güncelle:

```js
const WORKER_URL = "https://kripto-proxy.xxx.workers.dev";
```

Dosyayı tarayıcıda aç ya da bir web sunucusuna yükle.

## API Endpoint'leri (Worker)

| Endpoint | Açıklama |
|----------|----------|
| `GET /` | Sağlık kontrolü |
| `GET /cmc/listings?limit=500&convert=USD` | Top N coin listesi |
| `GET /cmc/quotes?symbol=BTC,ETH&convert=USD` | Belirli sembollerin fiyatı |
| `GET /fx/usdtry` | USD/TRY kuru |
| `GET /exchanges/listings` | Binance / OKX / Paribu / BTCTurk listeleri (6 saat cache) |

## Token Listesi

Token verileri `TOKEN LIST.xlsx` dosyasından türetilmiştir. Her satır şu alanları içerir:

- `sym` — Sembol (BTC, ETH, …)
- `name` — Token adı
- `network` — Ağ (Ethereum, Solana, BNB Smart Chain, …)
- `status` — Durum (Platform - Banka / Platform - Taurus / Platform / Taurus / Delist)

## CSV Export Formatı

| Sütun | Açıklama |
|-------|----------|
| Sembol | Token sembolü |
| Ad | Token adı |
| Ağ | Listelendiği ağ |
| Durum | Platform - Banka / Taurus / Delist vb. |
| Fiyat USD / TRY | Anlık fiyat |
| Değişim 1s / 24s / 7g % | Yüzdesel değişim |
| 24s Hacim USD | 24 saatlik işlem hacmi |
| Piyasa Değeri USD | Toplam piyasa değeri |
| CMC Sırası | CoinMarketCap sıralaması |
| Binance / OKX / Paribu / BTCTurk | Listeleme durumu (Evet / Hayır) |

## Gereksinimler

- Cloudflare hesabı (ücretsiz plan yeterli)
- [CoinMarketCap Pro API](https://pro.coinmarketcap.com/) anahtarı (ücretsiz Basic plan: 333 istek/gün)
- Modern bir tarayıcı
