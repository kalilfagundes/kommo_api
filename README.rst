# Kommo Python API Wrapper

A Python wrapper for the Kommo (formerly AmoCRM) API v2, adapted to work with the Kommo.com domain.

## Credits

This is an adaptation of the [amocrm_api](https://github.com/Krukov/amocrm_api) library originally created by **Dmitry Kryukov** (Krukov). The original library was designed for AmoCRM.ru, and this version has been adapted to work with Kommo.com.

**Original Author:** Dmitry Kryukov  
**Original Repository:** https://github.com/Krukov/amocrm_api  
**License:** MIT License

## About This Adaptation

Kommo (formerly AmoCRM) uses the same API endpoints and structure as AmoCRM, but operates on the `kommo.com` domain instead of `amocrm.ru`. This adaptation maintains all the functionality and features of the original library while ensuring compatibility with Kommo's western domain.

**Key Changes:**
- All API endpoints updated from `amocrm.ru` to `kommo.com`
- OAuth endpoints adapted for Kommo domain
- All functionality remains identical - same endpoints, same structure
- Full backward compatibility with the original API design

## Installation

```bash
pip install amocrm_api
```

Or install from source:

```bash
git clone https://github.com/kalilfagundes/amocrm_api
cd amocrm_api
pip install -e .
```

## Quick Start

### Authentication

Since June 2020, Kommo/AmoCRM requires OAuth 2.0 authentication. The library supports three token storage options:

- **MemoryTokensStorage** - Stores tokens in memory (lost on restart)
- **FileTokensStorage** - Saves tokens to files (default)
- **RedisTokensStorage** - Stores tokens in Redis (for multi-instance applications)

**Example:**

```python
from amocrm.v2 import tokens

tokens.default_token_manager(
    client_id="your-client-id",
    client_secret="your-client-secret",
    subdomain="your-subdomain",  # e.g., "mycompany" (without .kommo.com)
    redirect_url="https://your-redirect-url.com",
    storage=tokens.FileTokensStorage(),  # default
)

# Initialize with authorization code (first time only)
tokens.default_token_manager.init(code="authorization-code-from-kommo")
```

### Basic Usage

```python
from amocrm.v2 import Contact, Lead, Company

# Get a contact
contact = Contact.objects.get(query="John Doe")
print(contact.name)
print(contact.company.name if contact.company else "No company")

# Get all leads
for lead in Lead.objects.all():
    print(f"{lead.name} - {lead.price}")

# Create a new contact
contact = Contact(name="Jane Smith", first_name="Jane", last_name="Smith")
contact.save()

# Update a contact
contact = Contact.objects.get(query="Jane Smith")
contact.last_name = "Doe"
contact.save()
```

## Available Entities

- **Contact** - Contacts/People
- **Company** - Companies
- **Lead** - Deals/Opportunities
- **Customer** - Customers
- **Task** - Tasks
- **Note** - Notes/Comments
- **Event** - Events/Webhooks
- **Pipeline** - Sales Pipelines
- **Status** - Pipeline Statuses
- **User** - Users
- **Tag** - Tags
- **Call** - Phone Calls

## Entity Operations

Each entity has a manager (`objects`) with the following methods:

```python
# Get by ID or query
entity = Entity.objects.get(object_id=123)
entity = Entity.objects.get(query="search text")

# Get all entities (with automatic pagination)
all_entities = Entity.objects.all()

# Filter entities
filtered = Entity.objects.filter(
    query="search",
    filters=(your_filters,),
    order={"created_at": "desc"}
)

# Create
entity = Entity(name="New Entity")
entity.create()

# Update
entity.name = "Updated Name"
entity.update()

# Save (creates or updates automatically)
entity.save()
```

## Custom Fields

Kommo supports custom fields. You can map them in your models:

```python
from amocrm.v2 import Lead as _Lead, custom_field

class Lead(_Lead):
    utm_source = custom_field.UrlCustomField("UTM Source")
    delivery_type = custom_field.SelectCustomField("Delivery Type")
    address = custom_field.TextCustomField("Address")
```

You can also auto-generate custom field mappings using the CLI tool:

```bash
export AMOCRM_CLIENT_ID=xxx
export AMOCRM_SECRET=xxx
export AMOCRM_SUBDOMAIN=xxx
export AMOCRM_REDIRECT_URL=xxx
export AMOCRM_CODE=xxx  # optional
pyamogen > models.py
```

## Documentation

For comprehensive documentation, see [Documentacao.md](Documentacao.md) (in Portuguese) or the original repository's documentation.

## Requirements

- Python >= 3.6
- requests
- pyjwt

Optional:
- redis (for RedisTokensStorage)
- python-slugify (for pyamogen CLI tool)

## License

MIT License - Same as the original project.

## Contributing

This is an adaptation of the original work. For issues specific to this Kommo adaptation, please open an issue in this repository. For general API wrapper improvements, consider contributing to the original repository.

## Acknowledgments

Special thanks to Dmitry Kryukov (Krukov) for creating the original amocrm_api library, which this project is based on.

