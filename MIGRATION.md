# Example Pack Migration Guide

The example packs in this directory were created for an earlier version of the Align Spec and need migration to v1 format.

## Changes Required

### 1. Severity Values

**Old format:**
```yaml
severity: "MUST"   # or MUST without quotes
severity: "SHOULD"
severity: "MAY"
```

**New format:**
```yaml
severity: "error"  # blocking
severity: "warn"   # should fix
severity: "info"   # advisory
```

**Migration:** ✅ DONE - All files updated

### 2. Rule IDs

**Old format:**
```yaml
id: "require-ci-gates"  # Single segment
```

**New format:**
```yaml
id: "ci.gates.require"  # Min 3 segments: category.subcategory.rule-name
```

**Pattern:** `^[a-z0-9]+(\\.[a-z0-9-]+){2,}$`

**Status:** ⚠️ TODO - Needs systematic migration

### 3. Required Fields

**Missing:** `applies_to` field is now required on all rules

**Add:**
```yaml
rules:
  - id: "category.subcategory.rule"
    severity: "error"
    applies_to: ["**/*.ts"]  # Glob patterns
    # ... rest of rule
```

**Status:** ⚠️ TODO - Needs systematic migration

### 4. Check Input Fields

Different check types require different input field names:

**file_presence:**
```yaml
# Old
inputs:
  pattern: ".github/workflows/*.yml"

# New
inputs:
  paths: [".github/workflows/*.yml", ".github/workflows/*.yaml"]
```

**regex:**
```yaml
# Old
inputs:
  paths: [".git/COMMIT_EDITMSG"]

# New  
inputs:
  include: [".git/COMMIT_EDITMSG"]
```

**Status:** ⚠️ TODO - Needs systematic migration

## Migration Status

- [x] global.yaml - Fully migrated and validates
- [ ] debugging.yaml
- [ ] docs.yaml
- [ ] nextjs_app_router.yaml
- [ ] rule-authoring.yaml
- [ ] security.yaml
- [ ] tdd.yaml
- [ ] testing.yaml
- [ ] typescript.yaml
- [ ] vercel_deployments.yaml
- [ ] web_quality.yaml

## Validation

To validate a pack:

```bash
cd /Users/gabe/Sites/aligntrue
node -e "
const { parseYamlToJson, validateAlignSchema } = require('./packages/schema/dist/index.js');
const fs = require('fs');
const yaml = fs.readFileSync('examples/packs/FILENAME.yaml', 'utf8');
const parsed = parseYamlToJson(yaml);
const result = validateAlignSchema(parsed);
console.log('Valid:', result.valid);
if (!result.valid) {
  result.errors?.forEach(e => console.log(' -', e.path, ':', e.message));
}
"
```

## Next Steps

1. Create automated migration script
2. Migrate remaining 10 example files
3. Update integrity hashes
4. Add CI check to prevent schema regressions

