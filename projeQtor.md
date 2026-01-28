# ProjeQtOr API Integration Notes

## Instance Details
- **Base URL:** `https://portal.bfgintegration.ca/schedule2k23/`
- **API Endpoint:** `https://portal.bfgintegration.ca/schedule2k23/api/Ticket`
- **API Status:** Enabled (returns 401 Unauthorized - needs credentials)

## API Overview

ProjeQtOr REST API supports:
- `GET` - retrieve records
- `PUT/POST` - create/update records (requires AES encryption)
- `DELETE` - remove records (requires AES encryption)

### Endpoint Patterns
```
GET  /api/Ticket/{id}           - Get specific ticket
GET  /api/Ticket/all            - Get all tickets
POST /api/Ticket                - Create ticket (encrypted JSON body)
PUT  /api/Ticket                - Update ticket (encrypted JSON body)
```

## Authentication Requirements

### 1. HTTP Basic Auth (.htpasswd)
- Configured on the server at `/api/.htpasswd`
- Username must match a user in ProjeQtOr database
- Requires server-level access to configure

### 2. AES Encryption for POST/PUT/DELETE
- Data must be encrypted with AES-128/192/256
- Uses the user's **API Key** as the encryption key
- Default is AES-128

## Setup Steps (Requires Server Admin)

1. **Create API User in ProjeQtOr**
   - Go to Users screen
   - Create/select user for API access
   - Note the API Key from user profile
   - Ensure user has read/create rights for Tickets

2. **Server-side .htpasswd Setup**
   ```bash
   htpasswd -c /path/to/schedule2k23/api/.htpasswd username
   ```

3. **Configure .htaccess** (in /api/ folder)
   ```
   AuthUserFile "/full/path/to/schedule2k23/api/.htpasswd"
   AuthType Basic
   AuthName "ProjeQtOr API"
   Require valid-user
   ```

4. **Apache Config** - ensure `AllowOverride All` for api directory

## Credentials Needed for Integration

| Item | Source | Status |
|------|--------|--------|
| Username | .htpasswd | Need from admin |
| Password | .htpasswd | Need from admin |
| API Key | ProjeQtOr User screen | Need from admin |

## Integration Plan for Work Order Parser

Once credentials are obtained:

1. Add ProjeQtOr connection settings to `work-order-parser_fetch.html`
2. Implement AES encryption in JavaScript
3. Add "Create Ticket in ProjeQtOr" button
4. Map work order fields to Ticket fields:
   - WO#, WOT#, INC# → Ticket reference/description
   - Location/Address → Ticket location fields
   - Contact Name/Phone → Ticket contact info
   - Date/Time → Ticket scheduled date
   - Description → Ticket description

## Resources

- [ProjeQtOr Forum - How to obtain API Key](https://www.projeqtor.org/en/kunena-2/5-ask-questions/12384-solved-how-to-obtein-un-apikey)
- [ProjeQtOr Forum - API Activation](https://www.projeqtor.org/en/forum/5-ask-questions/8869-api-activation)
- [ProjeQtOr GitHub - API Source](https://github.com/papjul/projeqtor/blob/master/api/index.php)
