# 📱 Explication du Frontend - Application Caftan Location

## 🎯 Vue d'ensemble

Le **frontend** est la partie de l'application que l'utilisateur voit et avec laquelle il interagit. C'est une application mobile développée avec **React Native** et **Expo**.

---

## 📂 Structure du Projet

```
react-native-app/
├── App.js                    ← Fichier principal (tout le code)
├── package.json              ← Liste des dépendances
├── assets/                   ← Images et ressources
│   └── images/
│       ├── caftan0.jpg
│       ├── caftan1.jpg
│       └── ...
└── node_modules/             ← Bibliothèques installées
```

---

## 🏗️ Architecture de l'Application

L'application est composée de **5 écrans principaux** :

```
┌─────────────┐
│   LOGIN     │  ← Écran de connexion/inscription
└──────┬──────┘
       │ (connexion réussie)
       ▼
┌─────────────┐
│    HOME     │  ← Écran d'accueil avec menu
└──────┬──────┘
       │
       ├──────────────┬──────────────┐
       ▼              ▼              ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│ CATALOG  │  │ PROFILE  │  │ (autres) │
└────┬─────┘  └──────────┘  └──────────┘
     │
     │ (clic sur un caftan)
     ▼
┌─────────────┐
│  DETAILS    │  ← Détails d'un caftan
└─────────────┘
```

---

## 📱 Les 5 Écrans Détaillés

### 1. Écran de Connexion (`login`)

**Fonction** : Permet à l'utilisateur de se connecter ou de s'inscrire

**Éléments visuels** :
- Logo avec dégradé (rose → violet)
- Titre "Caftan Location"
- Deux onglets : "Connexion" et "Inscription"
- Champ Email
- Champ Mot de passe
- Bouton "Se connecter" / "S'inscrire"

**Fonctionnalités** :
- Vérifie que les champs sont remplis
- Compare email/mot de passe avec les identifiants de test
- Affiche un message d'erreur si incorrect
- Redirige vers l'écran d'accueil si connexion réussie

**Code clé** :
```javascript
const [email, setEmail] = useState('');
const [password, setPassword] = useState('');

const handleLogin = () => {
  if (email === TEST_EMAIL && password === TEST_PASSWORD) {
    setCurrentScreen('home'); // Change d'écran
  }
};
```

---

### 2. Écran d'Accueil (`home`)

**Fonction** : Menu principal après connexion

**Éléments visuels** :
- En-tête avec titre et bouton "Déconnexion"
- Message de bienvenue
- Deux cartes de menu :
  - 👗 Catalogue
  - 👤 Profil
- Statistiques (nombre de caftans disponibles, en location)

**Fonctionnalités** :
- Navigation vers le catalogue
- Navigation vers le profil
- Déconnexion avec confirmation

**Code clé** :
```javascript
<TouchableOpacity onPress={() => setCurrentScreen('catalog')}>
  <Text>Catalogue</Text>
</TouchableOpacity>
```

---

### 3. Écran Catalogue (`catalog`)

**Fonction** : Affiche la liste de tous les caftans disponibles

**Éléments visuels** :
- En-tête avec bouton "Retour"
- Liste de cartes (une par caftan) :
  - Image du caftan
  - Nom du caftan
  - Prix de location

**Fonctionnalités** :
- Affiche 9 caftans (données statiques)
- Clic sur une carte → ouvre l'écran de détails
- Scroll vertical pour voir tous les caftans

**Données affichées** :
```javascript
const caftans = [
  {
    id: 1,
    name: 'Caftan Traditionnel',
    rentalPrice: 150,
    image: require('./assets/images/caftan0.jpg')
  },
  // ... 8 autres caftans
];
```

---

### 4. Écran Détails (`details`)

**Fonction** : Affiche les détails complets d'un caftan sélectionné

**Éléments visuels** :
- Grande image du caftan
- Nom du caftan
- Deux boîtes de prix :
  - Prix de location (€/jour)
  - Prix d'achat (€)
- Description détaillée
- Caractéristiques :
  - Taille
  - Matériau
- Deux boutons d'action :
  - "Louer" (rose/violet)
  - "Acheter" (violet/rose)

**Fonctionnalités** :
- Affiche toutes les informations du caftan
- Bouton "Louer" → Affiche une alerte de confirmation
- Bouton "Acheter" → Affiche une alerte de confirmation
- Retour au catalogue après action

**Code clé** :
```javascript
const handleRent = () => {
  Alert.alert(
    'Location confirmée',
    `Vous avez choisi de louer "${selectedCaftan.name}"`
  );
};
```

---

### 5. Écran Profil (`profile`)

**Fonction** : Affiche les informations de l'utilisateur et ses réservations

**Éléments visuels** :
- Avatar avec initiale (dégradé)
- Nom de l'utilisateur
- Email
- Section "Informations" :
  - Téléphone
  - Adresse
- Section "Mes locations" :
  - Cartes de réservations avec :
    - Nom du caftan
    - Dates (début/fin)
    - Statut

**Fonctionnalités** :
- Affiche les informations utilisateur
- Liste les réservations (données statiques pour l'instant)

---

## 🔄 Comment ça Fonctionne ?

### 1. Gestion de l'État (State)

L'application utilise `useState` pour gérer les données :

```javascript
// État pour savoir quel écran afficher
const [currentScreen, setCurrentScreen] = useState('login');

// État pour les données de connexion
const [email, setEmail] = useState('');
const [password, setPassword] = useState('');

// État pour le caftan sélectionné
const [selectedCaftan, setSelectedCaftan] = useState(null);
```

**Principe** :
- `useState` crée une variable qui peut changer
- Quand la variable change, React Native **réaffiche** automatiquement l'écran
- `setCurrentScreen('home')` change l'écran affiché

---

### 2. Navigation entre Écrans

L'application n'utilise **pas** de bibliothèque de navigation. Elle utilise une approche simple :

```javascript
// Dans App.js
if (currentScreen === 'login') {
  return <LoginScreen />;
}
if (currentScreen === 'home') {
  return <HomeScreen />;
}
// etc.
```

**Avantages** :
- Simple à comprendre
- Pas de dépendance supplémentaire
- Parfait pour une petite application

**Inconvénients** :
- Pas d'historique de navigation
- Difficile à gérer pour beaucoup d'écrans

---

### 3. Affichage Conditionnel

L'application affiche différents écrans selon la valeur de `currentScreen` :

```javascript
export default function App() {
  const [currentScreen, setCurrentScreen] = useState('login');
  
  // Écran de connexion
  if (currentScreen === 'login') {
    return <LoginView />;
  }
  
  // Écran d'accueil
  if (currentScreen === 'home') {
    return <HomeView />;
  }
  
  // Écran catalogue
  if (currentScreen === 'catalog') {
    return <CatalogView />;
  }
  
  // etc.
}
```

---

## 🎨 Composants React Native Utilisés

### Composants de Base

| Composant | Utilisation |
|-----------|-------------|
| `View` | Conteneur (comme `<div>` en HTML) |
| `Text` | Texte affiché |
| `TextInput` | Champ de saisie (email, password) |
| `TouchableOpacity` | Bouton cliquable |
| `Image` | Image |
| `ScrollView` | Zone scrollable |
| `SafeAreaView` | Zone sûre (évite les encoches) |
| `StatusBar` | Barre de statut du téléphone |
| `Alert` | Popup d'alerte |

### Exemple d'utilisation :

```javascript
<View style={styles.container}>
  <Text style={styles.title}>Titre</Text>
  <TextInput
    placeholder="Email"
    value={email}
    onChangeText={setEmail}
  />
  <TouchableOpacity onPress={handleLogin}>
    <Text>Se connecter</Text>
  </TouchableOpacity>
</View>
```

---

## 🎨 Styling (Styles)

### Méthode : StyleSheet

Tous les styles sont définis dans un objet `StyleSheet.create()` à la fin du fichier :

```javascript
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFFFFF',
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#424242',
  },
  button: {
    backgroundColor: '#9C27B0',
    padding: 16,
    borderRadius: 8,
  },
});
```

### Propriétés CSS utilisées :

- `flex: 1` → Prend tout l'espace disponible
- `backgroundColor` → Couleur de fond
- `fontSize` → Taille du texte
- `fontWeight` → Gras du texte
- `padding` → Espacement interne
- `margin` → Espacement externe
- `borderRadius` → Coins arrondis
- `color` → Couleur du texte

---

## 🌈 Dégradés (LinearGradient)

L'application utilise `expo-linear-gradient` pour créer des dégradés de couleurs :

```javascript
import { LinearGradient } from 'expo-linear-gradient';

<LinearGradient
  colors={['#E91E63', '#9C27B0']}  // Rose → Violet
  start={{ x: 0, y: 0 }}
  end={{ x: 1, y: 1 }}
  style={styles.gradient}
>
  <Text>Contenu</Text>
</LinearGradient>
```

**Utilisé pour** :
- Logo
- Boutons
- Cartes de menu
- Avatar

---

## 📊 Données de l'Application

### Données Statiques (Hardcodées)

Actuellement, l'application utilise des **données statiques** (écrites directement dans le code) :

```javascript
// Données de connexion
const TEST_EMAIL = 'admin@caftan.com';
const TEST_PASSWORD = 'admin123';

// Liste des caftans
const getAllCaftans = () => [
  {
    id: 1,
    name: 'Caftan Traditionnel',
    rentalPrice: 150,
    purchasePrice: 1200,
    description: '...',
    size: 'Taille unique',
    material: 'Soie et broderie dorée',
    image: require('./assets/images/caftan0.jpg')
  },
  // ... 8 autres
];
```

**Avantages** :
- Fonctionne sans serveur
- Rapide pour les tests
- Pas besoin de connexion internet

**Inconvénients** :
- Données fixes (ne changent pas)
- Pas de vraie interaction avec le backend
- Impossible d'ajouter/modifier des caftans

---

## 🔄 Flux de Données

### Exemple : Connexion

```
1. Utilisateur tape email/password
   ↓
2. onChangeText met à jour l'état
   setEmail(text)
   setPassword(text)
   ↓
3. Utilisateur clique sur "Se connecter"
   ↓
4. handleLogin() est appelé
   ↓
5. Vérification des identifiants
   ↓
6. Si correct → setCurrentScreen('home')
   ↓
7. React Native réaffiche l'écran d'accueil
```

### Exemple : Affichage du Catalogue

```
1. Utilisateur clique sur "Catalogue"
   ↓
2. setCurrentScreen('catalog')
   ↓
3. getAllCaftans() retourne la liste
   ↓
4. map() crée une carte pour chaque caftan
   ↓
5. Affichage de toutes les cartes
```

---

## 🖼️ Gestion des Images

### Images Locales

Les images sont stockées dans `assets/images/` et chargées avec `require()` :

```javascript
<Image
  source={require('./assets/images/caftan1.jpg')}
  style={styles.image}
  resizeMode="cover"
/>
```

**Propriétés importantes** :
- `source` : Chemin de l'image
- `style` : Dimensions et apparence
- `resizeMode` : Comment l'image s'adapte
  - `cover` : Remplit l'espace (peut couper)
  - `contain` : Affiche toute l'image
  - `stretch` : Étire l'image

---

## ⚠️ Gestion des Erreurs

### Affichage des Erreurs

```javascript
const [showError, setShowError] = useState(false);
const [errorMessage, setErrorMessage] = useState('');

// Afficher une erreur
setErrorMessage('Email ou mot de passe incorrect');
setShowError(true);

// Dans le JSX
{showError && (
  <View style={styles.errorContainer}>
    <Text style={styles.errorText}>{errorMessage}</Text>
  </View>
)}
```

### Alertes

```javascript
Alert.alert(
  'Titre',
  'Message',
  [
    { text: 'Annuler', onPress: () => {} },
    { text: 'OK', onPress: () => {} }
  ]
);
```

---

## 🎯 Points Clés à Retenir

### 1. **React Native = JavaScript pour Mobile**
   - Code JavaScript
   - Composants réutilisables
   - Même logique que React (web)

### 2. **État (State) = Données qui Changent**
   - `useState` pour créer un état
   - Quand l'état change → l'écran se met à jour

### 3. **Navigation Simple**
   - Variable `currentScreen` détermine l'écran affiché
   - `setCurrentScreen('home')` change d'écran

### 4. **Données Statiques**
   - Actuellement, tout est écrit dans le code
   - Pour connecter au backend → utiliser `fetch` ou `axios`

### 5. **Styling avec StyleSheet**
   - Styles définis dans un objet
   - Propriétés CSS similaires au web

---

## 📝 Résumé Simple

**Le frontend c'est :**

1. **5 écrans** : Login, Home, Catalog, Details, Profile
2. **Navigation** : Changement d'écran avec `setCurrentScreen()`
3. **Données** : Stockées dans `useState` (état)
4. **Interface** : Composants React Native (View, Text, Button, etc.)
5. **Style** : StyleSheet avec propriétés CSS
6. **Images** : Stockées localement dans `assets/images/`

**Fonctionnement global :**
- L'utilisateur interagit avec l'interface
- Les actions modifient l'état (`useState`)
- React Native réaffiche automatiquement l'écran
- Navigation entre écrans selon les actions

---

## 🚀 Pour Aller Plus Loin

### Prochaines Étapes

1. **Connecter au Backend**
   - Remplacer les données statiques par des appels API
   - Utiliser `fetch` ou `axios`
   - Charger les caftans depuis le serveur

2. **Améliorer la Navigation**
   - Utiliser React Navigation
   - Ajouter un historique
   - Transitions animées

3. **Gérer l'Authentification**
   - Stocker le token utilisateur
   - Vérifier la session
   - Déconnexion automatique

4. **Ajouter des Fonctionnalités**
   - Recherche de caftans
   - Filtres (taille, couleur, prix)
   - Panier d'achat
   - Notifications

---

*Documentation générée le : 2025-12-15*
