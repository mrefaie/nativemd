---
title: Visit notification deep-link never fires
tags:
  - mobile
  - rep-app
description: Tapping the push notification does nothing.
---
# Visit notification deep-link never fires

## Summary

Tapping the push notification does nothing - no navigation, no list refresh.

## Root cause

The backend sends `type` as `Visit_<id>` with a capital V.

```php
$data['type'] = 'Visit_'.$visit_id;
```

## Fix

Lowercase the comparison.

### Details

More text here.
