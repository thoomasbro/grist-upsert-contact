# Grist — Formulaire Contact (Upsert par entreprise)

Widget custom (GitHub Pages-ready) pour écrire dans une table `contact` :
- Saisie d’une **entreprise** puis vérification
- Si l’entreprise existe, **prénom** et **nom** se pré-remplissent et peuvent être **modifiés**
- Sinon, un **nouveau** contact est **créé**
- L’écriture se fait par **upsert** (`PUT /records`) avec `require: {company: ...}`

## Pré-requis côté Grist

1. **Table**
   - Nom (tableId) : `contact` (ou adapte `TABLE_ID` dans `index.html`)
   - Colonnes (colIds) :
     - `first_name` (Prénom)
     - `last_name` (Nom)
     - `company` (Entreprise)
   - Assure-toi que les **colIds** correspondent (onglet *Raw Data* → menu colonne → *Column ID*).

2. **Access Rules (public sans lecture)**
   - Par défaut, **Deny All** pour les non-owners.
   - Règles pour `contact` :
     - Autoriser **Create** et **Update** sur les colonnes `first_name`, `last_name`, `company` (tu peux bloquer `Read`).
   - Active **Public access** sur le doc, rôle **Editor** (les règles ci-dessus protégeront la lecture).

3. **Widget Custom**
   - Ajoute un widget **Custom** sur une page.
   - URL : celle publiée par GitHub Pages (ex: `https://<ton-user>.github.io/grist-upsert-form/`).
   - **Access level** : *Full document access*.
   - Teste en navigation privée.

## Déploiement GitHub Pages

1. Crée un dépôt (ex: `grist-upsert-form`) et pousse `index.html` et `README.md`.
2. Active **Pages** (branche `main`, répertoire `/`).
3. Attends que la page soit en ligne puis utilise cette URL dans le widget.

## Personnalisation

- Dans `index.html`, modifie :
  ```js
  const TABLE_ID = 'contact';
  const COLS = { first: 'first_name', last: 'last_name', company: 'company' };
  ```
- Pour contraindre l’unicité `company`, tu peux ajouter une règle dans Grist ou gérer côté process.

## Notes techniques

- Recherche : `POST /records/query` avec `filters: {company: [<val>]}`, `limit: 1`
- Enregistrement : `PUT /records` avec
  ```json
  {
    "records": [{
      "require": {"company": "<val>"},
      "fields":  {"first_name": "...", "last_name": "...", "company": "..."}
    }],
    "add": true, "update": true, "onMany": "first"
  }
  ```
- Auth : jeton **scellé au document** via `grist.docApi.getAccessToken()` (aucune API key perso exposée).

---

Bon usage !
