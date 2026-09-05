# Architecture

## App Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Splash      │────▶│  World       │────▶│  Countries   │
│  Screen      │     │  States      │     │  List        │
│  (5s timer)  │     │  Screen      │     │  (searchable)│
└──────────────┘     └──────┬───────┘     └──────┬───────┘
                            │                     │
                     ┌──────▼───────┐     ┌──────▼───────┐
                     │  Pie Chart   │     │  Detail      │
                     │  + Stats     │     │  Screen      │
                     │  Card        │     │  (per country)│
                     └──────────────┘     └──────────────┘
```

## Data Flow

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  disease.sh  │     │  StatesServices │  │  Flutter UI  │
│  REST API    │────▶│  (HTTP layer) │───▶│  (Widgets)   │
└──────────────┘     └──────────────┘     └──────────────┘
       │                    │                     │
  GET /all            fetchWorldRecords()    WorldStatesScreen
  GET /countries      countriesListApi()     CountriesListScreen
                                          DetailScreen
```

## Component Interaction

```
┌──────────────────────────────────────────────────┐
│                FLUTTER APP                        │
│                                                   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐ │
│  │  SplashScreen│ │ WorldStates │ │ Countries  │ │
│  │  (animated) │ │  Screen     │ │ List       │ │
│  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘ │
│         │               │               │        │
│  ┌──────▼───────────────▼───────────────▼──────┐ │
│  │           StatesServices                     │ │
│  │  - fetchWorldRecords() → WorldStatesModel    │ │
│  │  - countriesListApi() → List<dynamic>        │ │
│  └──────────────────┬──────────────────────────┘ │
│                     │                             │
│  ┌──────────────────▼──────────────────────────┐ │
│  │           HTTP Client (dart:http)            │ │
│  └──────────────────┬──────────────────────────┘ │
└─────────────────────┼────────────────────────────┘
                      │
              ┌───────▼────────┐
              │  disease.sh    │
              │  REST API      │
              │  (224 countries)│
              └────────────────┘
```

## Screen Breakdown

### Splash Screen
- Rotating virus animation (3s)
- Auto-navigates to World States after 5s

### World States Screen
- Pie chart: Cases vs Recovered vs Deaths
- Stats card: Total, Deaths, Recovered, Active, Critical, Today Deaths
- "Track Countries" button → Countries List

### Countries List Screen
- Searchable text field
- ListView with country flag + name + cases
- Shimmer loading effect
- Tap → Detail Screen

### Detail Screen
- Country flag (circular avatar)
- Stats: Cases, Recovered, Deaths, Critical, Today Recovered, Tests

## Technology Decisions

| Choice | Why |
|--------|-----|
| **Flutter** | Cross-platform (Android + iOS + Web) |
| **disease.sh** | Free, no API key needed, real-time COVID data |
| **pie_chart** | Simple ring chart for global stats |
| **shimmer** | Better UX during loading states |
| **flutter_spinkit** | Animated loading indicators |
