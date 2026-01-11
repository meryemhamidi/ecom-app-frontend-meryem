# 🛒 Frontend E-Commerce Microservices (Angular)

## 📋 Table des Matières

- [📖 À Propos du Projet](#-à-propos-du-projet)
- [🏗️ Architecture du Système](#️-architecture-du-système)
- [🔧 Technologies Utilisées](#-technologies-utilisées)
- [📂 Structure du Projet](#-structure-du-projet)
- [🚀 Démarrage Rapide](#-démarrage-rapide)
- [⚙️ Configuration](#️-configuration)
- [📡 API Endpoints](#-api-endpoints)
- [📚 Ressources Pédagogiques](#-ressources-pédagogiques)
- [👤 Auteur](#-auteur)

---

## 📖 À Propos du Projet

Application frontend **Angular 19** consommant un backend e-commerce basé microservices (inventory, customer, billing) via un **API Gateway**. Elle offre la consultation des produits, des clients, des factures et des détails de facture en s’appuyant sur le proxy local pour router les appels.

### Fonctionnalités Principales

- 🛍️ **Catalogue Produits** : liste des produits depuis le service `inventory-service`.
- 👥 **Gestion Clients** : affichage des clients et navigation vers leurs factures.
- 🧾 **Factures** : liste globale ou filtrée par client depuis `billing-service`.
- 📑 **Détails de facture** : items, totaux, info client et produits associés.
- 🔀 **Proxy Angular** : routage des appels API vers le gateway (port 8888).
- 🌐 **SSR optionnel** : rendu côté serveur via Angular Universal + Express.

### Objectifs Pédagogiques

Projet réalisé dans le cadre du cours **J2EE** pour pratiquer :

- ✅ Consommation de microservices REST via API Gateway  
- ✅ Configuration proxy Angular pour le développement local  
- ✅ Navigation Angular standalone components + router  
- ✅ Rendu SSR (Angular Universal) et déploiement Express  
- ✅ Intégration UI rapide avec **Bootstrap 5**

---

## 🏗️ Architecture du Système

```text
┌──────────────────────────────────────────────────────────────┐
│                 FRONTEND ANGULAR (SSR ready)                 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Browser / Client ──────────► Angular App (port 4200)        │
│        ▲                              │                      │
│        │                              │ HTTP                 │
│        │                              ▼                      │
│  ┌────────────────────────────────────────────────────────┐  │
│  │                API Gateway (port 8888)                 │  │
│  └─▲───────────────┬───────────────────────┬──────────────┘  │
│    │               │                       │                 │
│    │               │                       │                 │
│    ▼               ▼                       ▼                 │
│ inventory-service  customer-service   billing-service        │
│ (produits)         (clients)           (factures + items)    │
└──────────────────────────────────────────────────────────────┘
```

### Flux de Données

```text
Client → /inventory-service/api/products → Liste produits
Client → /customer-service/api/customers → Liste clients
Client → /billing-service/api/bills?projection=fullBill → Factures
Client → /billing-service/api/bills/{id} → Détails facture + items
```

### Modules & Navigation

- `/products` : catalogue produits.  
- `/customers` : liste clients + bouton « View Bills ».  
- `/bills` : factures globales.  
- `/bills/:customerId` : factures d’un client.  
- `/bill-details/:billId` : détail complet d’une facture.

---

## 🔧 Technologies Utilisées

### Frontend

| Technologie | Version | Description |
| ----------- | ------- | ----------- |
| **Angular** | 19.x | Framework principal (standalone components) |
| **Angular Universal** | 19.x | Rendu SSR + Express |
| **RxJS** | 7.8.x | Flux asynchrones |
| **Bootstrap** | 5.3.x | UI & layout |

### Outils & Build

| Outil | Usage |
| ----- | ----- |
| **Angular CLI** (`ng`) | Dev server, build, tests |
| **TypeScript** | Typage statique |
| **Karma/Jasmine** | Tests unitaires |
| **Express** | Serveur SSR |

### Backend attendu (hors repo)

| Service | Rôle | Port (proxy) |
| ------- | ---- | ------------ |
| **API Gateway** | Routage vers microservices | 8888 |
| **inventory-service** | Produits | via gateway |
| **customer-service** | Clients | via gateway |
| **billing-service** | Factures & items | via gateway |

---

## 📂 Structure du Projet

```text
ecom-app-frontend-meryem/
├── angular.json                     # Configuration Angular CLI
├── package.json                     # Scripts & dépendances
├── proxy.conf.json                  # Proxy dev vers API Gateway (8888)
├── src/
│   ├── main.ts                      # Bootstrap client
│   ├── main.server.ts               # Bootstrap SSR
│   ├── server.ts                    # Serveur Express SSR
│   ├── styles.css                   # Styles globaux + Bootstrap
│   └── app/
│       ├── app.routes.ts            # Routes principales
│       ├── app.component.*          # Shell + navbar
│       ├── products/                # Liste produits (inventory-service)
│       ├── customers/               # Liste clients
│       ├── bills/                   # Factures (global/client)
│       ├── bills-details/           # Détails facture + items
│       ├── services/consumer.service.ts # Appels vers gateway
│       └── models/                  # Interfaces Product/Customer/Bill/Item
└── ...
```

---

## 🚀 Démarrage Rapide

### Prérequis

| Outil | Version | Vérification |
| ----- | ------- | ------------ |
| **Node.js** | 18+ recommandé | `node -v` |
| **npm** | 9+ | `npm -v` |
| **Angular CLI** | 19+ | `ng version` |
| **Backend** | API Gateway + microservices actifs sur `localhost:8888` | - |

### Étape 1 : Installer les dépendances

```bash
npm install
```

### Étape 2 : Lancer le frontend en dev (proxy actif)

```bash
npm start           # alias: ng serve --proxy-config proxy.conf.json
# Navigate to http://localhost:4200
```

### Étape 3 : Lancer en SSR (optionnel)

```bash
npm run build       # build SSR + client
npm run serve:ssr:ecom-app-frontend   # démarre l'app SSR sur http://localhost:4000
```

### Tests unitaires

```bash
npm test
```

---

## ⚙️ Configuration

### Proxy Angular (dev) — `proxy.conf.json`

```json
{
  "/customer-service": { "target": "http://localhost:8888", "secure": false, "changeOrigin": true },
  "/inventory-service": { "target": "http://localhost:8888", "secure": false, "changeOrigin": true },
  "/billing-service": { "target": "http://localhost:8888", "secure": false, "changeOrigin": true }
}
```

- Permet d’appeler les microservices via le même host/port que le dev server Angular (`http://localhost:4200`), évitant CORS.
- Assurez-vous que l’API Gateway écoute `localhost:8888`.

### Scripts npm clés (`package.json`)

| Script | Action |
| ------ | ------ |
| `npm start` | `ng serve` avec proxy (dev) |
| `npm run build` | Build production + SSR |
| `npm run watch` | Build continu (dev) |
| `npm test` | Tests unitaires |
| `npm run serve:ssr:ecom-app-frontend` | Démarrage SSR (Express) |

---

## 📡 API Endpoints

Endpoints consommés (via proxy) par le frontend :

| Méthode | Endpoint | Description |
| ------- | -------- | ----------- |
| `GET` | `/inventory-service/api/products` | Liste des produits (inventory) |
| `GET` | `/customer-service/api/customers` | Liste des clients |
| `GET` | `/billing-service/api/bills?projection=fullBill` | Liste des factures (avec items) |
| `GET` | `/billing-service/api/bills/search/findByCustomerId?customerId={id}&projection=fullBill` | Factures par client |
| `GET` | `/billing-service/bills/{id}` | Détails d’une facture |

### Exemples d’Utilisation (via curl)

```bash
curl http://localhost:4200/inventory-service/api/products
curl "http://localhost:4200/billing-service/api/bills/search/findByCustomerId?customerId=1&projection=fullBill"
curl http://localhost:4200/billing-service/bills/1
```

### Format de données (extraits)

#### Product

```json
{ "id": 1, "name": "Laptop", "price": 1200, "quantity": 15 }
```

#### Bill (projection `fullBill`)

```json
{
  "id": 10,
  "billingDate": "2025-01-01",
  "customerId": 1,
  "productItems": [
    { "productId": "P1", "unitPrice": 100, "quantity": 2, "product": { "name": "Laptop" } }
  ],
  "customer": { "id": 1, "name": "Alice", "email": "alice@test.com" }
}
```

---

## 📚 Ressources Pédagogiques

| Ressource | Lien | Description |
| --------- | ---- | ----------- |
| Angular Docs | [angular.dev](https://angular.dev) | Documentation officielle |
| Angular Universal | [angular.dev/guide/ssr](https://angular.dev/guide/ssr) | Guide SSR |
| Bootstrap 5 | [getbootstrap.com](https://getbootstrap.com) | Composants UI |
| RxJS | [rxjs.dev](https://rxjs.dev) | Programmation réactive |

---

## 👤 Auteur

<div align="center"> 

**Meryem HAMIDI**  
Étudiante en 5ème année Ingénierie Informatique et Réseaux (5IIR)  
École Marocaine des Sciences de l'Ingénieur (EMSI)

[![GitHub](https://img.shields.io/badge/GitHub-MeryemHamidi-181717?style=for-the-badge&logo=github)](https://github.com/MeryemHamidi)
