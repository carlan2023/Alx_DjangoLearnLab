# Custom User Model Setup Guide

## Overview
A custom user model has been created by extending Django's `AbstractUser` class with additional fields relevant to the application.

## Custom User Model Details

### Location
- **App**: `relationship_app`
- **Model Class**: `CustomUser` in `relationship_app/models.py`

### Custom Fields Added

1. **date_of_birth** (DateField)
   - Stores the user's date of birth
   - Optional field (null=True, blank=True)
   - Useful for age verification or birthday features

2. **profile_photo** (ImageField)
   - Stores the user's profile photo
   - Uploaded to: `media/profile_photos/`
   - Optional field (null=True, blank=True)
   - Requires Pillow package for image processing

## Configuration

### 1. Settings.py Update
The following changes have been made to `LibraryProject/settings.py`:

```python
# Custom User Model
AUTH_USER_MODEL = 'relationship_app.CustomUser'

# Media files configuration for profile photos
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

### 2. Admin Registration
The custom user model is registered in `relationship_app/admin.py` with:
- Extended fieldsets to display custom fields
- Custom list display showing date_of_birth
- Filtering capability by date_of_birth

## Installation Steps

### Step 1: Install Required Packages
```bash
pip install Pillow
```

### Step 2: Create Migration
```bash
python manage.py makemigrations relationship_app
```

### Step 3: Apply Migration
```bash
python manage.py migrate
```

### Step 4: Update URLconf (if needed)
Add media file serving to `LibraryProject/urls.py`:

```python
from django.conf import settings
from django.conf.urls.static import static

# ... existing patterns ...

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### Step 5: Create Superuser
```bash
python manage.py createsuperuser
```

## Usage Examples

### Creating a New User
```python
from relationship_app.models import CustomUser
from django.core.files.storage import default_storage

user = CustomUser.objects.create_user(
    username='john_doe',
    email='john@example.com',
    password='securepassword123',
    first_name='John',
    last_name='Doe',
    date_of_birth='1990-05-15'
)

# Adding profile photo
with open('path/to/photo.jpg', 'rb') as f:
    user.profile_photo.save('john_doe.jpg', f)
```

### Querying Users
```python
# Get users born in a specific year
users_1990 = CustomUser.objects.filter(date_of_birth__year=1990)

# Get users with profile photos
users_with_photos = CustomUser.objects.exclude(profile_photo='')

# Get user details
user = CustomUser.objects.get(username='john_doe')
print(f"{user.get_full_name()} - Born: {user.date_of_birth}")
```

## Important Notes

⚠️ **If migrating from default User model:**
- This setup assumes a fresh database or that you're starting with a new project
- If you have existing data, you'll need to use Django's `django-extensions` or manually handle the migration
- Once `AUTH_USER_MODEL` is set, it cannot be changed without significant database modifications

✅ **ImageField Requirements:**
- Ensure the `media` directory exists and has proper write permissions
- The directory will be created automatically when a profile photo is uploaded
- In production, use cloud storage (S3, Azure Blob Storage, etc.) instead of local filesystem

## Admin Interface
Access the Django admin at `/admin/` to:
- Create new users with profile photos and date of birth
- View and search users by date of birth
- Edit user information including new custom fields
