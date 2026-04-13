# Secure Stripe API Configuration Guide

## 🔒 Security Setup

Your Stripe API secret is now safely managed and separated from your version control system.

### What Changed

1. **Collection file** (`stripe-collection.json`): Now uses `{{stripe_secret}}` variable instead of hardcoded secret
2. **Environment file** (`stripe-env.json`): Added to `.gitignore` - never committed to Git 
3. **`.gitignore`**: Configured to exclude all sensitive files

### How to Use Locally

1. **Keep `stripe-env.json` locally only** (do not commit it)
   ```bash
   # File should be in: environment/stripe-env.json
   ```

2. **In Postman/Newman:**
   - Load your environment file: `environment/stripe-env.json`
   - The `{{stripe_secret}}` variable will automatically use the value from your environment

3. **Run Newman commands:**
   ```bash
   newman run collection/stripe-collection.json \
     -e environment/stripe-env.json
   ```

### For Team Members

1. Copy the template file:
   ```bash
   cp environment/.env.example environment/stripe-env.json
   ```

2. Edit `stripe-env.json` and replace `YOUR_STRIPE_SECRET_KEY_HERE` with your actual Stripe test key

3. **IMPORTANT:** Never commit this file. Git will ignore it automatically.

### Whats in `.gitignore`

- `environment/stripe-env.json` - Your local environment with real secrets
- `.env` - Any local environment files
- `.env.local` - Local overrides

### Git Status Check

```bash
git status
```

You should NOT see `stripe-env.json` in the list of changes. If you do, check your `.gitignore` is working.

### Best Practices

✅ **DO:**
- Keep secrets in your local environment file only
- Use variable placeholders (`{{stripe_secret}}`) in collections
- Share `.env.example` / templates with your team
- Rotate keys if accidentally committed
- Review `.gitignore` before commits

❌ **DON'T:**
- Commit `stripe-env.json` to Git
- Hardcode secrets in collection files
- Share real secrets in team chats or email
- Use production keys in test collections

### If Accidentally Pushed

If your secret was pushed to Git history:

1. **Invalidate the key immediately** in Stripe Dashboard
2. **Generate a new key** in Dashboard
3. Delete Git history (advanced):
   ```bash
   git filter-branch --force --index-filter \
     "git rm --cached --ignore-unmatch collection/stripe-collection.json" \
     --prune-empty --tag-name-filter cat -- --all
   git push --force --all
   ```

Or use BFG Repo-Cleaner for easier cleanup.

### Verify Current Safety

Check that your collection no longer contains hardcoded keys:
```bash
grep "sk_test" collection/stripe-collection.json
```

Should return no results if secrets are properly removed.
