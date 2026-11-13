# Notebook 01 report

Run: 2026-10-31T09:02:17

## Dataset
- DOI 10.17632/x6x5jkk873.2 (version 2); raw zip SHA-256 `314dc4152782e9ee...`
- Images found: 1720 (published: 1720); matches published counts: **True**; unreadable: 0

## Acquisition metadata
```
{
  "resolutions": {
    "4624x3472": 1720
  },
  "camera": {
    "unknown": 1720
  },
  "orientation_tags": {
    "1": 1720
  },
  "images_with_capture_date": 0,
  "capture_date_first": null,
  "capture_date_last": null,
  "distinct_capture_days": 0,
  "capture_hour_range": null,
  "flash_values": {
    "": 1720
  }
}
```

## Duplicates (final rule)
```
{
  "rule": "identical MD5, or pHash<=12 and dHash<=16, or same class and dinov2_vits14 cosine>=0.9694550037384033, or same class and cosine>=0.95 and pHash<=20 and dHash<=20",
  "embedding_model": "dinov2_vits14",
  "embedding_quantile": 0.999,
  "embedding_threshold": 0.9694550037384033,
  "exact_duplicate_pairs": 26,
  "pairs_total": 681,
  "pairs_found_by_hash_rule": 516,
  "pairs_added_by_dinov2_threshold": 96,
  "pairs_added_by_combined_rule": 69,
  "cross_class_pairs": 2,
  "images_in_a_duplicate_group": 1075,
  "groups_total": 1112,
  "largest_group": 38,
  "group_size_counts": {
    "1": 645,
    "2": 404,
    "3": 48,
    "4": 6,
    "5": 1,
    "6": 1,
    "7": 4,
    "8": 1,
    "14": 1,
    "38": 1
  }
}
```

## Threshold sensitivity
|   pHash<= |   dHash<= |   pairs |   images grouped |   groups |   largest group |   cross-class pairs | current   |
|----------:|----------:|--------:|-----------------:|---------:|----------------:|--------------------:|:----------|
|         4 |         6 |     233 |              420 |     1501 |               4 |                   0 |           |
|         6 |         8 |     328 |              589 |     1413 |               4 |                   0 |           |
|         8 |        10 |     403 |              730 |     1341 |               4 |                   0 |           |
|        10 |        12 |     466 |              833 |     1287 |               4 |                   0 |           |
|        12 |        14 |     511 |              894 |     1250 |               4 |                   1 |           |
|        12 |        16 |     516 |              903 |     1245 |               4 |                   2 | <-- used  |
|        12 |        20 |     525 |              918 |     1236 |               4 |                   5 |           |
|        12 |        24 |     532 |              925 |     1229 |               4 |                   9 |           |
|        14 |        16 |     549 |              946 |     1219 |               7 |                   6 |           |
|        16 |        18 |     635 |             1016 |     1151 |              15 |                  39 |           |

## Paper split audit
```
folder not available
```

## Mean images per CV fold
| role   |   Alternaria leaf spot |   Healthy |   Leaf spot (early and late) |   Rosette |   Rust |   Total |
|:-------|-----------------------:|----------:|-----------------------------:|----------:|-------:|--------:|
| train  |                    318 |     429.8 |                        321.2 |        71 |   85.6 |  1225.6 |
| val    |                     42 |      50.2 |                         38.8 |         9 |   10.4 |   150.4 |
| test   |                     90 |     120   |                         90   |        20 |   24   |   344   |