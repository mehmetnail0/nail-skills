---
name: eminmisin
description: Production safety verification checklist. Use before ANY code change on a live product with real users. Mandatory pre-commit/pre-push checkpoint.
---

# /eminmisin — Production Safety Verification

Bu skill, canlı kullanicilari olan bir urunde (400+ kayitli, 2-3 anlik aktif) HER kod degisikligi oncesi zorunlu kontrol listesidir.

## Ne Zaman Kullanilir
- Commit/push oncesi
- Her feature/bugfix tamamlandiginda
- Kullanici "emin misin?" dediginde
- Herhangi bir degisiklik production'a gitmeden once

## Zorunlu Kontrol Listesi

Asagidaki HER maddeyi tek tek kontrol et. Atlama. Emin degilsen DURMA, arastir.

### 1. Geriye Uyumluluk (Breaking Changes)
- [ ] Mevcut API endpoint'leri degisti mi? Evet → eski client'lar kirilir mi?
- [ ] Database schema degisti mi? Evet → migration gerekli mi? Mevcut veri bozulur mu?
- [ ] Environment variable eklendi/degisti mi? Evet → Railway'de set edildi mi?
- [ ] Mevcut kullanicilarin cached/stored verisi ile uyumlu mu?
- [ ] LocalStorage/IndexedDB yapisi degisti mi? Evet → migration/fallback var mi?

### 2. Kutuphane ve Bagimliliklar
- [ ] package.json'da yeni paket eklendi mi? Evet → guvenilir mi, lisansi uygun mu?
- [ ] Mevcut paket versiyonu degisti mi? Evet → breaking change var mi, changelog okundu mu?
- [ ] `pnpm install` gerekli mi? Lock dosyasi degisti mi?
- [ ] Bundle size onemli olcude artti mi?

### 3. Veri Guvenligi (Data Safety)
- [ ] Kullanici verisi kaybedilme riski var mi? (content, edges, nodes, settings)
- [ ] Race condition olusabilir mi? (concurrent users, multiple tabs)
- [ ] Auto-save pipeline bozuldu mu? Hash hesaplamasi dogru calisiyor mu?
- [ ] Supabase RLS kurallari etkilendi mi?

### 4. Runtime Guvenligi
- [ ] TypeScript hata veriyor mu? (`npx tsc --noEmit` kontrol et)
- [ ] Console.log birakildimi? (ESLint warn)
- [ ] `any` type eklendi mi?
- [ ] Error handling var mi? (try/catch, fallback, user-facing error message)
- [ ] Memory leak olusabilir mi? (event listener cleanup, interval clear, subscription unsubscribe)

### 5. Kullanici Deneyimi
- [ ] Loading state dogru calisiyor mu? (spinner, skeleton, overlay)
- [ ] Error state kullaniciya dogru gosteriliyor mu?
- [ ] Mevcut UI/UX akisi bozuldu mu?
- [ ] Mobile blocker etkilendi mi?
- [ ] Offline/yavas baglantida ne oluyor?

### 6. Performans
- [ ] Re-render sayisi artti mi? (useCallback/useMemo/memo dogru kullanildi mi?)
- [ ] Buyuk liste/array islemlerinde O(n^2) var mi?
- [ ] Network request sayisi artti mi?
- [ ] onlyRenderVisibleElements=true ile uyumlu mu?

### 7. Guvenlik
- [ ] XSS riski var mi? (DOMPurify kullaniliyor mu?)
- [ ] SQL injection riski var mi? (parametrized query mi?)
- [ ] SSRF riski var mi? (URL validation var mi?)
- [ ] Auth bypass olusabilir mi?
- [ ] Sensitive data loglaniyor mu?

## Cikti Formati

Kontrol tamamlandiginda su formatta rapor ver:

```
## /eminmisin Raporu

**Degisiklik:** [kisa ozet]
**Dosyalar:** [degisen dosya listesi]
**Risk Seviyesi:** DUSUK / ORTA / YUKSEK / KRITIK

### Kontrol Sonuclari
- Geriye uyumluluk: OK/RISK
- Kutuphane degisikligi: YOK/VAR (detay)
- Veri guvenligi: OK/RISK
- TypeScript: OK/HATA
- Performans: OK/RISK
- Guvenlik: OK/RISK

### Ozel Notlar
[varsa dikkat edilmesi gereken noktalar]

### Sonuc
GUVENLI — Push edilebilir / BEKLE — [neden]
```

## Onemli Kurallar

1. **"Muhtemelen guvenli" KABUL EDILMEZ** — ya emin ol ya arastir
2. **Degisiklik kucuk bile olsa kontrol et** — tek satir degisiklik bile production'i kirabilir
3. **Kutuphane guncellemesi varsa MUTLAKA changelog oku**
4. **Database migration varsa MUTLAKA kullanicidan onay al** (CLAUDE.md kurali)
5. **Main branch'e ASLA direkt push** — nail branch'e push, staging'de test et
6. **Supabase migration OTOMATIK uygulanmaz** — dosyayi hazirla, kullanici onaylasin
