# Evaluacije transparentnosti javne nabave (LLM)

Ovaj repozitorij sadrži strojno (LLM) generirane ocjene transparentnosti i
restriktivnosti za 458 dovršenih postupaka javne nabave Zadarske županije
objavljenih tijekom 2024. godine, prikupljenih s portala EOJN RH
(https://eojn.hr). Podaci su nusprodukt diplomskog rada *"Analiza javnih
nabava primjenom umjetne inteligencije u funkciji učinkovitog i djelotvornog
javnog menadžmenta"* (Sveučilište Jurja Dobrile u Puli, Odjel za ekonomiju i
turizam).

## ⚠️ Napomena o pouzdanosti

**Ove ocjene su AI-generirane i do trenutka objave nisu prošle formalnu
provjeru valjanosti** (nije proveden Cohenov kappa nasuprot ručnim ocjenama
istraživača). Treba ih čitati kao **indikativan**, istraživački signal, a ne
kao potvrđenu ili službenu ocjenu postupka, naručitelja ili ponuditelja.
Test-retest pilot proveden u sklopu rada pokazao je da je metoda pouzdana na
jasnim slučajevima, ali nestabilna na graničnim slučajevima (posebno kod
ocjene razine detalja opisa, potpunosti dokumentacije i restriktivnosti).

Model je za svaki postupak analizirao isključivo tekst natječajne
dokumentacije preuzete s EOJN RH (javno dostupni izvor) — sadržaj citata i
opisa u JSON datotekama odnosi se samo na ono što je u toj dokumentaciji
javno objavljeno.

## Struktura

- `ocjene/{id}.json` — finalna ocjena za postupak `{id}` (odgovara Id
  postupka na EOJN portalu, npr. `https://eojn.hr/tender-eo/{id}`). Za 434
  postupka to je rezultat jednog pokretanja LLM ocjenjivanja; za 24 postupka
  (metodološki stariji, pouzdaniji dizajn s 2-3 neovisna pokretanja i
  razrješavanjem razlika) to je razriješena, sintetizirana finalna ocjena —
  te datoteke imaju dodatna polja (`runovi`, `slaganje`,
  `napomena_resolvera`).
- `agregatna_statistika.json` — agregirani pokazatelji preko svih 458
  postupaka (prosječna ocjena po dimenziji, udio postupaka s detektiranom
  restriktivnošću, raspodjela razine rizika, raščlamba po sektoru).

## Shema pojedinačne datoteke

```
{
  "postupak_id": int,
  "transparentnost": {
    "jasnoca_kriterija":       { "ocjena": 1-5, "obrazlozenje": str, "citat": str },
    "razina_detalja_opisa":    { "ocjena": 1-5, "obrazlozenje": str, "citat": str },
    "potpunost_dokumentacije": { "ocjena": 1-5, "obrazlozenje": str, "citat": str },
    "prosjecna_ocjena": float
  },
  "restriktivnost": {
    "prisutno": bool,
    "indikatori": [ { "opis": str, "citat": str, "rizik_doprinos": str }, ... ],
    "rizik": "nizak" | "srednji" | "visok"
  },
  "napomena": str
}
```

## Metodologija (sažeto)

Za svaki postupak izvučen je tekst svih dostupnih dokumenata natječajne
dokumentacije, nakon čega je LLM (u zasebnom, neovisnom pozivu bez dijeljenog
konteksta s drugim postupcima) ocijenio tri dimenzije transparentnosti na
ljestvici 1–5 uz obrazloženje i doslovan citat iz dokumentacije, te detektirao
znakove prilagođenosti specifikacije određenom ponuditelju. Detaljan opis
metodologije, poznata ograničenja i rezultati na razini agregata nalaze se u
poglavljima 4.2. i 5.2. diplomskog rada.
