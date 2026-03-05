# Default Credentials

Many web applications are installed with default credentials for initial access. These must be changed after setup, as they provide attackers with an easy entry point. Testing for default credentials is essential per OWASP's Web Application Security Testing Guide. Common defaults include `admin` / `password`.

## Testing Default Credentials

### Public Databases

Several platforms maintain default credential lists:

- **CIRT.net Password Database**: https://www.cirt.net/passwords
- **SecLists Default Credentials**: Community-maintained repository
- **SCADA GitHub Repository**: Vendor-specific default passwords

### Internet Search

A targeted search can reveal defaults. For example, searching "bookstack default credentials" reveals the installation guide with default credentials:
- **Email**: `admin@admin.com`
- **Password**: `password`

Always change default credentials after initial setup.
