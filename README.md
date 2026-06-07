# DNS-AMP
## 🕵️‍♂️ DNS Amplification: Cuando usan servidores ajenos para atacar tu red (y cómo frenarlo)

No es la primera vez que vemos cómo los atacantes reutilizan la propia infraestructura pública de Internet para multiplicar su poder de fuego. Esta vez, el vector elegido fue un ataque de denegación de servicio por **Amplificación DNS**.

### 👀 El Contexto:
Un atacante comenzó a enviar ráfagas masivas de solicitudes de resolución de nombres a una lista enorme de servidores DNS públicos y abiertos (Open DNS). El truco estuvo en dos factores clave:
1. **IP Spoofing:** El atacante falsificó la dirección IP de origen de las consultas, colocando la IP de nuestra víctima (el ISP). Así, todas las respuestas de los servidores DNS no regresaron al atacante, sino que inundaron la red del ISP.
2. **Payload Masivo:** Solicitaron específicamente registros de tipo `TXT`, que están cargados de información y devuelven respuestas gigantescas. Una consulta de pocos bytes se transformó en una respuesta hasta 50 veces más grande. 

El resultado fue un tráfico entrante asimétrico y descomunal que amenazaba con saturar los enlaces principales del proveedor.

### 👌 El Despliegue de Emergencia
Al igual que en incidentes anteriores, no perdimos tiempo intentando mitigar el ataque dentro del core de la red. Colocamos nuestro **Zeus Firewall** en el borde absoluto, justo al inicio de la infraestructura, para interceptar el flujo antes de que tocara cualquier router interno.

### 🎩 La Lógica de Filtrado (Donde ocurre la magia)
La ventaja de trabajar a nivel de ISP es que la naturaleza del tráfico legítimo es predecible en sus flujos de conexión:

> "Ningún usuario de la red residencial o corporativa del ISP recibe respuestas DNS (`Puerto 53 UDP`) de servidores externos a menos que sus dispositivos hayan realizado primero una consulta (`Query`) saliente".

Volvimos a aplicar nuestra premisa fundamental de seguimiento de estados en **Zeus Firewall**: monitorear rigurosamente las tablas de flujos. Todo paquete UDP proveniente del puerto 53 (DNS) que llegara de forma masiva desde el exterior y que **NO coincidiera con una solicitud previa iniciada desde adentro**, fue catalogado inmediatamente como tráfico de amplificación y descartado en el acto.

### 🤓 El Resultado del Bloqueo
La arquitectura de Zeus demostró nuevamente su eficiencia frente a ataques volumétricos basados en UDP sin estado:

* **Mitigación Quirúrgica:** Todo el tráfico spoofeado y amplificado se cortó en el borde, impidiendo que saturara las interfaces de los routers de distribución.
* **Cero Falsos Positivos:** Los usuarios legítimos continuaron navegando y resolviendo nombres sin notar degradación, ya que sus flujos DNS solicitados permanecieron intactos.
* **Estabilidad Absoluta:** La red absorbió el impacto y el ataque se disipó por completo.

---

Los ataques de amplificación demuestran que las defensas tradicionales basadas en simple volumen o bloqueo por IPs no son suficientes cuando te inundan desde miles de servidores DNS legítimos de todo el mundo. Se necesita inspección inteligente de flujos en el borde de la red.

¿Querés saber cómo **Zeus Firewall** puede proteger la infraestructura de tu ISP contra ataques DNS reflectivos y volumétricos? 

Escribinos a **hola@waugi.com** o contactanos por mensaje privado para coordinar una demostración técnica sin costo ni compromiso. ¡Protegé tu red hoy!
