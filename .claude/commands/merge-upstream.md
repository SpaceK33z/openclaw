# Merge Upstream

Syncs this fork with upstream, resolves any merge conflicts, pushes, waits for CI, and fixes failures.

## Instructions

### 1. Run the merge upstream script

```bash
./scripts/merge-upstream.sh
```

If this succeeds with no conflicts, proceed to step 3.

### 2. Handle merge conflicts (if any)

If the merge fails with conflicts:

1. Check which files have conflicts:
   ```bash
   git status
   ```

2. For each conflicted file:
   - Read the file to understand both sides of the conflict
   - Resolve the conflict by choosing the appropriate changes
   - Prefer upstream changes for dependencies (`package.json`, `pnpm-lock.yaml`, patches)
   - For source files, merge logic carefully, keeping local customizations where intentional

3. After resolving all conflicts:
   ```bash
   git add <resolved-files>
   git commit --no-edit
   ```

### 3. Push changes

```bash
git push origin main
```

### 4. Wait for CI to start

Sleep for 3 minutes to allow CI workflows to initialize:

```bash
sleep 180
```

### 5. Watch CI and fix failures

Monitor the CI run:

```bash
gh run watch
```

If CI fails:

1. Get the failed run details:
   ```bash
   gh run list --branch main --limit 1 --json databaseId,status,conclusion
   ```

2. Fetch failure logs:
   ```bash
   gh run view <RUN_ID> --log-failed
   ```

3. Analyze the failure type:
   - **Build errors**: Fix TypeScript/compilation issues
   - **Test failures**: Fix failing tests or underlying code
   - **Lint errors**: Run `pnpm lint` and fix violations

4. Verify fixes locally:
   ```bash
   pnpm lint && pnpm build && pnpm test
   ```

5. Commit and push fixes:
   ```bash
   git add <fixed-files>
   git commit -m "fix: resolve CI failures after upstream merge"
   git push origin main
   ```

6. Repeat steps 4-5 until CI passes.

## Usage

Invoke with `/merge-upstream` when you want to sync with the upstream repository and ensure CI passes.
