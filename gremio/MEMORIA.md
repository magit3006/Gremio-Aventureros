# Memoria · Gremio de Aventureros

**Código de alumno/a:** 3008  (no pongas tu nombre)

## 1. Mi aventurero
- Usuario que he añadido a la tabla: manuel
- Comando o pasos que he usado para generar el hash:

  `php -r "echo password_hash('TuClave', PASSWORD_DEFAULT), PHP_EOL;"`

  Después inserté el hash generado en `sql/mi_aventurero.sql`. La contraseña
  se usa solo al iniciar sesión y no se guarda en la base de datos ni en cookies.

## 2. Parte 1 · match
No he puesto `default`: los seis rangos válidos están contemplados explícitamente.
Si se llamara directamente con `'Z'`, `match` lanzaría `UnhandledMatchError`.
Prefiero que un dato inesperado falle de forma visible; en la web, el filtro
valida antes el rango y convierte cualquier valor no válido en `null`.

## 3. Parte 2 · Validación de la URL
Con `tablon.php?rango=Z&oro=hola` aparecen las 10 misiones y no se produce
ningún error: `leerRangoFiltro()` solo acepta valores de `RANGOS`, y
`leerOroMinimo()` usa `filter_var()` con `FILTER_VALIDATE_INT` y límites de
0 a 10000. Al ser inválidos, el rango queda en `null` y el oro mínimo en `0`.

## 4. Parte 3 · Sesión frente a cookie
Las sesiones guardan el aventurero conectado, el zurrón, la hora de entrada
y el contador de visitas. Estos datos deben vivir en el servidor porque son
estado privado y temporal, y la cookie solo contiene el identificador de
sesión. Las cookies guardan el tema visual y, si se pide, el nombre de usuario
recordado. Son datos no sensibles y pueden sobrevivir al navegador. No guardo
el zurrón ni la contraseña en cookies, porque el cliente puede modificarlas y
porque no deben persistir entre sesiones o se expondrían datos innecesarios.

## 5. Parte 3 · Seguridad del login
Si se escribe `' OR '1'='1`, la consulta preparada trata el texto como un
valor y no lo interpreta como SQL, por lo que no encuentra coincidencias y
el acceso falla. `session_regenerate_id(true)` cambia el identificador de
sesión tras el login y elimina la cookie/ID antiguo, dificultando el
secuestramiento de sesiones. El usuario y la contraseña muestran el mismo
mensaje para evitar revelar si existe una cuenta concreta.

## 6. Pruebas que he hecho
| Prueba | Resultado esperado | Resultado obtenido |
|---|---|---|
| Entrar en tablon.php sin login | Redirección a login con aviso privado | Pendiente de ejecutar |
| Login con contraseña mala | Error genérico sin entrar | Pendiente de ejecutar |
| Aceptar la misma misión dos veces | No duplica ni permite reenvío de F5 | Pendiente de ejecutar |
| Cerrar sesión y volver al zurrón | Zurrón vacío tras login/cierre | Pendiente de ejecutar |
| Cambiar el tema y cerrar sesión | Tema conservado y zurrón vacío | Pendiente de ejecutar |

## 7. Dificultades y cómo las he resuelto

La principal dificultad fue distinguir qué información debía permanecer en el
servidor y cuál podía guardarse en el navegador. Guardé el estado del usuario
y el zurrón en la sesión, y reservé las cookies para el tema y el nombre de
usuario recordado. Para comprobar credenciales, usé una consulta preparada y
`password_verify()` para no concatenar valores en SQL ni almacenar claves en
claro.
