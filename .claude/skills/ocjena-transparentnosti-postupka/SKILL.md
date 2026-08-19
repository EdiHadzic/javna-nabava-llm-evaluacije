---
name: ocjena-transparentnosti-postupka
description: Koristiti za ocjenu jednog postupka javne nabave Zadarske županije (prema folderu Postupci/{id}) po semantičkoj transparentnosti i prilagođenosti specifikacije/restriktivnosti, kao zadatak semantičke analize 4.2 za diplomski rad — čita izvučeni tekst natječajne dokumentacije i vraća strukturiranu JSON ocjenu s citiranim dokazima.
---

# Ocjena transparentnosti i restriktivnosti postupka

## Pregled

Semantička ocjena jednog postupka javne nabave iz `Diplomski/Postupci/{id}/extracted/*.txt`
prema fiksnoj ljestvici, operacionalizirajući poglavlja 3.1. i 4.2. diplomskog rada
(informacijska asimetrija, screening/signalna teorija). Namijenjeno pozivanju **jednom po
postupku, po pokretanju** — ponovljeni pozivi na isti postupak (u odvojenim, neovisnim
pokretanjima bez zajedničkog konteksta) služe mjerenju konzistentnosti ocjenjivanja
(test-retest pouzdanost), ne jednom "konačnom" rezultatu.

## Kada koristiti

- Zadan je identifikator postupka (Id) iz `postupci_obogaceno.json`
- Postoji direktorij `Diplomski/Postupci/{id}/extracted/*.txt` s izvučenim tekstom
- Zadatak je dodijeliti ocjenu transparentnosti i/ili detektirati prilagođenost specifikacije
  određenom ponuditelju

Ne koristiti za ekstrakciju brojčanih polja (broj ponuditelja, ugovorena vrijednost,
dobavljač) — to je riješeno pravilima temeljenom ekstrakcijom u `extract_pipeline.py`
(4.1.), ne ovim zadatkom.

## Postupak

1. Pročitaj **sve** `.txt` datoteke u `Diplomski/Postupci/{id}/extracted/` za zadani `id`.
   Prioritiziraj (ako postoje): dokumentaciju o nabavi, tehničke specifikacije, kriterije za
   kvalitativni odabir, obavijest o nadmetanju. Ako ništa od navedenog ne postoji, javi to
   umjesto nagađanja.
2. Ocijeni **tri dimenzije transparentnosti** (Bosio i sur., 2021 — v. pogl. 2.2.), svaku na
   ljestvici 1–5 (1 = vrlo nejasno/nepotpuno, 5 = potpuno jasno/potpuno), uz kratko
   obrazloženje i **doslovan citat** (≤ 200 znakova) iz teksta koji potkrepljuje ocjenu:
   - `jasnoca_kriterija` — jesu li kriteriji za odabir ponude jasno definirani i mjerljivi
   - `razina_detalja_opisa` — je li opis predmeta nabave dovoljno konkretan da omogući
     usporedivu ponudu, bez da bude toliko uzak da isključuje razumne alternative
   - `potpunost_dokumentacije` — je li dostupna dokumentacija (troškovnik, tehnički uvjeti,
     obrasci) potpuna za samostalnu izradu ponude
3. Detektiraj **prilagođenost specifičnom ponuditelju** (negativna selekcija, pogl. 3.1.):
   traži reference na točno određeni proizvod/markicu, patentirano rješenje, neproporcionalno
   visoke financijske/referentne pragove nesrazmjerne vrijednosti nabave. Vrati
   `prisutno: true/false`, listu konkretnih indikatora s citatima, i `rizik: nizak/srednji/visok`.
4. Vrati **isključivo** JSON u shemi ispod — bez popratnog teksta prije/poslije.

## Izlazna shema (JSON)

```json
{
  "postupak_id": 33065,
  "transparentnost": {
    "jasnoca_kriterija": {"ocjena": 4, "obrazlozenje": "...", "citat": "..."},
    "razina_detalja_opisa": {"ocjena": 3, "obrazlozenje": "...", "citat": "..."},
    "potpunost_dokumentacije": {"ocjena": 5, "obrazlozenje": "...", "citat": "..."},
    "prosjecna_ocjena": 4.0
  },
  "restriktivnost": {
    "prisutno": false,
    "indikatori": [],
    "rizik": "nizak"
  },
  "napomena": "kratka napomena o kvaliteti/dostupnosti pročitanih dokumenata, ako relevantno"
}
```

## Mjerenje konzistentnosti (test-retest)

Za provjeru pouzdanosti opisanu u 4.2. (poglavlje "Validacija pouzdanosti"): pokreni ovaj
zadatak N puta (npr. N=5) nad **istim** `id`, svaki put u odvojenom, neovisnom pozivu (nova
subagent sesija bez memorije prethodnih pokretanja — inače anchoring umjetno smanjuje
varijancu). Usporedi `prosjecna_ocjena` i `restriktivnost.prisutno` kroz sve pokrete;
raspon/standardna devijacija ocjena je mjera pouzdanosti metode, ne šum koji treba ignorirati.

## Uobičajene pogreške

- Čitanje samo jedne datoteke umjesto svih relevantnih u `extracted/` — ocjena bez potpunog
  konteksta postupka.
- Izostavljanje citata — bez njega ocjena nije naknadno provjerljiva (gubi se cijela poanta
  sljedivosti opisane u 4.2.).
- Pokretanje "N puta" unutar iste konverzacije/konteksta — to mjeri konzistentnost sjećanja na
  vlastiti prethodni odgovor, ne stvarnu varijancu neovisnog LLM poziva.
