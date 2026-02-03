# BRIEF COMPLET : Refactorisation Configurateur 3D Maison

## 🎯 OBJECTIF
Refactoriser le configurateur 3D pour appliquer EXACTEMENT les mêmes améliorations UX/UI, fonctionnalités et tracking que le configurateur Tradi'home.

---

## 📋 TABLE DES MATIÈRES
1. [Architecture et Design System](#1-architecture-et-design-system)
2. [Améliorations UX/UI Desktop](#2-améliorations-uxui-desktop)
3. [Refonte Mobile Complète](#3-refonte-mobile-complète)
4. [Section CTA et Formulaires](#4-section-cta-et-formulaires)
5. [Tracking Complet (Google Ads + GA4 + Meta Pixel)](#5-tracking-complet)
6. [Intégration Calendly](#6-intégration-calendly)
7. [Webhook Make.com](#7-webhook-makecom)
8. [Corrections et Optimisations](#8-corrections-et-optimisations)
9. [Configuration Spécifique](#9-configuration-spécifique)

---

## 1. ARCHITECTURE ET DESIGN SYSTEM

### Layout Principal - Style Porsche
**Objectif** : Layout 60/40 inspiré du configurateur Porsche avec accordéons épurés.

#### Structure HTML
```html
<div class="config-content">
  <!-- LEFT SIDE (60%) - Accordéons de configuration -->
  <div class="config-left">
    <!-- Accordéon 1 : Lifestyle -->
    <div class="accordion-section">
      <div class="accordion-header" onclick="toggleAccordion(this)">
        <div class="header-content">
          <h3>🏡 Quel est votre style de vie ?</h3>
          <span class="selected-value">Choisir une option</span>
        </div>
        <span class="arrow">▼</span>
      </div>
      <div class="accordion-content">
        <!-- Options en cards -->
      </div>
    </div>

    <!-- Accordéon 2 : Terrain -->
    <!-- Accordéon 3 : Timeline -->
    <!-- Accordéons suivants : Options de configuration -->
  </div>

  <!-- RIGHT SIDE (40%) - Visualisation 3D + Récapitulatif -->
  <div class="config-right">
    <div class="viewer-container">
      <model-viewer src="model.glb" ...></model-viewer>
    </div>

    <div class="price-section">
      <h3>Récapitulatif</h3>
      <!-- Détails prix + performance énergétique -->
    </div>
  </div>
</div>

<!-- Section CTA (séparée, en dessous) -->
<div class="cta-section">
  <!-- 2 cards : PDF + Calendly -->
</div>
```

#### CSS - Layout 60/40
```css
.config-content {
  display: grid;
  grid-template-columns: 1fr 1.2fr; /* 60% gauche / 40% droite */
  gap: 1.5rem;
  padding: 2rem;
  max-width: 1600px;
  margin: 0 auto;
}

.config-left {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.config-right {
  position: sticky;
  top: 2rem;
  height: fit-content;
}
```

### Accordéons - Design Épuré
```css
.accordion-section {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  overflow: hidden;
  transition: all 0.3s ease;
}

.accordion-header {
  padding: 1.5rem;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: white;
  transition: background 0.3s ease;
}

.accordion-header:hover {
  background: #f8f9fa;
}

.accordion-header.active {
  background: #f0f2f5;
  border-bottom: 2px solid #0066cc;
}

.accordion-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.4s ease, padding 0.4s ease;
  padding: 0; /* ⚠️ IMPORTANT : padding 0 quand fermé */
}

.accordion-content.active {
  max-height: 2000px;
  padding: 1.5rem; /* padding seulement quand ouvert */
}

.arrow {
  transition: transform 0.3s ease;
}

.accordion-header.active .arrow {
  transform: rotate(180deg);
}
```

---

## 2. AMÉLIORATIONS UX/UI DESKTOP

### A. Cards de Sélection - Design Porsche

#### Structure HTML
```html
<div class="option-cards">
  <div class="option-card" onclick="selectOption(this, 'category', 'value')">
    <div class="card-icon">🏡</div>
    <h4>Titre de l'option</h4>
    <p>Description courte</p>
    <div class="card-check">✓</div>
  </div>
  <!-- Répéter pour chaque option -->
</div>
```

#### CSS Cards
```css
.option-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
}

.option-card {
  background: white;
  border: 2px solid #e0e0e0;
  border-radius: 12px;
  padding: 1.5rem;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  text-align: center;
}

.option-card:hover {
  border-color: #0066cc;
  transform: translateY(-4px);
  box-shadow: 0 8px 16px rgba(0,102,204,0.15);
}

.option-card.selected {
  border-color: #0066cc;
  background: linear-gradient(135deg, #f0f7ff 0%, #e6f2ff 100%);
  box-shadow: 0 4px 12px rgba(0,102,204,0.2);
}

.card-check {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 24px;
  height: 24px;
  background: #0066cc;
  color: white;
  border-radius: 50%;
  display: none;
  align-items: center;
  justify-content: center;
}

.option-card.selected .card-check {
  display: flex;
}

.card-icon {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.option-card h4 {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: #1a1a1a;
}

.option-card p {
  font-size: 0.9rem;
  color: #666;
  line-height: 1.4;
}
```

### B. Visualisation 3D - Model Viewer

#### Configuration Model Viewer
```html
<model-viewer
  src="path/to/model.glb"
  alt="Modèle 3D de la maison"
  camera-controls
  touch-action="pan-y"
  auto-rotate
  rotation-per-second="30deg"
  camera-orbit="45deg 75deg 8m"
  min-camera-orbit="auto auto 5m"
  max-camera-orbit="auto auto 15m"
  shadow-intensity="1"
  exposure="1"
  environment-image="neutral"
  style="width: 100%; height: 500px; background: #f5f5f5; border-radius: 12px;">

  <div class="loading-overlay" slot="poster">
    <div class="spinner"></div>
    <p>Chargement du modèle 3D...</p>
  </div>
</model-viewer>
```

#### CSS Loading State
```css
.loading-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #f5f5f5;
}

.spinner {
  width: 50px;
  height: 50px;
  border: 4px solid #e0e0e0;
  border-top-color: #0066cc;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

### C. Récapitulatif Prix + Performance Énergétique

#### HTML Récapitulatif
```html
<div class="price-section">
  <h3>Récapitulatif de votre configuration</h3>

  <!-- Options sélectionnées -->
  <div class="selected-options">
    <div class="option-line">
      <span>Bardage</span>
      <strong id="selected-bardage">-</strong>
    </div>
    <div class="option-line">
      <span>Menuiserie</span>
      <strong id="selected-menuiserie">-</strong>
    </div>
    <!-- Répéter pour chaque option -->
  </div>

  <!-- Prix total -->
  <div class="price-total">
    <div class="price-row">
      <span>Prix total HT</span>
      <strong id="total-ht">0 €</strong>
    </div>
    <div class="price-row main-price">
      <span>Prix total TTC</span>
      <strong id="total-ttc">0 €</strong>
    </div>
  </div>

  <!-- Performance énergétique -->
  <div class="energy-performance">
    <h4>Performance énergétique</h4>
    <div class="energy-badges">
      <div class="energy-badge" id="badge-dpe">
        <span class="badge-label">DPE</span>
        <span class="badge-value">A</span>
      </div>
      <div class="energy-badge" id="badge-ges">
        <span class="badge-label">GES</span>
        <span class="badge-value">A</span>
      </div>
    </div>
    <p class="energy-info">
      Consommation : <strong id="conso-value">45</strong> kWh/m²/an<br>
      Émissions : <strong id="emission-value">5</strong> kg CO₂/m²/an
    </p>
  </div>
</div>
```

#### CSS Récapitulatif
```css
.price-section {
  background: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  margin-top: 1.5rem;
}

.selected-options {
  margin: 1rem 0;
  padding: 1rem 0;
  border-top: 1px solid #e0e0e0;
  border-bottom: 1px solid #e0e0e0;
}

.option-line {
  display: flex;
  justify-content: space-between;
  padding: 0.5rem 0;
  font-size: 0.95rem;
}

.price-total {
  margin: 1.5rem 0;
}

.price-row {
  display: flex;
  justify-content: space-between;
  padding: 0.75rem 0;
  font-size: 1rem;
}

.price-row.main-price {
  font-size: 1.3rem;
  color: #0066cc;
  border-top: 2px solid #0066cc;
  padding-top: 1rem;
  margin-top: 0.5rem;
}

.energy-performance {
  margin-top: 1.5rem;
  padding-top: 1.5rem;
  border-top: 1px solid #e0e0e0;
}

.energy-badges {
  display: flex;
  gap: 1rem;
  margin: 1rem 0;
}

.energy-badge {
  flex: 1;
  background: linear-gradient(135deg, #00b894 0%, #00cec9 100%);
  color: white;
  border-radius: 8px;
  padding: 1rem;
  text-align: center;
}

.badge-label {
  display: block;
  font-size: 0.85rem;
  opacity: 0.9;
  margin-bottom: 0.25rem;
}

.badge-value {
  display: block;
  font-size: 2rem;
  font-weight: bold;
}

/* Couleurs badges selon performance */
.energy-badge.grade-A { background: linear-gradient(135deg, #00b894 0%, #00cec9 100%); }
.energy-badge.grade-B { background: linear-gradient(135deg, #55efc4 0%, #81ecec 100%); }
.energy-badge.grade-C { background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%); }
.energy-badge.grade-D { background: linear-gradient(135deg, #fab1a0 0%, #ff7675 100%); }
```

---

## 3. REFONTE MOBILE COMPLÈTE

### A. Layout Mobile - Colonne Unique Verticale

#### CSS Mobile (max-width: 768px)
```css
@media (max-width: 768px) {
  /* Forcer layout en colonne unique */
  .config-content {
    display: flex !important;
    flex-direction: column !important;
    grid-template-columns: none !important;
    gap: 0.75rem;
    padding: 1rem;
  }

  /* Tous les éléments en colonne */
  .config-left,
  .config-right {
    width: 100%;
  }

  /* Visualiseur 3D */
  .viewer-container {
    order: 1; /* En premier */
    height: 300px;
    margin-bottom: 1rem;
  }

  /* Accordéons */
  .accordion-section {
    order: 2; /* Après le viewer */
  }

  /* Récapitulatif AVANT CTA */
  .price-section {
    order: 10; /* Après accordéons (order: 2-9) mais avant CTA (order: 20) */
    width: 100%;
    margin-top: 1rem;
    padding: 1rem;
  }

  /* Section CTA en dernier */
  .cta-section {
    order: 20; /* En dernier */
    padding: 1.5rem 1rem 2rem 1rem !important;
    margin-top: 1.5rem;
  }

  /* Cards des accordéons en vertical */
  .option-cards {
    grid-template-columns: 1fr !important; /* 1 card par ligne */
    gap: 0.75rem;
  }

  .option-card {
    width: 100%;
    padding: 1rem;
  }

  /* Accordéons - padding réduit */
  .accordion-header {
    padding: 1rem;
  }

  .accordion-content.active {
    padding: 1rem;
  }

  /* Prix - texte plus petit */
  .price-row.main-price {
    font-size: 1.1rem;
  }

  /* Energy badges */
  .energy-badges {
    flex-direction: column;
    gap: 0.5rem;
  }

  .energy-badge {
    padding: 0.75rem;
  }

  /* Model viewer mobile */
  model-viewer {
    height: 300px !important;
  }

  /* CTA cards en vertical */
  .cta-options {
    display: flex !important;
    flex-direction: column !important;
    grid-template-columns: none !important;
    gap: 1rem;
    width: 100%;
  }

  .cta-card {
    width: 100%;
  }
}
```

### B. Accordéons Mobile - Pas de Preview

**IMPORTANT** : Quand un accordéon est fermé, **aucun contenu ne doit être visible**.

```css
.accordion-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.4s ease, padding 0.4s ease;
  padding: 0; /* ⚠️ CRUCIAL : Pas de padding quand fermé */
}

.accordion-content.active {
  max-height: 2000px;
  padding: 1.5rem; /* Padding SEULEMENT quand ouvert */
}

/* Mobile */
@media (max-width: 768px) {
  .accordion-content.active {
    padding: 1rem;
  }
}
```

### C. Ordre Mobile : Viewer → Accordéons → Récap → CTA

```css
@media (max-width: 768px) {
  /* Viewer 3D en premier */
  .viewer-container {
    order: 1;
  }

  /* Accordéons ensuite (order naturel ou 2-9) */
  .accordion-section:nth-child(1) { order: 2; }
  .accordion-section:nth-child(2) { order: 3; }
  .accordion-section:nth-child(3) { order: 4; }
  /* etc. */

  /* Récapitulatif AVANT CTA */
  .price-section {
    order: 10;
  }

  /* CTA en dernier */
  .cta-section {
    order: 20;
  }
}
```

---

## 4. SECTION CTA ET FORMULAIRES

### A. Structure - 2 Cards CTA

#### HTML CTA Section
```html
<div class="cta-section">
  <h2>Prêt à concrétiser votre projet ?</h2>

  <div class="cta-options">
    <!-- Card 1 : Télécharger le PDF -->
    <div class="cta-card">
      <div class="cta-icon">📄</div>
      <h3>Télécharger mon plan</h3>
      <p>Recevez le PDF détaillé de votre configuration avec plans et prix</p>
      <button class="btn-primary" onclick="toggleDownloadForm()">
        Télécharger le PDF
      </button>

      <!-- Formulaire inline (caché par défaut) -->
      <div id="download-form-inline" class="download-form-inline" style="display: none;">
        <form onsubmit="submitDownloadFormInline(event)">
          <input type="text" name="prenom" placeholder="Prénom *" required>
          <input type="text" name="nom" placeholder="Nom *" required>
          <input type="email" name="email" placeholder="Email *" required>
          <input type="tel" name="telephone" placeholder="Téléphone">
          <button type="submit" class="btn-submit">Télécharger</button>
        </form>
      </div>
    </div>

    <!-- Card 2 : Prendre RDV Calendly -->
    <div class="cta-card">
      <div class="cta-icon">📅</div>
      <h3>Parler avec un conseiller</h3>
      <p>Prenez rendez-vous avec un expert pour affiner votre projet</p>
      <button class="btn-secondary" onclick="openCalendlyPopup()">
        Prendre rendez-vous
      </button>
    </div>
  </div>
</div>
```

#### CSS CTA
```css
.cta-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 3rem 2rem;
  text-align: center;
  color: white;
}

.cta-section h2 {
  font-size: 2rem;
  margin-bottom: 2rem;
}

.cta-options {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

.cta-card {
  background: white;
  color: #1a1a1a;
  border-radius: 16px;
  padding: 2rem;
  box-shadow: 0 8px 24px rgba(0,0,0,0.15);
  transition: transform 0.3s ease;
}

.cta-card:hover {
  transform: translateY(-8px);
}

.cta-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
}

.cta-card h3 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #1a1a1a;
}

.cta-card p {
  color: #666;
  margin-bottom: 1.5rem;
  line-height: 1.6;
}

.btn-primary,
.btn-secondary {
  padding: 1rem 2rem;
  border-radius: 8px;
  border: none;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  width: 100%;
}

.btn-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.btn-primary:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.btn-secondary {
  background: white;
  color: #667eea;
  border: 2px solid #667eea;
}

.btn-secondary:hover {
  background: #667eea;
  color: white;
}

/* Formulaire inline */
.download-form-inline {
  margin-top: 1.5rem;
  padding-top: 1.5rem;
  border-top: 2px solid #e0e0e0;
  animation: slideDown 0.3s ease;
}

@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.download-form-inline input {
  width: 100%;
  padding: 0.75rem;
  margin-bottom: 0.75rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 0.95rem;
}

.download-form-inline input:focus {
  outline: none;
  border-color: #667eea;
}

.btn-submit {
  width: 100%;
  padding: 1rem;
  background: linear-gradient(135deg, #00b894 0%, #00cec9 100%);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.3s ease;
}

.btn-submit:hover {
  transform: scale(1.05);
}

/* Mobile */
@media (max-width: 768px) {
  .cta-options {
    grid-template-columns: 1fr;
    gap: 1rem;
  }

  .cta-section h2 {
    font-size: 1.5rem;
  }

  .cta-card {
    padding: 1.5rem;
  }
}
```

### B. CTA Conditionnelle - Terrain

**Si l'utilisateur sélectionne "Non, je cherche encore" pour le terrain**, afficher un CTA supplémentaire avec Calendly.

#### HTML CTA Terrain (après la card terrain)
```html
<!-- CTA conditionnelle si "Non, je cherche encore" -->
<div id="terrain-cta-box" class="terrain-cta-box" style="display: none;">
  <div class="cta-content">
    <span class="cta-icon">💡</span>
    <div class="cta-text">
      <strong>Besoin d'aide pour trouver votre terrain ?</strong>
      <p>Nos conseillers peuvent vous accompagner dans votre recherche</p>
    </div>
    <button class="btn-terrain-cta" onclick="openCalendlyTerrainPopup()">
      Prendre rendez-vous
    </button>
  </div>
</div>
```

#### CSS CTA Terrain
```css
.terrain-cta-box {
  margin-top: 1.5rem;
  padding: 1.5rem;
  background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%);
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(253, 203, 110, 0.3);
  animation: slideIn 0.4s ease;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.cta-content {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.cta-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.cta-text {
  flex: 1;
}

.cta-text strong {
  display: block;
  font-size: 1.1rem;
  color: #1a1a1a;
  margin-bottom: 0.25rem;
}

.cta-text p {
  font-size: 0.9rem;
  color: #666;
  margin: 0;
}

.btn-terrain-cta {
  padding: 0.75rem 1.5rem;
  background: white;
  color: #f39c12;
  border: 2px solid #f39c12;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  flex-shrink: 0;
}

.btn-terrain-cta:hover {
  background: #f39c12;
  color: white;
  transform: scale(1.05);
}

/* Mobile */
@media (max-width: 768px) {
  .cta-content {
    flex-direction: column;
    text-align: center;
  }

  .btn-terrain-cta {
    width: 100%;
  }
}
```

---

## 5. TRACKING COMPLET

### A. Scripts Tracking - Head Section

#### HTML Head - Ordre CRITIQUE
```html
<head>
  <!-- 1. Google Tag (gtag.js) - Google Ads + GA4 -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=GT-WPL2H37J"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    // ✅ CONSENT MODE - Accepter le tracking dans l'iframe configurateur
    // Ceci override le consent denied du parent Webflow
    gtag('consent', 'update', {
      'ad_storage': 'granted',
      'ad_user_data': 'granted',
      'ad_personalization': 'granted',
      'analytics_storage': 'granted'  // ← Permet à GA4 de recevoir les événements
    });

    // Google Tag
    gtag('config', 'GT-WPL2H37J');

    // Google Ads
    gtag('config', 'AW-17802163047');

    // Google Analytics 4
    gtag('config', 'G-864MHX1HKR', {
      'send_page_view': true,
      'page_title': 'Configurateur [NOM_MODELE]',  // ⚠️ CHANGER SELON LE MODÈLE
      'page_location': window.location.href
    });
  </script>

  <!-- 2. Meta Pixel Code -->
  <script>
    !function(f,b,e,v,n,t,s)
    {if(f.fbq)return;n=f.fbq=function(){n.callMethod?
    n.callMethod.apply(n,arguments):n.queue.push(arguments)};
    if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
    n.queue=[];t=b.createElement(e);t.async=!0;
    t.src=v;s=b.getElementsByTagName(e)[0];
    s.parentNode.insertBefore(t,s)}(window, document,'script',
    'https://connect.facebook.net/en_US/fbevents.js');
    fbq('init', '1875020966472884');
    fbq('track', 'PageView');
  </script>
  <noscript>
    <img height="1" width="1" style="display:none"
    src="https://www.facebook.com/tr?id=1875020966472884&ev=PageView&noscript=1"/>
  </noscript>
  <!-- End Meta Pixel Code -->

  <!-- 3. Calendly CSS pour PopupWidget -->
  <link href="https://assets.calendly.com/assets/external/widget.css" rel="stylesheet">
</head>
```

### B. Fonction Tracking Centralisée

#### JavaScript - trackConversion()
```javascript
/**
 * Fonction de tracking pour Google Ads, GA4 et Meta Pixel
 * @param {string} conversionType - 'pdf_download' ou 'calendly_booking'
 * @param {number} value - Valeur de la conversion en euros
 * @param {object} extraData - Données additionnelles (optionnel)
 */
function trackConversion(conversionType, value, extraData = {}) {
  console.log('📊 =================================');
  console.log('📊 TRACKING CONVERSION:', conversionType);
  console.log('📊 Valeur:', value + '€');
  console.log('📊 Extra data:', extraData);
  console.log('📊 =================================');

  // 1. GOOGLE ADS - Conversion avec labels générés par Google
  if (typeof gtag !== 'undefined') {
    // ⚠️ IMPORTANT : Ces labels sont générés automatiquement par Google Ads
    // Il faut créer 2 actions de conversion dans Google Ads et copier les labels réels
    const conversionLabels = {
      'pdf_download': 'dwmhCJ2fi_IbEOfm3ahC',      // ⚠️ REMPLACER par le vrai label Google Ads
      'calendly_booking': 'B74ICJaQjPIbEOfm3ahC'   // ⚠️ REMPLACER par le vrai label Google Ads
    };

    const adsPayload = {
      'send_to': 'AW-17802163047/' + conversionLabels[conversionType],
      'value': value,
      'currency': 'EUR'
    };

    console.log('🎯 Google Ads - Envoi conversion:', adsPayload);
    gtag('event', 'conversion', adsPayload);
    console.log('✅ Google Ads conversion envoyée');

    // 2. GOOGLE ANALYTICS 4 - Événement personnalisé
    // ⚠️ Pour GA4, on n'utilise PAS 'send_to', l'événement va automatiquement à tous les tags
    const ga4Payload = {
      'event_category': 'Conversion',
      'event_label': conversionType === 'pdf_download' ? 'Téléchargement PDF' : 'Prise RDV Calendly',
      'value': value,
      'currency': 'EUR',
      ...extraData
    };

    console.log('📈 GA4 - Envoi événement:', conversionType, ga4Payload);
    gtag('event', conversionType, ga4Payload);
    console.log('✅ GA4 événement envoyé à G-864MHX1HKR');

  } else {
    console.error('❌ gtag not loaded - tracking impossible');
  }

  // 3. META PIXEL - Lead event
  if (typeof fbq !== 'undefined') {
    const metaPayload = {
      content_name: conversionType === 'pdf_download' ? 'PDF Configuration [MODELE]' : 'RDV Calendly [MODELE]',  // ⚠️ CHANGER [MODELE]
      content_category: 'Configurateur',
      value: value,
      currency: 'EUR'
    };

    console.log('📘 Meta Pixel - Envoi Lead:', metaPayload);
    fbq('track', 'Lead', metaPayload);
    console.log('✅ Meta Pixel Lead envoyé');

    // Envoi également de l'événement custom détaillé (pdf_download ou calendly_booking)
    fbq('trackCustom', conversionType, metaPayload);
    console.log('✅ Meta Pixel custom event envoyé:', conversionType);

  } else {
    console.error('❌ fbq not loaded - Meta Pixel tracking impossible');
  }

  console.log('📊 ================================= FIN TRACKING');
}
```

### C. Appels Tracking

#### 1. Tracking Téléchargement PDF (25€)
```javascript
async function submitDownloadFormInline(event) {
  event.preventDefault();

  const form = event.target;
  const prenom = form.prenom.value.trim();
  const nom = form.nom.value.trim();
  const email = form.email.value.trim();
  const telephone = form.telephone.value.trim();

  // Préparer les données webhook
  const webhookData = {
    prenom: prenom,
    nom: nom,
    email: email,
    telephone: telephone,
    // ... autres données de configuration
  };

  try {
    // Envoi au webhook Make.com
    const response = await fetch('https://hook.eu1.make.com/jcibjkv691xvm739pfp5gbyf2n1m56kh', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(webhookData)
    });

    if (response.ok) {
      // 📊 TRACKING - Conversion téléchargement PDF (25€)
      trackConversion('pdf_download', 25, {
        'method': 'inline_form',
        'prenom': prenom,
        'nom': nom,
        'email': email
      });

      // Générer le PDF
      await saveConfiguration();

      alert('Votre PDF a été généré avec succès !');
      form.reset();
      toggleDownloadForm(); // Fermer le formulaire
    }
  } catch (error) {
    console.error('Erreur webhook:', error);
    alert('Une erreur est survenue. Veuillez réessayer.');
  }
}
```

#### 2. Tracking Prise RDV Calendly Général (75€)
```javascript
function openCalendlyPopup() {
  console.log('Ouverture Calendly PopupWidget');

  // 📊 TRACKING - Conversion prise RDV Calendly (75€)
  trackConversion('calendly_booking', 75, {
    'method': 'popup_general',
    'source': 'cta_section'
  });

  if (typeof Calendly !== 'undefined') {
    Calendly.initPopupWidget({
      url: 'https://calendly.com/lulyhomes/30min?hide_event_type_details=1&hide_gdpr_banner=1'
    });
  } else {
    console.error('Calendly not loaded');
    alert('Erreur : Calendly n\'est pas chargé. Veuillez rafraîchir la page.');
  }
}
```

#### 3. Tracking Prise RDV Calendly Terrain (75€)
```javascript
function openCalendlyTerrainPopup() {
  console.log('Ouverture Calendly PopupWidget - Terrain');

  // 📊 TRACKING - Conversion prise RDV Calendly Terrain (75€)
  trackConversion('calendly_booking', 75, {
    'method': 'popup_terrain',
    'source': 'terrain_cta',
    'terrain_status': 'recherche_en_cours'
  });

  if (typeof Calendly !== 'undefined') {
    Calendly.initPopupWidget({
      url: 'https://calendly.com/lulyhomes/30min?hide_event_type_details=1&hide_gdpr_banner=1'
    });
  } else {
    console.error('Calendly not loaded');
  }
}
```

### D. Configuration Google Ads - Labels de Conversion

**IMPORTANT** : Les labels de conversion Google Ads sont générés **automatiquement** par Google. Il faut :

1. **Créer 2 actions de conversion dans Google Ads** :
   - Action 1 : "Téléchargement PDF Configurateur [MODELE]" → Valeur 25€
   - Action 2 : "Prise RDV Calendly Configurateur [MODELE]" → Valeur 75€

2. **Copier les vrais labels** depuis les snippets Google Ads (format : `dwmhCJ2fi_IbEOfm3ahC`)

3. **Remplacer** les labels dans la fonction `trackConversion()` :
```javascript
const conversionLabels = {
  'pdf_download': 'VOTRE_VRAI_LABEL_PDF',      // ⚠️ À REMPLACER
  'calendly_booking': 'VOTRE_VRAI_LABEL_RDV'   // ⚠️ À REMPLACER
};
```

### E. Résolution Problème GA4 - Consent Mode

**CRITIQUE** : Si le configurateur est intégré dans un iframe Webflow avec Consent Mode qui bloque `analytics_storage`, il faut **override le consent** dans l'iframe :

```javascript
// ✅ CONSENT MODE - Override dans l'iframe
gtag('consent', 'update', {
  'ad_storage': 'granted',
  'ad_user_data': 'granted',
  'ad_personalization': 'granted',
  'analytics_storage': 'granted'  // ← Débloque GA4 dans l'iframe
});
```

Cette ligne **doit être placée AVANT** les `gtag('config', ...)`.

---

## 6. INTÉGRATION CALENDLY

### A. Calendly PopupWidget (Compatible Iframe)

**IMPORTANT** : Utiliser le **PopupWidget** de Calendly, pas de modal custom. Le PopupWidget fonctionne dans les iframes.

#### Script Calendly
```html
<!-- Dans le head -->
<link href="https://assets.calendly.com/assets/external/widget.css" rel="stylesheet">

<!-- Avant </body> -->
<script src="https://assets.calendly.com/assets/external/widget.js" type="text/javascript" async></script>
```

#### Fonction JavaScript
```javascript
function openCalendlyPopup() {
  if (typeof Calendly !== 'undefined') {
    Calendly.initPopupWidget({
      url: 'https://calendly.com/lulyhomes/30min?hide_event_type_details=1&hide_gdpr_banner=1'
    });
  } else {
    console.error('Calendly not loaded');
    alert('Erreur : Calendly n\'est pas chargé. Veuillez rafraîchir la page.');
  }
}
```

### B. CSS Calendly - Z-index Élevé

```css
/* Assurer que Calendly s'affiche au-dessus de tout */
.calendly-overlay {
  z-index: 999999 !important;
}

.calendly-popup {
  z-index: 999999 !important;
}

.calendly-popup-content {
  z-index: 999999 !important;
}
```

---

## 7. WEBHOOK MAKE.COM

### A. Structure JSON Complète

Toutes les données de qualification et configuration doivent être envoyées au webhook Make.com.

#### Données à Envoyer
```javascript
const webhookData = {
  // Informations personnelles (du formulaire)
  prenom: prenom,
  nom: nom,
  email: email,
  telephone: telephone || '',

  // Données de qualification
  lifestyle: currentConfig.lifestyle || '',
  lifestyle_label: getOptionLabel('lifestyle', currentConfig.lifestyle),

  terrain: currentConfig.terrain || '',
  terrain_label: getOptionLabel('terrain', currentConfig.terrain),

  timeline: currentConfig.timeline || '',
  timeline_label: getOptionLabel('timeline', currentConfig.timeline),

  // Options de configuration
  bardage: currentConfig.bardage || '',
  bardage_label: getOptionLabel('bardage', currentConfig.bardage),

  menuiserie: currentConfig.menuiserie || '',
  menuiserie_label: getOptionLabel('menuiserie', currentConfig.menuiserie),

  toiture: currentConfig.toiture || '',
  toiture_label: getOptionLabel('toiture', currentConfig.toiture),

  terrasse: currentConfig.terrasse || '',
  terrasse_label: getOptionLabel('terrasse', currentConfig.terrasse),

  // ⚠️ AJOUTER TOUTES LES OPTIONS SPÉCIFIQUES AU MODÈLE

  // Performance énergétique
  dpe: energyPerformance.dpe,
  ges: energyPerformance.ges,
  consommation: energyPerformance.consommation + ' kWh/m²/an',
  emissions: energyPerformance.emissions + ' kg CO₂/m²/an',

  // Prix
  prix_ht: pricing.totalHT.toFixed(2) + ' €',
  prix_ttc: pricing.totalTTC.toFixed(2) + ' €',
  tva: pricing.tva.toFixed(2) + ' €',

  // Métadonnées
  timestamp: new Date().toISOString(),
  source: 'configurateur_[MODELE]',  // ⚠️ CHANGER [MODELE]
  page_url: window.location.href
};
```

#### Fonction Webhook
```javascript
async function sendToWebhook(webhookData) {
  const webhookURL = 'https://hook.eu1.make.com/jcibjkv691xvm739pfp5gbyf2n1m56kh';

  try {
    const response = await fetch(webhookURL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(webhookData)
    });

    if (!response.ok) {
      throw new Error('Webhook failed: ' + response.status);
    }

    console.log('✅ Webhook envoyé avec succès');
    return true;
  } catch (error) {
    console.error('❌ Erreur webhook:', error);
    return false;
  }
}
```

---

## 8. CORRECTIONS ET OPTIMISATIONS

### A. Correction Modal dans Iframe

**Problème** : Les modals avec `position: fixed` ne fonctionnent pas dans les iframes.

**Solution** : Utiliser Calendly PopupWidget qui gère automatiquement les iframes.

**À NE PAS FAIRE** :
```css
/* ❌ NE PAS METTRE CETTE RÈGLE CSS */
* {
  position: relative !important;
}
```
Cette règle casse tous les éléments `position: fixed`.

### B. Correction Accordéons - Pas de Preview

**Problème** : Contenu visible même quand l'accordéon est fermé.

**Solution** :
```css
.accordion-content {
  max-height: 0;
  overflow: hidden;
  padding: 0; /* ⚠️ CRUCIAL */
}

.accordion-content.active {
  padding: 1.5rem; /* Padding SEULEMENT quand ouvert */
}
```

### C. Optimisation Performance

#### 1. Lazy Loading Model Viewer
```html
<script type="module" defer src="https://unpkg.com/@google/model-viewer@1.12.0/dist/model-viewer.min.js"></script>
```

#### 2. Async Scripts
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=GT-WPL2H37J"></script>
<script src="https://assets.calendly.com/assets/external/widget.js" type="text/javascript" async></script>
```

#### 3. Optimisation Fonts
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
}
```

### D. Accessibilité

#### 1. Labels ARIA
```html
<button aria-label="Prendre rendez-vous avec un conseiller" onclick="openCalendlyPopup()">
  Prendre rendez-vous
</button>

<div class="accordion-header" role="button" aria-expanded="false" tabindex="0">
  <h3>Titre de l'accordéon</h3>
</div>
```

#### 2. Focus States
```css
button:focus,
input:focus,
.option-card:focus {
  outline: 3px solid #0066cc;
  outline-offset: 2px;
}
```

---

## 9. CONFIGURATION SPÉCIFIQUE

### A. Variables à Adapter Selon le Modèle

#### Fichier de Configuration
```javascript
// ⚠️ CONFIGURATION SPÉCIFIQUE AU MODÈLE
const CONFIG = {
  // Nom du modèle
  modelName: 'Intemporel\'home',  // ⚠️ OU 'Modern\'home'

  // Modèle 3D
  modelPath: 'path/to/intemporelhome.glb',  // ⚠️ CHANGER

  // Tracking
  tracking: {
    googleTag: 'GT-WPL2H37J',
    googleAds: 'AW-17802163047',
    ga4: 'G-864MHX1HKR',
    metaPixel: '1875020966472884',
    // ⚠️ Labels de conversion Google Ads (à créer et copier)
    conversionLabels: {
      pdf_download: 'LABEL_PDF_INTEMPOREL',  // ⚠️ À REMPLACER
      calendly_booking: 'LABEL_RDV_INTEMPOREL'  // ⚠️ À REMPLACER
    }
  },

  // Webhook
  webhookURL: 'https://hook.eu1.make.com/jcibjkv691xvm739pfp5gbyf2n1m56kh',

  // Calendly
  calendlyURL: 'https://calendly.com/lulyhomes/30min?hide_event_type_details=1&hide_gdpr_banner=1',

  // Prix de base
  basePriceHT: 150000,  // ⚠️ Prix de base du modèle

  // Options spécifiques au modèle
  options: {
    bardage: [
      { id: 'bois', label: 'Bardage bois naturel', price: 0, icon: '🪵' },
      { id: 'composite', label: 'Bardage composite', price: 8000, icon: '🏗️' },
      // ⚠️ ADAPTER SELON LE MODÈLE
    ],
    menuiserie: [
      { id: 'alu-gris', label: 'Aluminium gris anthracite', price: 0, icon: '🪟' },
      { id: 'alu-noir', label: 'Aluminium noir', price: 1500, icon: '⬛' },
      // ⚠️ ADAPTER SELON LE MODÈLE
    ],
    // ⚠️ AJOUTER TOUTES LES OPTIONS SPÉCIFIQUES
  }
};
```

### B. Sections Accordéons Spécifiques

Les accordéons suivants sont **communs** à tous les modèles :
1. 🏡 Lifestyle (Travailleur à distance, Vie de famille, Retraité actif, etc.)
2. 🏞️ Terrain (Oui j'ai mon terrain, Non je cherche encore)
3. 📅 Timeline (Dans 6 mois, Dans 1 an, Dans 2 ans, Pas de date précise)

Les accordéons suivants sont **spécifiques** à chaque modèle (adapter les options) :
4. Options de configuration (Bardage, Menuiserie, Toiture, Terrasse, etc.)

### C. Performance Énergétique Spécifique

Adapter les valeurs DPE/GES selon le modèle :
```javascript
const energyPerformance = {
  dpe: 'A',  // ⚠️ ADAPTER
  ges: 'A',  // ⚠️ ADAPTER
  consommation: 45,  // kWh/m²/an - ⚠️ ADAPTER
  emissions: 5  // kg CO₂/m²/an - ⚠️ ADAPTER
};
```

---

## 10. CHECKLIST AVANT LANCEMENT

### ✅ Tracking
- [ ] Google Tag GT-WPL2H37J configuré
- [ ] Google Ads AW-17802163047 configuré
- [ ] GA4 G-864MHX1HKR configuré
- [ ] Meta Pixel 1875020966472884 configuré
- [ ] **Labels de conversion Google Ads créés et copiés**
- [ ] Consent Mode override dans l'iframe (analytics_storage: granted)
- [ ] Événements testés dans Google Tag Assistant
- [ ] Événements testés dans Meta Pixel Helper

### ✅ UX/UI
- [ ] Layout 60/40 desktop
- [ ] Accordéons avec animation smooth
- [ ] Cards de sélection avec hover effects
- [ ] Model viewer 3D chargé correctement
- [ ] Récapitulatif prix + performance énergétique
- [ ] Section CTA avec 2 cards
- [ ] CTA conditionnelle terrain si "Non, je cherche encore"

### ✅ Mobile
- [ ] Layout vertical (1 colonne)
- [ ] Ordre : Viewer → Accordéons → Récap → CTA
- [ ] Cards en vertical (1 par ligne)
- [ ] Accordéons fermés sans preview de contenu
- [ ] CTA cards en vertical
- [ ] Formulaire inline responsive

### ✅ Fonctionnalités
- [ ] Formulaire téléchargement PDF inline fonctionnel
- [ ] Calendly PopupWidget s'ouvre correctement
- [ ] Webhook Make.com reçoit toutes les données
- [ ] Génération PDF avec jsPDF
- [ ] Tracking déclenché sur PDF (25€) et Calendly (75€)

### ✅ Performance
- [ ] Scripts async/defer
- [ ] Model viewer en lazy loading
- [ ] Fonts optimisées
- [ ] Z-index Calendly élevé (999999)

### ✅ Configuration Spécifique
- [ ] Nom du modèle changé partout
- [ ] Modèle 3D (.glb) chargé
- [ ] Options spécifiques au modèle ajoutées
- [ ] Prix de base adapté
- [ ] DPE/GES adaptés
- [ ] Labels de conversion Google Ads spécifiques créés

---

## 11. FICHIERS ET RESSOURCES NÉCESSAIRES

### Fichiers à Préparer
1. **Modèle 3D** : `intemporelhome.glb` (ou `modernhome.glb`)
2. **Images/Icons** : Emojis ou SVG pour les options
3. **Logo** : Si nécessaire dans le header

### Scripts Externes Requis
```html
<!-- Google Tag Manager -->
<script async src="https://www.googletagmanager.com/gtag/js?id=GT-WPL2H37J"></script>

<!-- Meta Pixel -->
<script src="https://connect.facebook.net/en_US/fbevents.js"></script>

<!-- Calendly -->
<link href="https://assets.calendly.com/assets/external/widget.css" rel="stylesheet">
<script src="https://assets.calendly.com/assets/external/widget.js" async></script>

<!-- Model Viewer -->
<script type="module" defer src="https://unpkg.com/@google/model-viewer@1.12.0/dist/model-viewer.min.js"></script>

<!-- jsPDF (pour génération PDF) -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

---

## 12. EXEMPLE DE CODE COMPLET

Voir le fichier `index.html` du configurateur Tradi'home pour la référence complète :
`/home/user/L.H.TRADIHOME/index.html`

### Structure Fichier HTML
```
<!DOCTYPE html>
<html lang="fr">
<head>
  <!-- Tracking scripts -->
  <!-- Meta tags -->
  <!-- CSS inline -->
</head>
<body>
  <!-- Header (optionnel) -->

  <!-- Configuration Content -->
  <div class="config-content">
    <div class="config-left">
      <!-- Accordéons -->
    </div>
    <div class="config-right">
      <!-- Model viewer + Récap -->
    </div>
  </div>

  <!-- Section CTA -->
  <div class="cta-section">
    <!-- Cards CTA -->
  </div>

  <!-- JavaScript inline -->
  <script>
    // Configuration
    // Fonctions
    // Event listeners
  </script>
</body>
</html>
```

---

## 13. NOTES IMPORTANTES

### ⚠️ CRITIQUES À NE PAS OUBLIER

1. **Consent Mode GA4** :
   ```javascript
   gtag('consent', 'update', {
     'analytics_storage': 'granted'  // ← INDISPENSABLE
   });
   ```

2. **Labels Google Ads** :
   - Créer les conversions dans Google Ads
   - Copier les VRAIS labels (format : `dwmhCJ2fi_IbEOfm3ahC`)
   - Ne PAS inventer de labels custom

3. **Calendly PopupWidget** :
   - Utiliser PopupWidget, pas de modal custom
   - Z-index 999999 minimum

4. **Accordéons padding** :
   - `padding: 0` quand fermé
   - `padding: 1.5rem` quand ouvert SEULEMENT

5. **Mobile ordre** :
   - Viewer (order: 1)
   - Accordéons (order: 2-9)
   - Récap (order: 10)
   - CTA (order: 20)

6. **Webhook données complètes** :
   - Qualification (lifestyle, terrain, timeline)
   - Configuration (toutes les options)
   - Prix (HT, TTC, TVA)
   - Performance énergétique (DPE, GES)

---

## 14. SUPPORT ET CONTACT

Pour toute question sur l'implémentation :
- Consulter le fichier référence : `/home/user/L.H.TRADIHOME/index.html`
- Vérifier les commits Git pour l'historique des modifications
- Tester avec Google Tag Assistant et Meta Pixel Helper

---

**FIN DU BRIEF - Version 1.0 - Configurateur Tradi'home**

Ce brief est prêt à être donné à un agent IA (Claude) pour refactoriser les configurateurs Intemporel'home et Modern'home avec exactement les mêmes fonctionnalités et améliorations.
