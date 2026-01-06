# Interaction Frontend ↔ Backend - Application Caftan

## 📋 Table des matières
1. [Vue d'ensemble de l'architecture](#vue-densemble)
2. [Structure du Backend (Laravel)](#structure-backend)
3. [Structure du Frontend (React Native)](#structure-frontend)
4. [Endpoints API disponibles](#endpoints-api)
5. [Flux d'interaction détaillé](#flux-dinteraction)
6. [Exemples de code](#exemples-de-code)
7. [Format des données](#format-des-données)
8. [Gestion des erreurs](#gestion-des-erreurs)

---

## 🏗️ Vue d'ensemble de l'architecture {#vue-densemble}

```
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION MOBILE                      │
│                  (React Native - Frontend)                  │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Écran      │  │   Écran      │  │   Écran      │     │
│  │  Connexion   │  │  Catalogue   │  │  Réservation │     │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘     │
│         │                  │                  │             │
│         └──────────────────┼──────────────────┘             │
│                            │                                │
│                   ┌────────▼────────┐                       │
│                   │  Appels HTTP    │                       │
│                   │  (fetch/axios)   │                       │
│                   └────────┬────────┘                       │
└────────────────────────────┼────────────────────────────────┘
                             │
                             │ HTTP/HTTPS
                             │ JSON
                             │
┌────────────────────────────▼────────────────────────────────┐
│                  SERVEUR LARAVEL (Backend)                   │
│                                                              │
│  ┌────────────────────────────────────────────────────┐     │
│  │              routes/api.php                        │     │
│  │  (Définit les routes API)                         │     │
│  └──────────────┬─────────────────────────────────────┘     │
│                 │                                            │
│  ┌──────────────▼─────────────────────────────────────┐     │
│  │         Controllers (Logique métier)               │     │
│  │  - AuthController                                  │     │
│  │  - CaftanController                               │     │
│  │  - ReservationController                           │     │
│  │  - ProductController                               │     │
│  └──────────────┬─────────────────────────────────────┘     │
│                 │                                            │
│  ┌──────────────▼─────────────────────────────────────┐     │
│  │              Models (ORM Eloquent)                 │     │
│  │  - User                                           │     │
│  │  - Caftan                                         │     │
│  │  - Reservation                                    │     │
│  └──────────────┬─────────────────────────────────────┘     │
│                 │                                            │
│  ┌──────────────▼─────────────────────────────────────┐     │
│  │            Base de données MySQL                   │     │
│  │  (Tables: users, caftans, reservations, etc.)      │     │
│  └────────────────────────────────────────────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Principe de communication
- **Protocole** : HTTP/HTTPS
- **Format d'échange** : JSON (JavaScript Object Notation)
- **Méthodes HTTP** : GET, POST, PUT, DELETE
- **Base URL** : `http://votre-serveur.com/api` (ou `http://localhost:8000/api` en développement)

---

## 🔧 Structure du Backend (Laravel) {#structure-backend}

### 1. Routes API (`routes/api.php`)

Les routes sont préfixées avec `/api` automatiquement par Laravel.

```php
// Toutes les routes commencent par /api/
Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);
Route::get('/caftans', [CaftanController::class, 'index']);
Route::get('/caftans/{id}', [CaftanController::class, 'show']);
Route::post('/caftans', [CaftanController::class, 'store']);
Route::post('/reservations', [ReservationController::class, 'store']);
Route::get('/reservations/user/{id}', [ReservationController::class, 'userReservations']);
```

### 2. Controllers

Les contrôleurs traitent les requêtes HTTP et retournent des réponses JSON.

**Exemple : CaftanController**
- `index()` : Récupère tous les caftans
- `show($id)` : Récupère un caftan spécifique
- `store(Request $request)` : Crée un nouveau caftan

### 3. Models

Les modèles représentent les tables de la base de données et permettent d'interagir avec elles via Eloquent ORM.

---

## 📱 Structure du Frontend (React Native) {#structure-frontend}

### État actuel
L'application React Native utilise actuellement des **données statiques** (hardcodées) dans `App.js`. Pour une vraie interaction avec le backend, il faut :

1. **Installer une bibliothèque HTTP** : `axios` ou `fetch` (natif)
2. **Créer un service API** : Centraliser tous les appels HTTP
3. **Gérer l'authentification** : Stocker le token utilisateur
4. **Gérer les états** : Charger les données depuis l'API

---

## 🌐 Endpoints API disponibles {#endpoints-api}

### Base URL
```
http://localhost:8000/api
```
(En production, remplacez par l'URL de votre serveur)

### 1. Authentification

#### POST `/api/register`
**Description** : Inscription d'un nouvel utilisateur

**Requête** :
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

**Réponse (200)** :
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user",
  "created_at": "2025-12-15T10:00:00.000000Z",
  "updated_at": "2025-12-15T10:00:00.000000Z"
}
```

#### POST `/api/login`
**Description** : Connexion d'un utilisateur

**Requête** :
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Réponse (200)** :
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user",
  "created_at": "2025-12-15T10:00:00.000000Z",
  "updated_at": "2025-12-15T10:00:00.000000Z"
}
```

**Réponse (401 - Erreur)** :
```json
{
  "message": "Email ou mot de passe incorrect"
}
```

---

### 2. Caftans

#### GET `/api/caftans`
**Description** : Récupère la liste de tous les caftans

**Requête** : Aucun paramètre

**Réponse (200)** :
```json
[
  {
    "id": 1,
    "title": "Caftan Traditionnel",
    "description": "Un magnifique caftan traditionnel marocain",
    "size": "M",
    "color": "Rouge",
    "price": "150.00",
    "image_url": "http://localhost:8000/storage/caftans/image1.jpg"
  },
  {
    "id": 2,
    "title": "Caftan Moderne",
    "description": "Caftan moderne avec design contemporain",
    "size": "L",
    "color": "Bleu",
    "price": "200.00",
    "image_url": "http://localhost:8000/storage/caftans/image2.jpg"
  }
]
```

#### GET `/api/caftans/{id}`
**Description** : Récupère les détails d'un caftan spécifique

**Paramètres URL** :
- `id` : ID du caftan (ex: `/api/caftans/1`)

**Réponse (200)** :
```json
{
  "id": 1,
  "title": "Caftan Traditionnel",
  "description": "Un magnifique caftan traditionnel marocain",
  "size": "M",
  "color": "Rouge",
  "price": "150.00",
  "image_url": "http://localhost:8000/storage/caftans/image1.jpg"
}
```

**Réponse (404 - Erreur)** :
```json
{
  "message": "No query results for model [App\\Models\\Caftan] 1"
}
```

#### POST `/api/caftans`
**Description** : Crée un nouveau caftan (nécessite authentification admin)

**Requête (FormData)** :
```
title: "Nouveau Caftan"
description: "Description du caftan"
size: "M"
color: "Vert"
price: 180.00
image: [fichier image]
```

**Réponse (200)** :
```json
{
  "message": "Caftan créé avec succès",
  "caftan": {
    "id": 3,
    "title": "Nouveau Caftan",
    "size": "M",
    "color": "Vert",
    "price": "180.00",
    "image_url": "http://localhost:8000/storage/caftans/image3.jpg"
  }
}
```

#### GET `/api/caftans/{id}/image`
**Description** : Récupère l'image d'un caftan

**Réponse** : Fichier image (binaire)

---

### 3. Réservations

#### POST `/api/reservations`
**Description** : Crée une nouvelle réservation

**Requête** :
```json
{
  "user_id": 1,
  "caftan_id": 2,
  "start_date": "2025-12-20",
  "end_date": "2025-12-25",
  "status": "pending"
}
```

**Réponse (200)** :
```json
{
  "id": 1,
  "user_id": 1,
  "caftan_id": 2,
  "start_date": "2025-12-20",
  "end_date": "2025-12-25",
  "status": "pending",
  "created_at": "2025-12-15T10:00:00.000000Z",
  "updated_at": "2025-12-15T10:00:00.000000Z"
}
```

#### GET `/api/reservations/user/{id}`
**Description** : Récupère toutes les réservations d'un utilisateur

**Paramètres URL** :
- `id` : ID de l'utilisateur (ex: `/api/reservations/user/1`)

**Réponse (200)** :
```json
[
  {
    "id": 1,
    "user_id": 1,
    "caftan_id": 2,
    "start_date": "2025-12-20",
    "end_date": "2025-12-25",
    "status": "pending",
    "created_at": "2025-12-15T10:00:00.000000Z",
    "updated_at": "2025-12-15T10:00:00.000000Z"
  }
]
```

---

### 4. Produits

#### GET `/api/products`
**Description** : Récupère tous les produits

**Réponse (200)** :
```json
[
  {
    "id": 1,
    "name": "Produit 1",
    "price": "50.00",
    "stock_quantity": 10,
    "created_at": "2025-12-15T10:00:00.000000Z",
    "updated_at": "2025-12-15T10:00:00.000000Z"
  }
]
```

#### POST `/api/products`
**Description** : Crée un nouveau produit

**Requête** :
```json
{
  "name": "Nouveau Produit",
  "price": 75.50,
  "stock_quantity": 5
}
```

**Réponse (201)** :
```json
{
  "id": 2,
  "name": "Nouveau Produit",
  "price": "75.50",
  "stock_quantity": 5,
  "created_at": "2025-12-15T10:00:00.000000Z",
  "updated_at": "2025-12-15T10:00:00.000000Z"
}
```

---

## 🔄 Flux d'interaction détaillé {#flux-dinteraction}

### Scénario 1 : Connexion d'un utilisateur

```
1. Utilisateur saisit email/password dans React Native
   ↓
2. App.js appelle handleLogin()
   ↓
3. Requête HTTP POST vers /api/login
   POST http://localhost:8000/api/login
   Body: { "email": "...", "password": "..." }
   ↓
4. Laravel reçoit la requête
   - Route: routes/api.php → AuthController@login
   ↓
5. AuthController vérifie les credentials
   - Cherche l'utilisateur dans la DB
   - Vérifie le mot de passe avec Hash::check()
   ↓
6. Réponse JSON envoyée au frontend
   - Succès (200): { "id": 1, "name": "...", ... }
   - Erreur (401): { "message": "Email ou mot de passe incorrect" }
   ↓
7. React Native reçoit la réponse
   - Si succès: Stocke les infos utilisateur, navigue vers l'écran d'accueil
   - Si erreur: Affiche un message d'erreur
```

### Scénario 2 : Affichage du catalogue de caftans

```
1. Utilisateur ouvre l'écran "Catalogue"
   ↓
2. React Native envoie une requête GET
   GET http://localhost:8000/api/caftans
   ↓
3. Laravel traite la requête
   - Route: routes/api.php → CaftanController@index
   - Controller récupère tous les caftans depuis la DB
   - Transforme les données (ajoute image_url)
   ↓
4. Réponse JSON avec la liste des caftans
   [
     { "id": 1, "title": "...", "price": "...", ... },
     { "id": 2, "title": "...", "price": "...", ... }
   ]
   ↓
5. React Native reçoit les données
   - Met à jour l'état (useState)
   - Affiche les caftans dans une liste (FlatList)
```

### Scénario 3 : Création d'une réservation

```
1. Utilisateur clique sur "Louer" pour un caftan
   ↓
2. React Native prépare les données
   {
     "user_id": 1,
     "caftan_id": 2,
     "start_date": "2025-12-20",
     "end_date": "2025-12-25",
     "status": "pending"
   }
   ↓
3. Requête HTTP POST
   POST http://localhost:8000/api/reservations
   Headers: { "Content-Type": "application/json" }
   Body: { ... }
   ↓
4. Laravel traite la requête
   - Route: routes/api.php → ReservationController@store
   - Controller crée une nouvelle réservation dans la DB
   ↓
5. Réponse JSON avec la réservation créée
   {
     "id": 1,
     "user_id": 1,
     "caftan_id": 2,
     ...
   }
   ↓
6. React Native affiche un message de succès
   - Affiche une alerte "Réservation confirmée"
   - Retourne à l'écran précédent
```

---

## 💻 Exemples de code {#exemples-de-code}

### 1. Service API pour React Native

Créez un fichier `services/api.js` :

```javascript
// services/api.js
const API_BASE_URL = 'http://localhost:8000/api';

class ApiService {
  // Méthode générique pour les requêtes
  async request(endpoint, options = {}) {
    const url = `${API_BASE_URL}${endpoint}`;
    
    const config = {
      headers: {
        'Content-Type': 'application/json',
        ...options.headers,
      },
      ...options,
    };

    try {
      const response = await fetch(url, config);
      const data = await response.json();
      
      if (!response.ok) {
        throw new Error(data.message || 'Une erreur est survenue');
      }
      
      return data;
    } catch (error) {
      console.error('API Error:', error);
      throw error;
    }
  }

  // Authentification
  async login(email, password) {
    return this.request('/login', {
      method: 'POST',
      body: JSON.stringify({ email, password }),
    });
  }

  async register(name, email, password) {
    return this.request('/register', {
      method: 'POST',
      body: JSON.stringify({ name, email, password }),
    });
  }

  // Caftans
  async getCaftans() {
    return this.request('/caftans');
  }

  async getCaftan(id) {
    return this.request(`/caftans/${id}`);
  }

  // Réservations
  async createReservation(reservationData) {
    return this.request('/reservations', {
      method: 'POST',
      body: JSON.stringify(reservationData),
    });
  }

  async getUserReservations(userId) {
    return this.request(`/reservations/user/${userId}`);
  }
}

export default new ApiService();
```

### 2. Utilisation dans App.js (React Native)

```javascript
// App.js
import React, { useState, useEffect } from 'react';
import { View, Text, FlatList, TouchableOpacity, Alert } from 'react-native';
import ApiService from './services/api';

export default function App() {
  const [caftans, setCaftans] = useState([]);
  const [loading, setLoading] = useState(false);
  const [user, setUser] = useState(null);

  // Charger les caftans au montage du composant
  useEffect(() => {
    loadCaftans();
  }, []);

  // Fonction pour charger les caftans depuis l'API
  const loadCaftans = async () => {
    try {
      setLoading(true);
      const data = await ApiService.getCaftans();
      setCaftans(data);
    } catch (error) {
      Alert.alert('Erreur', error.message);
    } finally {
      setLoading(false);
    }
  };

  // Fonction de connexion
  const handleLogin = async (email, password) => {
    try {
      const userData = await ApiService.login(email, password);
      setUser(userData);
      // Stocker le token si vous utilisez l'authentification par token
      // AsyncStorage.setItem('token', userData.token);
      Alert.alert('Succès', 'Connexion réussie !');
    } catch (error) {
      Alert.alert('Erreur', error.message);
    }
  };

  // Fonction pour créer une réservation
  const handleReservation = async (caftanId) => {
    if (!user) {
      Alert.alert('Erreur', 'Vous devez être connecté');
      return;
    }

    try {
      const reservation = await ApiService.createReservation({
        user_id: user.id,
        caftan_id: caftanId,
        start_date: '2025-12-20',
        end_date: '2025-12-25',
        status: 'pending',
      });
      
      Alert.alert('Succès', 'Réservation créée avec succès !');
    } catch (error) {
      Alert.alert('Erreur', error.message);
    }
  };

  // Rendu de la liste des caftans
  const renderCaftan = ({ item }) => (
    <TouchableOpacity
      style={styles.caftanCard}
      onPress={() => handleReservation(item.id)}
    >
      <Text style={styles.title}>{item.title}</Text>
      <Text style={styles.price}>{item.price}€</Text>
      <Text style={styles.description}>{item.description}</Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      {loading ? (
        <Text>Chargement...</Text>
      ) : (
        <FlatList
          data={caftans}
          renderItem={renderCaftan}
          keyExtractor={(item) => item.id.toString()}
        />
      )}
    </View>
  );
}
```

### 3. Exemple avec Axios (alternative)

Si vous préférez utiliser Axios :

```bash
npm install axios
```

```javascript
// services/api.js avec Axios
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:8000/api',
  headers: {
    'Content-Type': 'application/json',
  },
});

// Intercepteur pour ajouter le token d'authentification
api.interceptors.request.use((config) => {
  const token = AsyncStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export const login = (email, password) => {
  return api.post('/login', { email, password });
};

export const getCaftans = () => {
  return api.get('/caftans');
};

export const createReservation = (data) => {
  return api.post('/reservations', data);
};
```

---

## 📊 Format des données {#format-des-données}

### Structure d'un Caftan

```typescript
interface Caftan {
  id: number;
  title: string;
  description: string | null;
  size: string;
  color: string;
  price: string; // Format décimal: "150.00"
  image_url: string; // URL complète de l'image
}
```

### Structure d'une Réservation

```typescript
interface Reservation {
  id: number;
  user_id: number;
  caftan_id: number;
  start_date: string; // Format: "YYYY-MM-DD"
  end_date: string;   // Format: "YYYY-MM-DD"
  status: string;     // "pending" | "confirmed" | "cancelled"
  created_at: string; // ISO 8601
  updated_at: string; // ISO 8601
}
```

### Structure d'un User

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  role: string; // "user" | "admin"
  created_at: string;
  updated_at: string;
}
```

---

## ⚠️ Gestion des erreurs {#gestion-des-erreurs}

### Codes de statut HTTP

- **200 OK** : Requête réussie
- **201 Created** : Ressource créée avec succès
- **400 Bad Request** : Données invalides
- **401 Unauthorized** : Non authentifié
- **404 Not Found** : Ressource introuvable
- **422 Unprocessable Entity** : Erreur de validation
- **500 Internal Server Error** : Erreur serveur

### Exemple de gestion d'erreurs

```javascript
const handleApiCall = async () => {
  try {
    const data = await ApiService.getCaftans();
    // Traiter les données
  } catch (error) {
    if (error.response) {
      // Erreur HTTP (4xx, 5xx)
      const status = error.response.status;
      const message = error.response.data.message;
      
      switch (status) {
        case 401:
          Alert.alert('Erreur', 'Vous devez vous connecter');
          break;
        case 404:
          Alert.alert('Erreur', 'Ressource introuvable');
          break;
        default:
          Alert.alert('Erreur', message || 'Une erreur est survenue');
      }
    } else if (error.request) {
      // Pas de réponse du serveur
      Alert.alert('Erreur', 'Impossible de contacter le serveur');
    } else {
      // Autre erreur
      Alert.alert('Erreur', error.message);
    }
  }
};
```

---

## 🔐 Authentification et sécurité

### Recommandations

1. **Utiliser HTTPS en production** : Ne jamais envoyer de données sensibles en HTTP
2. **Stocker les tokens** : Utiliser `AsyncStorage` ou `SecureStore` pour stocker les tokens
3. **Gérer l'expiration** : Vérifier si le token est encore valide
4. **Valider les données** : Toujours valider les données côté client ET serveur

### Exemple avec token JWT (si implémenté)

```javascript
// Stocker le token après connexion
const userData = await ApiService.login(email, password);
await AsyncStorage.setItem('token', userData.token);
await AsyncStorage.setItem('user', JSON.stringify(userData));

// Ajouter le token aux requêtes
api.interceptors.request.use((config) => {
  const token = await AsyncStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

---

## 🚀 Étapes pour intégrer l'API dans React Native

### 1. Installer les dépendances

```bash
cd react-native-app
npm install axios
# ou utiliser fetch (natif, pas besoin d'installation)
```

### 2. Créer le service API

Créez `react-native-app/services/api.js` avec le code fourni ci-dessus.

### 3. Modifier App.js

Remplacez les données statiques par des appels API :

```javascript
// Avant (données statiques)
const getAllCaftans = () => [
  { id: 1, name: 'Caftan Traditionnel', ... },
  // ...
];

// Après (données depuis l'API)
const [caftans, setCaftans] = useState([]);

useEffect(() => {
  const loadCaftans = async () => {
    try {
      const data = await ApiService.getCaftans();
      setCaftans(data);
    } catch (error) {
      Alert.alert('Erreur', error.message);
    }
  };
  loadCaftans();
}, []);
```

### 4. Tester la connexion

Assurez-vous que :
- Le serveur Laravel est démarré (`php artisan serve`)
- L'URL de l'API est correcte
- CORS est configuré si nécessaire
- Les routes API sont accessibles

---

## 📝 Notes importantes

1. **CORS** : Si vous testez depuis un émulateur Android, utilisez `http://10.0.2.2:8000` au lieu de `localhost`
2. **iOS Simulator** : Utilisez `http://localhost:8000` normalement
3. **Appareil physique** : Utilisez l'IP locale de votre machine (ex: `http://192.168.1.100:8000`)
4. **Production** : Configurez une URL de production sécurisée avec HTTPS

---

## 🔗 Ressources supplémentaires

- [Documentation Laravel API](https://laravel.com/docs/api)
- [React Native Networking](https://reactnative.dev/docs/network)
- [Axios Documentation](https://axios-http.com/docs/intro)
- [AsyncStorage](https://react-native-async-storage.github.io/async-storage/)

---

*Documentation générée le : 2025-12-15*
