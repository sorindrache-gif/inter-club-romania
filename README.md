# Inter Club România

Site neoficial de știri despre FC Internazionale Milano, în limba română: rezultate (Serie A, Coppa Italia, Champions League), program, clasament, infirmerie, mercato și știri cu surse.

## Structură

- `index.html` – pagina (design + logică de afișare). Nu conține date.
- `data.json` – tot conținutul site-ului. Este actualizat automat de două ori pe zi (10:00 și 18:00, ora României) printr-o sarcină programată Claude, care face commit în acest repository.
- `netlify.toml` – configurarea Netlify (fără pas de build; `data.json` nu este ținut în cache).

## Găzduire pe Netlify

Add new site → Import an existing project → GitHub → `inter-club-romania` → Deploy. Netlify republică site-ul la fiecare commit.

## Test local

```bash
python3 -m http.server 8000
```

apoi deschide http://localhost:8000.

## Actualizarea automată

Instrucțiunile pentru redacția automată (surse, reguli, schema `data.json`) sunt în `REDACTIE.md`. Editează acel fișier ca să schimbi ce face actualizarea.
