---
title: >-
  mobile(rep-app): visit notification deep-link never fires (case-sensitive type
  match)
tags:
  - mobile
  - rep-app
  - umc
description: >-
  Tapping the "لديك ملاحظه جديده" push notification does nothing — no navigation
  to `سجل الزيارات`, no list refresh.
public: true
publicExpires: '2027-08-12T16:57:29.530Z'
---
## Summary

Tapping the "لديك ملاحظه جديده" push notification does nothing — no navigation to `سجل الزيارات`, no list refresh.

The backend sends `type` as `Visit_<id>` (capital V):

```php
$data['type'] = 'Visit_'.$visit_id;   // ReportController::edit_visit()
```

The app matches lowercase:

```dart
if (type.contains('visit_')) {        // notification_service.dart:132
```

`String.contains` is case-sensitive in Dart, so the branch never runs. Compare `private_offer` / `public_offer` at `:112` / `:122`, which use exact `==` against payloads that happen to match.

## Fix

Lowercase the comparison in `lib/core/services/notification_service.dart:132`:

```dart
if (type.toLowerCase().contains('visit_')) {
```

Fixing it app-side rather than changing the payload avoids breaking any other client that may already depend on the current `type` string.

Once fixed, the existing branch body works as intended: navigate to `VisitsScreen(tabIndex: 1)` and `ref.invalidate(getVisitsProvider)`.

## Why this matters now

This is the second half of #590. Even after the note renders, the notification is the rep's only prompt to go look at it. Both need to land for the flow to work end to end.

## Optional

The `<id>` in the type is currently discarded — only the tab index is used, so the tap cannot open or scroll to the specific visit. Worth considering, but not required.
