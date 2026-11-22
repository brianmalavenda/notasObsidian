

1. **¿qué equipo hace hoy el ruteo entre VLANs y el firewall** (el router del ISP, un MikroTik, pfSense/OPNsense, otro)? De eso depende cómo implementar el bloqueo de DNS externo y las reglas inter-VLAN, y puedo darte los pasos concretos para ese equipo.
2. **Las VLANs ①, ② y ③ deben estar aisladas entre sí a nivel de firewall.** Que los tres segmentos “lleguen” a las apps no significa que deban verse entre sí. La regla debería ser: _denegar todo entre VLANs por defecto_ y permitir solo lo explícito.