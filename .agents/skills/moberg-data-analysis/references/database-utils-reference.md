# database-utils API Reference

REST-based client for querying patient metadata and annotations from the Moberg clinical database.

---

## Setup

```python
from database_utils import DatabaseUtils

db = DatabaseUtils(
    token: str = None,       # Defaults to AD_TOKEN env var
    iam_url: str = None,     # Defaults to IAM_URL env var
    workspace: str = None    # Defaults to WORKSPACE env var
)
```

In the JupyterHub sandbox, environment variables are pre-configured. Construct with no arguments:

```python
db = DatabaseUtils()
```

If you need to refresh the token from JupyterHub:

```python
from database_utils import jhub_get_access_token

token = jhub_get_access_token()  # Reads from JUPYTERHUB_USER, JPY_API_TOKEN, JUPYTERHUB_API_URL
db = DatabaseUtils(token=token)
```

---

## Querying Patients

### get_patients

```python
db.get_patients(
    page: int = 0,
    filters: dict = {},
    sort: list = [],
    admission_status: str = "all"
) -> dict
# Returns: {"data": [list of patient records], "total": int}
```

Paginated patient query. Page size is server-determined.

**Sortable/filterable fields:** `patient_identifier`, `site_id`, `project_id`, `data_file_size`, `annotation_count`, `start_time`, `end_time`, `total_monitoring_time`, `timestamp`, `first_name`, `status`

**Sort format:** List of `{"field": "...", "sort": "asc"|"desc"}` items.

### get_all_patients

```python
db.get_all_patients(
    filters: dict = {},
    sort: list = [],
    admission_status: str = "all"
) -> list[dict]
```

Fetches all matching patients, handling pagination automatically. Returns flat list of patient records.

---

## Annotations

Annotations are organized into groups. Retrieve them in two steps:

### Step 1: Get annotation groups for a patient

```python
db.get_annotation_groups(data_file_id: int) -> dict
```

`data_file_id` is the data object/file identifier from the patient record.

### Step 2: Get annotations within groups

```python
db.get_annotations_by_group(annotation_group_ids: list[int]) -> dict
```

Returns annotation records with timestamps, text, modality associations.

---

## Other Methods

```python
db.set_token(token: str) -> None       # Update auth token
db.set_workspace(workspace: str) -> None  # Switch workspace
db.get_env() -> dict                   # Fetch workspace environment config (called on init)
```

---

## Example: Full Workflow

```python
from database_utils import DatabaseUtils

db = DatabaseUtils()

# Get all patients
patients = db.get_all_patients()
print(f"Found {len(patients)} patients")

# Inspect a patient record
patient = patients[0]
print(patient.keys())  # Fields include: patient_identifier, start_time, end_time, etc.
data_file_id = patient["id"]

# Get annotation groups
groups = db.get_annotation_groups(data_file_id)
group_ids = [g["id"] for g in groups.get("data", [])]

# Get annotations
if group_ids:
    annotations = db.get_annotations_by_group(group_ids)
    for ann in annotations.get("data", []):
        print(f"{ann['start_time']} - {ann['end_time']}: {ann['text']}")
```
