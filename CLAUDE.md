# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Django 5.0 tutorial project. SQLite, single app (`accounts`) that swaps in a custom user model. No requirements file — dependencies live in `.venv` (Django, black).

## Commands

All commands assume the venv's interpreter; activate `.venv` or prefix with `.venv/bin/python`.

```bash
python manage.py runserver           # dev server at http://127.0.0.1:8000/
python manage.py makemigrations      # generate migrations after model changes
python manage.py migrate             # apply migrations (creates db.sqlite3)
python manage.py createsuperuser     # admin login (only route wired up is /admin/)
python manage.py test                # run test suite
python manage.py test accounts.tests.TestClassName.test_name  # single test
black .                              # format
```

## Architecture

- `django_project/` — project config. Only URL route is `admin/`; there are no app-level URLs or views yet.
- `accounts/` — custom user app. `CustomUser` extends `AbstractUser` adding an `age` field. `forms.py` provides `CustomUserCreationForm` / `CustomUserChangeForm`; `admin.py` registers them so the `age` field appears in both add and change fieldsets in Django admin.

### Custom user gotcha

`settings.py` is **missing** `AUTH_USER_MODEL = "accounts.CustomUser"` and `accounts/migrations/` has no initial migration. Running `migrate` without first adding that setting and generating the initial migration will bake in Django's default `auth.User` and corrupt the schema — fixing it later requires wiping the DB. Whenever working on auth/user code, verify `AUTH_USER_MODEL` is set and the initial `accounts` migration exists before any `migrate` call.
