# Hybrid RNG (AES + LFSR + DES + RSA)

Bu proje, RSA tabanlı seed üretimi ve AES, LFSR, DES algoritmalarının
birlikte kullanıldığı hibrit bir rastgele sayı üretecini içermektedir.

## Kullanılan Algoritmalar
- RSA: Seed üretimi (x² mod N)
- AES: Counter-mode benzeri karıştırma
- LFSR: Deterministik akış üretimi
- DES: Whitening (son karıştırma)

## Kurulum
```bash
pip install pycryptodome
SÖZDE KOD YAPISI
ALGORITHM HybridRNG

INPUT:
    low, high        // Üretilecek sayı aralığı
    n                // Üretilecek sayı adedi

OUTPUT:
    Rastgele sayılar

BEGIN
    // 1. RSA tabanlı seed üret
    N ← Rastgele büyük modül
    x ← Rastgele başlangıç değeri

    FOR i = 1 TO seed_length DO
        x ← (x * x) mod N
        seed_bytes ← seed_bytes || LSB(x)
    END FOR

    // 2. Seed'den anahtarları çıkar
    AES_key ← seed_bytes[0..15]
    AES_counter ← seed_bytes[16..31]
    DES_key ← seed_bytes[32..39]
    LFSR_state ← seed_bytes[40..47]

    // 3. LFSR başlat
    LFSR ← Initialize(LFSR_state)

    // 4. Rastgele sayı üretimi
    FOR i = 1 TO n DO
        AES_block ← AES_Encrypt(AES_key, AES_counter)
        AES_counter ← AES_counter + 1

        LFSR_stream ← LFSR_Generate(16 byte)

        Mixed ← AES_block XOR LFSR_stream

        Output_block ← DES_Encrypt(DES_key, Mixed[0..7]) 
                        || DES_Encrypt(DES_key, Mixed[8..15])

        RandomNumber ← Convert(Output_block)
        Print(RandomNumber mod (high - low) + low)
    END FOR
END


AKIŞ ŞEMASI
[BAŞLA]
   |
   v
[RSA ile Seed Üret]
   |
   v
[Seed'den Anahtarları Ayır]
(AES Key, Counter, DES Key, LFSR State)
   |
   v
[LFSR Başlat]
   |
   v
[Counter = Counter + 1]
   |
   v
[AES(counter) Hesapla]
   |
   v
[LFSR Keystream Üret]
   |
   v
[AES ⊕ LFSR]
   |
   v
[DES Whitening]
   |
   v
[Rastgele Sayı Üret]
   |
   v
[İstenen Sayı Adedine Ulaşıldı mı?]
   |           |
   | Hayır     | Evet
   |           v
   |        [BİTİR]
   |
   └───────────(Döngüye geri dön)





