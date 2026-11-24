# Globul cu mesaje

Un glob de cristal 3D așezat într-un laborator medical futurist. Îl freci cu mouse-ul sau cu degetul, iar când se „încarcă” eliberează un mesaj de la unul dintre oamenii dragi ai lui Ciocan.

- **Link public (artifact):** https://claude.ai/artifact/VxUvBKeYBmaHuqmsZ49VMx
- **Fișierul sursă:** `lab.html`, un singur fișier de aproximativ 60 KB
- **Versiunea veche:** `index.html` („Profeții 2027 · Prof. Tomi”), păstrată neatinsă

---

## 1. Cum îl folosești

1. Deschide `lab.html` în Chrome, Firefox sau Safari. Ai nevoie de internet, pentru biblioteca 3D și fonturi.
2. Ține apăsat pe glob și freacă-l: trage de mai multe ori peste el. Pe desktop merge și doar plimbând mouse-ul peste el, dar mai încet.
3. Bara **Rezonanță** se umple. La 100% globul explodează în lumină și mesajul zboară din el.
4. Atingi mesajul ca să-l închizi. Se întoarce în glob și poți freca pentru următorul.
5. După ultimul mesaj apare **mesajul final**, auriu, apoi globul o ia de la capăt.

---

## 2. Cum schimbi mesajele

La începutul scriptului din `lab.html` sunt trei lucruri de editat:

```js
const DESTINATAR = "Ciocan";          // numele din titlu și de pe ecrane

const MESAJE = [
  { de:"Familia", rol:"acasă", text:"..." },
  { de:"Colegii de la catedră", rol:"catedra", text:"..." },
  // adaugi oricâte rânduri vrei, în ordinea în care vrei să apară
];

const FINAL = { de:"Toți oamenii tăi dragi", rol:"împreună", text:"..." };
```

- `de` este cine trimite (nume sau grup) și apare mare pe card.
- `rol` este rândul mic de sub nume (relația).
- `text` este mesajul.

> ⚠️ Mesajele de acum sunt **exemple inventate**. Trebuie înlocuite cu cele reale înainte să trimiți link-ul.

Numărătoarea („Mesaje eliberate: 3 / 9”) se calculează singură din câte mesaje sunt în listă.

---

## 3. Cum se actualizează link-ul

Link-ul de artifact **rămâne mereu același**. Când `lab.html` se modifică, se publică din nou la aceeași adresă, iar toți cei care au link-ul văd ultima versiune.

- **Ca să-l dai altora:** deschide link-ul → butonul **Share** → alegi cine are acces. Până nu-l partajezi, doar tu îl poți deschide.
- **Copia publicată** este `lab.html` fără tag-urile `<!DOCTYPE>`, `<html>`, `<head>` și `<body>`, pe care platforma le adaugă singură. Conținutul e identic.

---

## 4. Cum e făcut

### Nu folosește assets externe
Nu există imagini, modele 3D sau fișiere audio. **Totul e generat din cod.** Din afară se încarcă doar:

| Ce | De unde | Licență |
|---|---|---|
| Three.js 0.160.0 (motorul 3D) | cdn.jsdelivr.net | MIT, gratuit |
| Fonturile Space Grotesk, JetBrains Mono, Cormorant Garamond | Google Fonts | OFL, gratuite |

### Laboratorul
- **Obiectele** (CT-ul, brațul robotic, soclul, banca de laborator, pereții de sticlă, lampa suspendată) sunt construite din forme simple: cilindri, cutii cu colțuri rotunjite, tori și sfere. Exemple:
  - CT-ul este un dreptunghi rotunjit cu o gaură circulară, extrudat în adâncime.
  - Brațul robotic e un lanț de articulații care se mișcă lent, după funcții sinus.
- **Pereții** sunt panouri multiplicate eficient (`InstancedMesh`), cu benzi LED între ele.
- **Podeaua** e o singură dală desenată cu cod, cu puțin zgomot ca să pară material real, repetată pe toată suprafața.
- **Reflexiile realiste** vin de la o cameră virtuală generată automat (`RoomEnvironment`), pe care o reflectă metalul, plasticul lucios și sticla.
- **Lumina:** un spot de sus cu umbre moi, un spot de contur albastru, o lumină ambientală slabă și ceață, pentru adâncime.
- **Ecranele** (EKG cu „Nivel de drag: ∞”, scanarea 3D a arcadei dentare cu „Status: zâmbet detectat”, monitorul de rezonanță) sunt desenate live pe un canvas, de 12 ori pe secundă, apoi „lipite” pe ecranele 3D. Monitorul de rezonanță arată în timp real cât ai frecat.
- **Holograma ADN** este o spirală de sfere colorate care se rotește deasupra unui proiector.

### Globul (mai multe straturi suprapuse)
1. **Nebuloasa:** un shader, adică un mic program care rulează pe placa video, calculează pentru fiecare pixel un „nor” de lumină în interiorul sferei. Norul e făcut din zgomot fractal (fbm), care se rotește în timp.
2. **Particulele:** sute de puncte luminoase care se rotesc înăuntru, mai repede pe măsură ce crește energia.
3. **Sticla:** o sferă neagră desenată „aditiv”. Corpul ei nu se vede, se văd doar reflexiile, ca la sticla reală.
4. **Conturul luminos:** un efect Fresnel, adică marginile strălucesc mai tare decât centrul.
5. **Aura și lumina:** un halou în jurul globului și o lumină reală în centrul lui, care luminează camera tot mai tare pe măsură ce îl freci.

### Frecatul
- Pagina calculează unde se află globul pe ecran și cât de mare apare.
- Fiecare mișcare peste glob adaugă energie proporțional cu distanța parcursă. Cam 26 de „raze” de glob de frecat umplu bara.
- Direcția în care freci rotește interiorul globului. Unde atingi apare o pată de lumină și ies scântei.
- Din glob scapă cuvinte din mesajul care urmează, ca un teaser.
- Dacă te oprești din frecat, energia scade încet.
- La 100% urmează explozia: undă de șoc, inel pe podea, 160 de scântei, sunet, vibrație pe telefon. Apoi cardul cu mesajul zboară din poziția globului spre centrul ecranului și textul se scrie literă cu literă.

### Sunetul
Generat direct în browser cu Web Audio, fără fișiere:
- **zumzetul** este un oscilator trecut printr-un filtru. Se aude mai tare și mai sus cu cât freci mai repede și cu cât e mai multă energie;
- **la eliberare** se aude un acord de clopoței plus un „whoosh” din zgomot filtrat;
- **la închiderea mesajului** se aude un „whoosh” invers.

Sunetul pornește la primul click (browserele nu permit sunet fără interacțiune) și se poate opri din butonul din dreapta sus.

### Strălucirea
Peste imaginea finală se aplică efectul de **bloom**: lucrurile foarte luminoase (LED-urile, globul, hologramele) „curg” în jur.

---

## 5. Optimizări de performanță

Măsurat în randare software, aceeași rezoluție: **~50% mai rapid** decât prima versiune.

- **Calitatea se reglează singură.** Pagina măsoară timpul real dintre cadre:
  - dacă un cadru durează peste 24 ms, scade rezoluția;
  - dacă durează sub 14 ms timp de 3 măsurători la rând, o crește la loc;
  - pe aparatele foarte slabe oprește strălucirea și umbrele.
- **Nebuloasa** folosește 12 pași de calcul (8 pe telefon) în loc de 18, și 3 niveluri de zgomot în loc de 4.
- **Fără antialiasing** pe ecranul final, pentru că oricum nu se vedea prin efectele de post-procesare.
- **Umbrele** se recalculează la fiecare al patrulea cadru, cu hartă de umbre mai mică.
- **O singură lumină** pe peretele din spate, în loc de trei.
- **Sfere mai simple** (mai puține poligoane), cu aspectul neschimbat.
- **Ecranele** se redesenează de 12 ori pe secundă în loc de 20, fără efectul de umbră pe text.
- **Strălucirea** se calculează la rezoluție redusă.
- **Neclaritatea din spatele cardului** e redusă, pentru că se recalcula continuu peste scena 3D.
- **Interfața** se actualizează doar când se schimbă ceva, nu la fiecare cadru.
- **Shaderele se compilează dinainte**, ca să nu sacadeze la pornire.

---

## 6. Cum a fost verificat

Pagina a fost deschisă automat într-un Chrome fără ecran (Playwright), la mărime de desktop (1400×850) și de telefon (390×844). S-au făcut capturi de ecran în repaus, în timpul frecatului și cu mesajul deschis. Frecatul a fost simulat cu mouse-ul și a eliberat corect primul mesaj. Prima versiune ieșise supraexpusă, aproape complet albă, și luminile au fost reglate pe baza capturilor.

---

## 7. Idei pentru mai departe

- Poze sau înregistrări vocale la unele mesaje.
- O fotografie reală de laborator ca fundal, cu globul 3D peste ea.
- Mesaj final adaptat ocaziei (zi de naștere, pensionare etc.).
