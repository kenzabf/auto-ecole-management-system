# AutoÉcole — Plateforme complète

Application Next.js 14 + Prisma + PostgreSQL pour la gestion d'auto-école.

## ✨ Fonctionnalités

### 🎯 Module Administration (`/`)
- **Tableau de bord** : KPIs en temps réel (CA, élèves, taux de réussite, alertes)
- **Élèves** : CRUD complet avec recherche, filtres, fiche détaillée
- **Moniteurs** : CRUD avec gestion des permis enseignés
- **Parc auto** : CRUD véhicules avec suivi assurance/maintenance
- **Planning** : Vue hebdomadaire navigable
- **Finances** : Factures, forfaits, KPIs financiers

### 👨‍🎓 Portail Élève (`/portail`)
- **Connexion** par CIN + mot de passe
- **Tableau de bord** : solde d'heures, prochaines leçons, dernier score code
- **Réservation de leçons** : choix créneau + moniteur en temps réel, prévention des doubles-réservations
- **Annulation** : règle des 48h respectée
- **Code de la route en ligne** :
  - 8 catégories thématiques (Panneaux, Priorités, Vitesse, etc.)
  - 15+ questions avec explications
  - Examen blanc chronométré (40 questions, 30 min, 75% pour réussir)
- **Suivi de progression** :
  - Compétences REA (référentiel officiel) sur 4 domaines
  - Graphique d'évolution des scores code
  - Graphique en barres par compétence
- **Achat de forfaits** : intégration Stripe Checkout (mode démo si pas de clé)
- **Profil** : modification email/téléphone/adresse

## 🛠️ Stack technique

- **Frontend** : Next.js 14 (App Router), React 18, TypeScript, Tailwind CSS
- **Backend** : Next.js API Routes
- **ORM** : Prisma 5
- **Base de données** : PostgreSQL 14+
- **Auth élève** : iron-session + bcryptjs
- **Paiements** : Stripe (mode test/production)
- **Graphiques** : Recharts
- **Icônes** : Lucide React

## 📋 Prérequis

1. **Node.js** 18+ ([nodejs.org](https://nodejs.org))
2. **PostgreSQL** 14+

### Installation PostgreSQL

#### Option facile : Docker
```bash
docker run --name autoecole-db \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=autoecole \
  -p 5432:5432 \
  -d postgres:16
```

#### Windows / macOS / Linux
Voir https://www.postgresql.org/download/

## 🚀 Installation

```bash
# 1. Installer les dépendances
npm install

# 2. Configurer .env (déjà fourni avec valeurs par défaut)
# Modifier DATABASE_URL si nécessaire
# Générer une SESSION_SECRET aléatoire pour la production

# 3. Créer les tables
npm run db:push

# 4. Remplir avec les données de démo
npm run db:seed

# 5. Lancer en développement
npm run dev
```

L'application est disponible sur :
- **Admin** : http://localhost:3000
- **Portail élève** : http://localhost:3000/portail/login

## 👤 Comptes de démo

Tous les élèves ont le mot de passe : **`demo123`**

| CIN        | Élève             | État formation                |
|------------|-------------------|-------------------------------|
| `AB123456` | Sara El Amrani    | ⭐ Compte le plus complet (leçons + code + REA) |
| `CD789012` | Youssef Berrada   | Conduite 20h en cours        |
| `EF345678` | Lina Tazi         | Code seul                     |
| `GH901234` | Omar Fassi        | Permis B avec impayé          |
| `IJ567890` | Hajar Benali      | Conduite 10h débutante        |

Connectez-vous avec **AB123456 / demo123** pour voir tout le portail rempli.

## 💳 Paiements Stripe

L'application fonctionne en **2 modes** :

### Mode démo (par défaut)
Si aucune clé Stripe n'est configurée, les achats sont **simulés** : la facture est créée comme payée et le forfait est attribué directement à l'élève. Parfait pour tester.

### Mode production
1. Créer un compte sur https://stripe.com (gratuit)
2. Récupérer les clés sur https://dashboard.stripe.com/test/apikeys
3. Mettre à jour `.env` :
   ```
   STRIPE_SECRET_KEY=sk_test_xxx
   STRIPE_PUBLISHABLE_KEY=pk_test_xxx
   NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_xxx
   ```
4. L'élève sera redirigé vers Stripe Checkout pour payer

Cartes de test Stripe : `4242 4242 4242 4242` (n'importe quelle date future + CVV)

## 📜 Scripts utiles

```bash
npm run dev          # Développement
npm run build        # Build production
npm run start        # Lancer en production

npm run db:push      # Créer/synchroniser les tables
npm run db:seed      # Données de démo
npm run db:studio    # Explorateur visuel sur :5555
npm run db:reset     # ⚠️ Reset complet
```

## 🗂️ Structure du projet

```
auto-ecole-admin/
├── prisma/
│   ├── schema.prisma          # 9 modèles de données
│   └── seed.ts                # Données + questions code
├── app/
│   ├── layout.tsx             # Layout racine minimal
│   ├── globals.css
│   ├── (admin)/               # Pages admin (sidebar)
│   │   ├── layout.tsx
│   │   ├── page.tsx           # Dashboard
│   │   ├── eleves/
│   │   ├── moniteurs/
│   │   ├── vehicules/
│   │   ├── planning/
│   │   └── finances/
│   ├── portail/               # Portail élève
│   │   ├── login/page.tsx     # Page publique
│   │   ├── page.tsx           # Redirect → dashboard
│   │   └── (auth)/            # Routes protégées (auth requise)
│   │       ├── layout.tsx     # Vérifie session, sinon → /login
│   │       ├── dashboard/
│   │       ├── reservation/
│   │       ├── code/
│   │       │   └── examen/
│   │       ├── progression/
│   │       ├── paiement/
│   │       └── profil/
│   └── api/
│       ├── dashboard/
│       ├── eleves/, moniteurs/, vehicules/, etc.
│       └── portail/           # API du portail élève
│           ├── auth/          # Login / logout
│           ├── creneaux/      # Créneaux disponibles
│           ├── reserver/      # Créer une réservation
│           ├── annuler/       # Annuler (règle 48h)
│           ├── mes-lecons/
│           ├── questions/     # Code de la route
│           ├── resultats-code/
│           ├── competences/   # REA
│           ├── profil/
│           └── paiement-init/ # Stripe Checkout
├── components/
│   ├── Sidebar.tsx            # Sidebar admin
│   ├── ui.tsx                 # Card, Badge, Stat
│   └── portail/
│       ├── PortailNav.tsx     # Top nav portail
│       └── Quiz.tsx           # Composant quiz réutilisable
├── lib/
│   ├── prisma.ts
│   ├── session.ts             # iron-session helper
│   └── stripe.ts
└── .env
```

## 🔐 Sécurité

- **Mots de passe** : hashés avec bcryptjs (10 rounds)
- **Sessions** : iron-session (cookies signés HMAC, durée 30 jours)
- **Cookies** : httpOnly + secure en production + sameSite=lax
- **Routes protégées** : layout `(auth)` vérifie la session avant rendu
- **API protégée** : chaque route `/api/portail/*` valide la session
- **Élève ne peut accéder qu'à ses propres données** (eleveId issu de la session, pas du body)

## 🌐 Endpoints API principaux

### Admin
| Route                       | Méthodes              |
|-----------------------------|-----------------------|
| `/api/dashboard`            | GET                   |
| `/api/eleves`               | GET, POST             |
| `/api/eleves/[id]`          | GET, PATCH, DELETE    |
| `/api/moniteurs`            | GET, POST             |
| `/api/moniteurs/[id]`       | GET, PATCH, DELETE    |
| `/api/vehicules`            | GET, POST             |
| `/api/vehicules/[id]`       | PATCH, DELETE         |
| `/api/forfaits`             | GET, POST             |
| `/api/lecons`               | GET (?from&?to), POST |
| `/api/factures`             | GET, POST             |

### Portail élève (auth requise)
| Route                            | Méthodes        |
|----------------------------------|-----------------|
| `/api/portail/auth`              | POST (login), DELETE (logout) |
| `/api/portail/profil`            | GET, PATCH      |
| `/api/portail/creneaux`          | GET (?date&?moniteurId) |
| `/api/portail/reserver`          | POST            |
| `/api/portail/annuler`           | POST            |
| `/api/portail/mes-lecons`        | GET             |
| `/api/portail/questions`         | GET (?categorie&?examen) |
| `/api/portail/resultats-code`    | GET, POST       |
| `/api/portail/competences`       | GET             |
| `/api/portail/paiement-init`     | POST            |

## 📊 Modèles de données

9 modèles principaux (voir `prisma/schema.prisma`) :
- **Eleve** (avec mot de passe hashé)
- **Moniteur**
- **Vehicule**
- **Forfait**
- **Lecon**
- **Facture**
- **QuestionCode** + **ReponseCode** (code de la route)
- **ResultatCode** (résultats des quiz)
- **Competence** (suivi REA)

## 🎨 Personnalisation

- **Couleurs** : `tailwind.config.js` → modifier `brand` (bleu) et `accent` (orange/ambre)
- **Questions code** : ajouter dans `prisma/seed.ts` puis `npm run db:seed`
- **Forfaits** : modifier dans le seed ou via l'API admin

## 📦 Prochaines étapes

À développer :
- **Module Moniteur** : planning personnel, fiche de suivi REA, validation séances
- **Authentification admin** : NextAuth.js avec rôles (admin/moniteur)
- **Notifications** : email (Resend/SendGrid) + SMS (Twilio) + push
- **App mobile** : React Native partageant la même API
- **RGPD** : export des données, droit à l'oubli, page consentement
- **Webhook Stripe** : confirmation asynchrone des paiements

## 🐛 Dépannage

**"Can't reach database server"**
→ Vérifier que PostgreSQL tourne (`pg_isready` ou `docker ps`)

**"Module not found: bcryptjs"**
→ `npm install`

**Session expire après login**
→ La `SESSION_SECRET` dans `.env` doit faire au moins 32 caractères

**Tout réinitialiser**
→ `npm run db:reset` (supprime tout et re-seed)

**Explorer la DB**
→ `npm run db:studio` (Prisma Studio sur http://localhost:5555)
