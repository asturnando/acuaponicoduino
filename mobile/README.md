# mobile/

App móvil iOS + Android. React Native + Expo SDK 52+ con TypeScript.

## Estado

Vacío. Se llenará a partir de **M4** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
mobile/
├── package.json
├── app.config.ts           # Expo config (variables por entorno)
├── eas.json                # EAS Build profiles
├── app/                    # Expo Router
│   ├── (auth)/
│   ├── (tabs)/             # dashboard, alerts, settings
│   └── device/[id]/        # detalle de device, calibración
├── components/
├── lib/
├── services/               # cliente API + BLE
├── hooks/
└── types/
```

## Stack clave

| Pieza | Tecnología |
|-------|-----------|
| Framework | Expo SDK 52+ |
| Navigation | Expo Router |
| Estilos | NativeWind (Tailwind para RN) |
| BLE | `react-native-ble-plx` |
| Push | Expo Notifications + FCM/APNs |
| Storage seguro | `expo-secure-store` (token) |
| OTA app | EAS Update |

## Run local

```bash
cd mobile
pnpm install
pnpm ios                    # simulador iOS (Mac only)
pnpm android                # emulador Android
pnpm start                  # Expo Dev Tools
```

## Builds

```bash
eas build --platform ios --profile preview
eas build --platform android --profile preview
eas update --branch production
```

## Permisos a declarar

- **Android**: `BLUETOOTH_SCAN`, `BLUETOOTH_CONNECT`, `ACCESS_FINE_LOCATION` (BLE), `POST_NOTIFICATIONS`.
- **iOS**: `NSBluetoothAlwaysUsageDescription`, `NSCameraUsageDescription` (escaneo QR de buffer).
