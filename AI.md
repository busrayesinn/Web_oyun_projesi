**Prompt:**
> Karakterimin Canvas sınırlarında kalmasını nasıl sağlarım?

**Yanıt:** ChatGPT:
Karakterinin Canvas sınırları içinde kalmasını sağlamak için, hareket ettirmeden önce yeni konumun sınırlar içinde olup olmadığını kontrol etmeliyiz.
🧠 Nasıl yapılır?
Her hareket komutunda, karakterin yeni konumu şu kuralları geçmemeli:
Soldan dışarı çıkamaz → karakterX >= 0
Sağdan dışarı çıkamaz → karakterX + karakterGenislik <= canvas.width
Yukarıdan dışarı çıkamaz → karakterY >= 0
Aşağıdan dışarı çıkamaz → karakterY + karakterYukseklik <= canvas.height
Örneğin:

```js 
if (e.key === "ArrowRight" && karakterX + kareBoyutu < canvas.width)
Bu, sağa hareket etmeden önce karakterin sağ kenarının canvas sınırını aşmayacağından emin olur.
```

**Prompt:**
> Karakterimin engelden geçmemesini nasıl sağlarım?

**Yanıt:**ChatGPT:
Aşağıdaki örnek satırlarla karakterinizin engelden geçmesini engelleyebilirsiniz:

```js 
if (!engelVarMi(yeniX, yeniY)) {
  karakterX = yeniX;
}
```
Burada `engelVarMi(x, y)` fonksiyonu, hareket edilecek konumda bir engel olup olmadığını kontrol eder. Eğer engel yoksa karakter hareket eder. Bu kontrolü `ArrowRight`, `ArrowLeft`, `ArrowUp`, `ArrowDown` gibi tuş olaylarının içinde uygularsınız.

**Prompt:**
> Bir 3x3 hücreli oyun haritam var. Her büyük karo (tile), bu 3x3'lük hücrelerden oluşuyor ve karakterim bu alt hücreler arasında dolaşıyor. Karakter bir büyük karodan çıkınca, o karonun engellerini bir azaltmak istiyorum. Ama karakter sadece farklı bir karoya geçtiğinde azalsın, aynı karonun içinde dolaşırken değil. Bunu nasıl kontrol ederim?

**Yanıt:**ChatGPT:
Bu durumu kontrol etmek için, karakterin hangi büyük karoda (örneğin 180x180’lik bir alan) olduğunu takip etmen gerekir. Bunun için, karakterin mevcut bulunduğu karoyu ve bir önceki bulunduğu karoyu ayrı ayrı hesaplamalı ve karşılaştırmalısın.
Aşağıdaki örnek yaklaşımı kullanabilirsin:

```js
const TILE_SIZE = 180;
let prevRow = Math.floor(playerY / TILE_SIZE);
let prevCol = Math.floor(playerX / TILE_SIZE);

function handleMovement(newX, newY) {
  const newRow = Math.floor(newY / TILE_SIZE);
  const newCol = Math.floor(newX / TILE_SIZE);

  // Sadece farklı bir karoya geçildiyse dayanıklılığı azalt
  if (newRow !== prevRow || newCol !== prevCol) {
    let oldTile = map[prevRow][prevCol];
    if (typeof oldTile === "number" && oldTile > 0) {
      map[prevRow][prevCol]--;  // Karo seviyesi bir azalır
    }
  }

  // Konumu güncelle
  playerX = newX;
  playerY = newY;
  prevRow = newRow;
  prevCol = newCol;
}
```

**Prompt:**
> Oyunumda engeller harita ve yıldızlar ayrı ayrı dizilerde tutuluyor. Şu şekilde:
  let harita = [
      [1, 1,  1,  1],
      [1, 1, "S", 1],
      [3, 1,  1,  1]
    ];

    // yildiz konumlarini tutan dizi: [karoSatiri, karoSutunu, icSatir, icSutun]
    let yildizlar = [
      [0, 0, 0, 0],
      [0, 3, 0, 2],
      [1, 1, 1, 2],
      [2, 0, 2, 0]
    ]; 
    Yeni bir kolay bölüm eklesem aktif bölümden önce nasıl eklerim basitçe?

**Yanıt:**
Oyuna yeni bir **kolay bölüm** eklemek istiyorsan ve bu bölümü **mevcut bölümden önce göstermek** istiyorsan, bunu aşağıdaki adımlarla basitçe yapabilirsin:
---
### 🔧 1. **Yeni harita ve yıldız verisi tanımla**
Kodun en üstüne yakın bir yerde `harita` ve `yildizlar` değişkenlerini şöyle bir yapıya dönüştür:
```js
const bolumler = [
  {
    harita: [
      [1, 1, 1, 1],
      [1, "S", 1, 1],
      [1, 1, 1, 1]
    ],
    yildizlar: [
      [0, 0, 0, 0],
      [1, 1, 1, 1],
    ]
  },
  {
    harita: [
      [1, 1, 1, 1],
      [1, 1, "S", 1],
      [3, 1, 1, 1]
    ],
    yildizlar: [
      [0, 0, 0, 0],
      [0, 3, 0, 2],
      [1, 1, 1, 2],
      [2, 0, 2, 0]
    ]
  }
];
```
### 🧠 2. **Aktif bölümü tutan değişken ekle**
```js
let aktifBolum = 0;
let harita = bolumler[aktifBolum].harita;
let yildizlar = [...bolumler[aktifBolum].yildizlar];
```
> `yildizlar`’ı `[...]` ile kopyalayarak kullanıyoruz, çünkü doğrudan referans verirseniz değişiklik kalıcı olur.

### ✅ Sonuç
* `bolumler` dizisiyle birden fazla bölüm tanımlayabilirsin.
* İlk öğe kolay bölüm olur.
* Her yıldızlar bitince sıradaki bölüme geçer.
---
Eğer istersen bunu bir butonla **"Bölüm Seç"** gibi yapıya da çevirebiliriz. Devam etmek istersen adım adım gösteririm.

**Prompt:**
> Karakter duruyorken düz hali, hareket ederken yan halini nasıl kodda yazarız? Görsel tuşa basıldığında yan halde olsun, bırakınca düz haline dönsün. 

**Yanıt:** ChatGPT:
Bunu yapmak için karakterin hareket edip etmediğini takip eden bir `hareketEdiyorMu` değişkeni tanımlarız ve tuşa basıldığında `true`, tuş bırakıldığında `false` yaparız. Ardından çizim fonksiyonunda bu duruma göre uygun resmi (düz ya da yan) çizeriz.
---

### 🧠 Gerekli Adımlar:

#### 1. İki resmi yükle:

```js
const karakterDuz = new Image();
karakterDuz.src = "karakter_duz.png"; // düz hali

const karakterYan = new Image();
karakterYan.src = "karakter_yan.png"; // yan hali
```

#### 2. `hareketEdiyorMu` değişkeni tanımla:

```js
let hareketEdiyorMu = false;
```

#### 3. Klavye olaylarında bu değeri güncelle:

```js
document.addEventListener("keydown", (e) => {
  if (["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)) {
    hareketEdiyorMu = true;
    // hareket kodlarını buraya ekle
  }
});

document.addEventListener("keyup", (e) => {
  if (["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)) {
    hareketEdiyorMu = false;
  }
});
```
#### 4. Çizim fonksiyonunda karakteri buna göre çiz:
```js
function cizKarakter() {
  const karakterResmi = hareketEdiyorMu ? karakterYan : karakterDuz;
  ctx.drawImage(karakterResmi, karakterX, karakterY, 60, 60); // boyutu ihtiyacına göre ayarla
}
```
Ve her karede çağırdığın ana çizim fonksiyonunda:
```js
function oyunDongusu() {
  // ... diğer çizimler
  cizKarakter();
  requestAnimationFrame(oyunDongusu);
}
```
İstersen bu kodları kendi dosyana uygun biçimde entegre etmene yardım edebilirim. Sadece karakterin nasıl hareket ettiğini gösteren kısmı paylaşman yeterli.

**Prompt:**
> müzik fonksiyonum şöyle: 
```js
    function muzikBaslat(){
      arkaplanMuzik.play();
      document.addEventListener("keydown", muzikBaslat);
    }
```  
    ve konsolda şöyle bir hata var nasıl çözerim: 
    Uncaught DOMException NotAllowedError: play() failed because the user didn't interact with the document first.
    at muzikBaslat (c:\Users\Büşra\Desktop\webTabanli\OYUN\index.html:214:21)
    at <anonymous> (c:\Users\Büşra\Desktop\webTabanli\OYUN\index.html:342:5)

**Yanıt:** ChatGPT:

Tarayıcıların çoğu (özellikle Chrome), **kullanıcı sayfayla etkileşime geçmeden** ses oynatılmasına izin vermez. Bu yüzden şu hatayı alıyorsun:

> `Uncaught DOMException: play() failed because the user didn't interact with the document first.`

### 🔍 Hatanın Sebebi:

Fonksiyonun `muzikBaslat()` sayfa açılır açılmaz çalışıyor gibi görünüyor ama henüz kullanıcı bir tuşa basmadıysa, tarayıcı `play()` komutuna izin vermez.

---

### ✅ Çözüm:

Müziği **ilk kullanıcı etkileşimiyle** (örneğin bir tuş basımıyla) başlatmalısın. Ayrıca `addEventListener`’ı fonksiyonun içine değil, dışına koymalısın. İşte düzeltilmiş hali:

```javascript
function muzikBaslat() {
  arkaplanMuzik.play();
  document.removeEventListener("keydown", muzikBaslat); // Müzik başladığında event listener'ı kaldır
}

// Kullanıcı ilk kez bir tuşa bastığında müzik başlayacak
document.addEventListener("keydown", muzikBaslat);
```
---
### 🎯 Ne Oldu?
* `keydown` ile kullanıcı ilk kez bir tuşa bastığında `muzikBaslat()` çağrılır.
* Müziği başlatır.
* Ardından `keydown` dinleyicisi kaldırılır ki tekrar tekrar çalışmasın.
Bu yöntemle hata ortadan kalkar **ve müzik düzgün başlar.** İstersen bu olayı `click` ile de yapabilirsin.


