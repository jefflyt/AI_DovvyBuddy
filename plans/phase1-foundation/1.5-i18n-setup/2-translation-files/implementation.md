# Step 2: Translation Files & Namespaces

**PR:** 1.5 Internationalization Setup  
**Step:** 2 of 4  
**Estimated Time:** 2-3 hours

---

## Goal

Create JSON translation files for all 8 supported languages across 3 namespaces (common, chat, diveSites). Start with English base translations, then add other languages.

---

## Files to Create

### Frontend Translations (public/locales/)

### 1. `public/locales/en/common.json`

```json
{
  "app": {
    "name": "DovvyDive",
    "tagline": "AI-Powered Dive Site Discovery",
    "description": "Discover the perfect dive sites with conversational AI assistance"
  },
  "nav": {
    "home": "Home",
    "chat": "Chat",
    "explore": "Explore",
    "about": "About",
    "contact": "Contact"
  },
  "actions": {
    "start": "Start Exploring",
    "learnMore": "Learn More",
    "search": "Search",
    "cancel": "Cancel",
    "save": "Save",
    "delete": "Delete",
    "edit": "Edit",
    "close": "Close",
    "loading": "Loading..."
  },
  "errors": {
    "generic": "Something went wrong. Please try again.",
    "network": "Network error. Please check your connection.",
    "notFound": "Page not found",
    "unauthorized": "You are not authorized to access this resource"
  },
  "footer": {
    "product": "Product",
    "company": "Company",
    "resources": "Resources",
    "copyright": "© {{year}} DovvyDive. All rights reserved."
  }
}
```

### 2. `public/locales/en/chat.json`

```json
{
  "title": "Chat with DovvyDive AI",
  "subtitle": "Ask me anything about dive sites, conditions, or recommendations!",
  "placeholder": "Type your message here...",
  "send": "Send",
  "newChat": "New Chat",
  "examples": {
    "title": "Try asking:",
    "beginner": "I'm looking for beginner-friendly dive sites in Southeast Asia",
    "advanced": "Show me challenging deep dives with wrecks",
    "seasonal": "Best dive sites to visit in December"
  },
  "status": {
    "typing": "DovvyDive is typing...",
    "thinking": "Thinking...",
    "searching": "Searching dive sites...",
    "error": "Failed to send message"
  },
  "messages": {
    "welcome": "Hi! I'm DovvyDive, your AI dive buddy. Where would you like to dive?",
    "empty": "No messages yet. Start a conversation!",
    "error": "Sorry, I encountered an error. Please try again."
  }
}
```

### 3. `public/locales/en/diveSites.json`

```json
{
  "title": "Dive Sites",
  "search": {
    "placeholder": "Search dive sites...",
    "filters": "Filters",
    "difficulty": "Difficulty",
    "location": "Location",
    "depth": "Depth",
    "type": "Dive Type",
    "rating": "Rating"
  },
  "difficulty": {
    "beginner": "Beginner",
    "intermediate": "Intermediate",
    "advanced": "Advanced",
    "expert": "Expert"
  },
  "types": {
    "reef": "Reef",
    "wreck": "Wreck",
    "cave": "Cave",
    "wall": "Wall",
    "drift": "Drift",
    "night": "Night",
    "deep": "Deep"
  },
  "details": {
    "maxDepth": "Max Depth",
    "visibility": "Visibility",
    "current": "Current",
    "temperature": "Temperature",
    "bestTime": "Best Time to Visit",
    "description": "Description",
    "rating": "Rating",
    "reviews": "Reviews"
  },
  "units": {
    "meters": "m",
    "feet": "ft",
    "celsius": "°C",
    "fahrenheit": "°F"
  },
  "notFound": "No dive sites found matching your criteria"
}
```

### 4. `public/locales/es/common.json` (Spanish)

```json
{
  "app": {
    "name": "DovvyDive",
    "tagline": "Descubrimiento de Sitios de Buceo con IA",
    "description": "Descubre los sitios de buceo perfectos con asistencia de IA conversacional"
  },
  "nav": {
    "home": "Inicio",
    "chat": "Chat",
    "explore": "Explorar",
    "about": "Acerca de",
    "contact": "Contacto"
  },
  "actions": {
    "start": "Comenzar a Explorar",
    "learnMore": "Más Información",
    "search": "Buscar",
    "cancel": "Cancelar",
    "save": "Guardar",
    "delete": "Eliminar",
    "edit": "Editar",
    "close": "Cerrar",
    "loading": "Cargando..."
  },
  "errors": {
    "generic": "Algo salió mal. Por favor, inténtalo de nuevo.",
    "network": "Error de red. Por favor, verifica tu conexión.",
    "notFound": "Página no encontrada",
    "unauthorized": "No tienes autorización para acceder a este recurso"
  },
  "footer": {
    "product": "Producto",
    "company": "Empresa",
    "resources": "Recursos",
    "copyright": "© {{year}} DovvyDive. Todos los derechos reservados."
  }
}
```

### 5. `public/locales/fr/common.json` (French)

```json
{
  "app": {
    "name": "DovvyDive",
    "tagline": "Découverte de Sites de Plongée par IA",
    "description": "Découvrez les sites de plongée parfaits avec l'assistance IA conversationnelle"
  },
  "nav": {
    "home": "Accueil",
    "chat": "Chat",
    "explore": "Explorer",
    "about": "À propos",
    "contact": "Contact"
  },
  "actions": {
    "start": "Commencer à Explorer",
    "learnMore": "En savoir plus",
    "search": "Rechercher",
    "cancel": "Annuler",
    "save": "Enregistrer",
    "delete": "Supprimer",
    "edit": "Modifier",
    "close": "Fermer",
    "loading": "Chargement..."
  },
  "errors": {
    "generic": "Quelque chose s'est mal passé. Veuillez réessayer.",
    "network": "Erreur réseau. Veuillez vérifier votre connexion.",
    "notFound": "Page non trouvée",
    "unauthorized": "Vous n'êtes pas autorisé à accéder à cette ressource"
  },
  "footer": {
    "product": "Produit",
    "company": "Entreprise",
    "resources": "Ressources",
    "copyright": "© {{year}} DovvyDive. Tous droits réservés."
  }
}
```

### Backend Translations (src/backend/locales/)

### 6. `src/backend/locales/en/errors.json`

```json
{
  "validation": {
    "required": "{{field}} is required",
    "invalid": "{{field}} is invalid",
    "tooShort": "{{field}} must be at least {{min}} characters",
    "tooLong": "{{field}} must be at most {{max}} characters",
    "invalidEmail": "Invalid email address",
    "invalidUUID": "Invalid ID format"
  },
  "http": {
    "400": "Bad Request",
    "401": "Unauthorized",
    "403": "Forbidden",
    "404": "Resource not found",
    "500": "Internal Server Error",
    "503": "Service Unavailable"
  },
  "database": {
    "connectionFailed": "Failed to connect to database",
    "queryFailed": "Database query failed",
    "recordNotFound": "Record not found"
  },
  "chat": {
    "sessionNotFound": "Chat session not found",
    "messageEmpty": "Message cannot be empty",
    "messageTooLong": "Message is too long (max 500 characters)"
  },
  "diveSites": {
    "notFound": "Dive site not found",
    "searchFailed": "Search failed"
  }
}
```

### 7. `src/backend/locales/es/errors.json` (Spanish errors)

```json
{
  "validation": {
    "required": "{{field}} es obligatorio",
    "invalid": "{{field}} no es válido",
    "tooShort": "{{field}} debe tener al menos {{min}} caracteres",
    "tooLong": "{{field}} debe tener como máximo {{max}} caracteres",
    "invalidEmail": "Dirección de correo electrónico no válida",
    "invalidUUID": "Formato de ID no válido"
  },
  "http": {
    "400": "Solicitud incorrecta",
    "401": "No autorizado",
    "403": "Prohibido",
    "404": "Recurso no encontrado",
    "500": "Error interno del servidor",
    "503": "Servicio no disponible"
  },
  "database": {
    "connectionFailed": "Error al conectar con la base de datos",
    "queryFailed": "Error en la consulta de base de datos",
    "recordNotFound": "Registro no encontrado"
  }
}
```

### 8. Create locale structure script: `scripts/create-locales.sh`

```bash
#!/bin/bash

# Create frontend locale directories
FRONTEND_LOCALES="public/locales"
BACKEND_LOCALES="src/backend/locales"

LANGUAGES=("en" "es" "fr" "de" "pt" "it" "ja" "zh")

echo "Creating frontend locale directories..."
for lang in "${LANGUAGES[@]}"; do
  mkdir -p "$FRONTEND_LOCALES/$lang"
  echo "Created $FRONTEND_LOCALES/$lang"
done

echo "Creating backend locale directories..."
for lang in "${LANGUAGES[@]}"; do
  mkdir -p "$BACKEND_LOCALES/$lang"
  echo "Created $BACKEND_LOCALES/$lang"
done

echo "Locale directories created successfully!"
```

---

## Installation Commands

```bash
# Make script executable
chmod +x scripts/create-locales.sh

# Create locale directories
./scripts/create-locales.sh

# Create all translation files
# Copy the JSON content above into respective files
```

---

## Testing Checklist

- [ ] Frontend locale directories created (8 languages)
- [ ] Backend locale directories created (8 languages)
- [ ] English common.json created
- [ ] English chat.json created
- [ ] English diveSites.json created
- [ ] Spanish common.json created
- [ ] French common.json created
- [ ] Backend errors.json created (en, es)
- [ ] At least 3 namespaces per language

---

## Validation Commands

```bash
# Verify directory structure
tree public/locales
tree src/backend/locales

# Should see:
# public/locales/
#   ├── en/
#   │   ├── common.json
#   │   ├── chat.json
#   │   └── diveSites.json
#   ├── es/
#   ├── fr/
#   └── ...

# Validate JSON syntax
find public/locales -name "*.json" -exec node -e "JSON.parse(require('fs').readFileSync('{}', 'utf8'))" \;

# Count translation keys
grep -r "\".*\":" public/locales/en/ | wc -l
# Should show 50+ keys
```

---

## Translation Notes

**For remaining languages (de, pt, it, ja, zh):**
- Copy en/common.json structure
- Translate values to target language
- Keep keys identical across all languages
- Use professional translation services or native speakers for production

**Placeholder translations:**
For Phase 1, you can use English for untranslated languages, or:
- German (de): Use machine translation
- Portuguese (pt): Brazilian Portuguese variant
- Italian (it): Standard Italian
- Japanese (ja): Include honorifics where appropriate
- Chinese (zh): Simplified Chinese

---

## Next Step

Proceed to Step 3: Language Switcher Components
