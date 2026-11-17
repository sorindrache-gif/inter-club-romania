# Instrucțiuni pentru redacția automată

Acest fișier descrie cum se actualizează `data.json`, fișierul din care https://interclub.ro își ia tot conținutul. Sarcina programată (zilnic la 10:00 și 18:00, ora României) urmează aceste instrucțiuni. Ca să schimbi ce face actualizarea automată, editează acest fișier.

## Reguli generale

- Modifici doar `data.json`. NU modifica `index.html`, `netlify.toml`, `README.md`, `REDACTIE.md` sau folderul `img/`.
- Toate textele sunt în limba română corectă, cu diacritice (ă, â, î, ș, ț). Ton factual.
- Nu inventa nimic. Fiecare fapt provine dintr-o sursă găsită în rularea curentă sau e păstrat din `data.json` anterior. Dacă nu ești sigur, lasă câmpul gol sau păstrează valoarea veche.
- Accesul direct (fetch/curl) la site-urile de mai jos e blocat. Lucrezi prin WebSearch (mode "extended"), încărcat cu ToolSearch `select:WebSearch` dacă nu e disponibil.

## Pași

1. În directorul repository-ului: `git fetch --depth=20 origin main && git reset --hard FETCH_HEAD`. Citește `data.json` (starea curentă) și acest fișier. Folosește data curentă (`date`) și fusul orar Europe/Bucharest.
2. Cercetează (vezi „Surse”).
3. Construiește noul `data.json` (vezi „Schema”).
4. Validează: `python3 -c "import json;json.load(open('data.json'))"`.
5. Commit doar cu `data.json`, cu mesajul `Actualizare date <YYYY-MM-DD HH:MM>`, urmat, după un rând gol, de liniile de atribuire din system-reminder (dacă există). Push: `git -c push.negotiate=true push origin HEAD:main`. Dacă push-ul e respins pentru că remote-ul are commituri noi: `git fetch --depth=20 origin main`, `git rebase FETCH_HEAD`, apoi push din nou o singură dată. Fără force push, fără pull request.
6. Încheie cu un rezumat scurt: ce s-a schimbat, ce surse ai folosit, hash-ul commitului.

## Surse

### Rezultate, program, clasamente
Caută cu `allowed_domains` ["fotmob.com","sofascore.com","flashscore.ro","flashscore.com"] și, pentru confirmare, ["inter.it","legaseriea.it","uefa.com","espn.com","skysports.com"]. Competiții: Serie A, Coppa Italia, UEFA Champions League, Supercoppa Italiana. Un scor, un marcator sau o poziție în clasament intră în date doar dacă apare la cel puțin o sursă dintre acestea și nu e contrazis de alta.

### Știri
Subiecte: antrenamente, accidentări, zvonuri de transfer, schimbări de jucători sau antrenori, contracte, declarații, jucători ai Interului la naționale.

a) **Comunitatea suporterilor:** pagina de Facebook https://www.facebook.com/iloveim1908 (I Love IM 1908), contul X https://x.com/AmalaTV_ (Amala TV), forzaim.com, interfans.org, forzainterforums.com, r/FCInterMilan. Rețelele sociale sunt adesea inaccesibile prin căutare: încearcă, iar dacă nu găsești nimic, treci mai departe. Folosește-le ca pistă. O informație de acolo intră pe site doar dacă e confirmată de presa de la punctul b). Ca sursă citezi articolul de presă, plus postarea, dacă ai linkul ei exact.

b) **Presa sportivă credibilă:**
- Italia: gazzetta.it, corrieredellosport.it, tuttosport.com, sport.sky.it, gianlucadimarzio.com, sportmediaset.mediaset.it, ansa.it, repubblica.it;
- Europa și internațional: theathletic.com, espn.com, lequipe.fr, kicker.de, marca.com, as.com, football-italia.net, uefa.com;
- specializate pe Inter: inter.it (oficial), fcinternews.it, fcinter1908.it, calciomercato.com, sempreinter.com.

Dacă unele domenii sunt blocate la căutare, continuă cu celelalte.

c) **Credibilitate:** preferă informațiile confirmate de surse oficiale sau de cel puțin două publicații de la punctul b). Zvonurile de transfer apar doar la „mercato” sau cu categoria „transfer”, cu nivelul de stadiu corect (1 = zvon dintr-o singură sursă).

## Schema `data.json`

- `updated`: momentul actualizării, ISO cu offset (ex. `2026-11-17T10:00:00+02:00`).
- `headline`: cea mai importantă știre a momentului `{date "YYYY-MM-DD", title, text, sources:[[nume,url],...]}`.
- `fixtures`: meciurile viitoare, cronologic, maximum 9: `{date, time "HH:MM" în ORA ROMÂNIEI (ora Italiei + 1h; omite dacă nu e confirmată), comp "sa"|"ucl"|"ci"|"sc", round (ex. "Etapa 12", "Faza ligii, etapa 5", "Optimi"), home, away}`. Scoate meciurile deja jucate.
- `results`: toate meciurile oficiale ale Interului jucate în sezonul 2026/27, cele mai recente primele: `{date, comp, round, home, away, hs, as (numere), scorers}`. `scorers` conține marcatorii Interului cu minutul, dacă sunt confirmați, altfel `""`. Echipa noastră se numește exact `"Inter"`.
- `table` (Serie A): `{"after": etapa, "rows":[{team, pts}, ...]}`, în ordinea clasamentului. Adaugă `p, w, d, l, gf, ga` la TOATE rândurile doar dacă le ai confirmate pentru toate echipele.
- `ucl`: bilanțul Interului în Champions League, `{played, w, d, l, gf, ga, pts, pos (null dacă nu e confirmat)}`.
- `ucl_table` (clasamentul fazei ligii Champions League): `{"after": etapa, "rows":[{pos, team, pts}, ...]}`, sortat după `pos`. Ideal toate cele 36 de echipe. Dacă nu poți confirma tot clasamentul, include doar pozițiile confirmate: cel puțin locurile 1–8, Interul și echipele din jurul lui. Pagina afișează „···” pentru pozițiile lipsă. Adaugă `p` (meciuri jucate) și `gd` (golaveraj, număr cu semn) la TOATE rândurile doar dacă le ai pentru toate. Numele echipelor în forma uzuală (ex. "Paris Saint-Germain", "Bayern München", "Inter"). După faza ligii (play-off, optimi etc.) păstrează clasamentul final, cu `after: 8`.
- `news`: 10–20 de știri, cele mai noi primele. Păstrează știrile încă relevante și scoate-le pe cele mai vechi de ~3 săptămâni: `{date "YYYY-MM-DD" (sau "YYYY-MM"), cat "meci"|"antrenament"|"transfer"|"accidentare"|"antrenor"|"club", title, text (2–3 propoziții), sources [[nume,url]]}`. Nu schimba titlul sau data unei știri existente: din ele se formează linkul distribuit pe WhatsApp.
- `injuries`: `[{player, status "out"|"doubt"|"back", note}]`, cu indisponibilii actuali și data estimată a revenirii. Scoate jucătorii complet refăcuți care au jucat deja.
- `mercato`: `[{title, text, level 1–5 (1 zvon, 2 discuții, 3 negocieri, 4 aproape, 5 oficial), source url}]`.
