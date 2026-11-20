# geo-todo

Location-aware Todo app for iOS / Android (Expo + React Native, TypeScript).
Tasks fire reminders when you arrive at / leave a place.

Status: early design. No app code yet — this repo starts with the core schema.

## Stack
- Expo / React Native (TypeScript), expo-location, expo-task-manager
- Tests: Vitest (pure logic), Maestro (E2E) with GPX replay on iOS Simulator / Android Emulator
- CI: GitHub Actions

## Core schema

```ts
export type LatLng = { lat: number; lng: number };

export type LocationEvent = {
  id: string;
  type: "enter" | "exit" | "dwell";
  placeId: string;
  coords: LatLng;
  accuracyM: number;
  at: string; // ISO 8601
  source: "geofence" | "significant-change" | "gpx-replay";
};

export type Place = {
  id: string;
  name: string;
  center: LatLng;
  radiusM: number;
};

export type Task = {
  id: string;
  title: string;
  placeId?: string;
  trigger: "on-enter" | "on-exit" | "on-dwell";
  dwellMinutes?: number;
  activeHours?: { start: string; end: string }; // "08:00"
  done: boolean;
};

export type NotifyDecision = {
  taskId: string;
  eventId: string;
  notify: boolean;
  reason: "match" | "outside-hours" | "already-done" | "low-accuracy" | "cooldown";
};
```

## Plan
1. Pure decision logic `decide(event, tasks, now): NotifyDecision[]` + Vitest
2. GPX replay E2E on simulators (Maestro)
3. Multi-agent PDCA: lead + workers + reviewer, CI must pass before merge
