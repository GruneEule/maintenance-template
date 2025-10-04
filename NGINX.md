# NGINX Integration for Maintenance Template

This guide explains how to integrate the maintenance template using NGINX.
A pre-made example configuration ([maintenance.conf](maintenance.conf)) is provided.

## Example Configuration

```nginx
# Wartungsmodus aktivieren (1 = aktiv, 0 = aus)
set $maintenance 0;

# Wartung prüfen
if ($maintenance = 1) {
    return 503;
}

# Wartungsseite bei 503
error_page 503 @maintenance;

location @maintenance {
    rewrite ^ /src/index.html break;
    proxy_pass http://maintenance-page.grueneeule.de;

    proxy_set_header Host maintenance-page.grueneeule.de;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    proxy_hide_header Content-Type;
    add_header Content-Type "text/html; charset=UTF-8";
}
```

## Explanation

- With `set $maintenance 1;`, the maintenance mode is **activated**.
- With `set $maintenance 0;`, the maintenance mode is **deactivated**.

## Usage

### 1. As a separate file:

- Create a file `maintenance.conf` with the content above.
- Include it in your NGINX configuration:

  ```nginx
  include /path_to_your_website/maintenance.conf;
  ```

### 2. Directly in NGINX Config:

- Copy the code block directly into your existing NGINX configuration file.

## Note

- The maintenance page is loaded from the GitHub repository via **jsDelivr**.
- Alternatively, you can host the HTML file locally.
