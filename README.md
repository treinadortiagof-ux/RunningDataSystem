# Running Data System (RDMS)

This project uses a **relational, bottom-up data model** to track athletic performance. The **Segments** file acts as the Single Source of Truth for all time and distance metrics.

## Core Design Principles
* **One CSV = One Data Type:** Different metrics (running vs. drills) are kept in separate files.
* **Atomic Units:** All time is in **seconds**; all distance is in **meters**; all weights are in **kg**.
* **Relational Integrity:** Files are linked via `athlete_id` and `session_id`.
* **Derived Totals:** Session-level totals (total distance/time) are not stored; they are calculated by Python from the segment data.

---

## Data Dictionary

### 1. `athletes.csv`
| Column | Type | Example | Description |
| :--- | :--- | :--- | :--- |
| `athlete_id` | String (PK) | `ATH_001` | Unique ID for the athlete. |
| `name` | String | `Carlos Silva` | Full name. |
| `dob` | Date | `1995-05-12` | Date of birth (YYYY-MM-DD). |
| `weight_kg` | Float | `72.5` | Body weight for metrics. |

### 2. `sessions.csv` Metadata of each session
| Column | Type | Example | Description |
| :--- | :--- | :--- | :--- |
| `athlete_id` | String (FK) | `ATH_0001` | Links to athletes file. |
| `session_id` | String (PK) | `20260120_01` | Unique ID (YYYYMMDD_NN). |
| `date` | Date | `2026-01-20` | Date of the workout. |
| `start_time` | Time | `17:30` | 24-hour format start time. |
| `workout_type` | Enum | `cc` | See Controlled Vocabulary. |
| `rpe` | Integer | `7` | Rate of Perceived Exertion (1-10). |
| `notes` | String | `20'cc + 6x800 + 1km` | Descrição do treino e notas |

### 3. `segments.csv` Detailed performance data
| Column | Type | Example | Description |
| :--- | :--- | :--- | :--- |
| `athlete_id` | String (FK) | `ATH_0001` | Links to athletes file. |
| `session_id` | String (FK) | `20260120_01` | Links to session file. |
| `segment_number`| Integer | `1` | Chronological order in session. |
| `segment_type` | Enum | `warmup run` | See Controlled Vocabulary. |
| `distance_m` | Integer | `5000` | Distance in meters. |
| `duration_sec` | Integer | `1200` | Duration in seconds. |
| `avg_hr` | Integer | `165` | Average HR, Nullable (leave blank if no data). |
| `shoe_id` | String (FK) | `METASPEED` | Links to shoes file. |

### 4. `drills.csv`
| Column | Type | Example | Description |
| :--- | :--- | :--- | :--- |
| `athlete_id` | String (FK) | `ATH_001` | Links to athletes file. |
| `session_id` | String (FK) | `20260120_01` | Links to session file. |
| `drill_name` | String | `A-Skips` | Name of the exercise. |
| `sets` | Integer | `3` | Number of sets. |
| `reps` | Integer | `10` | Repetitions per set. |
| `weight_kg` | Float | `0.0` | Resistance weight used. |
| `duration_sec` | Integer | `120` | Total time for this drill. |

### 5. `shoes.csv`
| Column | Type | Example | Description |
| :--- | :--- | :--- | :--- |
| `shoe_id` | String (PK) | `SHOE_01` | Unique ID for the pair. |
| `model` | String | `Pegasus 40` | Brand and model name. |
| `purchase_date` | Date | `2025-12-01` | Date bought (YYYY-MM-DD). |
| `status` | Enum | `active` | See Controlled Vocabulary. |

---

## Controlled Vocabulary (Enums)
* **`segment_type`**: `warmup run`, `warmup drills`, `work`, `recovery`, `sets`, `cooldown run`, `stretching`
* **`workout_type`**: `continuous run`, `intervals`, `sets`, `race`
* **`status`**: `active`, `retired`
