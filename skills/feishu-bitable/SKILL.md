---
name: feishu-bitable
description: Use when working with Feishu (飞书) Bitable tables. Supports creating records, querying data, updating rows, and managing Bitable bases. Required for tasks involving Feishu document automation, team databases, or飞书多维表格 operations.
---

# Feishu Bitable

Feishu Bitable (飞书多维表格) is a powerful spreadsheet-like database tool in Feishu/Lark. It supports structured data storage, collaboration, and API access.

## When to Use This Skill

- User mentions Feishu, 飞书, Bitable, or provides a Bitable URL
- Task involves reading/writing team data in Feishu
- Automation of Feishu document workflows
- Managing Bitable bases, tables, or records

## Quick Reference

| Task | Tool/Method |
|------|-------------|
| Parse Bitable URL | `feishu_bitable_get_meta` |
| List table fields | `feishu_bitable_list_fields` |
| List records | `feishu_bitable_list_records` |
| Get single record | `feishu_bitable_get_record` |
| Create record | `feishu_bitable_create_record` |
| Update record | `feishu_bitable_update_record` |

## Bitable URL Formats

Feishu Bitable URLs come in two formats:

1. **Base URL**: `https://open.feishu.cn/base/XXXXXXXX?table=YYYY`
   - `XXXXXXXX` = app_token (base ID)
   - `YYYY` = table_id

2. **Wiki URL**: `https://open.feishu.cn/wiki/XXXXXXXX?table=YYYY`
   - `XXXXXXXX` = wiki node token
   - `YYYY` = table_id

Use `feishu_bitable_get_meta` to extract `app_token` and `table_id` from any Bitable URL.

## Field Types

Bitable fields have different types that require specific formats:

| Type | Format | Example |
|------|--------|---------|
| Text | `"string"` | `"Hello"` |
| Number | `123` | `42` |
| Single Select | `"Option"` | `"Done"` |
| Multi Select | `["A", "B"]` | `["TODO", "Urgent"]` |
| DateTime | timestamp (ms) | `1704067200000` |
| User | `[{id:'ou_xxx'}]` | `[{id:'ou_12345'}]` |
| URL | `{text:'Display',link:'https://...'}` | `{text:'Doc',link:'https://...'}` |
| Checkbox | `true/false` | `true` |

## Core Operations

### Parse Bitable URL

```python
# Input: Bitable URL
url = "https://open.feishu.cn/base/XXXXXXXX?table=YYYY"

# Use tool: feishu_bitable_get_meta
# Returns: app_token, table_id
```

### List Records

```python
# Use tool: feishu_bitable_list_records
app_token = "XXXXXXXX"  # From get_meta
table_id = "YYYY"       # From get_meta
page_size = 100         # Optional, default 100

# Returns: List of records with all fields
```

### Create Record

```python
# Use tool: feishu_bitable_create_record
app_token = "XXXXXXXX"
table_id = "YYYY"
fields = {
    "Name": "John Doe",
    "Status": "Active",
    "Score": 85,
    "Tags": ["VIP", "Premium"],
    "CreatedAt": 1704067200000,  # DateTime in ms
}
```

### Update Record

```python
# Use tool: feishu_bitable_update_record
app_token = "XXXXXXXX"
table_id = "YYYY"
record_id = "rec_XXXXXX"
fields = {
    "Status": "Completed",
    "Score": 90,
}
```

### Get Single Record

```python
# Use tool: feishu_bitable_get_record
app_token = "XXXXXXXX"
table_id = "YYYY"
record_id = "rec_XXXXXX"
```

## Common Patterns

### Iterating All Records

For large tables, use pagination:

```python
# First call
response = feishu_bitable_list_records(app_token, table_id)
records = response.data.items
next_token = response.data.page_token

# Subsequent calls with page_token
while next_token:
    response = feishu_bitable_list_records(
        app_token, 
        table_id, 
        page_token=next_token
    )
    records.extend(response.data.items)
    next_token = response.data.page_token
```

### Filtering Records

Bitable API doesn't support server-side filtering. Filter client-side:

```python
# Get all records
records = feishu_bitable_list_records(app_token, table_id)

# Filter in Python
filtered = [
    r for r in records 
    if r.fields.get("Status") == "Active"
]
```

### Working with Users

For User-type fields, you need the user's open_id:

```python
# User field format
fields = {
    "Owner": [{id: "ou_xxxxxxxx"}],
}

# To get user ID, you may need to:
# 1. Ask user to provide it
# 2. Use Feishu contacts API
# 3. Look up from previous records
```

## Error Handling

Common errors:

| Error | Cause | Solution |
|-------|-------|----------|
| `invalid_app_token` | Wrong app_token | Verify URL or get fresh token |
| `table_not_found` | Wrong table_id | Check table ID in URL |
| `permission_denied` | App lacks permissions | Request scope: `bitable:bitable:read/write` |
| `field_type_mismatch` | Wrong field format | Check field type and format |

## Best Practices

1. **Always parse URL first** - Use `feishu_bitable_get_meta` to get correct IDs
2. **Check field types** - Use `feishu_bitable_list_fields` to see available fields
3. **Handle pagination** - Don't assume all records fit in one response
4. **Batch operations** - Create/update multiple records in batches when possible
5. **Cache metadata** - Store app_token and table_id for repeated operations

## Related Tools

- `feishu_doc` - For Feishu document operations
- `feishu_wiki` - For Feishu knowledge base operations
- `feishu_drive` - For Feishu cloud storage
