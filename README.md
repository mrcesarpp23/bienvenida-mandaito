# Mandaito — Página de Bienvenida (onboarding empresas)

Landing estática para anuncios de Facebook. El usuario llega desde el anuncio, ve qué es Mandaito + un video, y da clic en **"Registrar mi empresa"** (que lo lleva a `https://mandaitord.tech/registro/empresa`).

- **Sitio:** `https://bienvenida.mandaitord.tech`
- **Separada de la plataforma** (no toca el Laravel). Solo HTML estático.

## Antes de publicar — reemplazar 2 valores en `index.html`
1. **Video:** cambiar `VIDEO_ID` por el ID de tu video de YouTube.
   - Ej: si tu link es `https://youtu.be/AbC123xyz`, el ID es `AbC123xyz`.
2. **Píxel de Facebook:** cambiar `[PIXEL_ID]` (aparece 3 veces) por tu ID real del Píxel de Meta.

## Deploy en el VPS (una sola vez)

### 1. DNS (en Hostinger)
Crear un registro **A**: `bienvenida` → `187.124.233.210`

### 2. Clonar el repo
```bash
cd /var/www
git clone https://github.com/mrcesarpp23/bienvenida-mandaito.git bienvenida
```

### 3. nginx — nuevo server block
`/etc/nginx/sites-available/bienvenida.mandaitord.tech`:
```nginx
server {
    listen 80;
    server_name bienvenida.mandaitord.tech;
    root /var/www/bienvenida;
    index index.html;
    location / { try_files $uri $uri/ =404; }
}
```
```bash
ln -s /etc/nginx/sites-available/bienvenida.mandaitord.tech /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

### 4. SSL
```bash
certbot --nginx -d bienvenida.mandaitord.tech
```

## Actualizar la página (cada vez que cambie)
```bash
cd /var/www/bienvenida && git pull
```
(No necesita build — es estático.)

## Verificar
- Visitar `https://bienvenida.mandaitord.tech` → carga por HTTPS, el video reproduce, el botón abre el registro real.
- Validar el Píxel con la extensión **Meta Pixel Helper** (debe disparar `PageView`, y `Lead` al clic en registrar).
