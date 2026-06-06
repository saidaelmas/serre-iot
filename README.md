# 🌱 Projet IoT — Surveillance de Serre Connectée

Projet complet : **ESP32 + DHT22 + Blynk Cloud + PWA custom**

---

## 📦 Contenu du projet

| Fichier | Description |
|---------|-------------|
| `serre_esp32.ino` | Code Arduino pour l'ESP32 |
| `serre-pwa/index.html` | Ton application mobile (PWA) |
| `serre-pwa/manifest.json` | Métadonnées d'installation |
| `serre-pwa/service-worker.js` | Cache offline et installation |

---

## 🔧 ÉTAPE 1 — Configurer Blynk Cloud

1. Va sur [blynk.cloud](https://blynk.cloud) et crée un compte
2. **Developer Zone** → **+ New Template**
   - Hardware : **ESP32**
   - Connection Type : **WiFi**
3. Dans ton template, va dans l'onglet **Datastreams** et crée :
   - **V0** : Type *Virtual Pin*, Data Type *Double*, Min 0, Max 50, Unit °C (Temperature)
   - **V1** : Type *Virtual Pin*, Data Type *Double*, Min 0, Max 100, Unit % (Humidity)
   - **V2** : Type *Virtual Pin*, Data Type *Integer*, Min 0, Max 1 (Alert state)
4. Onglet **Events** → **+ Create Event** :
   - Code : `alerte_serre`
   - Name : Alerte Serre
   - Type : Warning
   - Notifications : Email + Push
5. **Search → Devices → + New Device → From template** : choisis ton template
6. Note ton **Auth Token** (clique sur le device → Device Info)

---

## 🔌 ÉTAPE 2 — Câblage matériel

```
DHT22 (face avant, grille vers toi)
┌─────────────┐
│  +  D NC  - │
│  │  │     │ │
│  │  │     └─→ GND ESP32
│  │  └────────→ GPIO 4 ESP32 (+ résistance 10kΩ vers 3.3V)
│  └───────────→ 3.3V ESP32
└─────────────┘
```

⚠️ **Résistance pull-up** : connecte une résistance de 10kΩ entre DATA et 3.3V. Sans elle, les lectures seront instables.

---

## 💻 ÉTAPE 3 — Programmer l'ESP32

1. Ouvre `serre_esp32.ino` dans Arduino IDE
2. Remplace dans le code :
   ```cpp
   #define BLYNK_TEMPLATE_ID   "TON_TEMPLATE_ID"
   #define BLYNK_AUTH_TOKEN    "TON_AUTH_TOKEN"
   char ssid[] = "NOM_DE_TON_WIFI";
   char pass[] = "MOT_DE_PASSE_WIFI";
   ```
3. Installe les bibliothèques (Croquis → Inclure une bibliothèque → Gérer les bibliothèques) :
   - **Blynk** (Volodymyr Shymanskyy)
   - **DHT sensor library** (Adafruit)
   - **Adafruit Unified Sensor** (Adafruit)
4. Outils → Type de carte → **ESP32 Dev Module**
5. Sélectionne le bon port COM
6. Upload (Ctrl+U)
7. Ouvre le moniteur série (115200 bauds) pour vérifier

---

## 📱 ÉTAPE 4 — Déployer la PWA

### Option A : Hébergement local rapide (pour tester)
```bash
cd serre-pwa
python -m http.server 8000
```
Puis ouvre `http://[IP-de-ton-PC]:8000` depuis ton téléphone (sur le même WiFi).

### Option B : GitHub Pages (recommandé, gratuit)
1. Crée un compte sur [github.com](https://github.com)
2. Crée un nouveau repository **public** appelé `serre-iot`
3. Upload les 3 fichiers du dossier `serre-pwa/`
4. Settings → Pages → Source : `main` branch → `/root` → Save
5. Attends 1-2 min, ton site sera à : `https://[ton-pseudo].github.io/serre-iot`

### Option C : Netlify (encore plus simple)
1. Va sur [netlify.com](https://app.netlify.com/drop)
2. Glisse-dépose ton dossier `serre-pwa`
3. URL générée immédiatement

---

## 📲 ÉTAPE 5 — Installer la PWA sur ton téléphone

### Android (Chrome)
1. Ouvre l'URL de ta PWA dans Chrome
2. Menu (⋮) → **Ajouter à l'écran d'accueil**
3. Ou clique le bouton vert "📥 Installer l'app" en bas à droite

### iOS (Safari)
1. Ouvre l'URL dans Safari
2. Bouton Partager (□↑) → **Sur l'écran d'accueil**

---

## ✅ ÉTAPE 6 — Premier lancement

1. Ouvre l'app installée
2. Colle ton **Auth Token Blynk** dans le champ
3. Clique **✅ Enregistrer et démarrer**
4. Les données apparaissent en temps réel toutes les 5 secondes !

---

## 🎯 Pour ta présentation école

**Points forts à mettre en avant :**
- Architecture IoT complète : **capteur → microcontrôleur → cloud → app**
- App **cross-platform** sans passer par les stores (PWA)
- **Notifications natives** + alertes visuelles
- Graphiques **temps réel** avec historique
- **Service Worker** pour fonctionnement offline (interface)
- Code modulable : seuils configurables, plusieurs capteurs possibles

**Améliorations possibles à mentionner :**
- Ajout d'une pompe d'arrosage automatique (relais sur GPIO)
- Stockage des données long terme (Firebase, MongoDB)
- Machine Learning pour prédire les besoins en eau
- Capteur d'humidité de sol (capacitif)
- Caméra ESP32-CAM pour photos quotidiennes

---

## 🐛 Dépannage

**L'ESP32 ne se connecte pas au WiFi ?**
- Vérifie que c'est un WiFi 2.4 GHz (l'ESP32 ne supporte pas le 5 GHz)
- Vérifie le SSID et mot de passe (sensible à la casse)

**Le DHT22 renvoie NaN ?**
- Vérifie la résistance pull-up 10kΩ
- Vérifie le câblage (VCC, DATA, GND)
- Augmente le délai dans le code (timer.setInterval à 10000)

**La PWA n'affiche pas les données ?**
- Vérifie l'Auth Token (copier-coller depuis Blynk)
- Ouvre la console du navigateur (F12) pour voir les erreurs
- Vérifie que ton ESP32 est bien en ligne (LED Blynk allumée dans le dashboard)

**Erreur CORS sur l'API Blynk ?**
- L'API Blynk autorise les requêtes cross-origin par défaut depuis HTTPS
- Sers ta PWA via HTTPS (GitHub Pages / Netlify le font automatiquement)
