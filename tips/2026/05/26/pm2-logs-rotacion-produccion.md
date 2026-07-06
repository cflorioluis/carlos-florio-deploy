# PM2: logs, rotación y diagnóstico

PM2 mantiene procesos Node vivos en servidores Linux.

---

## `ecosystem.config.js` mínimo

```javascript
module.exports = {
  apps: [{
    name: 'api',
    script: 'dist/main.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: { NODE_ENV: 'production' },
  }],
};
```

---

## Comandos útiles

```bash
pm2 start ecosystem.config.js
pm2 logs api
pm2 save && pm2 startup
```

Más detalles: https://pm2.keymetrics.io/docs/usage/log-management/
