# 🚦 Rate Limiting con Redis: Protege tu API

¿Tu API es pública? Entonces es vulnerable. Un script malintencionado (o un usuario impaciente pulsando F5) puede tumbar tu servidor. Necesitas **Rate Limiting**.

---

## 🛡️ ¿Qué es?

Es limitar cuántas peticiones puede hacer un usuario en un periodo de tiempo. Ej: "Máximo 100 peticiones cada 15 minutos".

---

## ⚡ ¿Por qué Redis?

Podrías guardar los contadores en memoria (RAM del servidor), pero:
1.  Si reinicias el servidor, se pierden.
2.  Si tienes múltiples instancias (escalado horizontal), la memoria no se comparte. Un usuario podría hacer 100 peticiones a la Instancia A y 100 a la Instancia B.

**Redis** es perfecto porque es rapidísimo y centralizado. Todas tus instancias consultan al mismo Redis.

---

## 💻 Implementación (Express + rate-limit-redis)

No reinventes la rueda. Usa `express-rate-limit` con `rate-limit-redis`.

```typescript
import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import { createClient } from 'redis';

// 1. Cliente Redis
const redisClient = createClient({ url: 'redis://localhost:6379' });
await redisClient.connect();

// 2. Configurar el Limiter
const limiter = rateLimit({
	windowMs: 15 * 60 * 1000, // 15 minutos
	max: 100, // Límite de 100 peticiones por IP
	standardHeaders: true, // Devuelve info en headers `RateLimit-*`
	legacyHeaders: false,
	store: new RedisStore({
		sendCommand: (...args: string[]) => redisClient.sendCommand(args),
	}),
    message: 'Too many requests, please try again later.',
});

// 3. Aplicar a todas las rutas
app.use(limiter);

// O solo a rutas sensibles (login, registro)
app.use('/api/auth', authLimiter);
```

---

## 🔍 Headers de Respuesta

Cuando usas esto, tu API responde con headers útiles para el cliente:

```http
RateLimit-Limit: 100
RateLimit-Remaining: 99
RateLimit-Reset: 1200
```

Así el frontend sabe cuándo debe dejar de enviar peticiones.

¡Pon un semáforo a tu API antes de que sea tarde!
