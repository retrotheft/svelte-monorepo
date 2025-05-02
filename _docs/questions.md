# Questions

This document contains questions and answers I had while learning about monorepos in LevelUpTuts' monorepo course.

## How does the monorepo handle different version dependencies? It must need to store multiple copies of the dependencies? Where does it store them?

pnpm keeps cached versions of all dependencies in the root `node_modules/.pnpm` folder. Further, you can use `list` and `why` to obtain specific dependency information. Refer to `setup.md` for more info.

---

## How to handle vercel websites that rely on git push to deploy when in a monorepo?

Vercel has monorepo support detailed on [this page](https://vercel.com/docs/monorepos).

As far as I understand, when creating branches you now branch the entire monorepo.

For preview deployments on Vercel, the easiest way is to create a pull request, which will automatically create a preview deployment.

I have not tested this yet.

---

## Handling the PNPM Configuration Warning

*NOTE: this answer came from Claude, so it might not be 100% correct.*

This warning occurs because you're using PNPM workspaces in a monorepo, and PNPM expects certain configurations to only exist at the root level.

### Solution: Remove From Subprojects

You should remove the `"pnpm"` field from your subproject's `package.json` file (`/Users/jamesbridger/dev/svelte5/svelte-monorepo/sites/svelte-site-example/package.json`) and only keep it in the root `package.json` of your monorepo.

### Example Structure:

```
svelte-monorepo/
├── package.json (keep "pnpm" field here)
├── pnpm-workspace.yaml
└── sites/
    └── svelte-site-example/
        └── package.json (remove "pnpm" field)
```

### Root package.json (keep this):

```json
{
  "name": "svelte-monorepo",
  "pnpm": {
    "peerDependencyRules": {
      "allowedVersions": {
        "svelte": "5.x"
      }
    },
    "overrides": {
      // any overrides you need
    }
  }
}
```

### Subproject package.json (edit this):

Before:
```json
{
  "name": "svelte-site-example",
  "pnpm": {
    "peerDependencyRules": {
      "allowedVersions": {
        "svelte": "5.x"
      }
    }
  }
}
```

After:
```json
{
  "name": "svelte-site-example"
  // "pnpm" field removed
}
```

This ensures all PNPM workspace configuration is centralized at the root level, which follows PNPM's intended usage and will resolve the warning.

### Additional Tip:

If you're migrating multiple projects with different PNPM settings, make sure to consolidate all necessary configuration into the root `package.json`. This might require merging settings if different projects had unique configurations.

What are the potential consequences of doing this? Do I lose any functionality at the sub project level?

### Consequences of Moving PNPM Configuration to Root Level

Moving the PNPM configuration from sub-projects to the root level has mostly positive consequences, with minimal downsides:

## What You Won't Lose

1. **Functionality Remains Intact**:
   - All the same rules still apply to your sub-projects
   - Dependency resolution works the same way
   - Build processes continue to function as before

2. **Easier Maintenance**:
   - Single source of truth for PNPM configuration
   - Avoids conflicting configurations between projects

### Potential Concerns

1. **Global vs. Project-Specific Settings**:
   - All projects now share the same PNPM configuration
   - If different projects previously had *conflicting* PNPM settings, you'll need to reconcile them

2. **For Svelte 5 Specifically**:
   - Since your example shows `"allowedVersions": { "svelte": "5.x" }`, moving this to the root means all projects in your monorepo will use this rule
   - This is fine if all projects are meant to use Svelte 5, but could be an issue if some projects were intentionally pinned to different Svelte versions

3. **Overrides Behavior**:
   - Any dependency overrides will apply workspace-wide instead of per-project
   - This is generally beneficial for consistency but requires careful management

### Real-World Impact

In practice, most teams find that centralizing PNPM configuration leads to:

- Fewer conflicts between packages
- More consistent dependency versions across the monorepo
- Easier upgrades when moving to new versions

If you did need project-specific behaviors, there are usually alternative approaches through other package.json fields or build tool configurations that don't rely on the PNPM-specific fields.

The warning exists precisely because PNPM was designed to have workspace-level configuration, not project-level configuration, for these settings.
