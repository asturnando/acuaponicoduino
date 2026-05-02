# ADR-0006: React Native + Expo (SDK 52+) para la app móvil

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

App móvil iOS + Android con:
- **BLE provisioning** del device (escaneo QR, conexión BLE, transferencia WiFi).
- Dashboard con WebSocket en vivo.
- Notificaciones push (FCM/APNs).
- OTA de la propia app sin pasar por stores cada vez.
- Compartir lo más posible con la web (tipos, schemas, lógica de negocio).
- Maintainable por una sola persona.

### Opciones evaluadas

| Framework | Pros | Contras |
|-----------|------|---------|
| **React Native + Expo SDK 52+** | Comparte stack mental con la web (React, TS, hooks), EAS Build/Update OTA, `react-native-ble-plx` maduro, comunidad enorme | Bundle size mayor que nativo, BLE en Android requiere permisos delicados (gestionable) |
| Flutter | Rendimiento UI top, hot reload excelente | Dart no comparte código con la web React; ecosistema BLE menos maduro que `react-native-ble-plx` |
| Nativo (Swift + Kotlin) | Calidad máxima | 2 codebases para una sola persona = no |
| Tauri Mobile | Reutiliza web | Inmaduro a 2026 para producción crítica |
| Capacitor + web | Reutiliza 100% web | BLE limitado a través de plugins; UX no se siente nativa |

## Decision

**Usamos React Native + Expo SDK 52+** con TypeScript estricto.

- **Expo Router** para navigation file-based (mismo modelo mental que Next.js App Router).
- **NativeWind** (Tailwind para RN) para mantener coherencia con la web.
- **`react-native-ble-plx`** para BLE.
- **Expo Notifications** + FCM (Android) + APNs (iOS).
- **`expo-secure-store`** para JWT (Keychain en iOS, EncryptedSharedPreferences en Android).
- **EAS Build** para iOS + Android.
- **EAS Update** para OTA del JS bundle.

## Consequences

### Positivas
- Una sola codebase para iOS + Android.
- Reutiliza tipos, fetchers y schemas con la web (web/ y mobile/ comparten `services/`).
- EAS Update permite arreglos rápidos sin pasar por App Store / Play Store review.
- BLE confiable con `react-native-ble-plx` (he revisado issues en GitHub, está activo y maduro).

### Negativas
- BLE en Android requiere `BLUETOOTH_SCAN`, `BLUETOOTH_CONNECT`, `ACCESS_FINE_LOCATION`. UX de permisos puede ser fea (gestionable con onboarding bien diseñado).
- iOS requiere cuenta Apple Developer (~99€/año) para distribución.
- App store reviews — Apple a veces es estricta con apps que controlan hardware. Mitigación: documentar muy bien el caso de uso.

### Neutras
- Build pipelines: EAS gestiona credenciales y firma. CI integra con `eas build` directamente.

### Riesgos
- Cambios en Expo SDK requieren upgrades guiados. Pinear versión y subir en PRs dedicadas con tests en TestFlight + internal track Android.

## Referencias

- [Expo SDK docs](https://docs.expo.dev/)
- [`react-native-ble-plx`](https://github.com/dotintent/react-native-ble-plx)
- [EAS Update](https://docs.expo.dev/eas-update/introduction/)
- ADR-0005 (Next.js para web)
