
**Universidad Tecnológica Nacional – Facultad Regional Resistencia**  
**Cátedra:** Programación IV · Cohorte 2026  
**Docente:** Fernando Aguirre  
**Proyecto:** Monitor de Servidor con Laravel (Lectura de métricas del sistema operativo Windows y exposición vía Web y API JSON).

1. ¿Por qué la temperatura suele aparecer como “No disponible” en Windows?

Porque Windows pide permisos de administrador para consultar la temperatura a través de WMI. Si corrés el servidor con una terminal normal sin privilegios, Windows bloquea la consulta y el código devuelve null para no romper la app. Además, muchas placas madre y procesadores modernos directmente no informan ese dato por WMI a menos que uses programas o drivers específicos.

2. ¿Por qué el script de PowerShell nunca debe incluir datos que vengan del usuario?

Porque abriría una falla de seguridad grave de inyección de comandos. La función shell_exec corre directamente en la consola del sistema operativo, si le metemos datos que vienen de un formulario o de la URL, un usuario podría inyectar comandos extras (usando ;, &, etc.) y ejecutar cualquier cosa en nuestra computadora o servidor.

3. ¿Qué riesgo tiene dejar /api/metrics sin autenticación en un servidor real?

Tiene dos riesgos principales:

Fuga de información: Cualquiera puede ver datos internos del servidor (nombre del equipo, versión de Windows y PHP, cuánto disco y memoria tiene libre,etc), lo que le facilita a un atacante buscar vulnerabilidades.
Sobrecarga del servidor: Como cada petición abre un proceso de PowerShell en segundo plano, si alguien bombardea la API con muchas peticiones seguidas, el servidor se puede saturar y caer.

4. Con polling cada 5 segundos, ¿cuántos pedidos por minuto recibe el servidor si hay 50 personas mirando?

Recibe 600 pedidos por minuto.

Calculo:

Cada persona hace 1 pedido cada 5 segundos, o sea 12 pedidos por minuto

60/5=12

Con 50 personas conectadas al mismo tiempo: 

50×12=600

600 pedidos por minuto (son 10 pedidos por segundo, cada uno abriendo PowerShell).

5. Si una app solo necesita la temperatura, ¿qué datos recibe de más al llamar a /api/metrics?

Recibe de más prácticamente todo el resto del JSON:

hostname
os
php
disk (total, libre y porcentaje)
memory (total, disponible y porcentaje)
cpu_pct
uptime_s
at (fecha y hora)
